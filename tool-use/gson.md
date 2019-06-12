```java
import com.google.gson.JsonArray;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileReader;
import java.util.ArrayList;
import java.util.List;


//需要jar包:gson
class ReadJSON {
    public static List<String> readJson(String path){
        //解析器
        JsonParser parser = new JsonParser();
        List<String>  names = new ArrayList<>();
        try {
            JsonObject object = (JsonObject) parser.parse(new FileReader(path));
            //取key对应value为jsonArray
            JsonArray array = object.get("pages").getAsJsonArray();
            //遍历array
            int size = array.size();
            for (int i = 0; i < size; i++) {
                JsonObject row = array.get(i).getAsJsonObject();
                names.add(row.get("part").getAsString());
            }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        return names;
    }
}

class ChangeName{
    public static void change(List<String> newNames,List<String> oldNames,String basePath){
        String eg = oldNames.get(0);
        String extension =  eg.substring(eg.lastIndexOf(".")+1);

        int len = newNames.size();
        for (int i = 0; i < len; i++) {
            String oldName = oldNames.get(i);
            String newName = newNames.get(i);
            File source = new File(basePath+ oldName);
            File dest = new File(basePath+newName+"."+extension);
            source.renameTo(dest);
        }
    }
}

class ChangeFileName{
    public static void changeFileName(String extension,int start,int end,String basePath,String json){

        String jsonFilePath = basePath + json;

        List<String> newNames = ReadJSON.readJson(jsonFilePath);

        List<String> oldNames = new ArrayList<>();
      
        for (int i = start;i<=end;i++){
            oldNames.add(i+"."+extension);
        }
        ChangeName.change(newNames,oldNames,basePath);
    }
}
public class Test {
    public static void main(String[] args) {
        ChangeFileName.changeFileName(extension,
                start,end,
                bashPath,
                json);
    }
}
```

