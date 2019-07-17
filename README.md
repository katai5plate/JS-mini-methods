# JS-mini-methods
便利なJS関数の覚書集

## 📜文字列系
### 指定した文字数で文字列を分割
```js
const chunkString = (str, size) => {
  const arr = [...str];
  return Array.from({ length: Math.ceil(arr.length / size) }, (v, i) =>
    arr.slice(i * size, i * size + size)
  ).map(v => v.join(''));
}
// chunkString("1234567890", 3); 
// -> ["123", "456", "789", "0"]
```
## 🗃️データ操作系
### 指定した数で配列を分割
- https://github.com/30-seconds/30-seconds-of-code#chunk
```js
const chunk = (arr, size) => {
  return Array.from({ length: Math.ceil(arr.length / size) }, (v, i) =>
    arr.slice(i * size, i * size + size)
  ).map(v => v.join(''));
}
// chunk([1, 2, 3, 4, 5, 6, 7, 8, 9, 0], 3); 
// -> [[1, 2, 3], [4, 5, 6], [7, 8, 9], [0]]
```
## ➗算術系
## 📦DOM操作系
## 🌐通信系
## 🔧その他
