# Node app
`middlewares` | `router` | `controller` | `service` | `mongoose model` | `npm original code` 

## Architecture
* Kiến trúc project khá cơ bản nên chỉ tập trung giải thích về cách hoạt động ở các layer sâu hơn.
```bash
├── middlewares
├── routes
├── controllers
├── services
├── models
├── utils
├── logs
├── docker
├── docker-compose.yml
├── env
├── errors
├── tests
```

## Use cases

==*Trường hợp call library dùng synchronous function*
* `crypto` là third-library dùng để tạo ra mã hash. Khi call function của crypto chỉ đơn giản là nó sẽ chạy lần lượt các đoạn code synchronously
```node.js
/*auth_service*/
const hashSHA512 = text =>
	crypto
	.createHash('sha512')
	.update(text)
	.digest('hex');
```

==*Trường hợp call library dùng asynchronous function* 
* `bcrypt` là third-party dùng để hash text với 1 key cho trước là salt. Để hash được thì cần 1 async function. Tuy nhiên vì phải thực hiện function này synchronously nên phải dùng await.
```node.js
/*auth_service*/
const hashBcrypt = async (text, salt) =>
	new Promise((resolve, reject) => {
		bcrypt.hash(text, salt, (err, hash) => {
			if (err) reject(err);
			resolve(hash);
	});
});

const encryptPassword = async (password, salt) => {
	const hashedBcrypt = await hashBcrypt(hashedSHA512, salt);

	const encryptAES256 = encrypt(hashedBcrypt);
	const encryptedPassword = encryptAES256;
	return encryptedPassword;
}
```
* `Promise sample` cho thấy rằng thực hiện function này synchronously nên main thread bị delay 1 lúc cho việc tính toán generate mã hash. Khi sử dụng await mọi thứ sẽ diễn ra trong [[libuv]]
![[promise()-sample.png]]

==*Trường hợp call library dùng node.js bindings hoặc standard libraries như http,... để tương tác với các I/O bound, CPU bound services* 
* `vbeeTTS` sử dụng function `axios` để thực hiện call API. 
* Quá trình thực hiện sẽ diễn ra trong [[libuv]]

```node.js
const vbeeTTS = async ({ inputText, speaker = 'ngochuyen', returnType }) => {
	const options = {
		url: `${BASE_URL}/predict-acoustic-features`,
		method: 'POST',
		timeout: 300000,
		headers: {
			'Content-Type': 'application/json',
			Authorization: SECRET_KEY,
		},
		data: {
			speaker,
			input_text: inputText,
			return_type: returnType,
			speed_rate: '1.3',
		},
	};
	
	try {
		const response = await axios(options);
		return response.data;
	} catch (error) {
		console.log(error);
		return { message: 'failed' };
	}
};
```

==*Trường hợp call mongoose model để query database* 
* `mongoose` được sử dụng để client tương tác với [[mongodb]]
```node.js
/*user_service.js*/
const findById = async id => {
	const result = await User.findById(id);
return result;

};
```

```node.js
/*user_model.js*/
const mongoose = require('mongoose');
const { ObjectId } = mongoose.Types;
const userSchema = new mongoose.Schema({
		email: { type: String, required: true },
		password: { type: String, required: true },
		salt: String,
		name: String,
		role: Number,
		ref: 'Result',
	},
	{
		timestamps: true,
		versionKey: false,
	},
);

module.exports = mongoose.model('User', userSchema);
```


## Tags
#howappworks