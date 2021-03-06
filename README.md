# 介绍
PlayerBase是一种将播放业务组件化处理的解决方案框架。无论是播放器内的控制视图还是业务视图，均可以做到组件化处理。将播放器的开发变得清晰简单，更利于产品的迭代。

无缝续播效果<br>
![image](https://github.com/jiajunhui/PlayerBase/raw/master/screenshot/play_go_on.gif)


# Demo下载
[Demo下载](https://fir.im/ya4e)

# 功能
__-解码方案的配置化管理__<br>
__-自定义接入各种解码方案__<br>
__-多种解码方案的切换__<br>
__-视图的组件化处理__<br>
__-可根据需求自定义视图组件层__<br>
__-统一的事件下发机制__<br>
__-扩展事件的添加__<br>
__-默认支持历史点定点播放__<br>
__-支持列表播放中的无缝续播__<br>
__-等功能……__<br>

![image](https://github.com/jiajunhui/PlayerBase/raw/master/screenshot/Screenshot_20171203-124242.png)
![image](https://github.com/jiajunhui/PlayerBase/raw/master/screenshot/Screenshot_20171203-124309.png)

# 使用
```gradle
dependencies {
  compile 'com.kk.taurus.playerbase:PlayerBase:2.1.2'
}
```
使用DefaultPlayer对象，可写入xml布局中，也可用代码创建。

```java
mPlayer = (DefaultPlayer) findViewById(R.id.player);
DefaultReceiverCollections receiverCollections = new DefaultReceiverCollections(this);
        receiverCollections.buildDefault();
mPlayer.bindReceiverCollections(receiverCollections);
VideoData videoData = new VideoData("http://url...");
mPlayer.setDataSource(videoData);
mPlayer.start();
```

# 说明
系统的播放模式有两种。<br>
一种是Decoder+RenderView方案，对应API设置中的__IPlayer.WIDGET_MODE_DECODER__<br>
另一种是VideoView方案。对应API设置中的__IPlayer.WIDGET_MODE_VIDEO_VIEW__。<br>
框架默认自带系统的MediaPlayer方案和VideoView方案。如果不做任何配置即为默认的MediaPlayer方案。当需要使用无缝切播功能时，必须设置为__IPlayer.WIDGET_MODE_DECODER__模式。

# 接入其他播放器
一下示例为接入IJKPlayer<br>
__Decoder方案，接入IjkMediaPlayer__<br>

```java
public class IJkDecoderPlayer extends BaseDecoder {

    private IjkMediaPlayer mMediaPlayer;

    public IJkDecoderPlayer(Context context) {
        super(context);
        init(context);
    }

    private void init(Context context) {
        mMediaPlayer = new IjkMediaPlayer();
        // init player
        IjkMediaPlayer.loadLibrariesOnce(null);
        IjkMediaPlayer.native_profileBegin("libijkplayer.so");
    }

    @Override
    public void setDataSource(VideoData data) {
        //......
    }

    @Override
    public void start() {
        //......
    }

    @Override
    public void pause() {
        //......
    }

    @Override
    public void resume() {
        //......
    }

    @Override
    public void seekTo(int msc) {
        //......
    }

    //......
}
```
具体参见项目代码。<br>
使用前做如下配置：<br>

```java
DecoderType.getInstance().addDecoderType(1,new DecoderTypeEntity("ijkplayer","com.kk.taurus.ijkplayer.IJkDecoderPlayer"));
DecoderType.getInstance().setDefaultDecoderType(1);
ConfigLoader.setDefaultWidgetMode(this, IPlayer.WIDGET_MODE_DECODER);
```

__VideoView方案，接入IjkVideoView__<br>
详见项目代码IJKVideoViewPlayer

# 组件视图
框架自带一套默认组件，包含Loading组件、Error组件、Controller组件、手势处理组件等。<br>
这些组件均继承自父类BaseCover（覆盖层基类）

自定义覆盖层组件

```java
public class CustomCover extends BaseCover{
	
	public CustomCover(Context context) {
        super(context);
    }

    public CustomCover(Context context, BaseCoverObserver coverObserver) {
        super(context, coverObserver);
    }
    
    //......
	
}
```

自定义组件的使用。

```java
mPlayer = (DefaultPlayer) findViewById(R.id.player);
DefaultReceiverCollections receiverCollections = new DefaultReceiverCollections(this);
receiverCollections
	.addCover("custom_cover",new CustomCover(context)).build();
mPlayer.bindReceiverCollections(receiverCollections);
```

# 无缝续播的使用
类似于今日头条等应用的效果，在列表中播放时无缝续播进入详情页或者无缝进入全屏页面。<br><br>
原理：使用Decoder+RenderView方案，解码器动态关联不同的渲染视图（RenderView），比如使用MediaPlayer动态关联SurfaceView，就如同一个电脑主机不断连接不同的显示器。
<br>
主要方法：

```java
public void setRenderViewForDecoder(IRender render)
```
详见项目代码ListVideoAdapter和SecondActivity。


# 交流
联系方式：junhui_jia@163.com
QQ群：600201778