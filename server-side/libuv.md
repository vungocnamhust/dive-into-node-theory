# Libuv
`call stack` | `heap` | `callback queue` | `node api` 

## How it works?
* `libuv` : là library viết bằng `C++` chịu trách nhiệm quản lý số lượng `worker thread` trong `Thread pool` để tránh bị out of memory và cung cấp `event-loop-mechanism` để execute code đối với `single-threaded model`
* `Event loop` ở node.js cơ chế giống với javascript chạy ở trên browser. Điều khác biệt là trên browser thì cần dùng web api, trên server dùng node api. Trên server còn có các api tương tác với OS như thao tác với file, http, timer,...
* `Call stack` là ngăn xếp chứa các đoạn code cần được execute
* `Node api` là khi có 1 function cần gọi đến I/O bound services, các services cần thời gian để execute, CPU bound services
* `Callback queue` chứa các function đã nhận được response qua `node api` để đợi đi vào `call stack` và execute. *Lưu ý*: chỉ khi `call stack empty` thì mới được đẩy các callback vào stack

* ![[event-loop-sample.png]]

## Demo
==*Trường hợp call library dùng asynchronous function* 
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

1. Đẩy `async-func-encryptPassword` vào `call stack`
2. Đẩy `async-func-hashBcrypt` vào `call stack`. Bởi vì sử dụng await nên các function ở dưới sẽ không được đẩy vào stack
3. Đẩy `Promise` vào `call stack`
4. Đẩy `bcrypt.hash()` vào `queue node api`  đợi execute async func. Đến khi có response `bcrypt.hash` sẽ đẩy vào `callback queue`. Đồng thời các function `Promise`, `hashBcrypt`, `encryptPassword` lần lượt bị đẩy khỏi `call stack` khiến stack empty. *Dự đoán hàm hash ở đây sử dụng nhiều CPU throughput nên mới phải dùng async*
5. `event loop` đẩy callback `bcrypt.hash` vào `call stack`
6. Đẩy `resolve()` hoặc `reject()` vào `call stack` 
7. Trả ra giả trị của `hashedBcrypt`
8. Hoàn thành.

==*Trường hợp call library dùng node.js bindings hoặc standard libraries như http,... để tương tác với các I/O bound, CPU bound services* 

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

1. Đẩy `vbeeTTS` vào `call stack`
2. Khai báo `options` trong `call stack`. Đẩy `options` khỏi stack
3. Đẩy `axios(options)` vào stack
4. Đẩy `axios(options)` vào `queue node api` đợi execute từ I/O bound, CPU bound service. Ở đây sử dụng `http library` (*xem ví dụ về* [[node-bindings_standard-libraries]]). Đồng thời các function `vbeeTTS` lần lượt bị đẩy khỏi `call stack` khiến stack empty.
5. Hoàn thành khi response trả về được `event loop` đưa vào `callback queue` rồi vào `call stack`.

## References
* *[The best understandable event loop](http://latentflip.com/loupe/?code=ZnVuY3Rpb24gcHJpbnRIaSgpIHsNCiAgICBjb25zb2xlLmxvZygnSGkgZnJvbSBiYXInKTsNCn0NCmZ1bmN0aW9uIHByaW50SGVsbG8oKSB7DQogICAgY29uc29sZS5sb2coJ0hlbGxvIGZyb20gYmF6Jyk7DQp9DQoNCmZ1bmN0aW9uIGJheigpIHsNCiAgICBzZXRUaW1lb3V0KHByaW50SGVsbG8sIDMwMDApOw0KfQ0KDQpmdW5jdGlvbiBiYXIoKSB7DQogICAgYmF6KCk7DQogICAgc2V0VGltZW91dChwcmludEhpLDApOw0KfQ0KDQpmdW5jdGlvbiBmb28oKSB7DQogICAgYmFyKCk7DQp9DQoNCmZvbygpOw%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

## Tags
#howappworks