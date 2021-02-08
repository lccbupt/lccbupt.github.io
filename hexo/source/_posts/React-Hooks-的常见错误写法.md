---
title: React Hooks 的常见错误写法
date: 2020-07-10 11:37:31
tags: React
---
> 原文地址（英文）：https://www.lorenzweiss.de/common_mistakes_react_hooks/

### 1、在不需要再次render的时候使用useState
react的核心概念之一就是处理state，你可以通过state控制你的整个数据流和渲染（rendering），每次虚拟Dom树再次渲染时，都很有可能和state的改变有关。

通过使用useState hook你可以在函数组件中定义state，这样处理state就变得很简单整洁，但是当我们看到下面的例子时也会觉得困惑。

假如有两个按钮，一个按钮是计数器，另一个按钮负责发送当前的数值。然而，当前的数值并不在组件内展示，它只是在按钮发送请求中使用。
<!--more-->
this is dangerous:

        function ClickButton(props) {
          const [count, setCount] = useState(0);

          const onClickCount = () => {
            setCount((c) => c + 1);
          };

          const onClickRequest = () => {
            apiCall(count);
          };

          return (
            <div>
              <button onClick={onClickCount}>Counter</button>
              <button onClick={onClickRequest}>Submit</button>
            </div>
          );
        }

问题：

刚开始你可能会问这有什么问题？这不就是state需要做的么？你是对的，这样写代码也是可以正常工作而且并不会有什么问题；但是在react中每一个state改变都会强制去再次render组件或者子组件。但是在上面的例子中我们并没有在render部分使用state中的count数字，这样就会造成在每次点击counter按钮时造成不必要的render，这样会影响性能或者产生其他负影响。

解决方法：

如果你需要在组件使用一个变量，但是该变量在rendering的时候保持其值不变而且也不强制执行render，你可以使用userRef hook，它可以保持你的变量值的同时不强制执行组件的render（重新渲染）

        function ClickButton(props) {
          const count = useRef(0);

          const onClickCount = () => {
            count.current++;
          };

          const onClickRequest = () => {
            apiCall(count.current);
          };

          return (
            <div>
              <button onClick={onClickCount}>Counter</button>
              <button onClick={onClickRequest}>Submit</button>
            </div>
          );
        }

### 2、使用router.push而不是link
这可能是很简单明了的一个问题，其和react本身并没有关系，但是却很常见
比如你设计一个按钮用户点击后跳转到其他页面，因为这是衣蛾SPA项目，使用客户端路由机制，所以你需要库函数去实现，在react中最流行的实现方法就是使用react—router，如下例子。
所以添加点击事件实现重定向到期望页面是对的吗？
this is dangerous

        function ClickButton(props) {
          const history = useHistory();

          const onClick = () => {
            history.push('/next-page');
          };

          return <button onClick={onClick}>Go to next page</button>;
        }

问题：

尽管这对大多数用户来说都很好，但当涉及到可访问性时，这里存在一个巨大的问题。按钮将不会被标记为链接到另一个页面，这使得它几乎不可能被屏幕阅读器识别。而且你能在一个新的标签或者窗口中打开它吗?答案是不能。

解决方法：

在可能的情况下，通过任何用户交互链接到其他页面应该由<Link>组件或普通的 a 标签处理。

        function ClickButton(props) {
          return (
            <Link to="/next-page">
              <span>Go to next page</span>
            </Link>
          );
        }

### 3、通过useEffect处理actions
React引入的最好、最深思熟虑的Hook之一是“useEffect”。它支持处理与props或state更改相关的actions。尽管它的功能很有帮助，但它也经常在不需要它的地方使用。

比如一个组件获取一个列表项并将它渲染到dom中，此外，如果请求成功，我们将调用“onSuccess”函数，该函数将作为props传递给组件

this is dangerous

        function DataList({ onSuccess }) {
          const [loading, setLoading] = useState(false);
          const [error, setError] = useState(null);
          const [data, setData] = useState(null);

          const fetchData = () => {
            setLoading(true);
            callApi()
              .then((res) => setData(res))
              .catch((err) => setError(err))
              .finally(() => setLoading(false));
          };

          useEffect(() => {
            fetchData();
          }, []);

          useEffect(() => {
            if (!loading && !error && data) {
              onSuccess();
            }
          }, [loading, error, data, onSuccess]);

          return <div>Data: {data}</div>;
        }

问题：

上面的例子中有两个useEffect ，第一个useEffect在初始render处理api的调用，第二个useEffect用来调用onSuccess函数，通过判断no loading,no error，而且data有值保证请求的成功调用，这样是对的么？

解决方法：

一个直接的解决办法是将onSuccess函数放到请求成功后的位置

        function DataList({ onSuccess }) {
          const [loading, setLoading] = useState(false);
          const [error, setError] = useState(null);
          const [data, setData] = useState(null);

          const fetchData = () => {
            setLoading(true);
            callApi()
              .then((fetchedData) => {
                setData(fetchedData);
                onSuccess();
              })
              .catch((err) => setError(err))
              .finally(() => setLoading(false));
          };

          useEffect(() => {
            fetchData();
          }, []);

          return <div>{data}</div>;
        }

### 4、单责任组件
设计组件不容易，何时该将组建拆分成更小的组件？你如何设计组件树？这些都是组件基础框架中遇到的问题，然而在设计组件中一个常见的错误时合并两个组件成为一个组件，如下面的例子header中在移动端显示button在pc端显示tab

this is dangerous

        function Header(props) {
          return (
            <header>
              <HeaderInner menuItems={menuItems} />
            </header>
          );
        }

        function HeaderInner({ menuItems }) {
          return isMobile() ? <BurgerButton menuItems={menuItems} /> : <Tabs tabData={menuItems} />;
        }

问题：

这样一来，HeaderInner组件就试图同时做两件不同的事情，同时做多件事情并不是很理想。这也使得在其他地方测试或重用组件变得更加困难

解决方法：

将条件提升一层，可以更容易地看到组件是为什么而设计，它们只有一个职责，即标题、选项卡或汉堡按钮，而不是试图同时成为两件事。

        function Header(props) {
          return (
            <header>{isMobile() ? <BurgerButton menuItems={menuItems} /> : <Tabs tabData={menuItems} />}</header>
          );
        }

### 5、单责任useEffects
还记得我们只有componentWillReceiveProps或componentDidUpdate方法来挂钩到react组件的呈现过程吗?它让人回想起黑暗的记忆，也让人意识到使用“useEffects”的美妙之处，尤其是你可以想要多少就有多少。

但有时忘记和使用“useEffects”会让人想起那些黑暗的记忆。例如，假设有一个组件，它以某种方式从后端获取一些数据，并根据当前位置显示面包屑。(再次使用react-router获取当前位置)

this is dangerous

        function Example(props) {
          const location = useLocation();

          const fetchData = () => {
            /*  Calling the api */
          };

          const updateBreadcrumbs = () => {
            /* Updating the breadcrumbs*/
          };

          useEffect(() => {
            fetchData();
            updateBreadcrumbs();
          }, [location.pathname]);

          return (
            <div>
              <BreadCrumbs />
            </div>
          );
        }

问题：

这里有两个用例，“数据获取”和“显示面包屑”。两者都使用useEffect进行更新。当fetchData和updateBreadcrumbs函数或location更改时这个单个useEffect挂钩将运行。主要问题是当location改变时仍会调用fetchData函数。这可能引起意想不到的副作用。

解决方法：

确保拆分effect，它只用于一种effect而且消除意想不到的额外影响

        function Example(props) {
          const location = useLocation();

          const updateBreadcrumbs = () => {
            /* Updating the breadcrumbs*/
          };

          useEffect(() => {
            updateBreadcrumbs();
          }, [location.pathname]);

          const fetchData = () => {
            /*  Calling the api */
          };

          useEffect(() => {
            fetchData();
          }, []);

          return (
            <div>
              <BreadCrumbs />
            </div>
          );
        }

### 总结
在react中编写组件时存在许多缺陷。不可能100%地理解整个机制，避免每一个小或者大的错误。但是在学习框架或编程语言时犯错误也是很重要的，可能没有人能100%避免这些错误。