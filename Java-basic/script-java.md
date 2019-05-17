对于项目易变代码，可以用脚本来编写，这样就可以不用重启JVM的情况下，照样可以运行修改部分的程序。

```java
class ExeCueteScript {
    public static Object exe(String path, String functionName,
                             Map<String, Object> context,
                             Object... params) {
        //获取脚本引擎
        ScriptEngine scriptEngine = new ScriptEngineManager().getEngineByName("javascript");
        //创建上下文环境
        Bindings bindings = scriptEngine.createBindings();
        //绑定上下文参数
        bindings.putAll(context);
        //设置上下文
        scriptEngine.setBindings(bindings, ScriptContext.ENGINE_SCOPE);

        try {
            //读取js代码
            scriptEngine.eval(new FileReader(path));
            //是否可调用方法
            if (scriptEngine instanceof Invocable) {
                Invocable invocable = (Invocable) scriptEngine;
                //执行js函数
                Object o = invocable.invokeFunction(functionName, params);
                return o;
            }
        } catch (ScriptException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

