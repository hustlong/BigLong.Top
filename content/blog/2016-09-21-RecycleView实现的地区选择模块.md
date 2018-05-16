---
title: RecycleView实现的地区选择模块
date: 2016-09-21
tags:
    - "Android"
    - "RecycleView"
    - "自定义View"
categories:
    - "技术文章"
---


　　 最近在做的一个项目里，需要在用户个人资料设置页有选择地区功能，看了一些开源的实现方法，大多还停留在底部弹出Dialog的形式。

感觉并不友好，微信的地区选择界面是本人比较喜欢的展示形式，地区选择的功能应该算是一个基础功能吧，社交类、外卖类等等APP貌似都需要用户输入地址，选择地区等等，所以就想着自己做一个类似的，不足的是没有加入定位功能，以后还有很多要补充，现在先把这一块功能独立出来做个demo，和大家交流学习一下（文末有实用小 ~~公举~~ 工具推荐哦）。
<!--more-->
　　 先上图看一下效果：

![RegionSelector.gif](https://github.com/hustlong/RegionSelector/blob/master/files/regionSelector_demo.gif?raw=true)

　　讲一下思路：结构很简单，点击第一个activity的设置地区，开启第二个activity，同时把已经选择的地区传值，格式为**“省份　城市　地区”**（没有引号），然后地区设置完成后setResult就可以了。地区activity选择用RecycleView实现。

```java
AActivity <----------------onActivityResult()
|                                        |
startActivityForResult()                 |
|                                        |
BActivity---------->setResult()---------->
```

## 布局

　　和使用ListView差不多，不同的是RecycleView并没有item点击事件，因此这个需要自己实现，这个后面会讲到。ok，第一步实现item布局和ViewHolder，分析一下item需要展示的内容：首先是**地名**，还有就是后面有没有**已选择**，不要忽略item的点击事件。

![item_region_layout.png](http://upload-images.jianshu.io/upload_images/1815913-f3f3144856d1fda5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

xml布局如下：

```xml
<RelativeLayout
    android:id="@+id/item_btn"  >

    <TextView
        android:id="@+id/item_tv"  />

    <TextView
        android:id="@+id/checked" />
</RelativeLayout>
```

ViewHolder如下：

```java
public class RegionViewHolder extends RecyclerView.ViewHolder {
    public TextView textView;
    public TextView checked;
    public ViewGroup itemBtn;

    public RegionViewHolder(View itemView) {
        super(itemView);
        initView();
    }

    private void initView() {
        textView = (TextView) itemView.findViewById(R.id.item_tv);
        checked = (TextView) itemView.findViewById(R.id.checked);
        itemBtn = (ViewGroup) itemView.findViewById(R.id.item_btn);
    }
}
```

## Adapter适配

　　我们的adapter要继承RecyclerView.Adapter，并且实现该抽象类的几个重要方法：

```java
public class RegionAdapter extends RecyclerView.Adapter<RegionViewHolder> {

    private List<String> itemList;//用于存放要展示的数据列表
    private String checkedStr;//当前选中的地区
    private Context context;
    private LayoutInflater layoutInflater;
    private OnItemClickListener listener;//回调点击事件

    public RegionAdapter(Context context) {
        this.context = context;
        layoutInflater = LayoutInflater.from(context);
    }

    /* 更新数据并展示 */
    public void setData(List<String> itemList,String checkedStr) {
        this.itemList = itemList;
        this.checkedStr = checkedStr;
        notifyDataSetChanged();
    }

    @Override
    public RegionViewHolder onCreateViewHolder(ViewGroup parent, int viewType) {
        return new RegionViewHolder(layoutInflater.inflate(R.layout.item_region_layout,parent,false));
    }

    @Override
    public void onBindViewHolder(final RegionViewHolder holder, final int position) {
        holder.textView.setText(itemList.get(position));
        holder.checked.setVisibility(checkedStr.equals(itemList.get(position))? View.VISIBLE:View.GONE);
        holder.itemBtn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (listener != null)
                    listener.onItemClick(holder,position);
            }
        });
    }

    @Override
    public int getItemCount() {
        return isEmpty(itemList) ? 0 : itemList.size();
    }

    private  <D> boolean isEmpty(List<D> list) {
        return (list == null || list.isEmpty());
    }

    public void setListener(OnItemClickListener listener) {
        this.listener = listener;
    }

    public interface OnItemClickListener {
        void onItemClick(RegionViewHolder holder, int position);
    }

}
```

## RegionActivity实现

　　在最开始的时，本来是想省份做一个activity，城市一个activity，然后地区一个，这样的话代码利用率太低，最好的办法还是在一个activity里实现，其实就是定义了一个curPage变量，记录用户当前选择到第几步了：

### 三个操作阶段

```java
switch (curPage) {
            case PROVINCE://用户选择了某个省份
                province = itemList.get(position);
                for (Result result : result_List) {
                    if (province.equals(result.getProvince()))
                        city_List = result.getCity();
                }
                provinces.clear();
                provinces.addAll(itemList);
                itemList.clear();
                for (City city : city_List) {
                    if (this.city.equals(city.getCity()))
                        itemList.add(0,city.getCity());
                    else
                        itemList.add(city.getCity());
                }
                adapter.setData(itemList,city);
                curPage++;
                break;

            case CITY://用户选择了某个城市
                city = itemList.get(position);
                for (City city : city_List) {
                    if (this.city.equals(city.getCity()))
                        district_List = city.getDistrict();
                }
                cities.clear();
                cities.addAll(itemList);
                itemList.clear();
                for (District district : district_List) {
                    if (this.district.equals(district.getDistrict()))
                        itemList.add(0,district.getDistrict());
                    else
                        itemList.add(district.getDistrict());
                }
                adapter.setData(itemList,district);
                curPage++;
                break;

            case DISTRICT://用户选择了某个地区
                district = itemList.get(position);
                setResult(RESULT_OK,new Intent().putExtra("result"
                        ,province + " " + city + " " + district));
                finish();
                break;
        }
    }
```

　　其实代码一眼就能看清楚，就是三个阶段，到最后一个阶段时就setResult把结果回传过去，不过这里要考虑到如果用户选择到最后一步时，如果想退出去重新选择城市的话，一按返回就退出activity了[捂脸/(ㄒoㄒ)/~~]，所以我们还需要重写onBackPressed：

### 三个返回阶段

```java
    @Override
    public void onBackPressed() {
        switch (curPage) {
            case PROVINCE:
                finish();
                break;

            case CITY:
                itemList.clear();
                itemList.addAll(provinces);
                adapter.setData(itemList,province);
                curPage--;
                break;

            case DISTRICT:
                itemList.clear();
                itemList.addAll(cities);
                adapter.setData(itemList,city);
                curPage--;
                break;
        }
    }
```

### 装载数据

  　　补充一下数据的装载，首先把json数据从raw文件夹里面读取出来，然后格式化：

```java
try {
	if (result_List == null) {
		Gson gson = new Gson();
		Root root = gson.fromJson(StreamUtils.get(this,R.raw.city), Root.class);
		result_List = root.getResult();
	}

	for (Result result : result_List) {
		if (province.equals(result.getProvince()))
			itemList.add(0,result.getProvince());
		else
			itemList.add(result.getProvince());
	}
	adapter.setData(itemList,province);
} catch (JsonSyntaxException e) {
	e.printStackTrace();
}
```

　　这里推荐一个实用的工具，就是json数据格式化工具：[pojo在线](http://www.bejson.com/json2javapojo/)
　　
![pojo_demo.gif](http://upload-images.jianshu.io/upload_images/1815913-2efa181cb4152ccf.gif?imageMogr2/auto-orient/strip)

　　顺便再推荐一个Material Design配色工具：[MaterialPalette在线](https://www.materialpalette.com/)

![materialpalette.gif](http://upload-images.jianshu.io/upload_images/1815913-586cb30f5d54397b.gif?imageMogr2/auto-orient/strip)

　　是不是非常的炫酷实用呢？有了这个还要什么设计狮( ⊙ o ⊙ )！
　　
　　最后再看一下项目结构：

![project_structure.png](http://upload-images.jianshu.io/upload_images/1815913-2a6ef4cee81f8701.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

[demo下载地址](https://github.com/hustlong/RegionSelector/blob/master/files/app-release.apk?raw=true)

[github项目地址](https://github.com/hustlong/RegionSelector)
