## 关于重写 huihuiblog 的 blog

1. [x] 用 nextjs, typescript 重写
2. [x] markdown 序号 bug
3. [x] 文章不可分享（路由问题）
4. [x] HOME 键有歧义
5. [x] 优化错误提示
6. [ ] 优化加载动画
7. [ ] 添加 blog 日期显示（github项目创建日期）
8. [x] 优化后端api

## what's new
- new 404 page
- ssg makes browsing faster 
- github api authorization

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
2. `nextjs` 的 css 强制使用 module 来作为组件级的样式表，而且不允许随便引用（真sb）
3. 奇淫技巧：ts 由于类型限制无法使用 `obj[key]` 的形式来直接对对象取值：
```ts
obj[key as keyof typeof obj]
```
4. 组件只取特定的父组件发来的值：
```ts
function Msg({msg} : {msg: Comment}) { }

<Msg key={index} msg={item}></Msg>; // 父组件发了俩值，只有一个有用
```
5. 官方指导的组件写法快速生成快捷键为 `tsrfce` ，e 指 `default export index`

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

7. `module css` 命名不允许中划线，只能下划线（turbofish命名法）（改捏🐎一晚上焯）

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

2. 挂载到根组件下
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
3. 子组件中调用
```tsx
import { ThemeContext } from "../ThemeContext";

const { isNight } = useContext(ThemeContext);
```
