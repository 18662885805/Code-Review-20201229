Code Review

## 1.**国元项目**

#### 1.1 测试&生产环境判断

```
if (window.location.origin == 'http://172.16.40.164:8080') {

  console.log('development环境')

  var base = {

    sq: '/api',

    bd: '/cailian',

    sd: '/tgc',

    bs: 'http://test.tgc.gyzq.com.cn'

  }

} else {

  console.log('production环境')

  var base = {

    sq: '',

    bd: '',

    sd: '',

    bs: 'https://tgc.gyzq.com.cn'

  }

} 

```



#### 1.2 APP版本判断

```
function checkNewVersion(){

  var ua = navigator.userAgent.toLocaleLowerCase();

  var ua_list = ua.split(' ');

  var app_version = 0;

  for (var i = 0; i < ua_list.length; i++) {

    if((ua_list[i]).match(/gydj/)){

      var version_list = ua_list[i].split('-');

      app_version = version_list&&version_list.length ? (version_list[1] ? version_list[1] : 0) : 0;

      break

    }

  }

  console.log(app_version)

  //判断版本号是否是新的

  if(cpr_version('6.10.5',app_version)){

    return true

  }else{

    return false

  }

}

```



##### 

## 2.Newsfeed

#### 2.1 Echarts图x坐标轴文本样式、格式化

```
xAxis: {

                type: 'category',

                boundaryGap: false,

                data: date,

                axisLabel: {//坐标轴刻度标签的相关设置。

                    textStyle: {

                        fontSize: 8

                    },

                    //格式设置

                    formatter: function(value, index ){

                        return value

                    },

                },

        },

```

```
 function timeFormatter (params) {

  var newParamsName = '';// 最终拼接成的字符串

  var paramsNameNumber = params.length;// 实际标签的个数

  var provideNumber = 10;// 每行能显示的字的个数

  var rowNumber = Math.ceil(paramsNameNumber / provideNumber);// 换行的话，需要显示几行，向上取整

  if (paramsNameNumber > provideNumber) {

      /* 循环每一行,p表示行 */

      for (var p = 0; p < rowNumber; p++) {

          var tempStr = '';// 表示每一次截取的字符串

          var start = p * provideNumber;// 开始截取的位置

          var end = start + provideNumber;// 结束截取的位置

          // 此处特殊处理最后一行的索引值

          if (p == rowNumber - 1) {

          // 最后一次不换行

              tempStr = params.substring(start, paramsNameNumber);

          } else {

          // 每一次拼接字符串并换行

              tempStr = params.substring(start, end) + '\n';

          }

          newParamsName += tempStr;// 最终拼成的字符串

      }

  } else {

  // 将旧标签的值赋给新标签

  newParamsName = params;

  }

  //将最终的字符串返回

  return newParamsName

}

```



#### 2.2 Antd V4 表单

v4 的 Form 不再需要通过 `Form.create()` 创建上下文。Form 组件现在自带数据域，因而 `getFieldDecorator` 也不再需要，直接写入 Form.Item 即可；onSubmit替换为onFinish。

*Antd V3 表单格式*

```
<Form onSubmit={this.handleSubmit}>
     <FormItem
        label={"资料名称" }
        {...formItemLayout}
      >
        {getFieldDecorator('name', {
          rules: [
                    {
                        required: true,
                        message: "请输入密码"}，
                    {
                        pattern: /^(?=.[A-Za-z])(?=.\d)[\x20-\x7e]{8,16}$/，
                        message: "密码要包含字母、数字，8-16位" 
                    }
                ],
        })(<Input />)}
      </FormItem>
      <FormItem
        label={"描述" }
        {...formItemLayout}
      >
        {getFieldDecorator('desc')(<Input />)}
      </FormItem>
      <FormItem {...submitFormLayout}>
        <div >
         <Button type="primary" htmlType="submit" >
             保存
          </Button>
        </div>
      </FormItem>
    </Form>
    
 export default Form.create()(AddForm)

```



*Antd V4 表单样式*

```
 <Form onFinish={this.handleSubmit}>
  <FormItem
    label={'新密码'}
    {...formItemLayout}
    name='new_pwd'
    rules={[
      {
        required: true,
        message: '请输入新密码',
      },
      {
        message:'密码由8-16位字符(数字、字母)组成,区分大小写',
        pattern: /^(?=.*[A-Za-z])(?=.*\d)[\x20-\x7e]{8,16}$/
      }
    ]}
  >
  </FormItem>

  <FormItem {...submitFormLayout}>
    <div>
      <Button type="primary" htmlType="submit">保存</Button>
    </div>
  </FormItem>
</Form>
```



#### 

#### 2.3 react hooks

##### useState 和 useReducer 

useState 的功能就是 useReducer 的子集。实际上，在 React 内部，useState 就是用 useReducer 实现的，useState 返回的函数内部封装了一个 dispatch。

- 更容易管理大量状态

- 更容易被其他开发者理解

- 更容易被测试

  如果用的是 useReducer，所有的跟 state 相关的业务逻辑代码都可以放到一个单独的函数里，跟你的组件分开，非常好测试。把状态更新代码和渲染逻辑分开，使得可以把测试代码也分成这两部分。

  ```
  const initialState = {
      values: '',
  }
  
  const reducer = (state, action) => {
      switch (action.type) {
          case 'setValues':
              return {
                  ...state,
                  values: action.payload,
              }
      }
  }
  
  
  const SidebarEditAdd = props => {
  	useEffect(() => {
         dispatch({ type: 'setValues', payload: '123' })
      }, [])
      
      const { values } = state
      return (
      	<div>{values}</div>
      )
  }
  ```

  





## 3.craco脚手架

使用 CRA 脚手架创建的项目，如果想要修改编译配置，通常可能会选择 `npm run eject` 弹出配置后魔改。但是，eject 是不可逆操作，弹出配置后，你将无法跟随官方的脚步去升级项目的 react-script 版本。 

如果想要无 eject 重写 CRA 配置，目前成熟的是下面这几种方式

1. 通过 CRA 官方支持的 `--scripts-version` 参数，创建项目时使用自己重写过的 react-scripts 包
2. 使用 [react-app-rewired](https://github.com/timarney/react-app-rewired) + [customize-cra](https://github.com/arackaf/customize-cra) 组合覆盖配置
3. 使用 [craco](https://github.com/gsoft-inc/craco) 覆盖配置

 

 

