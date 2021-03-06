# Cordova Watch Connectivity Plugin

Simple plugin that establishes iOS Watch Connectivity session with Watch OS and helps exchange of messages between a hybrid application and its Apple Watch application and vice-versa.

## Installation

### With cordova-cli

If you are using [cordova-cli](https://github.com/apache/cordova-cli), install
with:

    cordova plugin add https://github.com/guikeller/cordova-watchconnectivity-plugin.git

### With ionic

With ionic:

    ionic cordova plugin add https://github.com/guikeller/cordova-watchconnectivity-plugin.git

## Use from Javascript
Edit `www/js/index.js` and add the following code inside `onDeviceReady`
```js
    // Receiving messages from Watch :: AppleWatch -> iPhone
    var receiveMessageSuccess = function(message){
        // Either from a sendMessage (with or without handlers) or updateApplicationContext
        var value = JSON.stringify(message);
        alert("Received message from Apple Watch : "+value);
    };
    var receiveMessageFailure = function(){
        alert("Could not receive message from Apple Watch");
    };

    // Sending Messages to Watch :: iPhone -> AppleWatch
    var sendMessageSuccess = function() {
        alert("Message sent successfully!");
    };
    var sendMessageFailure = function(){
        alert("Could not send message to Apple Watch.");
    };
    
    // Initialised WatchConnectivity Session successfully
    var initWatchSuccess = function() {
        // Sends a message through 'sendMessage'
        var message = {message: "hello from phone", value: "1234", foo: "bar"};
        WatchConnectivity.sendMessage(message, sendMessageSuccess, sendMessageFailure);
	    // Sends a message through 'updateApplicationContext'
	    WatchConnectivity.updateApplicationContext(message, sendMessageSuccess, sendMessageFailure);
        
	    // Register to receive messages
        WatchConnectivity.messageReceiver(receiveMessageSuccess, receiveMessageFailure);
    };
    var initWatchFailure = function() {
        alert("Could not connect to Apple Watch.");
    };
    
    // Starts things up - WCSession.default
    WatchConnectivity.init(initWatchSuccess, initWatchFailure);
```
## Use from Apple Watch extension

### Swift
```swift
// Setup and activate session in 'awakeWithContext' or 'willActivate' methods
if (WCSession.isSupported()) {
    let session = WCSession.defaultSession();
    session.delegate = self;
    session.activateSession();
}

// Implement activationDidCompleteWith to know when it has been paired
func session(_ session: WCSession, activationDidCompleteWith activationState: WCSessionActivationState, error: Error?) {
    // Session started :: phone x watch
    if (error != nil) {
        print("InterfaceController :: session :: error: ", error);
    } else {
        print("InterfaceController :: session :: state: ", activationState);
    }
}


// - - - RECEIVING MESSAGES 'FROM' CORDOVA (JS)

// Implement didReceiveMessage WatchConnectivity - handler/callback to receive its incoming messages
func session(_ session: WCSession, didReceiveMessage message: [String : Any], replyHandler: @escaping ([String : Any]) {
    // Receiving messages from iPhone
    print("InterfaceController :: session :: didReceiveMessage: ", message);
}

// Implement didReceiveApplicationContext WatchConnectivity - handler/callback to receive its incoming messages
func session(session: WCSession, didReceiveApplicationContext applicationContext: [String : AnyObject]) {
    // Receiving messages from iPhone
    print("InterfaceController :: session :: updateApplicationContext: ", message);
}


// - - - SENDING MESSAGES 'TO' CORDOVA (JS)

// Sending a message to iPhone - WCSession.default.sendMessage
func sendMessageNowWithHandlers() {
    let message = ["message": "hello from watch", "value": "4321", "bar": "foo"];
    WCSession.default.sendMessage( 
        message,
        replyHandler: { (response) -> Void in
            print("InterfaceController :: sendMessageNow :: Send message success : \(response)")
        },
        errorHandler: { (error) -> Void in
            print("InterfaceController :: sendMessageNow :: An error happened: \(error)")
        }
    );
}

// Sending a message to iPhone - WCSession.default.sendMessage
func sendMessageNowWithoutHandlers() {
    let message2 = ["message2": "hello from watch", "value2": "4321", "bar2": "foo"];
    WCSession.default.sendMessage(message2, replyHandler:nil);
}

// Sending a message to iPhone - WCSession.default.updateApplicationContext
func sendMessage() {
    do {
        let message3 = ["message3": "hello from watch", "value3": "54321", "bar3": "foo"];
	try WCSession.default.updateApplicationContext(message3);
    } catch {
        print("InterfaceController :: sendMessage :: error: ", error.localizedDescription);
    }
}
```
### Objective-C
```objective-c

// FIXME - Help wanted! Translate the code above from Swift to Objective-C
// Send through a PR and I will happily merge it, thank you very much.

```

## Credits
[Gui Keller](https://www.github.com/guikeller)

## More Info
TODO: The plugin is very simple and short without much error handling. 
