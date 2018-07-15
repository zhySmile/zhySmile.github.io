---
title: U3D AssetBundle 官方文档（翻译版）学习笔记
categories: 技术分享
tags: [Unity,AssetBundle]
---
AssetBundle 官方文档学习笔记
<!-- more -->
最近在看[何三思](http://www.cnblogs.com/hearthstone/)翻译的 Unity AssetBundle 官方文档，在此整理一下前半部分笔记

# AssetBundles
## AssetBundle是什么？
会在运行时被加载的归档文件，包含了和平台相关的特定资源。（Models、Textures、Prefabs、Audio clips、Scenes）
## 压缩方式？
根据实际需要，选择引擎内置的算法进行压缩。（LZMA 算法和 LZ4 算法）
## 作用？
可下载的内容更新（DLC)、降低初始安装包大小、针对用户平台进行最优化资源加载、降低运行时的内存压力
## ssetBundle里有什么？
1、存储在磁盘上的实际文件，称这种文件为“AssetBundle 归档",里面保存了额外的一些文件。分为两种：序列化文件和资源文件。
2、实际的 AssetBundle 对象。包含了添加到归档中的所有资产（Assets）的文件路径以及与这些资产相对应的对象们这两者之间的映射。
什么是序列化文件？
用资产组合形成各个对象，将他们写入磁盘中的一个文件，这个文件就是序列化文件。
什么是资源文件？
一个个二进制数据块，这些数据块分别存储了特定类型的资产，这可以让我们在另一个线程上上更有效的从磁盘中加载它们。

# AssetBundle工作流
## 配置 Assets 到 AssetBundles?
在 Inspector 面板底部，可以设置 AssetBundle 及其变量。左侧设置 AssetBundle，右侧设置变量。下拉选项栏会显示已经注册过的 AssetBundle 的名称选项
## AssetBundle 名称？
支持目录结构。想要添加子文件夹，只需要用”/“分隔文件名称即可。
## 变量？
变量名称对创建 AssetBundle 不是必须的。
## 创建 AssetBundles？
写一个编辑器脚本
BuildPipeline.BuildAssetBundles(string outputPath, BuildAssetBundleOptions assetBundleOptions, BuildTarget targetPlatform)；
所有被命名了的 Assets 都会被构建成对应的 AssetBundle ，并放置在定义的路径下。
加载 AssetBundles 和 Assets?
本地仓库：AssetBundle.LoadFromFile(string path)；
远程仓库：UnityWebRequest.GetAssetBundle(string uri)；  DownloadHandlerAssetBundle.GetContent(UnityWebRequest www);
获取 AssetBundle 对象？
LoadAsset<T>(string)：  T 加载的资源类型  string 加载的对象名称。 返回从 AssetBundle 中加载的任何对象。

# 为打包 AssetBundles 准备资产
## 分组策略？
### 一：逻辑实体分组策略
概念：将 Assets 分配到 AssetBundle 的时候，考虑到的是这些 Assets ，代表了某个项目的某个功能性部分。
举例：
1.把一个 UI 界面的所有 textures 和布局数据，打包到一起
2.把一个或者一系列游戏角色的模型和动画动作，打包到一起
3.把需要在多个场景中共享的 textures 和模型，打包到一起
用途、优点：对于可下载内容（DLC)是个理想的解决方案，因为所有资产都是呗分隔开的。可以只改变单个实体，无需去下载其他额外的、并没有改变的 Assets。
注意：在给 AssetBundle 分配 Assets 的时候，必须明确的知道，每个资产在什么时候，以及什么地方会被项目用到。
### 二：类型分组策略
概念：把类型相似的资产打包到一起。
举例：音频文件、本地化语言文件等
用途、优点：适用于跨平台应用。例如，音频文件在 windows 和 mac 平台使用相同的压缩方法，就可以将音频资源打包到一个（或者多个，如果资源太多的话） AssetBundle中，并且可以反复使用这些 AssetBundles 。相反的一个例子是：mac 上压缩好的 shaders ，在 windows 中是不能使用的。
注意：如果纹理压缩格式以及相关设置，和代码脚本、预设等 Assets 相比没有那么频繁地改动，那么使用这个策略可以使 AssetBundles 兼容更多 Unity 版本。
### 三：并行分组策略
概念：将需要同时加载和使用的资产打包到一起。
举例：游戏中的一个关卡，包含了独属于这个关卡的角色、纹理、音乐等资源，该策略就是用于打包这类资产。最典型的案例，是将整个场景打包成 AssetBundle 。这样的话，每个场景都应该包含了其需要的所有依赖。
注意：需要完全确定某个 AssetBundle 中的资产，只会同该 AssetBundle 中的其他资产同时使用。如果 AssetBundle A 对 AssetBundle B 中的某个资产有依赖，那么会显著增加加载时间。因为在加载 AssetBundle A 的同时，会被强制加载整个 AssetBundle B
## 分组打包策略总结:
1.将频繁更新的资源，与那些几乎不会更新的资源，打包进不同的 AssetBundle。
2.将可能会被同时加载的资源打包到一起。比如：一个模型及其纹理和动画。
3.如果一些 AssetBundle 中的一些对象，对另一个 AssetBundle 中的某个资产有依赖，那么应该把被依赖的资产单独提出来，打包成一个 AssetBundle 。另外，如果有一些 AssetBundles 拥有相同的一组资产，那么应该将这组资产提出来，打包成一个 AssetBundle ，作为共享资源包。
4.如果两组对象不太可能同时被加载，比如标准画质和高级画质对应的资产，确保它们有各自的 AssetBundle。
5.如果某个 AssetBundle 中将近 50% 的资产不会频繁的与其他资产同时加载，那么可以考虑将它们分开。
6.如果有少量资产（比如5到10个），它们会被频繁地同时被加载，那么可以考虑将它们合并。
7.如果有一组对象，仅仅只是同一对象的不同版本，那么可以考虑使用 AssetBundle 变量。

# 创建 AssetBundles
## 深入解析 API BuildPipeline.BuildAssetBundles(string outputPath, BuildAssetBundleOptions assetBundleOptions, BuildTarget targetPlatform)
### 参数1：outputPath
只是一个目录，创建好的 AssetBundles 将输出到该目录下，可以更改为任何想要的目录，只要确保在创建 AssetBundles 前该目录真实存在。
### 参数2：assetBundleOptions
关于 BuildAssetBundleOptions 的部分 API:
BuildAssetBundleOptions.None？
概念:使用 LZMA 算法压缩文件，把序列化数据文件压缩为一个 LZMA 流。如果使用了该算法，在使用 AssetBundle 内的资源前，需要将 AssetBundle 完全解压。这就导致了即便使用了尽可能小的文件，仍然需要长一些的加载时间。
注意：当使用这个选项时，想要使用任何 AssetBundle 中的资源，整个 AssetBundle 必须处于未压缩状态。
一旦 AssetBundle 被解压，它将再次被 LZ4 压缩算法压缩，并存储在磁盘中。与 LZMA 算法不同的是，使用 LZ4 算法压缩的 AssetBundle，不需要等待整个 AssetBundle 被解压，就可以使用其中的任何资源。这种算法最好的使用场景是：一个 AssetBundle 包含了很多资源，当我们获取并使用其中的某个资源后，其他资源也将被陆续加载。比如：打包一个角色或者一个场景的所有资源。
用途、优点：LZMA 压缩算法只被推荐用于从远程服务器下载的小文件，作为初始化之用。一旦文件被下载下来，它们就会被缓存为 LZ4 算法压缩的 AssetBundle。（LZMA 算法的压缩比更大，相应的解压时间更长；LZ4 算法的压缩比稍小，但是解压时间大幅缩短。因此除了上段推荐的“从远程服务器下载小文件用于初始化”，其他场景建议使用 LZ4 算法压缩 AssetBundle）
BuildAssetBundleOptions.UncompressedAssetBundle？
概念：使用这个选项创建的 AssetBundle，其中的资源将完全不压缩。
用途、优缺点：这会导致下载大文件时耗时较长，但是相应的，一旦下载完成，加载资源会很快。
BuildAssetBundleOptions. ChunkBasedCompression？
概念：这个选项使用 LZ4 算法压缩资源，和 LZMA 压缩算法相比，压缩后的 AssetBundle 更大，但是在使用其中资源的时候不需要整个 AssetBundle 被解压。LZ4 使用一种基于数据块的算法， AssetBundle 在被加载时，会被当成一片片“数据块”依次加载。因此当一片“数据块”被解压后，其中包含的资源就可被使用，就算 AssetBundle 中的其他“数据块”还没有被解压。
用途、优缺点：ChunkBasedCompression选项拥有与完全不压缩的 AssetBundle 相近的加载时间，同时降低了存储在磁盘上的大小。
### 参数3：targetPlatform
这个参数是用来告诉“构建流水线”（build pipeline）我们想要在哪个平台使用这些 AssetBundles。你可以在 BuildTarget 的 API 引用中查看详细的选项列表。当然，如果你不愿意硬编码 BuildTarget ，你可以使用 EditorUserBuildSettings.activeBuildTarget ，它会根据你当前设置的编译平台，来自动找到对应的平台创建 AssetBundle。
## 创建成功 AssetBundles ？
成功创建 AssetBundles之后，在 AssetBundles 目录下一共有 2(n+1) 个多出来的其他文件。
### 目录文件？
对于编辑器中的每个 AssetBundle，有一个以该 AssetBundle 名称命名、以“.manifest”为后缀的文件。
另外还有一个额外的 bundle，并且 manifest 的名字没有和任何一个你创建的 AssetBundle 同名。它实际上是以所在的目录命名（即 AssetBundles 被创建到的目录）。这就是 Manifest Bundle。
### AssetBundle 文件？
这是指没有 “.manifest” 后缀名的文件，我们将在运行时加载它，并获取利用其中的资源。
AssetBundle 文件是一个包含了多种多样文件的归档（压缩）文件。归档的结构会有轻微的变化，这取决于它是一个普通的 AssetBundle 还是一个场景的
### Manifest 文件?
对于每个生成的 AssetBundle，包括额外的 Manifest Bundle，都会有个相关联的 manifest 文件同时生成。manifest 文件可以被任何文本编辑器打开，里面包含了 AssetBundle 相关的一些信息，比如循环冗余检查（CRC：cyclic redundancy check）以及其他依赖数据。

# AssetBundle依赖关系
如果一个或者多个 UnityEngine.Objects 引用了其他 AssetBundle 中的 UnityEngine.Object，那么 AssetBundle 之间就产生的依赖关系。相反，如果 UnityEngine.ObjectA 所引用的UnityEngine.ObjectB 不是其他 AssetBundle 中的，那么依赖就不会产生。
假若这样（指的是前面两个例子的后者，既不产生依赖的情况），被依赖对象（UnityEngine.ObjectB）将被拷贝进你创建的 AssetBundle（指包含 UnityEngine.ObjectA 的 AssetBundle）。
更进一步，如果有多个对象（UnityEngine.ObjectA1、UnityEngine.ObjectA2、UnityEngine.ObjectA3......）引用了同一个被依赖对象（UnityEngine.ObjectB），那么被依赖对象将被拷贝多份，打包进各个对象各自的 AssetBundle。
如果一个 AssetBundle 存在依赖性，那么要注意的是，那些包含了被依赖对象的 AssetBundles，需要在你想要实例化的对象的加载之前加载。Unity 不会自动帮你加载这些依赖。
想想看下面的例子， Bundle1 中的一个材质（Material）引用了 Bundle2 中的一个纹理（Texture）：
在这个例子中，在从 Bundle1 中加载材质前，你需要先将 Bundle2 加载到内存中。你按照什么顺序加载 Bundle1 和 Bundle2 并不重要，重要的是，想从 Bundle1 中加载材质前，你需要先加载 Bundle2。

未完待续 敬请期待...