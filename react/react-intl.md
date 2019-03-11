## react-intl

### 国际化多语言方案

大体的实现原理和react-redux的实现原理类似，最外层包一个Provider，利用getChildContext，将intlConfigPropTypes存起来，在FormattedMessage、FormattedNumber等组件或者调用injectIntl生成的高阶组件中使用，来完成国际化的。 

使用过程也非常简单，和react-redux的使用方法非常类似。

#### 国际化资源文件内容

目前我们管理资源文件的方式是在 src/locales 文件夹下：

```
.
├── en-US.js
├── en-US.messages.js
├── zh-CN.js
└── zh-CN.messages.js
```

**注意： 文件命名、变量命名，是有规律的**

*.messages.js 是我们的资源文件(这里我们采用了 js 格式，你也可以使用 json 等等)，返回的是一个对象，key 为我们翻译用的 id，value 为具体语言的翻译，内容是：

```javascript
const login={
  'username':'Username',
  'new_user':'new user?'
}

const reset={
  'reset.title':'Reset Your Password',
}

const en_US = {
  ...login,
  ...reset
};
export default en_US;
```

en-US.js 文件封装了 messages、locale 等国际化上下文组件需要的内容：

```javascript
import datePickerLocale from 'antd/lib/date-picker/locale/en_US';
import appLocaleData from 'react-intl/locale-data/en';
import messages from './en-US.messages.js';
import antdEn from 'antd/lib/locale-provider/en_US';

window.appLocale = {
  // 合并所有 messages，加入 antd 组件的 messages
  messages: Object.assign({}, messages, {
    datePickerLocale,
  }),

  // locale语言标记
  locale: 'en-US',

  // react-intl locale-data
  data: appLocaleData,

  antd:antdEn,

  // 自定义 formates
  formats: {
    date: {
      normal: {
        hour12: false,
        year: 'numeric',
        month: '2-digit',
        day: '2-digit',
        hour: '2-digit',
        minute: '2-digit',
      },
    },
  },
};

export default window.appLocale;
```

有了这些资源文件以及相关的封装之后，我们就可以在 `InltProvider` 中使用了。

------

在 React 專案中可以透過 Yahoo Open Source 的 React-intl 專案來幫忙，第一種是用 Component 來定義字串，像是這樣：

```
<FormattedMessage
  id="CopyButton.text"
  defaultMessage="複製"
/>
```

第二種是用在某些無法塞 Component 的情況，例如 Input 的 *placeholder* Attribute，這時候可以使用 HOC 把翻譯的 Function 以 props 傳遞進去：

```
injectIntl(Component);
this.props.intl.formatMessage(...);
```

#### Babel Plugin

在專案內順利地使用 React-intl 定義完後，接下來是要想如何有效率的把字串抽出來，這時候是透過 [babel-plugin-react-intl](https://github.com/yahoo/babel-plugin-react-intl) 在 Babel 階段來處理，因此跑過整份專案後就可以產生相對應一份份的 Json 格式檔案：

```
// components/CopyButton.js
[
  {
    "id": "CopyButton.text",
    "defaultMessage": "複製"
  }
]
```

接下來就可以對這些抽取出來的字串進行操作、翻譯、合併、轉換。

**IntlProvider中的属性变更并不会触发FormattedMessage重新渲染，刚开始想要forceUpdate强制更新组件，后来上网查了一个解决方案，在组件中加入key，就能解决这个问题**