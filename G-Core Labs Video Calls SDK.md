

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
		view.setNeedsLayout()
	}
	```

2. При входе в комнату нового участника,  так же добавляем его в массив подключённых участников и на сетку ScrollView соответственно

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
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTAzNjI2MTI2Myw2OTI4MTQxOTksMjA4MD
MxMDM2OSwtODMyNDI1MjgzLC02MjMyNTgxODRdfQ==
-->