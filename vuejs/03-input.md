```html
<!DOCTYPE html>
<html>

<head>
    <meta charset="utf-8">
    <script src="vue.js"></script>
    <title>vue.js-2</title>
</head>

<body>
    <div id="app">
        <h5>表单输入绑定</h5>
        <!--v-model,绑定到data域的属性中-->
        <!--单行-->
        singLine:<input type="text" v-model="singLine"><br>
        <p>{{singLine}}</p>

        <!--多行-->
        <textarea v-model="multiLine"></textarea>
        <p>{{multiLine}}</p>

        <!--复选框,公用一个v-model-->
        <div>
            <label for="jack">jack</label>
            <input type="checkbox" v-model="checkedNames" value="Jack" id="jack">
            <label for="john">John</label>
            <input type="checkbox" v-model="checkedNames" value="John" id="john">
            <label for="bob">Bob</label>
            <input type="checkbox" v-model="checkedNames" value="Bob" id="bob">
            checked：<span>{{checkedNames.length == 0 ?'':checkedNames}}</span><!--嵌入表达式-->
        </div>

        <!--单选框-->
        <div>
            <label for="one">One</label>
            <input type="radio" v-model="picked" id="one" value="One">
            <label for="two">Two</label>
            <input type="radio" v-model="picked" id="two" value="Two">
            <label for="three">Three</label>
            <input type="radio" v-model="picked" id="three" value="Three">
            picked:{{picked}}
        </div>

        <!--下拉框-->
        <div>
            <select v-model="selected">
                <option value="" disabled>请选择</option>
                <option>A</option>
                <option>B</option>
                <option>C</option>
            </select>
            selected:{{selected}}
            <!--动态渲染-->
            <div>
                <select v-model="selected">
                    <option v-for="option in optionDatas">{{option.text}}</option>
                </select>
            </div>
        </div>

        <div>
            number:<input v-model.number="num" type="number">
            <p>{{num}}</p>
        </div>
        <div>
            string:<input v-model.trim="string" type="text">
            <p>{{string}}</p>
        </div>
    </div>

    <script>
        var app = new Vue({
            el: '#app',
            data: {
                singLine: "11111",//为输入框初始化
                multiLine:"",
                checkedNames:[],
                picked:'One',//默认选中
                selected:'A',
                optionDatas:[
                    {text:'A',value:'A'},
                    {text:'B',value:'B'},
                    {text:'C',value:'C'}
                ],
                num:'',
                string:''
            }
        });
    </script>
</body>

</html>
```

