# Dive into Node.JS application

## Purposes
- Project nhằm mục đích học sâu backend bằng cách vẽ lại sơ đồ hoạt động của ứng dụng [Vietstudy] từ lúc user ấn nút cho đến việc lấy data từ server. Trong quá trình học sâu sẽ hiểu được tổng quan việc developer viết code sẽ chạy ra sao ở tầng ứng dụng, tầng network, tầng os,...
* Có những phần sử dụng 1 đoạn code cụ thể để demo cách hoạt động
* Có những phần chỉ nêu ý tưởng cách hoạt động chung chung


## Architecture
* Code base được viết khá đơn giản theo mô hình MVC gồm có:
	* Model: mongoose để kết nối với mongodb
	* Controller: nơi nhận request và dùng nghiệp vụ (business) để xử lý request
	* Service: nơi tương tác với database
	* Middleware
* Deploy:
	* Docker, Gitlab runner, Gitlab CI/CD
* Database
	* Mongodb
* Node.js
![[node.js-architecture.png]]


## Expectations
1. Hiểu luồng hoạt động của project, các original library viết gì, node module chứa gì [MVC, Node.JS] ==status: available
2. Hiểu code sẽ biên dịch ra cái gì [V8 engine, JS compiler & interpreter, AST] ==status: available
3. Hiểu mongo hoạt động như nào ở own server và db server [MongoAtlas, Mongos, Mongd, Mongsh, Mongo driver] ==status: available
4. Hiểu node.js sẽ lấy code và xử lý như nào [Call stack & heap, Event loop, Libuv, Thread pool, Node api, Garbage collection] ==status: available
5. Hiểu được 1 chút về javascript [Javacript programming] ==status: unavailable
6. Hiểu bảo mật trên internet như nào [Security] ==status: unavailable
7. Hiểu việc test chạy như nào trong code [Testing] ==status: unavailable
8. Hiểu làm sao mà khi viết script lại chạy được các file code, các câu lệnh CI/CD thực sự chạy cái gì [Devop, Bash script, Docker, CI/CD] ==status: unavailable
9. Hiểu làm sao request đến được server [Network] ==status: unavailable
10. Đánh giá được điểm mạnh & yếu của architecture và đưa ra cải tiến [Software architecture] ==status: unavailable

## Document
- Client side (*[[client-side]]*)
- Server side (*[[server-side]]*)
* [Project tương tự có dùng docker, gitlab ci/cd](https://gitlab.com/soict-it4552e/20202/group10/book-tour-backend)

### To be continued ...
==*Hiểu được luồng từ lúc user tương tác với Chrome đến khi thao tác xong. Lúc ấy đổi thành dive-into-fullstack

## Tags
#howappworks