<!DOCTYPE html>
<html>

<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>G-Core Labs Video Calls SDK</title>
  <link rel="stylesheet" href="https://stackedit.io/style.css" />
</head>

<body class="stackedit">
  <div class="stackedit__left">
    <div class="stackedit__toc">
      
<ul>
<li>
<ul>
<li><a href="#системные-требования">Системные требования</a></li>
<li><a href="#интеграция-sdk">Интеграция SDK</a></li>
<li><a href="#системные-требования-1">Системные требования</a></li>
<li><a href="#интеграция-sdk-1">Интеграция SDK</a></li>
</ul>
</li>
</ul>

    </div>
  </div>
  <div class="stackedit__right">
    <div class="stackedit__html">
      <h2 id="системные-требования">Системные требования</h2>
<ul>
<li>Минимальная версия iOS: 12.1</li>
</ul>
<h2 id="интеграция-sdk">Интеграция SDK</h2>
<h3 id="добавление-sdk">Добавление SDK</h3>
<ol>
<li>
<p>Создаём новый проект</p>
</li>
<li>
<p>Устанавливаем зависимости Pod</p>
<p><strong>Podfile</strong></p>
<pre class=" language-ruby"><code class="prism  language-ruby">pod <span class="token string">'Starscream'</span><span class="token punctuation">,</span> <span class="token string">'~&gt; 4.0.4'</span>
pod <span class="token string">'SwiftyJSON'</span><span class="token punctuation">,</span> <span class="token string">'~&gt; 4.0'</span>
pod <span class="token string">"mediasoup_ios_client"</span><span class="token punctuation">,</span> <span class="token string">'1.5.3'</span>
</code></pre>
</li>
<li>
<p>Копируем GCoreLabMeetSDK.framework в папку проекта</p>
</li>
</ol>
<h3 id="импорт-sdk-и-настройка-проекта">Импорт SDK и настройка проекта</h3>
<ol>
<li>Перетаскиваем <code>GCoreLabMeetSDK.framework</code> в папку проекта</li>
<li>В таргете проекта в разделе <strong>General -&gt; Frameworks, Libraries, and Embedded Content</strong> выставляем значение <strong>Embed</strong> на <code>Embed &amp; Sign</code> для <code>GCoreLabMeetSDK.framework</code></li>
<li>В <strong>Build Settings</strong> таргета устанавливаем <code>ENABLE_BITCODE = NO</code></li>
<li>В <strong>Info.plist</strong> добавляем описание для параметров <strong>NSCameraUsageDescription</strong> и <strong>NSMicrophoneUsageDescription</strong></li>
</ol>
<h3 id="инициализация-sdk">Инициализация SDK</h3>
<ol>
<li>
<p>Импортируем зависимости</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">import</span> <span class="token builtin">GCoreLabMeetSDK</span>
<span class="token keyword">import</span> <span class="token builtin">WebRTC</span>
</code></pre>
</li>
<li>
<p>Активируем логирование</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token builtin">GCoreRoomLogger</span><span class="token punctuation">.</span><span class="token function">activateLogger</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Определяем параметры для коннекта к серверу</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">let</span> options <span class="token operator">=</span> <span class="token function">RoomOptions</span><span class="token punctuation">(</span>cameraPosition<span class="token punctuation">:</span> <span class="token punctuation">.</span>front<span class="token punctuation">)</span>

<span class="token keyword">let</span> parameters <span class="token operator">=</span> <span class="token function">MeetRoomParametrs</span><span class="token punctuation">(</span>
    roomId<span class="token punctuation">:</span> <span class="token string">"serv01234"</span><span class="token punctuation">,</span>
    displayName<span class="token punctuation">:</span> <span class="token string">"Рыыжий котэ"</span><span class="token punctuation">,</span>
    accessToken<span class="token punctuation">:</span> <span class="token string">""</span><span class="token punctuation">,</span>
    webrtcWebsocketHost<span class="token punctuation">:</span> <span class="token string">"webrtc1.youstreamer.com"</span><span class="token punctuation">,</span>
    apiHost<span class="token punctuation">:</span> <span class="token string">""</span><span class="token punctuation">,</span>
    host<span class="token punctuation">:</span> <span class="token string">"https://meet.gcorelabs.com"</span><span class="token punctuation">,</span>
    path<span class="token punctuation">:</span> <span class="token string">""</span>
<span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Создаём экземпляр объекта клиента и конектимся</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">var</span> client<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token operator">?</span>

client <span class="token operator">=</span> <span class="token function">GCoreRoomClient</span><span class="token punctuation">(</span>roomOptions<span class="token punctuation">:</span> options<span class="token punctuation">,</span> requestParameters<span class="token punctuation">:</span> parameters<span class="token punctuation">,</span> roomListener<span class="token punctuation">:</span> <span class="token keyword">self</span><span class="token punctuation">)</span>

<span class="token keyword">try</span><span class="token operator">?</span> client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Активируем аудиосессию</p>
<p>По умолчанию аудиосессией SDK управлять не умеет, чтобы включить базовый функционал, нужно вызвать метод активации аудиосессии. Будет активирована громкая связь при разговоре и переключение на наушники при их подключении.</p>
<pre class=" language-swift"><code class="prism  language-swift">client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">audioSessionActivate</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Подписываемся на методы делегата <code>RoomListener</code></p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClientHandle</span><span class="token punctuation">(</span>error<span class="token punctuation">:</span> <span class="token builtin">RoomError</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientDidConnected</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientReconnecting</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientReconnectingFailed</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientSocketDidDisconnected</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">)</span>

<span class="token comment">/// Возвращает пиры, находящиеся в комнате в момент входа</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> joinWithPeersInRoom peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">PeerObject</span><span class="token punctuation">]</span><span class="token punctuation">)</span>

<span class="token comment">/// Пир вошёл в комнату</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handlePeer<span class="token punctuation">:</span> <span class="token builtin">PeerObject</span><span class="token punctuation">)</span>

<span class="token comment">/// Пир вышел из комнаты</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> peerClosed<span class="token punctuation">:</span> <span class="token builtin">String</span><span class="token punctuation">)</span>

<span class="token comment">// Локальный пир</span>
<span class="token comment">/// Локальный видеопоток получен</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token punctuation">)</span>

<span class="token comment">/// Локальный аудиопоток получен</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalAudioTrack audioTrack<span class="token punctuation">:</span> <span class="token builtin">RTCAudioTrack</span><span class="token punctuation">)</span>

<span class="token comment">/// Был закрыт локальный видеопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token operator">?</span><span class="token punctuation">)</span>

<span class="token comment">/// Был закрыт локальный аудиопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseLocalAudioTrack audioTrack<span class="token punctuation">:</span> <span class="token builtin">RTCAudioTrack</span><span class="token operator">?</span><span class="token punctuation">)</span>

<span class="token comment">// Внешние пиры</span>
<span class="token comment">/// Получен внешний видеопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handledRemoteVideo videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 Внешний ведопоток был закрыт
 - parameter byModerator Если пир вышел не сам, а его закрыл модератор
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseRemoteVideoByModerator byModerator<span class="token punctuation">:</span> <span class="token builtin">Bool</span><span class="token punctuation">,</span> videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span>

<span class="token comment">/// Получен внешний аудиопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceRemoteAudio audioObject<span class="token punctuation">:</span> <span class="token builtin">AudioObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 Внешний аудиопоток был закрыт
 - parameter byModerator Если пир вышел не сам, а его закрыл модератор
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseRemoteAudioByModerator byModerator<span class="token punctuation">:</span> <span class="token builtin">Bool</span><span class="token punctuation">,</span> audioObject<span class="token punctuation">:</span> <span class="token builtin">AudioObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 В массиве приходит пир с активным микрофоном. Микрофон пира считается активным до тех пор
 пока не будет вызван этот же метод, в массиве которого не будет соответствующего пира
 - parameter peers массив Id у которых активен микрофон
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> activeSpeakerPeers peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">String</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Рекомендуемые надстройки после успешного подключения</p>
<p>Метод <code>roomClientDidConnected</code> вызывается после успешного подключения к серверу. То есть мы зашли в комнату. Для последующего подключения видео и аудио, вызываем у клиента методы <code>toggleAudio(isOn: Bool)</code> и <code>toggleVideo(isOn: Bool)</code> соответственно</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClientDidConnected</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token builtin">DispatchQueue</span><span class="token punctuation">.</span>main<span class="token punctuation">.</span><span class="token function">asyncAfter</span><span class="token punctuation">(</span>deadline<span class="token punctuation">:</span> <span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">self</span><span class="token punctuation">.</span>client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">toggleVideo</span><span class="token punctuation">(</span>isOn<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
		<span class="token keyword">self</span><span class="token punctuation">.</span>client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">toggleAudio</span><span class="token punctuation">(</span>isOn<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
</ol>
<h3 id="взаимодействие-с-клиентским-приложением">Взаимодействие с клиентским приложением</h3>
<h4 id="видеопотоки">Видеопотоки</h4>
<ol>
<li>
<p>Создаём RTCEAGLVideoView для отображения видеопотоков</p>
<p>Создаём RTCEAGLVideoView для локального потока и массив вьюх для удалённого потока</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">private</span> <span class="token keyword">let</span> localVideoView <span class="token operator">=</span> <span class="token function">RTCEAGLVideoView</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

<span class="token keyword">struct</span> <span class="token builtin">RemoteVideoItem</span> <span class="token punctuation">{</span>
	<span class="token keyword">let</span> peerId<span class="token punctuation">:</span> <span class="token builtin">String</span>
	<span class="token keyword">let</span> videoView<span class="token punctuation">:</span> <span class="token builtin">RTCEAGLVideoView</span>
<span class="token punctuation">}</span>
<span class="token keyword">private</span> <span class="token keyword">var</span> remoteItems <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token builtin">RemoteVideoItem</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>Получаем локальный поток в методе делегата и передаём ему View для отображения видео</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	videoTrack<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>localVideoView<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Видеопотоки других участников приходят в виде объекта <code>VideoObject</code>, у которого есть идентификатор пира <code>peerId</code> и видеопоток <code>rtcVideoTrack</code></p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handledRemoteVideo videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Создаём объект пира с вьюхой, которая будет отображать поток</span>
	<span class="token keyword">let</span> remoteItem <span class="token operator">=</span> <span class="token function">RemoteVideoItem</span><span class="token punctuation">(</span>
		peerId<span class="token punctuation">:</span> videoObject<span class="token punctuation">.</span>peerid
		videoView<span class="token punctuation">:</span> <span class="token function">RTCEAGLVideoView</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
	<span class="token punctuation">)</span>
	remoteItems<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">)</span>
	
	<span class="token comment">// Добавляем потоку вьюху</span>
	videoObject<span class="token punctuation">.</span>rtcVideoTrack<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">.</span>videoView<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Так как у нас есть массив Пиров с вьюхами, мы можем ими управлять, если приходит событие на удаление видео потока для конкретного пира.</p>
</li>
</ol>
<h4 id="аудиопотоки">Аудиопотоки</h4>
<h2 id="системные-требования-1">Системные требования</h2>
<ul>
<li>Минимальная версия iOS: 12.1</li>
</ul>
<h2 id="интеграция-sdk-1">Интеграция SDK</h2>
<h3 id="добавление-sdk-1">Добавление SDK</h3>
<ol>
<li>
<p>Создаём новый проект</p>
</li>
<li>
<p>Устанавливаем зависимости Pod</p>
<p><strong>Podfile</strong></p>
<pre class=" language-ruby"><code class="prism  language-ruby">pod <span class="token string">'Starscream'</span><span class="token punctuation">,</span> <span class="token string">'~&gt; 4.0.4'</span>
pod <span class="token string">'SwiftyJSON'</span><span class="token punctuation">,</span> <span class="token string">'~&gt; 4.0'</span>
pod <span class="token string">"mediasoup_ios_client"</span><span class="token punctuation">,</span> <span class="token string">'1.5.3'</span>
</code></pre>
</li>
<li>
<p>Копируем GCoreLabMeetSDK.framework в папку проекта</p>
</li>
</ol>
<h3 id="импорт-sdk-и-настройка-проекта-1">Импорт SDK и настройка проекта</h3>
<ol>
<li>Перетаскиваем <code>GCoreLabMeetSDK.framework</code> в папку проекта</li>
<li>В таргете проекта в разделе <strong>General -&gt; Frameworks, Libraries, and Embedded Content</strong> выставляем значение <strong>Embed</strong> на <code>Embed &amp; Sign</code> для <code>GCoreLabMeetSDK.framework</code></li>
<li>В <strong>Build Settings</strong> таргета устанавливаем <code>ENABLE_BITCODE = NO</code></li>
<li>В <strong>Info.plist</strong> добавляем описание для параметров <strong>NSCameraUsageDescription</strong> и <strong>NSMicrophoneUsageDescription</strong></li>
</ol>
<h3 id="инициализация-sdk-1">Инициализация SDK</h3>
<ol>
<li>
<p>Импортируем зависимости</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">import</span> <span class="token builtin">GCoreLabMeetSDK</span>
<span class="token keyword">import</span> <span class="token builtin">WebRTC</span>
</code></pre>
</li>
<li>
<p>Активируем логирование</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token builtin">GCoreRoomLogger</span><span class="token punctuation">.</span><span class="token function">activateLogger</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Определяем параметры для коннекта к серверу</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">let</span> options <span class="token operator">=</span> <span class="token function">RoomOptions</span><span class="token punctuation">(</span>cameraPosition<span class="token punctuation">:</span> <span class="token punctuation">.</span>front<span class="token punctuation">)</span>

<span class="token keyword">let</span> parameters <span class="token operator">=</span> <span class="token function">MeetRoomParametrs</span><span class="token punctuation">(</span>
    roomId<span class="token punctuation">:</span> <span class="token string">"serv01234"</span><span class="token punctuation">,</span>
    displayName<span class="token punctuation">:</span> <span class="token string">"Рыыжий котэ"</span><span class="token punctuation">,</span>
    accessToken<span class="token punctuation">:</span> <span class="token string">""</span><span class="token punctuation">,</span>
    webrtcWebsocketHost<span class="token punctuation">:</span> <span class="token string">"webrtc1.youstreamer.com"</span><span class="token punctuation">,</span>
    apiHost<span class="token punctuation">:</span> <span class="token string">""</span><span class="token punctuation">,</span>
    host<span class="token punctuation">:</span> <span class="token string">"https://meet.gcorelabs.com"</span><span class="token punctuation">,</span>
    path<span class="token punctuation">:</span> <span class="token string">""</span>
<span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Создаём экземпляр объекта клиента и конектимся</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">var</span> client<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token operator">?</span>

client <span class="token operator">=</span> <span class="token function">GCoreRoomClient</span><span class="token punctuation">(</span>roomOptions<span class="token punctuation">:</span> options<span class="token punctuation">,</span> requestParameters<span class="token punctuation">:</span> parameters<span class="token punctuation">,</span> roomListener<span class="token punctuation">:</span> <span class="token keyword">self</span><span class="token punctuation">)</span>

<span class="token keyword">try</span><span class="token operator">?</span> client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">open</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Активируем аудиосессию</p>
<p>По умолчанию аудиосессией SDK управлять не умеет, чтобы включить базовый функционал, нужно вызвать метод активации аудиосессии. Будет активирована громкая связь при разговоре и переключение на наушники при их подключении.</p>
<pre class=" language-swift"><code class="prism  language-swift">client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">audioSessionActivate</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Подписываемся на методы делегата <code>RoomListener</code></p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClientHandle</span><span class="token punctuation">(</span>error<span class="token punctuation">:</span> <span class="token builtin">RoomError</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientDidConnected</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientReconnecting</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientReconnectingFailed</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
<span class="token keyword">func</span> <span class="token function">roomClientSocketDidDisconnected</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">)</span>

<span class="token comment">/// Возвращает пиры, находящиеся в комнате в момент входа</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> joinWithPeersInRoom peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">PeerObject</span><span class="token punctuation">]</span><span class="token punctuation">)</span>

<span class="token comment">/// Пир вошёл в комнату</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handlePeer<span class="token punctuation">:</span> <span class="token builtin">PeerObject</span><span class="token punctuation">)</span>

<span class="token comment">/// Пир вышел из комнаты</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> peerClosed<span class="token punctuation">:</span> <span class="token builtin">String</span><span class="token punctuation">)</span>

<span class="token comment">// Локальный пир</span>
<span class="token comment">/// Локальный видеопоток получен</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token punctuation">)</span>

<span class="token comment">/// Локальный аудиопоток получен</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalAudioTrack audioTrack<span class="token punctuation">:</span> <span class="token builtin">RTCAudioTrack</span><span class="token punctuation">)</span>

<span class="token comment">/// Был закрыт локальный видеопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token operator">?</span><span class="token punctuation">)</span>

<span class="token comment">/// Был закрыт локальный аудиопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseLocalAudioTrack audioTrack<span class="token punctuation">:</span> <span class="token builtin">RTCAudioTrack</span><span class="token operator">?</span><span class="token punctuation">)</span>

<span class="token comment">// Внешние пиры</span>
<span class="token comment">/// Получен внешний видеопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handledRemoteVideo videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 Внешний ведопоток был закрыт
 - parameter byModerator Если пир вышел не сам, а его закрыл модератор
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseRemoteVideoByModerator byModerator<span class="token punctuation">:</span> <span class="token builtin">Bool</span><span class="token punctuation">,</span> videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span>

<span class="token comment">/// Получен внешний аудиопоток</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceRemoteAudio audioObject<span class="token punctuation">:</span> <span class="token builtin">AudioObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 Внешний аудиопоток был закрыт
 - parameter byModerator Если пир вышел не сам, а его закрыл модератор
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> didCloseRemoteAudioByModerator byModerator<span class="token punctuation">:</span> <span class="token builtin">Bool</span><span class="token punctuation">,</span> audioObject<span class="token punctuation">:</span> <span class="token builtin">AudioObject</span><span class="token punctuation">)</span>

<span class="token comment">/**
 В массиве приходит пир с активным микрофоном. Микрофон пира считается активным до тех пор
 пока не будет вызван этот же метод, в массиве которого не будет соответствующего пира
 - parameter peers массив Id у которых активен микрофон
 */</span>
<span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> activeSpeakerPeers peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">String</span><span class="token punctuation">]</span><span class="token punctuation">)</span>
</code></pre>
</li>
<li>
<p>Рекомендуемые надстройки после успешного подключения</p>
<p>Метод <code>roomClientDidConnected</code> вызывается после успешного подключения к серверу. То есть мы зашли в комнату. Для последующего подключения видео и аудио, вызываем у клиента методы <code>toggleAudio(isOn: Bool)</code> и <code>toggleVideo(isOn: Bool)</code> соответственно</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClientDidConnected</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token builtin">DispatchQueue</span><span class="token punctuation">.</span>main<span class="token punctuation">.</span><span class="token function">asyncAfter</span><span class="token punctuation">(</span>deadline<span class="token punctuation">:</span> <span class="token punctuation">.</span><span class="token function">now</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token operator">+</span> <span class="token number">1</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token keyword">self</span><span class="token punctuation">.</span>client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">toggleVideo</span><span class="token punctuation">(</span>isOn<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
		<span class="token keyword">self</span><span class="token punctuation">.</span>client<span class="token operator">?</span><span class="token punctuation">.</span><span class="token function">toggleAudio</span><span class="token punctuation">(</span>isOn<span class="token punctuation">:</span> <span class="token boolean">true</span><span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
</ol>
<h3 id="взаимодействие-с-клиентским-приложением-1">Взаимодействие с клиентским приложением</h3>
<h4 id="видеопотоки-1">Видеопотоки</h4>
<ol>
<li>
<p>Создаём RTCEAGLVideoView для отображения видеопотоков</p>
<p>Создаём RTCEAGLVideoView для локального потока и массив вьюх для удалённого потока</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">private</span> <span class="token keyword">let</span> localVideoView <span class="token operator">=</span> <span class="token function">RTCEAGLVideoView</span><span class="token punctuation">(</span><span class="token punctuation">)</span>

<span class="token keyword">struct</span> <span class="token builtin">RemoteVideoItem</span> <span class="token punctuation">{</span>
	<span class="token keyword">let</span> peerId<span class="token punctuation">:</span> <span class="token builtin">String</span>
	<span class="token keyword">let</span> videoView<span class="token punctuation">:</span> <span class="token builtin">RTCEAGLVideoView</span>
<span class="token punctuation">}</span>
<span class="token keyword">private</span> <span class="token keyword">var</span> remoteItems <span class="token operator">=</span> <span class="token punctuation">[</span><span class="token builtin">RemoteVideoItem</span><span class="token punctuation">]</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
</code></pre>
<p>Получаем локальный поток в методе делегата и передаём ему View для отображения видео</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> produceLocalVideoTrack videoTrack<span class="token punctuation">:</span> <span class="token builtin">RTCVideoTrack</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	videoTrack<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>localVideoView<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Видеопотоки других участников приходят в виде объекта <code>VideoObject</code>, у которого есть идентификатор пира <code>peerId</code> и видеопоток <code>rtcVideoTrack</code></p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">roomClient</span><span class="token punctuation">(</span>roomClient<span class="token punctuation">:</span> <span class="token builtin">GCoreRoomClient</span><span class="token punctuation">,</span> handledRemoteVideo videoObject<span class="token punctuation">:</span> <span class="token builtin">VideoObject</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Создаём объект пира с вьюхой, которая будет отображать поток</span>
	<span class="token keyword">let</span> remoteItem <span class="token operator">=</span> <span class="token function">RemoteVideoItem</span><span class="token punctuation">(</span>
		peerId<span class="token punctuation">:</span> videoObject<span class="token punctuation">.</span>peerid
		videoView<span class="token punctuation">:</span> <span class="token function">RTCEAGLVideoView</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
	<span class="token punctuation">)</span>
	remoteItems<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">)</span>
	
	<span class="token comment">// Добавляем потоку вьюху</span>
	videoObject<span class="token punctuation">.</span>rtcVideoTrack<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">.</span>videoView<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
<p>Так как у нас есть массив Пиров с вьюхами, мы можем ими управлять, если приходит событие на удаление видео потока для конкретного пира.</p>
</li>
</ol>
<h4 id="аудиопотоки-1">Аудиопотоки</h4>

    </div>
  </div>
</body>

</html>
