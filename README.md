# CSS Module

> CSS를 불러와서 사용할 때 클래스 이름을 고유한 값, 즉 [파일 이름]\_[클래스 이름]\_\_[해시값] 형태로 자동으로 만들어서 컴포넌트 스타일 클래스 이름이 중첩되는 현상을 방지해 주는 기술

- CSS Module을 사용하면 클래스 이름을 지을 때 고유성에 대해 고민하지 않아도 됨.
- 기본적으로 css파일을 만들 때 [컴포넌트 이름].module.css 라는 네이밍 컨벤션을 갖는다.
- 사용하기 위해선 webpack에서 사용하는 css-loader 가 필요함.
- CRA로 만든 프로젝트에서는 자동으로 세팅이 되어있어서 자유롭게 가져다 쓸 수 있다.

# Naming Rule

- 컴포넌트의 이름은 다른 컴포넌트랑 중복되지 않게 한다.
- 컴포넌트의 최상단 CSS 클래스는 컴포넌트의 이름과 일치시킨다. (예: .Button
- 컴포넌트 내부에서 보여지는 CSS 클래스는 CSS Selector 를 잘 활용한다. (예: .MyForm .my-input)

# Usage

- 먼저 `[컴포넌트 이름].module.css` 라는 이름의 css 를 만들어 준다.
- 컴포넌트 상단에 해당 css를 import (styles와 같은 변수에 담을 수 있음)
- styles 객체 안에 있는 클래스 값들을 styles.Box와 같은 이름으로 가져오게 되면 해싱 된 값으로 보여지게 됨
- 만약에 클래스 이름에 -가 들어가 있으면 `styles['my-class']` 와 같이 가져옴
  여러 개가 있다면 `${styles.one}` `${styles.two}`와 같이 조회해야함. 이런 번거로움을 개선한 것이 **classnames** 라이브러리의 bind기능

> ### classNames 라이브러리
>
> - classNames의 bind 기능 이용해 조건부 렌더링이나 여러 개의 클래스를 사용시 좀 더 편하게 사용할 수 있게 함.
>
> #### Usage
>
> - 사용법
> - `npm install classnames`
> - component 파일 상단에 import classNames from 'classnames/bind';
> - const cx = classNames.bind(styles);
> - className={cx("클래스 이름")} 과 같이 해당 스타일 가져옴
>
> #### 여러 클래스 사용하기
>
> `cx('one', 'two')`
>
> #### 조건부 스타일링
>
> ```javascript
> cx("my-component", {
>   condition: true,
> });
> cx("my-component", ["another", "classnames"]);
> ```
>
> #### 사용예
>
> ```javascript
> // javascript
> import styles from "../styles/Box.module.css";
> import { MdCheckBox, MdCheckBoxOutlineBlank } from "react-icons/md";
> import classNames from "classnames/bind";
>
> const cx = classNames.bind(styles);
>
> const CheckBox = ({ children, checked, ...rest }) => {
>   return (
>     <div className={cx("checkbox")}>
>       <label>
>         <input type="checkbox" checked={checked} {...rest} />
>         <div className={cx("icon")}>
>           {checked ? <MdCheckBox /> : <MdCheckBoxOutlineBlank />}
>         </div>
>       </label>
>       <p className={cx("make-this-local")}>local</p>
>       <p className="aqua">global</p>
>       {/*글로벌 스타일을 적용할 수 있음*/}
>       <span>{children}</span>
>     </div>
>   );
> };
>
> export default CheckBox;
> ```
>
> ```scss
> // css
> :global .aqua {
>   color: aqua;
> }
> :local .make-this-local {
>   color: palevioletred;
> }
> ```

# Options

- `: global` → 글로벌하게 적용되는 CSS임을 알 수 있게 해줌
- `: local` → 그 해당 컴포넌트에서만 사용될 수 있는 클래스임을 정의

# Advantages

- 레거시 프로젝트에 리액트를 도입할 때 기존 프로젝트에 있는 css클래스와 이름이 중복되어도 새로운 이름이 입혀지기 때문에 스타일이 꼬이지 않게 해준다.
- CSS클래스를 중복되지 않게 작성하기 위해 css클래스 네이밍 규칙을 짓기위해 애쓰지 않아도 된다.

# Cusmom CehckBox 만들기

```javascript
// components/CheckBox.js
import React from "react";

function CheckBox({ checked, children, ...rest }) {
  return (
    <div>
      <label>
        <input type="checkbox" checked={checked} {...rest} />
        <div>{checked ? "체크됨" : "체크 안됨"}</div>
      </label>
      <span>{children}</span>
    </div>
  );
}

export default CheckBox;
```

- `...rest` 를 사용한 이유는, CheckBox 컴포넌트에게 전달하게 될 `name`, `onChange` 같은 값을 그대로 input 에게 넣어주기 위함

<br>

- App 컴포넌트에서 렌더링

```javascript
// App.js
import React, { useState } from "react";

import CheckBox from "./components/CheckBox";

function App() {
  const [check, setCheck] = useState(false);
  const onChange = (e) => {
    setCheck(e.target.checked);
  };
  return (
    <div>
      <CheckBox onChange={onChange} checked={check}>
        다음 약관에 모두 동의
      </CheckBox>
      <p>
        <b>check: </b>
        {check ? "true" : "false"}
      </p>
    </div>
  );
}

export default App;
```
