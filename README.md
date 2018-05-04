## angular-cli 构建工具 与 angular 版本的兼容情况

> 通过 npm 去 运行安装在本地的 angular-cli 而不是全局的 cli 来解决版本兼容的问题

1. 明确构建工具 兼容的angular的版本；

npm uninstall -g @angular/cli@wished.version.here

* angular 2.1 所兼容的 cli 版本

npm i angular-cli@1.0.0-beta.18 -g

* 将其安装到项目本地，而不是全局，利用npm 命令 去运行；


```js
formErrors = {
    name: '',
    username: '',
    addresses: [
      { city: '', country: '' }
    ]
  };
  validationMessages = {
    name: {
      required: 'Name is required.',
      minlength: 'Name must be 3 characters.',
      maxlength: 'Name can\'t be longer than 6 characters.'
    },
    username: {
      required: 'Username is required.',
      minlength: 'Username must be 3 characters.'
    },
    addresses: {
      city: {
        required: 'City is required.',
        minlength: 'City must be 3 characters.'
      },
      country: {
        required: 'Country is required.'
      }
    }
  };

// now we've moved a lot of information 

```