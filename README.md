# 猪猪视频

[TOC]

一个安卓视频播放器，已上传github：

[猪猪视频播放器https://github.com/ydyzlq/Android](https://github.com/ydyzlq/Android)

## 详细介绍

该软件主要分为多个板块，登录注册页面，视频播放页面，个人中心页面等。

### 登录注册页面

1. 用户信息存储于SQLite数据库中，在进行登录验证时，从数据库中读取数据进行判断，注册同理（保证用户id跟用户名唯一）。

自己封装实现了DbcUtils类以及DbContect类用来连接以及操作数据库。

以下是封装实现的该应用的一些数据库操作。

![image.png](https://pic.leetcode.cn/1702651396-gWoGeb-image.png)

2. 实现了记住密码功能，当用户登录成功时点击保存密码，那么下次用户打开APP或者是进入到登录页面时，就会进行自动填充，不需要再次输入。

使用SharedPreferences类来将记住密码的账号密码存储在缓存中，如果下一个不选择记住密码则进行清空。初始为空值。

![image.png](https://pic.leetcode.cn/1702651430-eTvQYk-image.png)

### 视频播放页面

#### ListView  -- ListViewFragment

使用简单的继承自BaseFragment的实现类，其显示内容为数据库中所有用户的所有视频，通过查询数据库的方式去InitData，单个item实现点击播放以及暂停功能。

#### 抖音 --  TikTokListFragment

同样继承自BaseFragment，但是这里的显示内容为爬虫所获取到的存储在云端的视频连接进行显示。

```java
public class TikTokListFragment extends BaseFragment
```

在上面类中实现View视图以及适配器

布局设置

![image.png](https://pic.leetcode.cn/1702651461-chZpSC-image.png)

每一行使用两列布局，实现两个视频同时在一行显示，扩充视图中的视频容量。

#####  视图显示

fragment_tiktok_list 设置主要格式，里面就设置一个RecycleView，然后定义了一个item_tiktok_list来设置每一块的布局，包括视频跟标题。

##### 适配器设置

![image.png](https://pic.leetcode.cn/1702651484-xbBmge-image.png)

onCreateViewHolder 方法用于创建并返回一个TikTokListViewHolder对象。这个对象表示RecyclerView中每个列表项的视图。

onBindViewHolder 方法用于将数据绑定到TikTokListViewHolder中的视图上。它从数据集合中获取特定位置的TiktokBean对象，然后将其标题设置到TextView上，将封面图加载到ImageView中。

![image.png](https://pic.leetcode.cn/1702651506-JLaypF-image.png)

TikTokListViewHolder 是一个内部类，表示RecyclerView中每个列表项的视图持有者。它包含了列表项中显示的ImageView和TextView，以及一个点击事件监听器。当列表项被点击时，它会启动TikTok2Activity，并传递相应的位置信息。

这就会产生从下左图到右图的效果：

![image-20231225091956005](D:\Android\course-work\tmp\README.assets\image-20231225091956005.png)

##### TikTok2Activity 
initVideoView 方法初始化了视频播放组件 VideoView。
initViewPager 方法初始化了垂直滚动的 ViewPager，设置了适配器、页面切换监听器。
startPlay 方法用于开始播放指定位置的视频。在滚动页面时，根据页面位置找到对应的 ViewHolder，通过 VideoView 播放视频。
addData 方法用于添加视频数据，从 DataUtil 中获取 TikTok 风格的数据并更新适配器。、

TikTok2Activity 具体的适配器设置：与其他适配器类的主要区别在于对于initViewPager（）方法
用来判断当前页面滑动顺序播放页面  也就是实现了一个滑动监听器。

设置 ViewPager 的滑动监听器：
setOnPageChangeListener: 通过 ViewPager.SimpleOnPageChangeListener 监听器，实现对页面切换的监听。

onPageScrolled 方法：
监听页面滑动过程中的回调。在这里，通过比较当前位置 position 和上一次的位置 mCurItem，判断滑动的方向，即是否反向滑动。

onPageSelected 方法：
页面选中时的回调。在这里，判断当前选中的页面位置是否和上一次的位置相同，如果不同，则调用 startPlay 方法，可能用于开始播放视频。

![image-20231225092151187](D:\Android\course-work\tmp\README.assets\image-20231225092151187.png)

### 个人中心页面

这里主要实现一些APP的关于账户的操作，包括退出登录，修改密码，显示个人信息等操作。

该页面通过Intent进行组件间的通信，从登录页面获取到当前登录用户的信息，并显示在用户名中。

![image-20231225094854345](D:\Android\course-work\tmp\README.assets\1703468823-nEVCfj-image.png)

![image-20231225094815113](D:\Android\course-work\tmp\README.assets\1703468817-PdBwix-image.png)

修改密码功能，为了减少Fragment的设计数量，选择使用弹窗的形式进行操作。

![image.png](https://pic.leetcode.cn/1702651541-Xnkfab-image.png)

通过 `LayoutInflater` 创建一个自定义的视图（`R.layout.dialog_update_password`），该视图包含三个输入框用于输入原密码、新密码和确认新密码。在点击确定按钮时，可以通过 `view.findViewById()` 获取输入框的引用，并获取输入的内容进行相应的处理。确保在你的布局文件中包含了这三个输入框。

##### 上传文件

当点击文件上传按钮时，则会跳转到文件夹中，选择视频然后进行本地上传。同时当前上传的视频会显示在按钮下方。同时在文件上传成功之后，通过动态广播机制，通知“我的”页面进行数据更新。

<img src="D:\Android\course-work\tmp\README.assets\1703469136-yGWCBc-image.png" alt="image-20231225095155440" style="zoom: 67%;" />

<img src="D:\Android\course-work\tmp\README.assets\1703469153-xiTQlt-image.png" alt="image-20231225095141249" style="zoom: 67%;" />

它的主要功能包括从 selectedVideoUri 获取输入流，将视频保存到本地目录，并显示本地视频文件。
然后发送广播，通知 我的 页面进行页面数据更新

同时为了防止文件上传被覆盖，使用UUID进行文件名唯一生成，可以防止这个问题

<img src="D:\Android\course-work\tmp\README.assets\1703469188-lMdmjV-image.png" alt="image-20231225095320456" style="zoom:67%;" />

##### 改进

改进：使用后台上传文件
考虑到可能视频文件较大，影响上传效率，使用Service后台进程实现文件上传工作。在UploadService中实现文件上传逻辑，并用intent从主类中接收到需要上传的文件路径

<img src="D:\Android\course-work\tmp\README.assets\1703469273-UPpkyF-image.png" alt="image-20231225095438760" style="zoom:67%;" />

![image-20231225095517656](D:\Android\course-work\tmp\README.assets\1703469300-OntaNZ-image.png)

在页面的最下面，设置了一个VideoView用来展示最新上传的视频.此外，给这个视频也添加了一个后台Service任务，使得切换到其他视图时，也能够接着播放。

![image-20231225095543383](D:\Android\course-work\tmp\README.assets\1703469346-cBzbeu-image.png)

### 我的 页面

该页面功能较为简单，用来显示用户个人所上传的视频信息。主要操作在于将LoginActivity中的登录信息传递到该Fragment，以及接收广播获取到最新的用户视频数据

（上面个人中心页面也使用该方式）首先是使用Intent通信，将LoginActivity中的登录信息，包括个人账号名以及密码（用于修改密码页面作用）等信息传递到MainActivity。然后在这个里面创建加入Fragment的时候，使用setArguments方法，将从Login接收到的消息传递给Fragment组件。然后通过Adapter参数构造传递给数据构造InitData函数。

接收到广播之后，同时也会接收到新增加的视频的文件路径，会进行数据刷新。
此外，当前视频item显示的封面是采用视频首帧来设置的，将获取到的封面同时上传到本地，命名与视频名称一致。

![image-20231225095724034](D:\Android\course-work\tmp\README.assets\1703469449-dYgLWz-image.png)

<img src="D:\Android\course-work\tmp\README.assets\1703469481-OfvDOD-image.png" alt="image-20231225095757825" style="zoom:67%;" />

个人页面数据初始化：从虚拟机存储路径中获取到上传的所有视频以及保存的封面。

主要通过构建视频文件存放的绝对路劲进行获取。

<img src="D:\Android\course-work\tmp\README.assets\1703469562-QzQaDB-image.png" alt="image-20231225095912381" style="zoom:67%;" />

### 仿Youtube 视频播放页面

效果展示：

![image.png](https://pic.leetcode.cn/1702807268-gDZQly-image.png)

主要布局XML说明如下：

首先在fragment_channel 页面显示视频列表，该列表最外层使用SwipeRefreshLayout布局，这是Google官方推荐的下拉刷新布局控件，只需要把RecycleView或者ListView放在里面就可以实现简单的下拉刷新。

![image.png](https://pic.leetcode.cn/1702699007-vUkpMA-image.png)

#### 下拉刷新SwipeRefreshLayout

在需要刷新视频内容的ChannelFragment中，代码如下。

其中，刷新算法updateChannelId通过一个`List<String> remainingChannels`，每次从中选择随机项（视频id），然后从列表中移除。当移除为空的时候，使用` Collections.shuffle(remainingChannels);`进行打乱，从而进行下一轮的随机刷新选择。

```java
public class ChannelFragment extends Fragment
{
    private SwipeRefreshLayout swipeRefreshLayout;
    public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState){
          swipeRefreshLayout = view.findViewById(R.id.swipeRefreshLayout);
        // 下拉刷新时的监听事件
         swipeRefreshLayout.setOnRefreshListener(this::refreshContent);
    }          
```

![image.png](https://pic.leetcode.cn/1702804023-QYiEgu-image.png)

在values/channel_id.xml下，提供了几个Youtube频道爬取id：

![image.png](https://pic.leetcode.cn/1702804086-RgGODG-image.png)

#### Googleapis爬取视频

由于是爬取的Youtube视频，需要到Google的开发者控制台https://console.cloud.google.com/ 下申请启用[YouTube Data API v3](https://console.cloud.google.com/apis/api/youtube.googleapis.com/overview?project=youtubeapi-407214) 服务。在”凭据“处获取API KEY，此密钥用于访问爬取服务googleapis。

![image.png](https://pic.leetcode.cn/1702804332-ZfdJft-image.png)

![image.png](https://pic.leetcode.cn/1702804473-iioVZS-image.png)

在其中，maxResults表示最大请求数量，比如如果是访问频道视频，最多会爬取20个视频信息。googleapis获取的频道链接内容是Json格式，需要做Parse解析工作，得到需要的视频信息列表。

#### OkHTTP 请求Url + AsyncTask 异步网络请求

上面准备工作完成后，需要对URL进行请求，并处理响应(response)，通过响应的内容（Json字符串）进行解析，得到数据。在使用OkHTTP的时候，在ChannelFragment中，进行引用：

```java
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
```

`RequestYoutubeAPI`类是ChannelFragment中的内部类，用于GoogleapiURL的请求。它是一个继承自AsyncTask的类。由于主线程用于处理 UI 事件，如果在主线程中执行一些耗时的操作（例如网络请求、文件读写等），可能会导致界面卡顿或 ANR（Application Not Responding）错误。

为了解决这个问题，可以使用 AsyncTask 将耗时的任务放在后台线程（子线程）中执行，而在主线程（UI线程）中执行一些与 UI 相关的操作。 在早期版本的 Android 中，AsyncTask 主要用于在后台线程执行异步任务，并在主线程中更新 UI。它不是传统意义上的多线程或协程。它是一种轻量级的异步任务框架， 其内部实现依赖于线程池和消息队列，以简化在 Android 应用中进行异步操作的代码编写。

```java
private class RequestYoutubeAPI extends AsyncTask<Void, String, String>
    // AsyncTask 用于后台线程执行异步任务的类，它基于线程池和消息处理机制。
```

AsyncTask 是一个在不需要开发者直接操作多线程和 Handler 的情况下的帮助类，适用于短时间的操作（最多几秒）。 如需长时间的线程操作，建议使用多线程包 java.util.concurrent 中的功能，比如线程池。

在使用的时候，它的三个类型：`AsyncTask<Params, Progress, Result>` 描述如下：

| 属性       | 描述                                   |
| ---------- | -------------------------------------- |
| `Params`   | 执行任务前，传入的参数的类型           |
| `Progress` | 后台线程执行的时候，用来表示进度的类型 |
| `Result`   | 表示执行结果的类型                     |

要使用 AsyncTask ，必须新建一个类来继承它，并且重写 `doInBackground` 方法。通常也会重写 `onPostExecute` 方法。 执行异步任务的时候，我们主要关心下面这4个方法。

| 方法                                      | 描述                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| `onPreExecute()`                          | **执行任务前**在ui线程调用。通用用来设置任务，比如在界面上显示一个进度条。在本项目中没有使用到。 |
| `Result doInBackground(Params... params)` | 在 `onPreExecute()` 结束后立即调用这个方法。**耗时的异步任务就在这里操作。**执行任务时传入的参数会被传到这里。本项目中，用于OkHTTP的网络请求。 |
| `onProgressUpdate(Progress... values)`    | 在 ui 线程中执行。后台任务还在进行的时候，这里负责处理进度信息。比如在这显示进度条动画，修改文字显示等。在本项目中没有使用到。 |
| `onPostExecute(Result result)`            | **后台任务结束了调这个方法。它在 ui 线程执行**。最后的结果会传到这。本项目中，用于在这个方法中更新 UI，处理执行结果。 |

doInBackground 方法的重写（传入Void类型，返回String类型的请求结果）。

步骤如下，首先通过new OkHttpClient新建一个请求客户端，然后Build一个请求new Request.Builder().url(频道URL)).build()，在请求中，最多进行MAX_RETRIES次请求，每一次请求时，通过客户端创建一个newCall并执行。

execute是同步方法，网络请求是耗时的，因而需要放到AsyncTask后台线程中。

```java
Response response = client.newCall(request).execute();
```

然后通过`response.isSuccessful()`判断是否请求成功，否则输出异常信息。

![image.png](https://pic.leetcode.cn/1702805376-MWurlX-image.png)

在请求完成后，执行重写的onPostExecute(String response)方法，它接受请求的结果。这是Json字符串构成的信息，然后通过解析Json, 更新数据，更新视图显示，再停止播放刷新动画：`swipeRefreshLayout.setRefreshing(false);`

![image.png](https://pic.leetcode.cn/1702805656-aZoSRU-image.png)

其中，通过数据更新视图是在该方法中调用init(_data)方法，该方法实现中，主要是重设_ recyclerView 的布局管理器（线性布局），然后初始化adapter适配器对象，绑定到该Activity上，再将适配器设置到循环试图即可。适配器的实现，会放在后面介绍。

```java
    private void init(ArrayList<YoutubeDataModel> data)
    {
        _recyclerView.setLayoutManager(new LinearLayoutManager(getActivity()));
        _adapter = new VideoPostAdapter(data, getActivity());
        _recyclerView.setAdapter(_adapter);
    }
```

最后，通过`private ArrayList<YoutubeDataModel> parseVideoListFromResponse(JSONObject jsonObject)` 算法，进行Json解析。这个过程类似于剥洋葱，一层一层的往里面寻找信息。最后完成时，创建YoutubeDataModel对象，这是用来保存视频信息数据的类。

![image.png](https://pic.leetcode.cn/1702805893-QORsRf-image.png)

#### VideoPostAdapter 视频列表适配器

该类继承自RecyclerView.Adapter<T> 类，泛型参数为其内部自定义类VideoPostAdapter.YoutubePostHolder。

定义 Adapter 时，需要替换三个关键方法：

- onCreateViewHolder：每当 `RecyclerView` 需要创建新的 `ViewHolder` 时，它都会调用此方法。此方法会创建并初始化 `ViewHolder` 及其关联的 `View`，但不会填充视图的内容，因为 `ViewHolder` 此时尚未绑定到具体数据。
- onBindViewHolder：`RecyclerView` 调用此方法将 `ViewHolder` 与数据相关联。
- getItemCount：RecyclerView 调用此方法来获取数据集的大小

当RecyclerView 需要展示一个新的项时，它会调用适配器的 onCreateViewHolder 方法来创建一个新的 ViewHolder 实例。一个Holder 代表RecyclerView中的一个项目，表示一个视频。这个类YoutubePostHolder主要作用是绑定到R.layout.layout_youtube_post 中的各个控件，并存储在Holder中。

![image.png](https://pic.leetcode.cn/1702806186-KYrQwd-image.png)

![image.png](https://pic.leetcode.cn/1702806602-KgmxDY-image.png)

内部类YoutubePostHolder实现如下：

```java
 public static class YoutubePostHolder extends RecyclerView.ViewHolder
    {
        TextView _textViewTitle;
        TextView _textViewDes;
        TextView _textViewData;
        ImageView _imageThumb;

        public YoutubePostHolder(@NonNull View itemView)
        {
            super(itemView);
            this._textViewTitle = (TextView) itemView.findViewById(R.id.textViewTitle);
            this._textViewDes = (TextView) itemView.findViewById(R.id.textViewDes);
            this._textViewData = (TextView) itemView.findViewById(R.id.textViewData);
            this._imageThumb = (ImageView) itemView.findViewById(R.id.imageThumb);
        }
    }
```

#### onBindViewHolder 绑定数据与视图

在方法`public void onBindViewHolder(@NonNull YoutubePostHolder holder, int position)`中，会获得类内存储的YoutubeDataModel数据列表信息：

```java
  YoutubeDataModel dataObject = _listVideoDatas.get(position);
```

然后通过Picasso第三方库中提供的加载图片url的方法，将从数据对象中获得到的封面URL字符串信息，加载并装入到ImageView控件中：

![image.png](https://pic.leetcode.cn/1702806999-vdwbfO-image.png)

Picasso还能自动帮我们做以下事情：

- 处理Adapter 中ImageView的回收和取消下载。
- 使用最小的内存 来做复杂的图片变换。比如高斯模糊，圆角、圆形等处理。
- 自动帮我们缓存图片。内存和磁盘缓存。

使用前，需要添加依赖：

```kotlin
//  Picasso 开源图片加载库
implementation 'com.squareup.picasso:picasso:2.71828'
```

设置视频项点击事件（holder.itemView.setOnClickListener），通过实例化一个匿名内部类，作为setOnClickListener方法的参数，这是一种”函数时编程“的思想，实际上是为接口类型参数，填充一个实例化对象。可以类比于C++中的函数指针，C#中的委托。

点击事件需要启动视频播放页面（YoutubeVideoPlayActivity，会在后面介绍），同时向它传递信息，通过intent.putExtra实现。这里需要传递视频的id信息，然后启动页面。

![image.png](https://pic.leetcode.cn/1702807591-JLYwMH-image.png)



### 视频播放 + 评论区页面

这个页面的主要代码，放在YoutubeVideoPlayActivity中。

最终效果，能够实现上方是视频小窗播放，下面是可滚动的评论区。评论区总体上也采用RecycleView布局，每一项是一个Holder，基本与视频列表类似。

评论区理论上能够显示评论发布者的id，头像，发布时间，评论内容等。但是这里从简化处理，省略了头像的显示，如果希望实现，其实与视频封面的显示同理，可以通过Picasso进行get。

![image.png](https://pic.leetcode.cn/1702807503-vuDtqa-image.png)

在实现的时候，大部分代码和视频列表部分类似。需要实现`VideoCommentAdapter`，用作评论适配器；实现内部类`CommentHolder`，用于每条评论视图控件的绑定；`CommentDataModel`类，用作评论数据的存储；在`YoutubeVideoPlayActivity`中，也需要实现`VIDEO_COMMENTS_URL() `获得评论Url，RequestComment 类用于异步网络请求，`parseCommentListFromJson `用于解析评论信息等。

#### AsyncTask替代品：封装Thread

实际上AsyncTask已经被废弃。因此在评论获取的过程中，希望使用Thread多线程替代AsyncTask。后者的主要问题包括由于AsyncTask`与`Activity`或`Fragment`的生命周期无关而导致的内存泄漏等等。

因而我简单封装了Thread 执行子线程任务的函数为一个抽象类BackgroundTask，其命名规范与AsyncTask完全一致。![image.png](https://pic.leetcode.cn/1702882123-VAfbFO-image.png)

在启动子线程任务的时候，首先启动后台子线程。然后使用隶属于Acitivity的方法runOnUiThread()，这个方法是运行于主线程中，可以进行UI的更新。同样也是传递一个Runnable的匿名内部类并实现run()方法。

![image.png](https://pic.leetcode.cn/1702882453-TCTzqh-image.png)

评论数据请求的类定义为：

```java
private class RequestComment extends BackgroundTask
```

#### 视频播放页面布局 ：NestedScrollView 嵌套 RecyclerView

顶层布局使用NestedScrollView，然后内部是一个RelativeLayout，再往内部嵌套了用于视频播放的WebView 和 评论区显示的RecyclerView。

NestedScrollView其作用就是作为控件父布局，从而具备（嵌套）滑动功能。ScrollView嵌套RecyclerView存在滑动冲突，显示不全的问题。NestedScrollView嵌套RecyclerView不会存在显示问题。

#### 视频播放WebView 与 iFrame

播放视频采用WebView嵌入HTML语言的方式实现。这是因为需要调用Youtube提供的iFrame框架，实现获取Youtube视频源。其中HTML示例代码如下：

```html
<!DOCTYPE html>
<html>
  <body>
    <div id="player"></div>
    <script>
      var tag = document.createElement('script');

      tag.src = "https://www.youtube.com/iframe_api";
      var firstScriptTag = document.getElementsByTagName('script')[0];
      firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
  
      var player;
      function onYouTubeIframeAPIReady() {
        player = new YT.Player('player', {
          height: '200',
          width: '400',
          videoId: 'qZ4UEhFEzvI',
          events: {
            'onReady': onPlayerReady,
            'onStateChange': onPlayerStateChange
          }
        });
      }

      function onPlayerReady(event) {
        event.target.playVideo();
      }

      var done = false;
      function onPlayerStateChange(event) {
        if (event.data == YT.PlayerState.PLAYING && !done) {
          setTimeout(stopVideo, 6000);
          done = true;
        }
      }
      function stopVideo() {
        player.stopVideo();
      }
    </script>
  </body>
</html>
```

1. `onYouTubeIframeAPIReady` 函数会在播放器 API 代码下载后马上运行，构造视频播放器对象。
2. `onPlayerReady` 在 `onReady` 事件触发时运行
3. API会在播放器状态改变时调用 `onPlayerStateChange` 函数，指示播放器正在播放、已暂停、已完成等等。

使用iFrame的好处，可以方便的解析视频源，获取Youtube提供的视频播放器功能（倍速，暂停，快进，画质设置等）且完美兼容嵌入到APP中。

#### 横屏全屏播放

上述过程中，使得WebView和评论区RecycleView在一个滚动布局下。但是问题是如果希望横屏时，能够实现类似于全屏播放的效果，可能需要再开一个Activity。这样做会带来很多麻烦，因此为了避免这个问题，采用重设布局参数的方式解决。

当传感器检测到横屏时，能够自动将WebView的布局重设，并重新加载Landscape摸索下的HTML（长宽参数改变），同时”隐藏“评论区视，隐藏状态栏和APP信息栏。

在视频播放界面创建的时候，需要做如下设置：

```java
// 获取状态栏
// getSupportActionBar 是 父类AppCompatActivity的函数
actionBar = getSupportActionBar();

// 获取传递过来的视频 ID
// getIntent()是最顶层父类Activity中的函数
_videoId = getIntent().getStringExtra("videoId");

// 设置 WebView 
_webView.getSettings().setJavaScriptEnabled(true);

// 加载Url，data_portait是默认竖屏下的HTML脚本
_webView.loadDataWithBaseURL(null, data_portait, "text/html", "UTF-8", null);

```

同时需要设置WebView全局布局监听事件，创建匿名内部类，并重写onGlobalLayout方法。

![image.png](https://pic.leetcode.cn/1702811139-kuZbyS-image.png)

通过getResources() 在检测到横屏时，设置全屏标记，隐藏状态栏，移除评论区ViewGroup，并重新加载横屏下的WebViewURL，这里使用data_landscape。

![image.png](https://pic.leetcode.cn/1702811217-fvGXjT-image.png)

到检测到切换竖屏，显示状态栏，清楚全屏标志，先清空评论区视图，再重新添加，然后重设WebView参数为竖屏状态。

![image.png](https://pic.leetcode.cn/1702811395-tdnqMA-image.png)

最终效果如下，在横屏后自动检测并将视频播放状态变为全屏模式。

同时，下图展示了Iframe提供的Youtube播放服务，包括画质，播放速度等。

![image.png](https://pic.leetcode.cn/1702811511-QDcyDA-image.png)

### Service与后台服务

![image.png](https://pic.leetcode.cn/1703410192-JSCElv-image.png)

- 启动状态
    当应用组件（如 Activity）通过调用 startService() 启动服务时，服务即处于“启动”状态。一旦启动，服务即可在后台无限期运行，即使**启动服务的组件已被销毁也不受影响，除非手动调用才能停止服务**， 已启动的服务通常是执行单一操作，而且**不会将结果返回给调用方**。

- 绑定状态
    当应用组件通过调用 bindService() 绑定到服务时，服务即处于“绑定”状态。绑定服务提供了一个客户端-服务器接口，允许**组件与服务进行交互、发送请求、获取结果**，甚至是利用**进程间通信 (IPC) 跨进程执行这些操作。** 仅当与另一个**应用组件绑定时，绑定服务才会运行**。 多个组件可以同时绑定到该服务，但全部取消绑定后，该服务即会被销毁。

#### 启动服务

启动服务使用startService(Intent intent)方法，仅需要传递一个Intent对象即可，在Intent对象中指定需要启动的服务。而使用startService()方法启动的服务，在服务的外部，必须使用stopService()方法停止，在服务的内部可以调用stopSelf()方法停止当前服务。

如果使用stopService()或者stopSelf()方法请求停止服务，系统会就会尽快销毁这个服务。值得注意的是对于启动服务，一旦启动将与访问它的组件无任何关联，即使访问它的组件被销毁了，这个服务也一直运行下去，直到手动调用停止服务才被销毁，至于onBind方法，只有在绑定服务时才会起作用，在启动状态下，无需关注此方法。

#### 生命周期回调类

在YoutubeVideoPlayActivity中实现一个子类，继承自ActivityLifecycleCallbacks。

```java
 private class MyActivityLifecycleCallbacks implements Application.ActivityLifecycleCallbacks
```

实现应用进入前台的回调监听onActivityStarted，这里传入的参数activity就是YoutubeVideoPlayActivity。设置意图为希望启动的服务，这里是YoutubeService，向其传递cmd信息为1，表示应用进入前台，可以关闭后台服务。

多次启动一个服务，onCreate方法只会调用一次，但是onStartCommand的方法会反复调用。

![image.png](https://pic.leetcode.cn/1703405198-OYEopS-image.png)

实现应用进入后台的回调监听onActivityStopped。忽略如屏幕变换到引起的情况，在真正进入后台时，再次启动服务，向其cmd传入0，表示应用进入后台，开启后台服务。

![image-20231224160852293](C:\Users\TsingPig\AppData\Roaming\Typora\typora-user-images\image-20231224160852293.png)

#### YoutubeService + Handle实现定时任务：更新进度条

```java
public class YoutubeService extends Service
```

**onCreate**方法在首次启动服务时调用初始化媒体播放器，进度条控件，进度条handler。

![image.png](https://pic.leetcode.cn/1703409658-UhbIkt-image.png)

 Handler用于更新进度条，创建Handler对象，并传入一个 Handler.Callback 的接口的实现（匿名接口）作为构造函数参数。 重写handleMessage方法， 该方法定义了在接收到消息时的处理逻辑。消息的获取由主线程的Looper从主线程的消息队列中完成。

```java
 _progressHandler = new Handler(new Handler.Callback()
        {
            @Override
            public boolean handleMessage(@NonNull Message msg)
            {
                if (mediaPlayer != null)
                {
                    int progress = (int) (((float) mediaPlayer.getCurrentPosition() / mediaPlayer.getDuration()) * 100);
                    updateNotification(progress);

                    // 这行代码会向与 Handler 相关联的消息队列（主线程中的消息队列）发送一条空消息，
                    // 并且要求在指定的延迟时间后执行。
                    // 具体来说，这里是在 1000 毫秒（1秒）后执行。
                    // 发送的消息会进入消息队列，消息不会立即被处理。只有当循环到达消息的执行时间时，才会调用 handleMessage 方法。
                    // 相当于，每次接收消息对会发送一条在1秒后执行的，what为0的消息，实现后台任务的一直执行
                    _progressHandler.sendEmptyMessageDelayed(0, 1000); // 每隔1秒更新一次
                }
                return false;
            }
        });
```

 在 Service 中使用 Handler 通常用于在服务的后台线程中执行异步任务、定时任务或处理其他一些与服务相关的逻辑。Service的生命周期独立于创建它的Activity。Handler的生命周期与所在Service关联。而Service在主线程中执行（默认情况下，除非需要在Service中执行后台耗时操作），Handler 被创建时没有显式传递 Looper，因此它默认与当前线程的 Looper 相关联，Handler的Looper即主线程的Looper。

Looper：Looper 是一个消息循环器，它不断地从消息队列中取出消息并传递给相应的 Handler 处理。每个线程只能有一个 Looper。

MessageQueue：MessageQueue 是消息队列，存储着待处理的消息。Looper 从队列中取出消息，并将其传递给关联的 Handler 处理。

 Handler 本身并不是多线程的，但它能够在多线程环境中实现线程间通信。 通过在不同线程上创建 Handler，可以在一个线程中投递消息或任务，然后在关联的线程上处理它们。 这种机制有助于避免直接在不同线程之间进行共享数据，从而减少了多线程编程中的竞态条件和死锁等问题。

**onStartCommand**当反复启动服务时调用，接收当前需要开启/关闭后台播放服务的命令，以及显示的标题和描述。

```java
public int onStartCommand(Intent intent, int flags, int startId)
{
    int cmd = intent.getExtras().getInt("cmd");

    // cmd 为0， 表示应用进入后台，开启后台服务
    if (cmd == 0)
    {
        if (!isRemove)
        {
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O)
            {
                _title = intent.getExtras().getString("title");
                _description = intent.getExtras().getString("description");
                start();
            }
        }
        isRemove = true;
    }
    else    // cmd为1，表示应用进入前台，关闭后台服务
    {
        if (isRemove)
        {
            stop();
        }
        isRemove = false;
    }
    return super.onStartCommand(intent, flags, startId);
}
```

当后台服务启动时，创建通知，更新remoteViews界面，并且播放（或者继续播放）媒体音乐，然后立即向消息队列发送一条没有延迟的空消息，从而开始更新进度条。

```java
private void start()
{
    isPlaying = true;
    createNotification();
    mediaPlayer.start();
    _progressHandler.sendEmptyMessage(0); // 立即发送第一条消息

}
```

当后台服务关闭时，暂停媒体音乐播放，调用Service类方法stopForeground，关闭通知，并通过handler.removeMessages方法，将消息队列中所有what字段为0的消息都移除，从而实现停止handle处理消息，停止进度条的更新的效果。

```java
private void stop()
{
    isPlaying = false;
    stopForeground(true);
    mediaPlayer.pause();

    // handler.removeMessages(0) 则用于停止这个定时任务，即移除所有待处理的空消息。
    // 通过移除所有what字段为0的消息，可以实现停止定时任务的继续产生。
    _progressHandler.removeMessages(0); // 停止Handler的消息发送
}
```

**updateNotification**方法更新任务栏通知，更新进度条进度。

```java
private void updateNotification(int progress)
{
    _remoteViews = new RemoteViews(getPackageName(), R.layout.notification_layout);
    _remoteViews.setProgressBar(R.id.progressBar, 100, progress, false);
    _remoteViews.setTextViewText(R.id.notificationTitle, _title);
    _remoteViews.setTextViewText(R.id.description, _description);

    Notification notification = null;
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.O)
    {
        notification = new Notification.Builder(this, "your_channel_id").setContentTitle("Your Notification Title")
                .setContentText(_description).setContentTitle("正在播放：" + _title)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.ic_hello))
                .setSmallIcon(R.drawable.ic_hello).setCustomContentView(_remoteViews)
                .setCustomBigContentView(_remoteViews).build();
    }

    NotificationManager notificationManager = null;
    if (android.os.Build.VERSION.SDK_INT >= android.os.Build.VERSION_CODES.M)
    {
        notificationManager = getSystemService(NotificationManager.class);
    }
    notificationManager.notify(NOTIFICATION_ID, notification);
}
```

## 尾声

在仿Youtube视频播放实现过程中，比较创新性，有挑战性的实现了Googleapis爬取视频功能，并加入刷新功能。在刷新功能实现后，甚至可以做随机推荐和算法推荐，即推送用户可能喜欢的视频。同时这里也可以加入通过标签和标题进行视频搜索播放功能，后续可以进行探究。

然后，添加了iFrame框架，嵌入WebView HTML 语言的方式，实现了比较成熟的视频播放器。具体参数可以在HTML中设置。并且，加入NestedScrollView，解决嵌套滚动冲突问题，很好的实现了滚动评论区。

最后，比较创新的实现了全屏播放效果和竖屏之间切换的逻辑，注重了用户体验性。

开发过程中，使用了sourceTree图形化版本管理软件，本人主页[TsingPig](https://github.com/TsingPig)

和[ydy](https://github.com/ydyzlq) 共同开发。

![image.png](https://pic.leetcode.cn/1702885941-Bmwunl-image.png)



### 分工

登录，个人信息，视频上传：杨大宇

Youtube视频爬取/播放，后台Service音乐：朱正阳
