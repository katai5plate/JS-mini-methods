# JS-mini-methods
便利なJS関数の覚書集

## 📜 文字列系
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
## 🗃️ データ操作系
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
## ➗ 算術系
### 点と点を結ぶ中継地点の座標を算出
```
const lerp = (a, x, y) => (x, y, a) => x + (y - x) * a;
const lerp2D = (a, { x: x1, y: y1 }, { x: x2, y: y2 }) => {
  const calc = (x, y, a) => x + (y - x) * a;
  return { x: calc(x1, x2, a), y: calc(y1, y2, a) };
};
const lerp3D = (a, { x: x1, y: y1, z: z1 }, { x: x2, y: y2, z: z2 }) => {
  const calc = (x, y, a) => x + (y - x) * a;
  return { x: calc(x1, x2, a), y: calc(y1, y2, a), z: calc(z1, z2, a) };
};

// lerp(0.5, 0, 2);
// -> 1
```
## 📦 DOM操作系
## 🌐 通信/ブラウザ系
### URLパラメータ(クエリ文字列)を取得
- https://github.com/30-seconds/30-seconds-of-code#geturlparameters
```
const getURLParams = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );
```
## 🔧 その他
### BrainfuckをJavaScriptにコンパイル
```js
const bf2js = bf =>
  `(async()=>{var p=[],c=0,i="",w=()=>new Promise(r=>setTimeout(r,1));document.onkeydown=e=>i=e.key.charCodeAt();${bf.replace(
    /(.)/g,
    m =>
      [
        '++c;',
        '--c;',
        'p[c]=p[c]===undefined?1:p[c]+1;',
        'p[c]=p[c]===undefined?-1:p[c]-1;',
        'while(p[c]){',
        'await w();}',
        'p[c]=i;',
        'console.log(String.fromCharCode(p[c]));'
      ]['><+-[],.'.indexOf(m)]
  )}})()`;

// const src = '>+++++++++[<++++++++>-]<.>+++++++[<++++>-]<+.+++++++..+++.[-]>++++++++[<++++>-]<.>+++++++++++[<+++++>-]<.>++++++++[<+++>-]<.+++.------.--------.[-]>++++++++[<++++>-]<+.[-]++++++++++.';
// await eval(bf2js(src));
// -> Hello World
```
