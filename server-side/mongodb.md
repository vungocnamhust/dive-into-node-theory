# Mongodb
`mongo driver` | `mongos` | `mongod` | `shard (replica set)` | `mongosh` | `mongo atlas` | `config server` | `scale`

## Architecture
* `mongodb`: phát triển năm 2007 là NoSQL database sử dụng non-structure query language & document-oriented data model. Nó được xây dựng nhằm tương thích với các modern programming language,...
* `shard (replica set)`: là 1 cụm tập hợp các mongodb server. Ở đó có 1 `primary` server (server chính) và các `secondary` server. mongos có quyền được `write` vào primary, quyền được `read` ở secondary. Nếu có 2 secondary thì cần cơ chế `heart beat` để bầu primary khi primary bị down
* `mongo driver`: là các library viết bằng các ngôn ngữ khác nhau để giúp developer xây dựng application tương tác dễ dàng hơn với database. Với Node.js thì có library `node-mongodb-native` 
* `mongoose`: là `mongodb object modeling tool` dùng để work in an asynchronous environment. Mongoose supports promises & callbacks. Tức là sử dụng mongoose như là 1 tool để lấy ra các document object model lưu trong mogodb
* `mongos (query router)`:  là interface giúp driver tương tác với server. Nó có thể cache metadata trong `config server` để `routing` từ application đến `mongod instance`. Nó cung cấp API để xây dựng các thư viện như mongoose (node.js), mongo (c++),... tối ưu cho developer sử dụng
* `mongod`: là 1 daemon process dùng để handle requests, manage data access, thực hiện các tác vụ ngầm. Mỗi mongdb database sẽ có 1 mongod.
* `mongodb atlas`: là database trên cloud được xây dựng dành cho những application gobal
* `mongosh`: là shell dùng cho developer có thể truy cập bằng `command line` tương tác với `mongos`
* `vertical scale`: là 1 cách để scale khả năng của server. Mongo cung cấp 1 cách là `sharding`, có nghĩa là chúng ta sẽ có nhiều shards ở nhiều nơi trên thế giới lưu trữ data giống nhau để tối ưu việc truy cập database gobally.
* `config server`: lưu các metadata của các shards như:
		-   [`changelog`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.changelog)
		-   [`chunks`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.chunks)
		-   [`collections`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.collections)
		-   [`databases`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.databases)
		-   [`lockpings`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.lockpings)
		-   [`locks`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.locks)
		-   [`mongos`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.mongos)
		-   [`settings`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.settings)
		-   [`shards`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.shards)
		-   [`version`](https://docs.mongodb.com/manual/reference/config-database/#mongodb-data-config.version)

![[mongodb-architecture.png]]

## How it works?
* Demo cách hoạt động với function `connect()`
```node.js
/*node app: index.js*/
mongoose.connect(mongoUri, {
	autoIndex: false,
	useNewUrlParser: true,
	useFindAndModify: false,
	useUnifiedTopology: true,
});
```

* Trong lib `mongoose` sẽ chạy function `index.js` và call function `openUri()`
```node.js
/* mongoose/lib/index.js */
Mongoose.prototype.connect = function() {
	const _mongoose = this instanceof Mongoose ? this : mongoose;
	const conn = _mongoose.connection;
	return conn.openUri(arguments[0], arguments[1], arguments[2]).then(() => _mongoose);
};
```

* Trong module `connection` định nghĩa function `openUri` là 1 Promise() trả về `MongoClient instance`.
```node.js
/* mongoose/lib/connection.js */

Connection.prototype.openUri = function(uri, options, callback) {
	// code xử lý options: processConnectionOptions(uri, options)

	const promise = new Promise((resolve, reject) => {
		let client;		
		try {
			client = new mongodb.MongoClient(uri, options);
		} catch (error) {
			_this.readyState = STATES.disconnected;
			return reject(error);
		}
		
		_this.client = client;
		client.setMaxListeners(0);
		client.connect((error) => {
			if (error) {
				return reject(error);
			}
			_setClient(_this, client, options, dbName);
			resolve(_this);
		});
	});
		
	const serverSelectionError = new ServerSelectionError();
		
	this.$initialConnection = promise.then(() => this).catch(err => {
			this.readyState = STATES.disconnected;
			if (err != null && err.name === 'MongoServerSelectionError') {
				err = serverSelectionError.assimilateError(err);
			}
			if (this.listeners('error').length > 0) {
				immediate(() => this.emit('error', err));
			}
			throw err;
		});
		
		if (callback != null) {
			this.$initialConnection = this.$initialConnection.then(() => { 
				callback(null, this); return this; },
				err => callback(err)	
			);
	}
	return this.$initialConnection;
}
```

* `MongoClient instance` dùng để talk với `MongDB`. Mongoose `automatically` sets this property khi connection được opened.
```node.js
/* mongoose/lib/connection.js
*
* @property client
* @memberOf Connection
* @instance
* @api public
*/

Connection.prototype.client;
```

* Libary `node-mongodb-native` là `offical MongoDB driver` cho Node.js cung cấp các api để tương tác giữa client vs mongodb
* *Khá là khó để hiểu code nó làm gì*
* [Code MongoClient](https://github.com/mongodb/node-mongodb-native/blob/c3256c41366532d95dc5b1dff8b92bfe41e9f718/src/mongo_client.ts#L332)
```typescript
/* node-mongodb-native/src/mongo_client.ts */
```
* Hoàn thành


## Tags
#howappworks