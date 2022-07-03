# [webpack] webpack 기초
> SKILL : `webpack`, `html`, `react`, `css`, `scss`
> <br>
> webpack Docs : [webpack Docs](https://webpack.js.org/concepts/)
> <br>
> Blog: [jeff0720.log](https://velog.io/@jeff0720/React-개발-환경을-구축하면서-배우는-Webpack-기초)

## 목차
1. [프로젝트 생성](#프로젝트-생성)
2. [웹팩으로 HTML 빌드하기](#웹팩으로-html-빌드하기)
3. [웹팩으로 React 빌드하기](#웹팹으로-리액트-빌드하기)
4. [웹팩으로 CSS 사용하기](#웹팩에서-css-사용하기)
5. [웹팩으로 SCSS 사용하기](#웹팩으로-scss-사용하기)
6. [웹팩 개발 서버 적용하기](#웹팩-개발-서버-적용하기)
7. [빌드 할 때 웹팩 플러그인 정리](#빌드-할-때-웹팩-플러그인-정리)
8. [웹팩 빌드 모드 나누기](#웹팩-빌드-모드-나누기)

## 프로젝트 생성
### package.json 생성
```bash
npm init
```

### webpack 및 라이브러리 설치
```bash
npm install -save-dev @babel/core @babel/preset-env @babel/preset-react babel-loader clean-webpack-plugin css-loader html-loader html-webpack-plugin mini-css-extract-plugin node-sass react react-dom sass-loader style-loader webpack webpack-cli webpack-dev-server
```

### .gitignore 생성
```bash
/* .gitignore */
# macOS
.DS_Store

# package-lock.json
package-lock.json

# Dependency directories
node_modules

# Compile
dist
```

### src 폴더에 test.js 파일 생성
```javascript
console.log("webpack test");
```

### webpack.config.js 생성
```javascript
const path = require("path");

module.exports = {
    entry: "./src/test.js", // 웹팩이 빌드할 파일
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist") // 빌드 되어진 폴더
    },
    mode: "none"
};
```

### package.json 수정
scripts > dev 수정
```json
{
  "name": "webpack-react",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "dev": "webpack"
  },
  "author": "gangpro",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.18.6",
    "@babel/preset-env": "^7.18.6",
    "@babel/preset-react": "^7.18.6",
    "babel-loader": "^8.2.5",
    "clean-webpack-plugin": "^4.0.0",
    "css-loader": "^6.7.1",
    "html-loader": "^3.1.2",
    "html-webpack-plugin": "^5.5.0",
    "mini-css-extract-plugin": "^2.6.1",
    "node-sass": "^7.0.1",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "sass-loader": "^13.0.2",
    "style-loader": "^3.3.1",
    "webpack": "^5.73.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.9.3"
  }
}
```

### 빌드 해보기
``./dist/app.js`` 파일이 생성 됨
```bash
npm run dev
```

<br>
<br>
<br>

## webpack HTML
### 웹팩으로 HTML 빌드하기
``로더(loader) 기능``은 자바스크립트 파일뿐만 아니라 파일들도 모듈로 관리 할 수 있다. 
```javascript
module : {
	rules: {
		test: '가지고올 파일 정규식',
		use: [
			{
				loader: '사용할 로더 이름',
				options: { 사용할 로더 옵션 }
			}
		]
	}
}
```

### public/index.html 파일 생성
```html
<!DOCTYPE html>
<html lang="kr">
  <head>
    <meta charset="utf-8" />
    <title>WEBPACK REACT</title>
  </head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

### webpack.config.js 수정
html-webpack-plugin은 웹팩이 html 파일을 읽어서 html 파일을 빌드할 수 있게 해준다. 
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");

module.exports = {
    entry: "./src/test.js",
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    mode: "none",
    module: {
        rules: [
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html', // public/index.html 파일을 읽는다.
            filename: 'index.html' // output으로 출력할 파일은 index.html 이다.
        })
    ]
};
```

### 빌드 해보기 
``./dist/index.html`` 파일이 생성된다.
```bash
npm run dev
```

### 웹 브라우저 콘솔 확인하기
http://127.0.0.1:5500/dist/index.html
콘솔 창에서 ``webpack test``를 확인 할 수 있다.

<br>
<br>
<br>

## webpack React
### 웹팹으로 리액트 빌드하기
``./src/index.js`` 파일 생성
```javascript
import React from 'react';
import { ReactDOM } from 'react-dom';
import Root from './Root';

ReactDOM.render(<Root />, document.getElementById('root'));
```

``./src/Root.js`` 파일 생성
```javascript
import React from "react";

const Root = () => {
    return (
        <h3>Hello, React</h3>
    );
};

export default Root;
```

### .babelrc 바벨 파일 생성
바벨(babel)은 ES6에서 ES5로 자바스크립트를 변환해주는 역할을 한다.
```babelrc
{
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
}
```

### webpack.config.js 수정
babel-loader 추가
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");

module.exports = {
    name: "webpack-react",
    mode: 'development',
    devtool: 'eval',
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        })
    ]
};
```

## webpack css
### 웹팩에서 CSS 사용하기
``./src/style.css`` 파일 생성
```css
.title {
	color: #2196f3;
    font-size: 52px;
    text-align: center;
}
```
``./src/Root.js`` 파일 수정
```javascript
import React from 'react';
import './style.css';

const Root = () => {
    return (
        <div>
            <h3 className='title'>Hello, Root</h3>
        </div>
    );
};

export default Root;
```

### error
```bash
ERROR in ./src/style.css 1:0
Module parse failed: Unexpected token (1:0)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> .title {
|       color: #2196f3;
|     font-size: 52px;
 @ ./src/Root.js 2:0-21
 @ ./src/index.js 3:0-26 4:50-54

webpack-react (webpack 5.73.0) compiled with 1 error in 3009 ms
```

### css-loader 적용
``webpack.config.js``수정
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");

module.exports = {
    name: "webpack-react",
    mode: 'development',
    devtool: 'eval',
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: ['css-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        })
    ]
};
```

### error : CSS가 적용이 안됨
웹팩에서 CSS 파일을 읽은 후 어딘가에 저장을 해야하기 때문에 ``webpack.config.js``에 CSS를 추출해서 파일로 저장하는 플로그인 적용이
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    name: "webpack-react",
    mode: 'development',
    devtool: 'eval',
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader'] // 오른쪽에서 왼쪽 순서로 실행
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        })
    ]
};
```

## webpack SCSS
### 웹팩으로 SCSS 사용하기
``./src/style.scss`` 파일 생성
```scss
$fontColor: #2dba20;
$fontSize: 52px;


.title {
    color: $fontColor;
    font-size: $fontSize;
    text-align: center;
}
```

``./src/Root.js`` 파일 수정
```javascript
import React from 'react';
// import './style.css';
import './style.scss';

const Root = () => {
    return (
        <div>
            <h3 className='title'>Hello, Root</h3>
        </div>
    );
};

export default Root;
```

### error
SCSS 파일을 읽을 수 없어서 에러 발생
```bash
ERROR in ./src/style.scss 1:13
Module parse failed: Unexpected character '2' (1:13)
You may need an appropriate loader to handle this file type, currently no loaders are configured to process this file. See https://webpack.js.org/concepts#loaders
> $fontColor: #2dba20;
| $fontSize: 52px;
| 
 @ ./src/Root.js 3:0-22
 @ ./src/index.js 3:0-26 4:50-54

webpack-react (webpack 5.73.0) compiled with 1 error in 3382 ms
```


### scss-loader 적용
``webpack.config.js`` 수정 <br>
sass-loader로 scss 파일을 읽고 css로 변환한 후 css-loader로 css을 읽는다. 그 후 MiniCssExtractPlugin으로 ``style.css`` 파일 추출
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    name: "webpack-react",
    mode: 'development',
    devtool: 'eval',
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.scss$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        })
    ]
};
```

## webpack DEV server
### 웹팩 개발 서버 적용하기
``webpack.config.js`` 파일 수정
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
    name: "webpack-react",
    mode: "development",
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    devServer: {
        static: {
            directory: path.join(__dirname, 'dist'),
        },
        compress: true,
        port: 9000,
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.scss$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        })
    ]
};
```

### 접속 확인하기
``http://localhost:9000/`` 접속 확인하기

## webpack clean plugin
### 빌드 할 때 웹팩 플러그인 정리
``webpack.config.js`` 파일 수정
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    name: "webpack-react",
    mode: "development",
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "/dist")
    },
    devServer: {
        static: {
            directory: path.join(__dirname, 'dist'),
        },
        compress: true,
        port: 9000,
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.scss$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        }),
        new CleanWebpackPlugin()
    ]
};
```

## webpack development & production
### 웹팩 빌드 모드 나누기
``./confing/webpack.config.dev.js`` 파일 생성
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    name: "webpack-react",
    mode: "development",
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.js",
        path: path.resolve(__dirname + "../dist")
    },
    devServer: {
        static: {
            directory: path.join(__dirname, '../dist'),
        },
        compress: true,
        port: 9000,
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.scss$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        }),
        new CleanWebpackPlugin()
    ]
};
```

``./confing/webpack.config.prod.js`` 파일 생성
```javascript
const path = require("path");
const HtmlWebPackPlugin = require("html-webpack-plugin");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const { CleanWebpackPlugin } = require('clean-webpack-plugin');

module.exports = {
    name: "webpack-react",
    mode: "production",
    resolve: {
        extensions: ['.js', '.jsx'],
    },
    entry: {
        app: ['./src/index'],
    },
    output: {
        filename: "app.[contenthash].js",
        path: path.resolve(__dirname + "../dist")
    },
    module: {
        rules: [
            {
                test: /\.(js|jsx)$/,
                loader: 'babel-loader',
                options: {
                    presets: [['@babel/preset-env'], '@babel/preset-react'],
                },
            },
            {
                test: /\.html$/,
                use: [
                    {
                        loader: "html-loader",
                        options: { minimize: true }
                    }
                ]
            },
            {
                test: /\.css$/,
                use: [MiniCssExtractPlugin.loader, 'css-loader']
            },
            {
                test: /\.scss$/,
                use: [MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]
            }
        ]
    },
    plugins: [
        new HtmlWebPackPlugin({
            template: './public/index.html',
            filename: 'index.html'
        }),
        new MiniCssExtractPlugin({
            filename: 'style.css'
        }),
        new CleanWebpackPlugin()
    ]
};
```

``./package.json`` 수정
```json
{
  "name": "webpack-react",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "start": "webpack-dev-server --config ./config/webpack.config.dev.js",
    "build": "webpack --config ./config/webpack.config.prod.js"
  },
  "author": "gangpro",
  "license": "ISC",
  "devDependencies": {
    "@babel/core": "^7.18.6",
    "@babel/preset-env": "^7.18.6",
    "@babel/preset-react": "^7.18.6",
    "babel-loader": "^8.2.5",
    "clean-webpack-plugin": "^4.0.0",
    "css-loader": "^6.7.1",
    "html-loader": "^3.1.2",
    "html-webpack-plugin": "^5.5.0",
    "mini-css-extract-plugin": "^2.6.1",
    "node-sass": "^7.0.1",
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "sass-loader": "^13.0.2",
    "style-loader": "^3.3.1",
    "webpack": "^5.73.0",
    "webpack-cli": "^4.10.0",
    "webpack-dev-server": "^4.9.3"
  }
}

```























