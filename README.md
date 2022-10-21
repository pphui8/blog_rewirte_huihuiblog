## 关于重写 huihuiblog 的 blog

1. [x] 用 nextjs, typescript 重写
2. [x] markdown 序号 bug
3. [x] 文章不可分享（路由问题）
4. [x] HOME 键有歧义
5. [x] 优化错误提示
6. [x] 优化后端api

## what's new
- new 404 page
- ssg makes browsing faster 
- github api authorization
- better blog view

## 心得
#### nextjs
1. 在 `next.config.js` 中设置自动路由跳转，加一个异步函数：
```js
  async redirects() {
    return [
      {
        source: "/",
        destination: "/home",
        permanent: true,
      },
    ];
  },
 ```
 
 ---
 
2. `nextjs` 的 css 强制使用 module 来作为组件级的样式表，而且不允许随便引用（真sb）

---

3. 奇淫技巧：ts 由于类型限制无法使用 `obj[key]` 的形式来直接对对象取值：
```ts
obj[key as keyof typeof obj]
```

---

4. 组件只取特定的父组件发来的值：
```ts
function Msg({msg} : {msg: Comment}) { }

<Msg key={index} msg={item}></Msg>; // 父组件发了俩值，只有一个有用
```

---

5. 官方指导的组件写法快速生成快捷键为 `tsrfce` ，e 指 `default export index`

---

6. 让组件 fetch 时报错只报一次：
> 核心思想：减少 `setXxx` 函数的调用来减少重构次数
```ts
let isShow = false;

const getData = () => {
  fetch('url')
    .then(res => res.json()
    .catch(err =>{
      toast.Error(err);
      isShow = true;
    })
}

useEffect(() => {
  getData()
}, [isShow])
```

---

7. `module css` 命名不允许中划线，只能下划线（turbofish命名法）（改捏🐎一晚上焯）

---

8. `useContext` 作全局状态管理
> 1. 定义状态组件
```tsx
interface Theme {
  // 数据
  isNight: Boolean;
  // 更新数据的函数
  toggleTheme: () => void;
}
export const ThemeContext = React.createContext<Theme>({
  isNight: false,
  toggleTheme: () => {},
});
// childern 即所有共享状态的子组件
export const ThemeContextProvider = ({ children }: any) => {
  const [theme, setTheme] = React.useState(false);
  return (
    <ThemeContext.Provider
      value={{
        isNight: theme,
        toggleTheme: () => setTheme(!theme),
      }}
    >
      <div className={theme ? styles.body_night : ''}>{children}</div>
    </ThemeContext.Provider>
  );
}
```

> 2. 挂载到根组件下
```tsx
import { ThemeContextProvider } from './ThemeContext';


function MyApp({ Component, pageProps }: AppProps) {
  return (
    <ThemeContextProvider>
      <Component {...pageProps} />
    </ThemeContextProvider>
  )
}
```
> 3. 子组件中调用
```tsx
import { ThemeContext } from "../ThemeContext";

const { isNight } = useContext(ThemeContext);
```

9. 类似 `golang` 的组合类型错误处理
可以使用下面语法来处理错误，判断时使用 `data.toString() === '[object Object]'` 来判断，使用 `data as Data` 语法来对类型强制转换。
```tsx
type Res = {
  data: Data | Error;
}
```

10. 动态路由组件不刷新：使用 `let` 声明的全局变量不会在页面重新进入时重置
```tsx
useEffect(() => {
  dealIndex(data);    // <- 若 indexHasDeal == true 则直接返回不执行，保证只执行一次
  return () => {      // <- 在离开页面时重置为 false
    indexHasDeal = false;
    cur_index = [];
    cur_file = [];
  };
}, [indexHasDeal]);   // <- 全局变量: let indexHasDeal = false;
```

---

11. 列表元素一个个加载
> 关键是 `intersection-observer` 组件更改组件是否可视以及根据 `index` 大小设置了动画的 delay
```tsx
    <InView
      onChange={(inView) => {
        if (!inView) return;    <- 动画只播放一次
        setInView(inView);
      }}
    >
      {({ ref }) => (
        <div
          className={inView ? styles.inView : ""}
          style={{ animationDelay: delay * 0.1 + "s" }}   <- 动态设置动画
        >
          <components />
        </div>
      )}
    </InView>
```

---

12. 注意到很奇怪的一点是在 props 中对象的值为 undefined 时报错为不能序列化。

---

13. 使用 base64 编码 url 避免汉字冲突：
```tsx
const encoded = Buffer.from(url).toString('base64');
const decoded = Buffer.from(url, 'base64').toString('utf-8');
```

---

