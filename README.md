# vue-devops

## Project setup
```
yarn install
```

### Compiles and hot-reloads for development
```
yarn serve
```

### Compiles and minifies for production
```
yarn build
```

### Run your unit tests
```
yarn test:unit
```

### Lints and fixes files
```
yarn lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
# 산출물
yarn 깔기 
~~~
>> yarn global add @vue/cli
>> vue -V
@vue/cli 4.5.13
~~~
yarn 깔때 nodejs 때문에 권한 문제가 생기면
~~~
sudo npm install --global yarn  
~~~
이 명령어로 설치한다.
~~~ 
git config --global uesr.name  "김주연"   
git config --global uesr.email krju13@gmail.com

vue create vue-devops 
~~~
선택할 항목 : 
> Manual select features  
> Unit Testing 추가선택  
> 3.x  
> ESLint + Prettier  
> Lint on save  
> Jest  
> In dedicated config file  
> N  
> 

중간에 use npm or use yarn 이 나오면 yarn 을 선택한다.
~~~
yarn serve 
~~~
로 실행을 확인하고 서버를 종료시킨다.  
깃헙의 레포지토리를 만들고 remote 시킨다.

github pages에 배포하기 위한 라이브러리를 추가한다.
~~~
yarn add gh-pages -D
~~~
package.json 에 배포를 위해 필요한 정보를 추가한다.
 ~~~
{
  "name": "vue-devops",
  "version": "0.1.0",
  "private": true,
  "homepage": "https://krju13.github.io/vue-devops",
  "scripts": {
    "serve": "vue-cli-service serve",
    "build": "vue-cli-service build",
    "predeploy":"vue-cli-service build",
    "deploy":"gh-pages -d dist",
    "clean":"gh-pages-clean",
    "test:unit": "vue-cli-service test:unit",
    "lint": "vue-cli-service lint"
  },
  "dependencies": {
    "core-js": "^3.6.5",
    "vue": "^3.0.0"
  },
  "devDependencies": {
    "@vue/cli-plugin-babel": "~4.5.0",
    "@vue/cli-plugin-eslint": "~4.5.0",
    "@vue/cli-plugin-unit-jest": "~4.5.0",
    "@vue/cli-service": "~4.5.0",
    "@vue/compiler-sfc": "^3.0.0",
    "@vue/eslint-config-prettier": "^6.0.0",
    "@vue/test-utils": "^2.0.0-0",
    "babel-eslint": "^10.1.0",
    "eslint": "^6.7.2",
    "eslint-plugin-prettier": "^3.3.1",
    "eslint-plugin-vue": "^7.0.0",
    "gh-pages": "^3.2.3",
    "prettier": "^2.2.1",
    "typescript": "~3.9.3",
    "vue-jest": "^5.0.0-0"
  }
}
~~~
package.json과 같은 위치에 vue.config.js파일을 생성하고 밑에 내용을 넣는다.
~~~
module.exports={
  publicPath:"/vue-devops/",
  outputDir:"dist"
};
~~~
정적파일을 원격 저장소의 gh-pages 브랜치 생성하고 푸시하기
~~~
yarn deploy
~~~

로그인 실패로 재시도 할때 이미 gh-pages가 있다는 오류가 나오면
~~~
yarn clean
~~~
후에 다시 시도합니다

배포주소에 접속해서 확인해봅니다
이때 시간이 많이 걸렸는데
배포자 컴에서는 안보이고 다른 사람 컴에서 먼저 동작해서 나중에 결과를 볼수 있었습니다.

# github Action workflow 배포 자동화
레포의 action 탭에서 
~~~
set up this workflow
~~~
버튼을 누르고
deploy.yml파일을 만듭니다
~~~
# This is a basic workflow to help you get started with Actions

name: Deployment

# Controls when the workflow will run
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  deploy:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout source code
        uses: actions/checkout@master

      - name: Set up Node.js
        uses: actions/setup-node@master
        with:
          node-version: 14.x
      - name: Install dependencies
        run: yarn install

      - name: Test unit
        run: yarn test:unit

      - name: Build page
        run: yarn build
        env:
          NODE_ENV: production
      - name: Deploy to gh-pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
~~~
start commit을 하면 기본 flow가 돌아가고 yml파일을 위에꺼로 바꿔줍니다
웹 페이지 내용을 바꾸고 push하면 자동으로 확인하는 flow를 볼수 있습니다.
