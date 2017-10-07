# Introducing SignalR.TypeScript

Hello, fellow programmers.

If you, like me, love both SignalR and TypeScript, you might find this interesting and/or useful.

## What is this?

SignalR.TypeScript helps you generate TypeScript type definitions for your SignalR hubs. It also generates type definitions for the items you pass between client and server.

Some benefits of using TypeScript include:

- Build-time type checking (instead of just runtime)
- Better tooling support (e.g. great IntelliSense support on Visual Studio)


If you prefer to use TypeScript instead of raw JavaScript on your client code, this tool can be really handy and would save you from hand-rolling your .d.ts files or using “any” everywhere. Please note that this is still in “alpha” so there might be rough edges.

## How do I use this with Visual Studio 2012 or 2013?

- Ensure you have TypeScript VS tooling installed. If not, go ahead and install it.
- Load your SignalR project or create a new one. Add some hubs, whatever.
- In the Package Manager Console type `Install-Package SignalR.TypeScript -Pre`. Three files will be added to your project’s root folder: types.tt, types.d.ts and signalr.d.ts. This package also contains command line tool srts.exe.
- You might have to acknowledge a security warning: “Running this text template can potentially harm your computer”. This is because you’re running a third party text template with executable code.
- If you are using Visual Studio 2013, you might also get a window informing you that you now have TypeScript support in your project, and asking if you’d like to look at some TypeScript related packages. Click “No” for now.
- Types.d.ts file contains your type declarations. You can reference this file from your .ts files
- You are free to move these three files anywhere in the project you’d like.
- To regenerate types.d.ts file (to reflect the changes in your hubs) you can right click on types.tt file and select “run custom tool.”
- You can also use srts.exe from the command line. You can find this file under packages\SignalR.TypeScript.0.1.0-alpha\srts folder. Copy this file to the folder where your assemblies are. Run “srts.exe MYASSEMBLY.DLL > myassambly.d.ts”

## Supported .NET types

- Custom classes/interfaces
- Tuple types
- Nullable types
- Task<T>
- List, IEnumerable etc.
- Dictionary etc.
- Enums

## Known issues/current limitations

- The tool does not detect changes in your hub automatically. After changing your hub, you’ll need to rebuild your project, and  “run custom tool” on the types.tt file to get an updated types.d.ts file. You can potentially automate this step using srts.exe and a bit of build magic
- Hub base’s methods (OnConnected, OnReconnected, etc) are not used on the client side, but they’re still included in the generated declarations file.
- If you’d like to use srts.exe from the command line, you’ll need to copy it to the directory that your assembly resides in.
- The tool works by scanning your assembly for hub classes. If you have something funky going on with your assembly, it might cause this scanning process to fail. (Hypothetical)

## An example types.d.ts  output

This file was generated for the JabbR project.

```typescript
/* Sample code -- create a .ts file, and paste the following to get started
	/// <reference path="types.d.ts" />
	/// <reference path="signalr.d.ts" />
	// Proxies 
	var monitor: IMonitorProxy = (<any>$.connection).monitor;
	var chat: IChatProxy = (<any>$.connection).chat;
	// Client implementations go here... add some functions.
	monitor.client = { 
	};
	chat.client = { 
	};
	$.connection.hub.start().done((a) => {
		// alert('connected.'); 
		// (do something) 
	});
*/

// Client interfaces. These are the return types for the proxies.
// Implement these in your .ts file, but do not manually edit them here. 
interface IMonitorClient { }
interface IChatClient { }

// Data interfaces 
interface ClientMessage {
	Id: string;
	Content: string;
	Room: string;
}
interface UserViewModel {
	Name: string;
	Hash: string;
	Active: boolean;
	Status: string;
	Note: string;
	AfkNote: string;
	IsAfk: boolean;
	Flag: string;
	Country: string;
	LastActivity: string;
	IsAdmin: boolean;
}
interface LobbyRoomViewModel {
	Name: string;
	Count: number;
	Private: boolean;
	Closed: boolean;
	Topic: string;
}
interface MessageViewModel {
	HtmlEncoded: boolean;
	Id: string;
	Content: string;
	HtmlContent: string;
	When: string;
	User: UserViewModel;
	MessageType: number;
	UserRoomPresence: string;
	ImageUrl: string;
	Source: string;
}
interface RoomViewModel {
	Name: string;
	Count: number;
	Private: boolean;
	Topic: string;
	Closed: boolean;
	Welcome: string;
	Users: Array<UserViewModel>;
	Owners: Array<string>;
	RecentMessages: Array<MessageViewModel>;
}
interface ClientNotification {
	Room: string;
	ImageUrl: string;
	Source: string;
	Content: string;
}

// Hub interfaces 
interface IMonitor {
}
interface IChat {
	onConnected(): IPromise<void>;
	join(): IPromise<void>;
	join(reconnecting: boolean): IPromise<void>;
	send(content: string, roomName: string): IPromise<boolean>;
	send(clientMessage: ClientMessage): IPromise<boolean>;
	getUserInfo(): IPromise<UserViewModel>;
	onReconnected(): IPromise<void>;
	onDisconnected(): IPromise<void>;
	getCommands(): IPromise<any>;
	getShortcuts(): IPromise<any>;
	getRooms(): IPromise<Array<LobbyRoomViewModel>>;
	getPreviousMessages(messageId: string): IPromise<Array<MessageViewModel>>;
	loadRooms(roomNames: Array<any>): IPromise<void>;
	getRoomInfo(roomName: string): IPromise<RoomViewModel>;
	postNotification(notification: ClientNotification): IPromise<void>;
	postNotification(notification: ClientNotification, executeContentProviders: boolean): IPromise<void>;
	typing(roomName: string): IPromise<void>;
	updateActivity(): IPromise<void>;
	tabOrderChanged(tabOrdering: Array<any>): IPromise<void>;
}

// Generetated proxies 
interface IMonitorProxy {
		server: IMonitor;
		client: IMonitorClient;
}
interface IChatProxy {
		server: IChat;
		client: IChatClient;
}
```
