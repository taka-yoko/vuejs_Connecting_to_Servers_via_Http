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

requestとresponseの間に入って、何かを実行するのがinterceptors。
main.jsで以下を記述。
POSTメソッドをPUTメソッドに変更する。
こうすると、firebaseに送られたデータが上書きされる。
POSTのように新規追加ではなくなる。
nextを書かないと、そこで処理が終わってしまう。

```javascript
Vue.http.interceptors.push((request, next) => {
  console.log(request);
  if(request.method == 'POST') {
    request.method = 'PUT';
  }
  next();
});
```

## Intercepting Responses

responseデータに対してinterceptする。
responseデータはnextで受け取れる。今回はmethodをPUTにしたことで、
受け取るデータの形式が変わってしまったので、これを以下のように整形する。
(POSTのときと合わせる)

```javascript
Vue.http.interceptors.push((request, next) => {
  console.log(request);
  if(request.method == 'POST') {
    request.method = 'PUT';
  }
  next(response => {
    response.json = () => {
      return {messages: response.body}
    }
  });
});
```

## Where the "resource" in vue-resource Comes From

https://github.com/pagekit/vue-resource/blob/develop/docs/resource.md
resourceから、リクエストメソッドをマッピングしたメソッドを使うことができる。

dataプロパティでresourceを設定。

```javascript
resource: {}
```

globalに使用する場合はVue.resource。
Vue instance内で使用する場合はthis.$resource。

```javascript
created() {
    this.resource = this.$resource('data.json');
}
```

saveアクションは、POSTメソッドにマッピングされている。

```javascript
this.resource.save({}, this.user);
```

## Creating Custom Resources

カスタムアクションの追加。

カスタムアクションを設定し、this.$resourceの引数に追加する。

```javascript
created() {
    const customActions = {
        saveAlt: {method: 'POST', url: 'alternative.json'}
    };
    this.resource = this.$resource('data.json', {}, customActions);
}
```

submitメソッド内で、以下のように呼ぶと、firebaseのalternativeノードに
POSTしたデータが送られる。

```javascript
this.resource.saveAlt(this.user);
```

## Resources vs "Normal" Http Requests

## Understanding Template URLs

下記のようにハードコードされているdata.jsonのような所を
ダイナミックに変更するにはどうすればよいか？

```javascript
this.resource = this.$resource('data.json');
```

customActions内にgetDataアクションを定義。

```javascript
const customActions = {
                saveAlt: {method: 'POST', url: 'alternative.json'},
                getData: {method: 'GET'}
            };
```

this.$resourceの第一引数のURLを以下のようにcurly braceで宣言。

```javascript
this.resource = this.$resource('{node}.json', {}, customActions);
```

getDataメソッド（アクション）を呼ぶ際に、keyがnodeのオブジェクトを渡す。
オブジェクトのvalueが、上の{node}の部分に代入される。

```javascript
this.resource.getData({node: this.node})
```

view上で、以下のようにnodeデータを動的に変更できるようにすれば、
firebase上の好きなノードからデータを取得することができる。

```javascript
<input type='text' class='form-control' v-model="node">
```
