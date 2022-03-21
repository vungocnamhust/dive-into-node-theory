# V8 Engine
`interpreter` | `compiler` | `call stack` | `heap memory` | `garbage collector` 

## Architecture

![v8-engine-works.png](app://local/%2FUsers%2Fvunam%2Fworkspace%2Fworkspace-note%2Fprojects%2Fassets%2Fv8-engine-works.png?1642051219136)

![[v8-engine-architecture.png]]

## How it works?
* `V8 engine` là 1 library của google sử dụng trên `google search engine`. Sau đó mang V8 về node.js để biên dịch javascript ra `machine code` để tối ưu hiệu suất 
* `Garbage collector` dùng các thuật toán để dọn dẹp các variable, function không còn được sử dụng nhưng vẫn phải lưu ở `stack` | `heap`.
* `Call stack` | `Heap memory` dùng để lưu function, variable,...
1. V8 compiles scripts thành `AST` & `scopes`.
2. 1 `compiler` compiles `AST` & `scopes` thành `machine codes`.
3. V8 detects được các đoạn machine codes lặp lại nhiều lần thì `marks them as “hot".`
4. 1 `compiler khác` optimizes các đoạn `“hot” codes` thành `optimized machine codes`. Bởi vì tránh sai xót trong việc compile. 
	* **Ví dụ:** *1 func findNumber(value) và findNumber(1) đều là function findNumber nhưng mục đích khác nhau & compiler lại không thể phân biệt được nên sẽ gây ra sai xót*
5. Nếu `optimization fails`, compiler runs `de-optimization`  process (*loại bỏ việc optimize*).


## References
* [How v8 engine works?](https://cabulous.medium.com/how-v8-javascript-engine-works-5393832d80a7)
* [Context scope trong javascript](https://cabulous.medium.com/javascript-execution-context-lexical-environment-and-block-scope-part-3-fc2551c92ce0)


## Tags
#howappworks