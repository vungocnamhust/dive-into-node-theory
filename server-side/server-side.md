# Server side

## Architecture
* Bước 1: Server xác nhận IP address, port đều đang open & được cấp permission. Với `UDP` thì sau khi xác nhận phải gửi `ack` message cho client biết là đã kết nối thành công
* Bước 2: Request sau khi đi vào `ip_address:port`  sẽ gọi command line (đã được developer setup `gitlab CI/CD, bash script` từ trước hoặc dùng 1 phần mềm như `docker, npm`) để tương tác với application (*deploy app, build app, write, read,...*)
* Bước 3a: Nếu request dạng build app, deploy app thì docker sẽ download & update các library như npm, bash, fs... rồi sau đó dùng các script được viết trong file `docker-compose.yml` để call OS API. Hệ điều hành sẽ tạo các process, thread để execute các câu lệnh
```bash
docker-compose build
docker-compose up -d
npm server.js
apk add --update openssh-client bash
ping -c 3 13.76.131.255
eval $(ssh-agent -s)
```
* Bước 3b: Nếu request dạng read, write data thì sẽ đi [[node-app]] -> [[libuv]]-> [[node-bindings_standard-libraries]] -> [[mongodb]]. Trong quá trình đó nếu muốn computer hiểu được function viết gì thì phải sử dụng [[v8-engine]] để biên dịch ra `machine code`. Từ đó computer mới hiểu và ra lệnh thực thi
* > `Libuv | V8 | C-ares | http-parser | open-ssl | zlib` -> `mongos` + `config servers` -> `shards (replica set) | all mongod` -> `mongod` -> `document` 
1. Node application ([[node-app]])
2. Libuv ([[libuv]])
3. V8 engine ([[v8-engine]])
4. Mongodb ([[mongodb]])


## Tags
#howappworks