# vue-cli

> A Vue.js project

## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build
```

For detailed explanation on how things work, consult the [docs for vue-loader](http://vuejs.github.io/vue-loader).


## What's this?

UdemyのVue JS - The Complete GuideのSection15 Connecting to Servers via Http - Using vue-resourceをまとめたもの。 https://www.udemy.com/vuejs-2-the-complete-guide

## Module Introduction

## Accessing Http via vue-resource - Setup

vue-resourceをnpm installして、main.jsで読み込む

```javascript
import VueResource from 'vue-resource';
Vue.use(VueResource);
```

## Creating an Application and Setting Up a Server (Firebase)

Firebase上にprojectを作成

権限設定。これは本番のアプリではないのでtrueにしておく。

```javascript
{
  "rules": {
    ".read": true,
    ".write": true
  }
}
```

App.vueにinputフィールド、およびv-model、@clickイベントを仕込んでおく。

```html
<div class='form-group'>
    <label>Username</label>
    <input type='text' class='form-control' v-model="user.name">
</div>
<div class='form-group'>
    <label>Mail</label>
    <input type='text' class='form-control' v-model="user.email">
</div>
<button class='btn btn-primary'@click="submit">Submit!</button>
```
```javascript
<script>
    export default {
        data() {
            return {
                user: {
                    name: "",
                    email: ""
                }
            };
        },
        methods: {
            submit() {
                console.log(this.user);
            }
        }
    }
</script>
```

## POSTing Data to a Server (Sending a POST Request)

firebaseにデータをpostする。
$httpメソッドはvue-resourceをmain.jsでVue.useしているので利用できる。

```javascript
submit() {
    this.$http.post('https://xxx.firebaseio.com/data.json', this.user)
        .then(response => {
            console.log(response);
        }, error => {
            console.log(error);
        });
}
```

## GETting and Transforming Data (Sending a GET Request)

## Configuring vue-resource Globally

POSTリクエストとGETリクエストで指定しているURLは全く同じ。
DRY原則に則って、この共通URLをmain.jsで以下のように設定する。

```javascript
Vue.http.options.root = 'https://vuejs-http-6e4fe.firebaseio.com/data.json';
```

これで、postやgetメソッド宣言時、URL指定を空にすると、上記のURLがデフォルトで指定されることになる。

```javascript
this.$http.get('')
```

```javascript
this.$http.post('', this.user)
```

## Intercepting Requests

## Intercepting Responses

## Where the "resource" in vue-resource Comes From

## Creating Custom Resources

## Resources vs "Normal" Http Requests

## Understanding Template URLs

