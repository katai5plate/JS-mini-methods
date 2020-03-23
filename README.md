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
### ネストされたオブジェクト内の値をドット区切り文字列で参照する
```js
const nestPicker = (obj, selector, delim = ".") =>
  selector.split(delim).reduce((p, c) => p[c], obj);

// var obj = {a: {b: {c: 1, f: 4}, e: 3}, d: 2}
// nestPicker(obj, "a.b.c") -> 1
```
### 指定した数で配列を分割
```js
const chunk = (arr, size) => {
  return Array.from({ length: Math.ceil(arr.length / size) }, (v, i) =>
    arr.slice(i * size, i * size + size)
  ).map(v => v.join(''));
}

// chunk([1, 2, 3, 4, 5, 6, 7, 8, 9, 0], 3); 
// -> [[1, 2, 3], [4, 5, 6], [7, 8, 9], [0]]
```
- https://github.com/30-seconds/30-seconds-of-code#chunk
### CSVをJSONに変換
```js
const CSVToJSON = (data, delimiter = ',') => {
  const titles = data.slice(0, data.indexOf('\n')).split(delimiter);
  return data
    .slice(data.indexOf('\n') + 1)
    .split('\n')
    .map(v => {
      const values = v.split(delimiter);
      return titles.reduce((obj, title, index) => ((obj[title] = values[index]), obj), {});
    });
};
```
- https://github.com/30-seconds/30-seconds-of-code#csvtojson-
### 配列の要素と長さを維持したまま前後にずらす
```js
const offset = (arr, offset) => [...arr.slice(offset), ...arr.slice(0, offset)];
```
- https://github.com/30-seconds/30-seconds-of-code#offset
### 配列をシャッフル
```js
const shuffle = ([...arr]) => {
  let m = arr.length;
  while (m) {
    const i = Math.floor(Math.random() * m--);
    [arr[m], arr[i]] = [arr[i], arr[m]];
  }
  return arr;
};
```
- https://github.com/30-seconds/30-seconds-of-code#shuffle
### 固定の手順と各々の分岐を記録した配列を総当り
```js
const bruteForce = (...[a, ...[b, ...rest]]) => b
  ? bruteForce(b.reduce((acc, x) => [...acc, ...a.map(y => [...y, ...x])], []), ...rest)
  : a;
```
```js
const data = [
  [ // 手順1
    [1, 2], // 分岐1
    [3]     // 分岐2
  ],
  [ // 手順2
    [4]     // 分岐1
  ],
  [ // 手順3
    [5, 6], // 分岐1
    [7],    // 分岐2
    [8, 9]  // 分岐3
  ]
]
bruteForce(...data);
/* -> [
  [1, 2, 4, 5, 6], // 1-1 * 2-1 * 3-1
  [3, 4, 5, 6],    // 1-2 * 2-1 * 3-1
  [1, 2, 4, 7],    // 1-1 * 2-1 * 3-2
  [3, 4, 7],       // 1-2 * 2-1 * 3-2
  [1, 2, 4, 8, 9], // 1-1 * 2-1 * 3-3
  [3, 4, 8, 9]     // 1-2 * 2-1 * 3-3
]
*/
```
## ➗ 算術系
### 点と点を結ぶ中継地点の座標を算出
```js
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
### テーブルをオブジェクトに変換
```js
const tableToObject = table =>
  [...table.querySelectorAll('tr')]
    .map(({ children }) => [...[...children].map(({ innerText }) => innerText)])
    .map((row, _, self) =>
      self[0].reduce((p, c, i) => ({ ...p, [c]: row.slice('-4')[i] }), {})
    )
    .slice(1);

// const table = document.querySelector('table');
// tableToObject(table);
```
## 🌐 通信/ブラウザ系
### Fetch APIをラップしてログを取得
```js
window._fetch = window._fetch ? window._fetch : window.fetch;
delete window.fetch;
window.fetch = (...args) => {
  console.log(...args);
  const returns = window._fetch(...args);
  returns.then(res => res.text()).then(res => console.log(res))
  return returns;
}
```
### URLパラメータ(クエリ文字列)を取得
```js
const getURLParams = url =>
  (url.match(/([^?=&]+)(=([^&]*))/g) || []).reduce(
    (a, v) => ((a[v.slice(0, v.indexOf('='))] = v.slice(v.indexOf('=') + 1)), a),
    {}
  );
```
- https://github.com/30-seconds/30-seconds-of-code#geturlparameters
## ⌚ 非同期処理系
### 数秒待つ
```js
const sleep = ms => new Promise(res => setTimeout(res, ms));
// await sleep(1000);
```
### パラメータだけ異なる関数を非同期で順次実行する
```js
const promiseStep = async (list, cb, ms) => {
  let res = [];
  let index = 0;
  for(const elm of list){
    index++;
    res.push(await new Promise(r => {
      setTimeout(async () => {
        r(await cb(elm, index, list));
      }, ms);
    }))
  }
  return res;
}
// await promiseStep(
//   ["https://github.com/", "https://github.com/katai5plate/"],
//   async url => (await fetch(url)).text(),
//   1000
// );
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
