```java
package com.younger.springbootstart.controller;

import com.younger.springbootstart.model.MenuItem;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.util.*;

@RestController
public class HelloController {


    private MenuItem parent = null;

    @RequestMapping(value = "/menuList")
    public Object getMenuList(){
        List<MenuItem> sources = this.getList();
        List<MenuItem> dest = new ArrayList<>(3);
        while (sources.size() > 0){
            for(int index = 0; index < sources.size(); index++){
                MenuItem menuItem = sources.get(index);
                if("0".equalsIgnoreCase(menuItem.getParentId())){
                    dest.add(menuItem);
                    sources.remove(menuItem);
                }else{
                    parent = null;
                    String parentId = menuItem.getParentId();
                    boolean in = this.findParent(dest,parentId);
                    if(in && parent != null){
                        if(parent.getSubMenuList() == null){
                            parent.setSubMenuList(new ArrayList<>());
                        }
                        parent.getSubMenuList().add(menuItem);
                        sources.remove(menuItem);
                    }
                }
            }
        }
        return dest;
    }

    /**
     * 查找结点的父节点
     * @param dest
     * @param parentId
     * @return
     */
    private boolean findParent(List<MenuItem> dest, String parentId) {
        for (MenuItem parentItem : dest){
            if(parentId.equalsIgnoreCase(parentItem.getMenuId())){
                parent = parentItem;
                return true;
            }else{
                if(parentItem.getSubMenuList() != null
                        && parentItem.getSubMenuList().size()>0){
                    boolean in = this.findParent(parentItem.getSubMenuList(),parentId);
                    if(in){
                        return true;
                    }
                }
            }
        }
        return false;
    }

    /**
     * 获取菜单数据(模拟)
     * @return
     */
    private List<MenuItem> getList(){
        MenuItem menuItem1 = new MenuItem("01","0",null);
        MenuItem menuItem2 = new MenuItem("0101","01",null);
        MenuItem menuItem3 = new MenuItem("0102","01",null);
        MenuItem menuItem4 = new MenuItem("0103","01",null);
        MenuItem menuItem5 = new MenuItem("010101","0101",null);
        MenuItem menuItem6 = new MenuItem("010102","0101",null);
        MenuItem menuItem7 = new MenuItem("010103","0101",null);
        MenuItem menuItem14 = new MenuItem("010201","0102",null);
        MenuItem menuItem8 = new MenuItem("02","0",null);
        MenuItem menuItem9 = new MenuItem("0201","02",null);
        MenuItem menuItem10 = new MenuItem("0202","02",null);
        MenuItem menuItem11 = new MenuItem("020201","0202",null);
        MenuItem menuItem12 = new MenuItem("03","0",null);
        MenuItem menuItem13 = new MenuItem("0301","03",null);
        List<MenuItem> sources = new ArrayList<>(14);
        sources.add(menuItem1);
        sources.add(menuItem2);
        sources.add(menuItem3);
        sources.add(menuItem4);
        sources.add(menuItem5);
        sources.add(menuItem6);
        sources.add(menuItem7);
        sources.add(menuItem8);
        sources.add(menuItem9);
        sources.add(menuItem10);
        sources.add(menuItem11);
        sources.add(menuItem12);
        sources.add(menuItem13);
        sources.add(menuItem14);
        return sources;
    }
}

```

