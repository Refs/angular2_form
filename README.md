## angular-cli 构建工具 与 angular 版本的兼容情况

> 通过 npm 去 运行安装在本地的 angular-cli 而不是全局的 cli 来解决版本兼容的问题

1. 明确构建工具 兼容的angular的版本；

npm uninstall -g @angular/cli@wished.version.here

* angular 2.1 所兼容的 cli 版本

npm i angular-cli@1.0.0-beta.18 -g

* 将其安装到项目本地，而不是全局，利用npm 命令 去运行；

2. 快速查看 angular 与 angular-cli 的历史发布的版本

> 通过 npm 中进行查看，要比在github 中查看的更加直观，但明显github中更加详细

![](./images/angular-cli.png)

3. 通过对比 angular 与 @angular/cli 的版本发布时间，来思考两者之间的兼容性；

> The princple is simple, just see @angular/platform-server@2.1.0's release date , then go to angular-cli npm version page, to find the spec verison which is  related to above data; At least it can run the spec version angular;


4. through `npm install angular-cli@1.0.0-beta.18 --save-dev` comand install old spec version angular-cli to project local node_modules;

> note must be -dev model

5. custom package.json file to config npm commant  `"serve": "ng serve"`, and run the command in the terminal;

>  note you must add `run` keyword , when you run the custome npm script,eg. `npm run serve` 

6. 一个项目要牵扯到的环境

> until all above condition be fitted , we can run our app
* angular version
* @angular/cli version
* typescript version
* node.js version

7. 最终代码号跑不起来的话， 就去根据老代码的逻辑与最新的官方文档，去重构一下，这是最彻底的解决方式；


## 表单验证提示信息

* Grap an input field by name
* Clean that input field errors
* figoure out the type of error
* assign that type of error message to a variable which is going to be displayed to our template

1. original code 

```js
export class ReactiveFormComponent implements OnInit {
  ....

  validateForm() {
    // Clean that input field errors
    this.nameError = '';
    this.usernameError = '';

    // grap an input field by name
    let name = this.form.get('name');
    let username = this.form.get('username');

    // figoure out the type of error
    if (name.invalid && name.dirty) {
      if (name.errors['required']) {
        // assign that type of error message to a variable which is going to be displayed to our template
        this.nameError = 'Name is required';
      } else if (name.errors['minlength']) {
        this.nameError = 'Name must be at least 3 characters';
      } else if (name.errors['maxlength']) {
        this.nameError = 'Name can\'t be more than 6 characters';
      }
    }
    if (username.invalid && username.dirty) {
      if (username.errors['required']) {
        this.usernameError = 'userName is required';
      } else if (username.errors['minlength']) {
        this.usernameError = 'userName must be at least 3 characters';
      } 
    }
  }
}

```
2. refactoring above code

The cool thing about this is that we can actually grap validation messages from service or some external place that isn't in this class and tha's how we can grap different languages for this validation messages , so bear with me here we're going to go through  

```js
formErrors = {
    name: '',
    username: '',
    addresses: [
      { city: '', country: '' }
    ]
  };
// type of error in here
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

// 目的是给 formErrors 对象的每一个字节进行根据不同的情况，赋值不同的错误信息， 最后要绑定到template 的是 {{ this.formErrors.name }}
validateForm() {
  // loop over the formErrors field names
  for ( let field in this.formErrors ) {
    // Clean that input field errors
    this.formErrors[field] = '';

    // grap an input field by name
    let input = this.form.get(field);

    // figoure out the type of error
    if (input.invalid && input.dirty) {
      for (let error in input.errors) {
         // assign that type of error message to a variable which is going to be displayed to our template
        this.formErrors[field] = this.validatorMessages[field][error];
        // 上面的一行代码 不好去理解
        /**
         * 1. this.formErrors[field] 是我们存放错误信息的变量，也是最终我们与模板绑定在一起的变量 最终的形式是 this.formErrors.name
         * 2. this.validatorMessages[field][error] 是我们要赋值给错误信息变量的具体信息字符串；最终形式 'Name is required.'
         * 3. 看上面的validationMessages对象；this.validatorMessages[field] 就是this.validatorMessages.name 
         * 4. [error] 是 formGroup/this.form 通过 get 方法获取的 formControl/ input 的 erors属性数组中的一个元素；元素是一个键值对， keyname 刚好与 this.validatorMessages[field][error] 一致 即 this.validatorMessages.name.required 值就是 'Name is required.'
         * 5. 最终的结构就是 this.formErrors.name = ''Name is required.''
         * */ 

      }
    }

  }

}

```

```html
<div class="form-group">
        <label for="name">Name</label>
        <input type="text" class="form-control" name="name" required
            formControlName="name">

        <span class="help-block" *ngIf="formErrors.name">
            {{ formErrors.name }}
        </span>
    </div>

    <div class="form-group">
        <label for="username">Username</label>
        <input type="text" class="form-control" name="username" required
            formControlName="username">

        <span class="help-block" *ngIf="formErrors.username">
            {{ formErrors.username }}
        </span>
    </div>

```

## FormArray class

```ts
class FormArray extends AbstractControl {
  constructor(controls: AbstractControl[], validatorOrOpts?: ValidatorFn | ValidatorFn[] | AbstractControlOptions | null, asyncValidator?: AsyncValidatorFn | AsyncValidatorFn[] | null)
  controls: AbstractControl[]
  get length: number

  // to find a specific FormGroup 
  at(index: number): AbstractControl

  // to add different things to this FormArray
  push(control: AbstractControl): void
  insert(index: number, control: AbstractControl): void

  // remove certain 
  removeAt(index: number): void
  setControl(index: number, control: AbstractControl): void
  setValue(value: any[], options: {...}): void
  patchValue(value: any[], options: {...}): void
  reset(value: any = [], options: {...}): void
  getRawValue(): any[]
 
  // inherited from forms/AbstractControl
  constructor(validator: ValidatorFn | null, asyncValidator: AsyncValidatorFn | null)
  get value: any
  validator: ValidatorFn | null
  asyncValidator: AsyncValidatorFn | null
  get parent: FormGroup | FormArray
  get status: string
  get valid: boolean
  get invalid: boolean
  get pending: boolean
  get disabled: boolean
  get enabled: boolean
  get errors: ValidationErrors | null
  get pristine: boolean
  get dirty: boolean
  get touched: boolean
  get untouched: boolean
  get valueChanges: Observable<any>
  get statusChanges: Observable<any>
  get updateOn: FormHooks
  get root: AbstractControl
  setValidators(newValidator: ValidatorFn | ValidatorFn[] | null): void
  setAsyncValidators(newValidator: AsyncValidatorFn | AsyncValidatorFn[] | null): void
  clearValidators(): void
  clearAsyncValidators(): void
  markAsTouched(opts: {...}): void
  markAsUntouched(opts: {...}): void
  markAsDirty(opts: {...}): void
  markAsPristine(opts: {...}): void
  markAsPending(opts: {...}): void
  disable(opts: {...}): void
  enable(opts: {...}): void
  setParent(parent: FormGroup | FormArray): void
  abstract setValue(value: any, options?: Object): void
  abstract patchValue(value: any, options?: Object): void
  abstract reset(value?: any, options?: Object): void
  updateValueAndValidity(opts: {...}): void
  setErrors(errors: ValidationErrors | null, opts: {...}): void
  get(path: Array<string | number> | string): AbstractControl | null
  getError(errorCode: string, path?: string[]): any
  hasError(errorCode: string, path?: string[]): boolean
}

```



## password and comfirm password  validator







The cli no longer supports creating new projects with ng2 since ng4 is out. This does not mean that it stopped supporting it though you can still build and serve your applications just like before but ng new will always be on the latest major release.

@dannypule no, you can still install latest version, but have to manually fall back Angular module versions in package.json after ng new. Yet I don't like this forced.
