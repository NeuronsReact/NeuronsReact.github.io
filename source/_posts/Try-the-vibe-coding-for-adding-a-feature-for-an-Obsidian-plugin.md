---
title: Try the vibe coding for adding a feature for an Obsidian plugin
tags:
  - Software
categories:
  - IT
  - Software
date: 2026-05-02 23:56:00
updated: 2026-05-02 23:56:00
---


之前在 IDE 用 AI 只是来写脚本和做运维之类的杂活。这次尝试做一个稍大的 project，就是给 Obsidian LiveSync 插件的 S3/C2 连接配置增加一个 journal compact 功能，降低 S3/C2 连接配置下的网络 IO。

<!-- more -->


## 需求的来源
LiveSync 使用 S3/C2 连接配置 + event-triggered syncing 时，每一个小更改都会打包成一个 raw journal。远端存储桶中的 journal 会越来越多，几个月下来可能累计到几千个 journals。同步操作会变得比较耗时。尤其是 S3/C2 连接配置+移动端的组合下，同步不总是可靠，有时要重传所有 journals 来修复，非常折磨。因此笔者每隔一段时间就重建一次远端。不过重建远端需要的时间、精力不可忽视，最终还是要彻底解决。笔者计划先实现自动 compact 远端，之后再考虑解决 journal 同步不够可靠的问题。

## Let's start
对 coding 一窍不通，也不了解数据库结构？不慌，先写一版自然语言的需求：
<details>
我对该Obsidian同步拓展有一个设想。

假定使用S3/C2同步profile，有A、B、C三个终端，每个终端的同步状况如下

|Journal 序号| 终端A | 终端B | 终端C |  云端 |
|------------|------|-------|-------|-----|
|1|A|B|C|S|
|2|A|B|C|S|
|3|A|B||S|
|4|A|B||S|
|5|A|B|||
|6|A||||
|7|A||||


功能设想1: 对于各端都有的journal，比如journal1、2，各端都能将本地和云端这两个journal合为1个，并且尽量不要因为这个journal合并触发一次有写入动作或传输journal内容的同步。

可能出现的问题1-1: C对比云端之后，发现3和4都需要拉取下来，于是先拉取3，然后触发了journal1~3的合并，之后拉取4，结果再次触发journal1~3和journal4的合并。这些合并操作中可能有不必要的。

可能出现的问题1-2：A正在将1和2合并为1个journal，而C拉取journal3到本地，触发了journal1~3的合并。这两个合并在同步时可能会冲突。

可能的解决方案1-2: 先获取journal在各端的存在状态，然后先本地合并，再上传。

可能出现的问题1-3: 在现有的功能中，C是否能够发现journal1~2是各端都有的？如果不行，云端的journal可能需要记录哪些设备有，这样会不会造成云端database过于庞大和复杂，并且一旦有设备不再使用该云端进行同步，其他端会始终认为有设备还没有同步，导致有些journal不能被合并，尽管真实情况是合并不会带来任何影响？

可能的解决方案1-3: 增加一个字段记录各journal在哪些设备上存在，并且给出一个管理设备的UI界面，可以从所有字段中增加、删除某个设备。

功能设想2: 对于journal3~5，A、B两个端可以触发合并，直接覆盖掉journal3~4.

可能的问题2-1: C端只看到3~4有更新，于是尝试拉取。之后再拉取时可能只能获取到3~5合并之后的，与3~4合并后的存在冲突。

</details>

`git clone` + codex，启动！让 gpt 借鉴他认为成功的大型项目和查阅已有代码来回复。gpt 把自然语言需求转化成了开发需求，给出了需求落地的思路和落地顺序：
<details>
**协议上要新增的两个关键概念**

- **连续进度 cursor**
    - 设备只能上报“我已连续处理到哪”
    - 不上报“我看到过最新的哪个文件”
    - 这样不会因为中间有洞而误删 journal
- **manifest generation**
    - 设备要知道自己看到的是哪一版 segment 布局
    - GC 不仅看 cursor，还要看设备是否已看到足够新的 manifest

**建议的落地顺序**

- 第 1 阶段：先做设备 cursor/heartbeat/retired UI
    - 这是两种功能设想的基础
- 第 2 阶段：只做“公共前缀 compact”，不删除 raw journal
    - 风险最低，也最容易验证
- 第 3 阶段：引入 manifest 驱动读取，让新设备/落后设备可直接消费 compact segment
- 第 4 阶段：再做安全 GC，删除被 compact 逻辑替代且已确认无人需要的 raw journal
- 第 5 阶段：最后再做“中间区段/尾区段 compact”，也就是你说的 3~5 覆盖 3~4

**我最推荐的结论**

- 设想1 可以做，而且应优先做；但应定义成“**公共前缀 compact + manifest 替代**”，不是每端重写 journal
- 设想2 也能做，但前提是先完成“**manifest 驱动读取**”；否则会和当前线性 StartAfter 拉取模型天然冲突
- 你提出的“设备管理 UI”很有必要，但它应管理**设备 cursor/活跃状态**，而不是逐 journal 存在表
</details>

让 gpt 生成了一版设计草案 markdown，开始阅读，和 gpt 讨论修改。比如 gpt 建议引入设备管理 UI 以便支持 `stale` / `retired` 状态处理，但设备状态切换本应是个几乎无须用户设置的功能，所以将这个工作移出了 MVP（最小可行实现）。

初步流程看上去没问题了。让 gpt 输出了草案、数据结构、开发步骤三份 markdown 给进一步细化做准备。

目前为止，情况还比较愉悦。接下来就是需求细化环节了。

## 需求细化
开始阅读草案。LLM 熟悉语法，但逻辑、视野、细节上就差一些，因此阅读过程中主要关注这两方面问题，与 gpt 持续讨论、修改文档。比如：
```
- 被判断为Retired的设备的json要能够被其他Active设备清除
- 新增compact类型的segment之后，是否会造成该plugin的其他功能异常？
- 如果可行，考虑不要新增compact这种新类型，而是在GC时替换原有的raw segment
- 每个journal的内容往往只有几个字，如果manifest中包含所有journal的信息，体积会不会变得很大，甚至比journal本身还大？
- ……
```

最终的方案是 `Compaction Edit Log + Snapshot Manifest`，简单来说是每个 client 都会自主查询所有 client 的 raw journal 的拥有情况，在一定条件下生成一个 compact journal。一旦需要重新下载所有 journal，获取的是 compact journal + raw journal，不会再出现拉取数千个 journal 的情况了。之后在一定情况下删除被 compact 的 raw journal，也就是 GC。开发流程大致如下：
```
第一阶段：设备状态与基础上报
第二阶段：Snapshot Manifest + Edit Log + 公共前缀 Compact
- Snapshot Manifest 基础能力
- Edit Log 基础能力
- 公共前缀 Compact
- 读取路径接入
第三阶段：MVP 收尾与兼容性验证
第四阶段：Post-MVP 安全 GC
第五阶段：中间区段 Compact 与设备管理
最后阶段：Snapshot Rewrite 与 Edit Log GC
```


接下来细化到数据结构、处理逻辑，比如单个 compact journal 适合的大小、对旧拓展的兼容性、manifest snapshot 压缩控制面的方式。这时，笔者已经感受到了些许压力。

细化几个问题 + 重新输出文档，重开干净 session 让 gpt 检查各文档的问题。gpt 给出了一份长长的清单：
```
- required_cursor 的语义不够清楚：文档定义了 GCPlan.required_cursor，但没有明确它是“区间结束游标”“全局安全前缀”还是别的边界。
- cursor 与 compacted_prefix 的编号体系缺少统一定义：文档多次使用逻辑 segment 编号，但没有写明 raw 对象如何映射到连续逻辑编号
- ……
```
开始汗流浃背了。一方面笔者自己也不知道原始代码中如何实现增量同步、raw journal key 生成和 raw journal 命名的，只能不停地问和消化 + 在 2 个 session 之间来回搬；另一方面要警惕 gpt 增加一些不需要的功能和规则。比如

| gpt “乱加”的功能和规则                            | 为什么不需要                                       |
| ----------------------------------------- | -------------------------------------------- |
| compact 后要触发 GC                           | GC 和 compact 操作是解耦的，可以不绑定                    |
| 维护一套全局编号                                  | 要改底层逻辑；不是 compact 必须的<br>（不过可以考虑，提高安全性）      |
| compact edit log 记录所包含的所有 raw journal key | 不是 compact 必须的；可能导致 log 比 compact journal 还大 |
随着讨论细到代 (绷) 码  (不) 层 (住) 面 (了)，笔者觉得逻辑上基本收口了，决定先开始开发，看它生成什么代码，围绕代码检查和改进。

## 开始开发
#### Stage 1: Device info 上报
开 plan agent，提了些技术意见改改就开写了：
```
`cursor`如果只用来计算是否执行compact，则不需要经常更新、维护一个单独的表。
`cursor`用receivedFiles和sentfiles的并集懒计算。
```
写完让它解释再自己 review：
```
msg1:解释一下你写的这些函数，层级从高到低
msg2:如果`DeviceStateDocument`被判断为含有坏数据，之后的行为是怎样的？
msg3:对于你新增的代码，进行review，关注你说的这些函数、数据是否有逻辑错误或数据结构对应错误、是否有冗余代码（为之后阶段提前做铺垫的不算冗余）：<把msg1的回复贴在这里>
```

Review 似乎没什么问题。开个空的 C2 对象存储，设置好 plugin，同步一下，存储桶`_control/devices/<devicesid>.json`中就有了如下内容：
```
{"cursor":"1777962498338-docs.jsonl.gz","manifest_seen_generation":0,"last_heartbeat":1777962504005}
```
不错，符合预期。最后让它根据代码实际实现的功能返回修改 3 份 markdown 设计文件。

### Stage 2: Journal compact
同样地，甩给 plan agent：
```
阅读`checklist.md`、`data-structures.md`、`draft.md`。规划checklist中“3. 第二阶段：Snapshot Manifest + Edit Log + 公共前缀 Compact”这一阶段的开发。开发规范遵循`devs.md`.
```
看 Plan 没发现问题，开始 implement。之后应用同样的套路：
```
msg1:解释一下你写的这些函数，层级从高到低
msg2:对于你新增的代码，进行review，关注你说的这些函数、数据是否有逻辑错误或数据结构对应错误、是否有冗余代码（为之后阶段提前做铺垫的不算冗余）
msg3:再重新读一下你自己写的函数解释：
<把msg1的回复贴在这里>
说明 async _getRemoteJournals() 的 const StartAfter 应该以 receivedFiles 计算还是receivedFiles+sentFiles
msg4:JournalSyncCompaction.ts 的 localCursor = computeCursorFromJournalFileSets() ，是用来做什么的？
msg5:JournalSyncCompaction.ts 的 localCursor = computeCursorFromJournalFileSets() 应当用 receivedFiles 还是 sentFiles与receivedFiles 并集？
```
过程看麻了，太多字了。不过 Review 并没有发现问题，于是 `npm run dev`，试着同步一下，报错了：
<details>

```
5/9/2026, 11:38:20 PM->Replication closed
5/9/2026, 11:38:20 PM->[ReplicatorService]  Acquiring new replicator
5/9/2026, 11:38:20 PM->[ReplicatorService]  Replicator (MINIO) initialised and activated
5/9/2026, 11:38:22 PM->Could not download json _00000000-milestone.json
5/9/2026, 11:38:22 PM->Error:Request failed, status 404
[StackTrace]: _LiveSyncError: [Error Logged]: Request failed, status 404
    at globalLogFunction (plugin:obsidian-livesync:29113:47)
    at Logger (plugin:obsidian-livesync:4769:3)
    at JournalSyncMinio.downloadJson (plugin:obsidian-livesync:67461:7)
    at async LiveSyncJournalReplicator.markRemoteLocked (plugin:obsidian-livesync:96642:10)
    at async eval (plugin:obsidian-livesync:90640:16)
    at async ObsidianReplicationService.markLocked (plugin:obsidian-livesync:90637:12)
    at async ServiceRebuilder.rebuildEverything (plugin:obsidian-livesync:93036:5)
    at async eval (plugin:obsidian-livesync:99909:7)
    at async processVaultInitialisation (plugin:obsidian-livesync:99860:22)
    at async onScheduled (plugin:obsidian-livesync:99908:12)
[CausedBy]: Error: Request failed, status 404
    at new t (app://obsidian.md/app.js:1:1066656)
    at ly (app://obsidian.md/app.js:1:1066848)
    at app://obsidian.md/app.js:1:1067527
    at app://obsidian.md/app.js:1:258511
    at Object.next (app://obsidian.md/app.js:1:258616)
    at a (app://obsidian.md/app.js:1:257334)
5/9/2026, 11:38:22 PM->Lock remote bucket to prevent data corruption
5/9/2026, 11:38:24 PM->Replication closed
5/9/2026, 11:38:26 PM->1 items has been deleted!
5/9/2026, 11:38:26 PM->Nothing to delete!
5/9/2026, 11:38:26 PM->Remote Bucket Cleared
5/9/2026, 11:38:26 PM->Replication closed
5/9/2026, 11:38:26 PM->Remote Database Created or Connected
5/9/2026, 11:38:27 PM->Could not download json _obsidian_livesync_journal_sync_parameters.json
5/9/2026, 11:38:27 PM->Error:Request failed, status 404
[StackTrace]: _LiveSyncError: [Error Logged]: Request failed, status 404
    at globalLogFunction (plugin:obsidian-livesync:29113:47)
    at Logger (plugin:obsidian-livesync:4769:3)
    at JournalSyncMinio.downloadJson (plugin:obsidian-livesync:67461:7)
    at async JournalSyncMinio.getSyncParameters (plugin:obsidian-livesync:66620:36)
    at async _fetchSyncParameters (plugin:obsidian-livesync:38952:24)
    at async Object.getPBKDF2Salt (plugin:obsidian-livesync:39020:26)
    at async JournalSyncMinio.getReplicationPBKDF2Salt (plugin:obsidian-livesync:66787:12)
    at async LiveSyncJournalReplicator.getReplicationPBKDF2Salt (plugin:obsidian-livesync:96435:12)
    at async LiveSyncJournalReplicator.ensurePBKDF2Salt (plugin:obsidian-livesync:38887:21)
    at async LiveSyncJournalReplicator.tryCreateRemoteDatabase (plugin:obsidian-livesync:96625:5)
[CausedBy]: Error: Request failed, status 404
    at new t (app://obsidian.md/app.js:1:1066656)
    at ly (app://obsidian.md/app.js:1:1066848)
    at app://obsidian.md/app.js:1:1067527
    at app://obsidian.md/app.js:1:258511
    at Object.next (app://obsidian.md/app.js:1:258616)
    at a (app://obsidian.md/app.js:1:257334)
5/9/2026, 11:38:27 PM->Could not retrieve remote sync parameters
```
</details>

除了无法下载，似乎上传也有问题：
<details>

```
5/9/2026, 11:38:40 PM->Could not upload _control/manifest/manifest-000000.json
5/9/2026, 11:38:40 PM->Error:Request failed, status 501
[StackTrace]: _LiveSyncError: [Error Logged]: Request failed, status 501
    at globalLogFunction (plugin:obsidian-livesync:29113:47)
    at Logger (plugin:obsidian-livesync:4769:3)
    at JournalSyncMinio.uploadFileInternal (plugin:obsidian-livesync:67519:7)
    at async JournalSyncMinio.uploadFileConditional (plugin:obsidian-livesync:67492:12)
    at async JournalSyncMinio.uploadJsonConditional (plugin:obsidian-livesync:67442:14)
    at async JournalSyncCompaction.ensureInitialSnapshotManifest (plugin:obsidian-livesync:63226:28)
    at async eval (plugin:obsidian-livesync:63333:29)
    at async task (plugin:obsidian-livesync:6183:18)
[CausedBy]: Error: Request failed, status 501
    at new t (app://obsidian.md/app.js:1:1066656)
    at ly (app://obsidian.md/app.js:1:1066848)
    at app://obsidian.md/app.js:1:1067527
    at app://obsidian.md/app.js:1:258511
    at Object.next (app://obsidian.md/app.js:1:258616)
    at a (app://obsidian.md/app.js:1:257334)
5/9/2026, 11:38:41 PM->Rebuild everything operation completed.
```
</details>

问了一下 gpt，似乎是 `501 Not Implemented` —— 当前远端不支持 AWS S3 的条件写。但为了支持 S3 同步花这份钱，还不如搞个容器云跑 CouchDB。后者 + LiveSync 拓展的表现很接近官方同步。所以最后搏一把，设计另一种不需要条件写的方案：
<details>
We have to abandon manifest/edit scheme because the remote does not support conditional `put`. The new scheme is inspired by "单点写入化 (Single-Writer Pattern)". 单个设备的写入是物理上几乎不可能发生冲突的。新方案的思路有如下几点：

- Raw journal key changed to <timestamp>-<deviceId>.json . To maintain the compatibility with the old raw journal, the old raw journal keys without "-<deviceId>" should be treated as they are uploaded from a device named "others".

- Compact journal key changed to <raw journal key range>-<deviceId>.json

- 不再区分中间段 compact 和所有 devices 都消费过的 raw journal （"cursor")组成的 compact。Every device activates compact when "the number of non-compacted raw journals generated from itself (remoted raw journals + sentFile list)" > 6. 如果准备发送的 raw journals 的队列超过 6，则先进行 compact 再发。

- The receivedFiles's format changed to array, recording the received file list from all devices.

- Every time a device pull the journals, it firstly `list` the compact journals under `_control/compaction-edits/`, to get new compact journals from known devices and new devices and also update the receivedFiles array. Then it `list` the raw journals and sorts them by <device>. After that, it pull the raw journals that havn't been received.

- If a device is thought as retired/inactivated + all its raw and compact journals received on all active devices, its raw and compact journals should be or have been rewritten as the journals from "archived" devices, and its <device> json should be removed.

when the number of compact journals > number of devices*4.

- If a retired/inactivated device is not related to any existing compact/raw journal, it should be removed from the `_control/devices`.

- If a device finds out that a device in local device list is not existing in the remote, it should immediately check whether itself had been retired or inactivated (its <device> json tagged with "retired" or "inactivated",or not existing on the remote like a new devices). If itself is marked as retired or inactivated, it should prevents syncing and refetch all remote data. (如果有多个 devices 同时 retired，那么有可能它们的本地数据都是 outdated 的。这时无法确定自己的本地数据库中另一个 retired 的数据是不是完全获取了，无法保证本地数据的完整性。此时最粗暴的方式是 refetch，然后与本地文件进行冲突处理）If not, 检查有没有 archive 导致的 journal 被漏掉，见问题2. 如果没有，只更新本地 receivedFiles 和 cursor。

- A device should occasionally check whether itself had been retired or inactivated (its <device> json tagged with "retired" or "inactivated",or not existing on the remote like a new devices.)

问题1：
设备 A 通过获取 device json 发现多个 retired 的设备，且它们的 raw、compact 已被所有 active 设备接收，要将它们的 raw、compact rewrite 到 ”archived“ device （一个用于存储 retired 设备 journal 的虚拟 device）是否满足"单点写入化 (Single-Writer Pattern)"？比如有两个 retired 设备的 compact/raw journal 时间戳有重叠，归到 archived 会不会漏掉一些 journal？

回答1:分情况
如果两个或多个设备发现的 retired 的设备是一样的，那 archive 出来的文件是一样的，问题不大
如果遇到非常特别的情况，两个设备或多个设备认为的 retired 设备不同，那之后的文件不同，数据完整性会有问题
建议：实施 archive 的时候 lock remote，不允许其他 device 修改，archive 结束再 unlock

问题2:
客户端怎么发现 archive 导致的 journal 被漏掉，又如何处理？

回答2: 检查本地记录的 device 和 remote 端的 compact，看哪个、哪些 device 被 retired，然后比对 receivedFiles 中这些 device 进行 archive 之后的 key 和 remote 上 archive 的 key 是否一致，不一致说明一定有漏的。但一致也不能说明没有漏的。
建议2: 发现之后停止同步，refetch

你来检查这个新方案是否合理，哪里有逻辑问题，哪里可能会有风险。
</details>

新方案合并了旧 journal compact 和中间 journal compact，只是条件写改成了软锁，似乎也不错，而且看起来就要收敛了。这时事情出现了转折。


## 跑路，但也算解决了
在设计、实现 Stage 2 的 compact 功能时，笔者也在考虑以下问题：
- 这样魔改是否会破坏 Pouch + CouchDB 原本的逻辑？
- 纯 S3 的数据库操作太慢了。独立的 SQL + S3 是否才适合笔记同步？

带着这些问题给 Gemini 提问：
Q1: 有哪些免费的sql托管服务？
Q2: Neon 似乎是建立在 S3 对象存储上的数据库。如果我有自己的对象存储，能否自部署数据库？
A2: 只有持久化层能用自己的 S3.
Q3: 我希望给一个笔记App的同步功能找一个CouchDB的替代方案。不是每个人都愿意开一个付费的CouchDB托管。有些用户想要利用已有的S3/C2对象存储
A3: Joplin 模式\RxDB + Remote Storage\PouchDB + 自定义适配器\Hocuspocus (Yjs) + S3. 显然 PouchDB + 自定义适配器大概率就是 LiveSync 的实现方式。
Q4: 已经尝试了PouchDB + 自定义适配器。问题是通过S3进行数据库类型的读写，会在存储桶产生有很多json，而这些json始终通过网络传输，速率不高，体验较差。CouchDB有没有内置方法改进这点？

结论似乎指向了“没有内置方法改进 PouchDB + 自定义适配器方案”。这时笔者打算碰运气找找有没有 Yjs + S3 的实现，结果还真有：[https://github.com/No-Instructions/Relay](https://github.com/No-Instructions/Relay). 不过是付费的，似乎由开发者自己托管。
有付费托管，那肯定有纯自托管的。继续找发现这个：[YAOS](https://github.com/kavinsood/yaos)，只需要免费 Cloudflare 的 worker 就能部署。作者做好了一键部署，只要网页端点一下按钮，登录 Cloudflare、授权 GitHub，一步步按提示就完成了文本同步的配置，甚至不需要电脑。
R2 存储这里有点小坑，主要是 Cloudflare worker 中添加 R2 binding 时，并没有在 `repo/wrangler.toml` 中添加上 R2，所以是无效的，要手动在 `repo/wrangler.toml` 中添加

```toml
[[r2_buckets]]
binding = "YAOS_BUCKET"
bucket_name = "<Your R2 bucket name>"
```
注意 binding 的名称必须是 "YAOS_BUCKET"，否则插件/ worker 不认。
保存之后会自动重新部署，再去 Obsidian 中 Refresh，附件同步也就好了。至于同步效果，几乎是 1 秒以内的延迟。就算多端同时编辑同一份笔记，也不冲突。

至于之前 Vibe 的产物，GitHub 上已经存了：https://github.com/NeuronsReact/obsidian-livesync 和 https://github.com/NeuronsReact/livesync-commonlib

## 结语
Vibe 的过程似乎揭示了软件开发甚至是产品开发的基本逻辑：应用需求 -> 技术需求 -> 技术路线设计、标志物设置（本文中是生成的代码，也就是围绕生成的代码进行功能检查和改进，而不是围绕 3 份手册）-> 具体开发任务、测试 -> 检验、验收。虽然最终没能用 Vibe 实现想要的功能，但结果是比预期还要好不少的。