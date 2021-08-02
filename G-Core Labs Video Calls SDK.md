---


---

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
<h4 id="пиры-участники">Пиры (участники)</h4>
<ol>
<li>
<p>При успешном коннекте, вызывается метод, который возвращает всех участников, находящихся в комнате, сохраняем их, рисуем элементы для отображения участников</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">joinWithPeersInRoom</span><span class="token punctuation">(</span><span class="token number">_</span> peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">PeerObject</span><span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token comment">// Как пример, создание объекта пира и добавление нового вью в сетку ScrollView</span>
	peers<span class="token punctuation">.</span>forEach <span class="token punctuation">{</span> peer <span class="token keyword">in</span>
		<span class="token keyword">let</span> remoteItem <span class="token operator">=</span> <span class="token function">GCLRemoteItem</span><span class="token punctuation">(</span>peerObject<span class="token punctuation">:</span> peer<span class="token punctuation">)</span>
		remoteItems<span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">)</span>
		mainScrollView<span class="token punctuation">.</span><span class="token function">addSubview</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">.</span>view<span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
<li>
<p>При входе в комнату нового участника,  так же добавляем его в массив подключённых участников и на сетку ScrollView соответственно</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">handledPeer</span><span class="token punctuation">(</span><span class="token number">_</span> peer<span class="token punctuation">:</span> <span class="token builtin">PeerObject</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">let</span> remoteItem <span class="token operator">=</span> <span class="token function">GCLRemoteItem</span><span class="token punctuation">(</span>peerObject<span class="token punctuation">:</span> peer<span class="token punctuation">)</span>
	remoteItems<span class="token punctuation">.</span><span class="token function">insert</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">)</span>
	mainScrollView<span class="token punctuation">.</span><span class="token function">addSubview</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">.</span>view<span class="token punctuation">)</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
<li>
<p>При отключении участника, удаляем его из массива</p>
</li>
</ol>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">peerClosed</span><span class="token punctuation">(</span><span class="token number">_</span> peer<span class="token punctuation">:</span> <span class="token builtin">String</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span> <span class="token keyword">let</span> remoteItem <span class="token operator">=</span> remoteItems<span class="token punctuation">.</span><span class="token function">first</span><span class="token punctuation">(</span><span class="token keyword">where</span><span class="token punctuation">:</span> <span class="token punctuation">{</span> $<span class="token number">0</span><span class="token punctuation">.</span>peerId <span class="token operator">==</span> peer <span class="token punctuation">}</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		remoteItem<span class="token punctuation">.</span>view<span class="token punctuation">.</span><span class="token function">removeFromSuperview</span><span class="token punctuation">(</span><span class="token punctuation">)</span>
		remoteItems<span class="token punctuation">.</span><span class="token function">remove</span><span class="token punctuation">(</span>remoteItem<span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
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
<ol>
<li>
<p>При изменении аудиопотока кого-то из участников (вкл/выкл микрофона), вызывается метод, в котором мы можем проверить состояние потока и отрисовать соответствующую иконку.</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">audioDidChanged</span><span class="token punctuation">(</span><span class="token number">_</span> audioObject<span class="token punctuation">:</span> <span class="token builtin">AudioObject</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	<span class="token keyword">if</span> <span class="token keyword">let</span> remoteItem <span class="token operator">=</span> remoteItems<span class="token punctuation">.</span><span class="token function">first</span><span class="token punctuation">(</span><span class="token keyword">where</span><span class="token punctuation">:</span> <span class="token punctuation">{</span> $<span class="token number">0</span><span class="token punctuation">.</span>peerId <span class="token operator">==</span> audioObject<span class="token punctuation">.</span>peerId <span class="token punctuation">}</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		remoteItem<span class="token punctuation">.</span><span class="token function">isEnableAudio</span><span class="token punctuation">(</span>audioObject<span class="token punctuation">.</span>rtcAudioTrack<span class="token punctuation">.</span>isEnabled<span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
</ol>
<h4 id="прочие-события">Прочие события</h4>
<ol>
<li>
<p>Разговаривающие в данный момент участники</p>
<p>Метод <strong>activeSpeakerPeers(_ peers: [String])</strong> возвращает идентификаторы активно говорящих пиров, таким образом мы можем отрисовать для конкретного участника соответствующую иконку</p>
<pre class=" language-swift"><code class="prism  language-swift"><span class="token keyword">func</span> <span class="token function">activeSpeakerPeers</span><span class="token punctuation">(</span><span class="token number">_</span> peers<span class="token punctuation">:</span> <span class="token punctuation">[</span><span class="token builtin">String</span><span class="token punctuation">]</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
	remoteItems<span class="token punctuation">.</span>forEach <span class="token punctuation">{</span> item <span class="token keyword">in</span>
		item<span class="token punctuation">.</span><span class="token function">isSpeekingActive</span><span class="token punctuation">(</span>peers<span class="token punctuation">.</span><span class="token function">contains</span><span class="token punctuation">(</span>item<span class="token punctuation">.</span>peerId<span class="token punctuation">)</span><span class="token punctuation">)</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
</li>
</ol>


## Системные требования
* Минимальная версия iOS: 12.1

## Интеграция SDK

### Добавление SDK

1. Создаём новый проект
2. Устанавливаем зависимости Pod

	**Podfile**
	```ruby
	pod 'Starscream', '~> 4.0.4'
	pod 'SwiftyJSON', '~> 4.0'
	pod "mediasoup_ios_client", '1.5.3'
	```
	
4. Копируем GCoreLabMeetSDK.framework в папку проекта

### Импорт SDK и настройка проекта

1. Перетаскиваем `GCoreLabMeetSDK.framework` в папку проекта
2. В таргете проекта в разделе **General -> Frameworks, Libraries, and Embedded Content** выставляем значение **Embed** на `Embed & Sign` для `GCoreLabMeetSDK.framework` 
3. В **Build Settings** таргета устанавливаем `ENABLE_BITCODE = NO`
4. В **Info.plist** добавляем описание для параметров **NSCameraUsageDescription** и **NSMicrophoneUsageDescription**

### Инициализация SDK

1. Импортируем зависимости

	```swift
	import GCoreLabMeetSDK
	import WebRTC
	```	
	
3. Активируем логирование

	```swift
	GCoreRoomLogger.activateLogger()
	```
	
4. Определяем параметры для коннекта к серверу

	```swift
	let options = RoomOptions(cameraPosition: .front)
	
	let parameters = MeetRoomParametrs(
	    roomId: "serv01234",
	    displayName: "Рыыжий котэ",
	    accessToken: "",
	    webrtcWebsocketHost: "webrtc1.youstreamer.com",
	    apiHost: "",
	    host: "https://meet.gcorelabs.com",
	    path: ""
	)
	```
	
5. Создаём экземпляр объекта клиента и конектимся

	```swift
	var client: GCoreRoomClient?
	
	client = GCoreRoomClient(roomOptions: options, requestParameters: parameters, roomListener: self)

	try? client?.open()
	```
	
6. Активируем аудиосессию

	По умолчанию аудиосессией SDK управлять не умеет, чтобы включить базовый функционал, нужно вызвать метод активации аудиосессии. Будет активирована громкая связь при разговоре и переключение на наушники при их подключении.
	
	```swift
	client?.audioSessionActivate()
	```
	
7. Подписываемся на методы делегата `RoomListener`

	```swift
    func roomClientHandle(error: RoomError)
    func roomClientDidConnected()
    func roomClientReconnecting()
    func roomClientReconnectingFailed()
    func roomClientSocketDidDisconnected(roomClient: GCoreRoomClient)
    
    /// Возвращает пиры, находящиеся в комнате в момент входа
    func roomClient(roomClient: GCoreRoomClient, joinWithPeersInRoom peers: [PeerObject])
    
    /// Пир вошёл в комнату
    func roomClient(roomClient: GCoreRoomClient, handlePeer: PeerObject)
    
    /// Пир вышел из комнаты
    func roomClient(roomClient: GCoreRoomClient, peerClosed: String)
    
    // Локальный пир
    /// Локальный видеопоток получен
    func roomClient(roomClient: GCoreRoomClient, produceLocalVideoTrack videoTrack: RTCVideoTrack)
    
    /// Локальный аудиопоток получен
    func roomClient(roomClient: GCoreRoomClient, produceLocalAudioTrack audioTrack: RTCAudioTrack)
    
    /// Был закрыт локальный видеопоток
    func roomClient(roomClient: GCoreRoomClient, didCloseLocalVideoTrack videoTrack: RTCVideoTrack?)
    
    /// Был закрыт локальный аудиопоток
    func roomClient(roomClient: GCoreRoomClient, didCloseLocalAudioTrack audioTrack: RTCAudioTrack?)

    // Внешние пиры
    /// Получен внешний видеопоток
    func roomClient(roomClient: GCoreRoomClient, handledRemoteVideo videoObject: VideoObject)
    
    /**
     Внешний ведопоток был закрыт
     - parameter byModerator Если пир вышел не сам, а его закрыл модератор
     */
    func roomClient(roomClient: GCoreRoomClient, didCloseRemoteVideoByModerator byModerator: Bool, videoObject: VideoObject)
    
    /// Получен внешний аудиопоток
    func roomClient(roomClient: GCoreRoomClient, produceRemoteAudio audioObject: AudioObject)
    
    /**
     Внешний аудиопоток был закрыт
     - parameter byModerator Если пир вышел не сам, а его закрыл модератор
     */
    func roomClient(roomClient: GCoreRoomClient, didCloseRemoteAudioByModerator byModerator: Bool, audioObject: AudioObject)
    
    /**
     В массиве приходит пир с активным микрофоном. Микрофон пира считается активным до тех пор
     пока не будет вызван этот же метод, в массиве которого не будет соответствующего пира
     - parameter peers массив Id у которых активен микрофон
     */
    func roomClient(roomClient: GCoreRoomClient, activeSpeakerPeers peers: [String])
    ```
    
8. Рекомендуемые надстройки после успешного подключения

	Метод `roomClientDidConnected` вызывается после успешного подключения к серверу. То есть мы зашли в комнату. Для последующего подключения видео и аудио, вызываем у клиента методы `toggleAudio(isOn: Bool)` и `toggleVideo(isOn: Bool)` соответственно
	
	```swift
   func roomClientDidConnected() {
		DispatchQueue.main.asyncAfter(deadline: .now() + 1) {
			self.client?.toggleVideo(isOn: true)
			self.client?.toggleAudio(isOn: true)
		}
   }
	```
    
### Взаимодействие с клиентским приложением

#### Пиры (участники)

1. При успешном коннекте, вызывается метод, который возвращает всех участников, находящихся в комнате, сохраняем их, рисуем элементы для отображения участников

	```swift
	func joinWithPeersInRoom(_ peers: [PeerObject]) {
		// Как пример, создание объекта пира и добавление нового вью в сетку ScrollView
		peers.forEach { peer in
			let remoteItem = GCLRemoteItem(peerObject: peer)
			remoteItems.insert(remoteItem)
			mainScrollView.addSubview(remoteItem.view)
		}
	}
	```

2. При входе в комнату нового участника,  так же добавляем его в массив подключённых участников и на сетку ScrollView соответственно

	```swift
	func handledPeer(_ peer: PeerObject) {
		let remoteItem = GCLRemoteItem(peerObject: peer)
		remoteItems.insert(remoteItem)
		mainScrollView.addSubview(remoteItem.view)
	}
	```
	
3. При отключении участника, удаляем его из массива

```swift
func peerClosed(_ peer: String) {
	if let remoteItem = remoteItems.first(where: { $0.peerId == peer }) {
		remoteItem.view.removeFromSuperview()
		remoteItems.remove(remoteItem)
	}
}
```

#### Видеопотоки

1. Создаём RTCEAGLVideoView для отображения видеопотоков

	Создаём RTCEAGLVideoView для локального потока и массив вьюх для удалённого потока

	```swift
	private let localVideoView = RTCEAGLVideoView()
	
	struct RemoteVideoItem {
		let peerId: String
		let videoView: RTCEAGLVideoView
	}
	private var remoteItems = [RemoteVideoItem]()
	```
	
	Получаем локальный поток в методе делегата и передаём ему View для отображения видео

	```swift
	func roomClient(roomClient: GCoreRoomClient, produceLocalVideoTrack videoTrack: RTCVideoTrack) {
		videoTrack.add(localVideoView)
	}
	```
	
	Видеопотоки других участников приходят в виде объекта `VideoObject`, у которого есть идентификатор пира `peerId` и видеопоток `rtcVideoTrack`
	
	```swift
	func roomClient(roomClient: GCoreRoomClient, handledRemoteVideo videoObject: VideoObject) {
		// Создаём объект пира с вьюхой, которая будет отображать поток
		let remoteItem = RemoteVideoItem(
			peerId: videoObject.peerid
			videoView: RTCEAGLVideoView()
		)
		remoteItems.add(remoteItem)
		
		// Добавляем потоку вьюху
		videoObject.rtcVideoTrack.add(remoteItem.videoView)
	}
	```
	
	Так как у нас есть массив Пиров с вьюхами, мы можем ими управлять, если приходит событие на удаление видео потока для конкретного пира.
	
#### Аудиопотоки

1. При изменении аудиопотока кого-то из участников (вкл/выкл микрофона), вызывается метод, в котором мы можем проверить состояние потока и отрисовать соответствующую иконку.

	```swift
	func audioDidChanged(_ audioObject: AudioObject) {
		if let remoteItem = remoteItems.first(where: { $0.peerId == audioObject.peerId }) {
			remoteItem.isEnableAudio(audioObject.rtcAudioTrack.isEnabled)
		}
	}
	```

#### Прочие события

1. Разговаривающие в данный момент участники
	
	Метод **activeSpeakerPeers(_ peers: [String])** возвращает идентификаторы активно говорящих пиров, таким образом мы можем отрисовать для конкретного участника соответствующую иконку

	```swift
	func activeSpeakerPeers(_ peers: [String]) {
		remoteItems.forEach { item in
			item.isSpeekingActive(peers.contains(item.peerId))
		}
	}
	```
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTExNzAxMjg5MzNdfQ==
-->