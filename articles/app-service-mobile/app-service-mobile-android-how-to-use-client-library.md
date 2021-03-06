---
title: "如何使用 Android 移动应用客户端库"
description: "如何使用 Azure 移动应用的 Android 客户端 SDK。"
services: app-service\mobile
documentationcenter: android
author: ysxu
manager: adrianha
editor: 
ms.assetid: 5352d1e4-7685-4a11-aaf4-10bd2fa9f9fc
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 10/01/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: 277df83870c9cbfcb2407999ca9bc6a799284abe
ms.lasthandoff: 03/09/2017


---
# <a name="how-to-use-the-android-client-library-for-mobile-apps"></a>如何使用移动应用的 Android 客户端库
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

本指南演示了如何使用用于移动应用的 Android 客户端 SDK 实现常见方案，如：

* 查询数据（插入、更新和删除）。
* 身份验证。
* 处理错误。
* 自定义客户端。

此外，还深入探讨了大多数移动应用中使用的常见客户端代码。

本指南侧重于客户端 Android SDK。  若要详细了解移动应用的服务器端 SDK，请参阅 [Work with .NET backend SDK][10]（使用 .NET 后端）或 [How to use the Node.js backend SDK][11]（如何使用 Node.js 后端 SDK）。

## <a name="reference-documentation"></a>参考文档
可以在 GitHub 上找到有关 Android 客户端库的 [Javadocs API 参考][12]。

## <a name="supported-platforms"></a>支持的平台
Azure 移动应用 Android SDK 支持 API 级别 19 到 24（KitKat 到 Nougat）。  

“服务器流”身份验证使用 WebView 显示 UI。 如果设备无法显示 WebView UI，则需要使用产品范围以外的其他身份验证方法。  此 SDK 不适用于手表型或类似的受限设备。

## <a name="setup-and-prerequisites"></a>安装与先决条件
完成[移动应用快速入门](app-service-mobile-android-get-started.md)教程。  确保满足用于开发 Azure 移动应用的所有先决条件。  快速入门教程还有助于配置帐户并创建第一个移动应用后端。

如果决定不完成快速入门教程，请完成以下任务：

* [创建移动应用后端][13]，以与 Android 应用配合使用。
* 在 Android Studio 中，[更新 Gradle 生成文件](#gradle-build)。
* [启用 Internet 权限](#enable-internet)。

### <a name="gradle-build"></a>更新 Gradle 生成文件
更改以下两个 **build.gradle** 文件：

1. 将以下代码添加到 *buildscript* 标记内*项目*级别的 **build.gradle** 文件：

        buildscript {
            repositories {
                jcenter()
            }
        }
2. 将以下代码添加到*依赖关系*标记内*模块应用*级别的 **build.gradle** 文件：

        compile 'com.microsoft.azure:azure-mobile-android:3.1.0'

    目前的最新版本为 3.1.0。 [此处][14]列出了支持的版本。

### <a name="enable-internet"></a>启用 Internet 权限
若要访问 Azure，应用中必须已启用 Internet 权限。 如果尚未启用，请将以下代码行添加到 **AndroidManifest.xml** 文件：

    <uses-permission android:name="android.permission.INTERNET" />

## <a name="the-basics-deep-dive"></a>基础知识的深入探讨
本部分讨论了一些适用于使用 Azure 移动应用的快速入门应用中的代码。  

### <a name="data-object"></a>定义客户端数据类
若要访问 SQL Azure 表的数据，可定义对应于移动应用后端中表的客户端数据类。 本主题中的示例采用名为 **ToDoItem** 的表，其中包含以下列：

* id
* text
* complete

相应的类型化客户端对象如下：

    public class ToDoItem {
        private String id;
        private String text;
        private Boolean complete;
    }

代码驻留在名为 **ToDoItem.java** 的文件中。

如果 SQL Azure 表包含多个列，请将相应的字段添加到此类。  例如，如果 DTO（数据传输对象）包含整数 Priority 列，则可以添加此字段，以及其 getter 和 setter 方法：

    private Integer priority;

    /**
    * Returns the item priority
    */
    public Integer getPriority() {
        return mPriority;
    }

    /**
    * Sets the item priority
    *
    * @param priority
    *            priority to set
    */
    public final void setPriority(Integer priority) {
        mPriority = priority;
    }

若要了解如何在移动应用后端中创建更多表，请参阅[如何定义表控制器][15]（.NET 后端）或[使用动态架构定义表][16]（Node.js 后端）。 对于 Node.js 后端，也可以使用 [Azure 门户]中的“简易表”设置。

### <a name="create-client"></a>如何创建客户端上下文
此代码创建用于访问移动应用后端的 **MobileServiceClient** 对象。 代码将进入 *AndroidManifest.xml* 中指定为 **MAIN** 操作和 **LAUNCHER** 类别的 **Activity** 类的 `onCreate` 方法。 在快速入门代码中，代码将进入 **ToDoActivity.java** 文件。

        MobileServiceClient mClient = new MobileServiceClient(
            "MobileAppUrl", // Replace with the Site URL
            this)

在此代码中，请将 `MobileAppUrl` 替换为移动应用后端的 URL，可以在 [Azure 门户]中移动应用后端的边栏选项卡内找到该 URL。 若要编译此代码行，还需要添加以下 **import** 语句：

    import com.microsoft.windowsazure.mobileservices.*;

### <a name="instantiating"></a>如何创建表引用
在后端中查询或修改数据的最简单方法就是使用*类型化编程模型*，因为 Java 是强类型化语言。 在客户端对象与后端 Azure SQL 中的表之间发送数据时，此模型使用 [gson][3] 库提供无缝的 JSON 序列化和反序列化。

若要访问表，请先通过对 [MobileServiceClient][9] 调用 **getTable** 方法来创建一个 [MobileServiceTable][8] 对象。  此方法有两个重载：

    public class MobileServiceClient {
        public <E> MobileServiceTable<E> getTable(Class<E> clazz);
        public <E> MobileServiceTable<E> getTable(String name, Class<E> clazz);
    }

在以下代码中，**mClient** 是对 MobileServiceClient 对象的引用。  如果类名称与表名称相同，则使用第一个重载，这也是快速入门中使用的重载：

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable(ToDoItem.class);

如果表名称与类型名称不同，则使用第二个重载：第一个参数是表名称。

    MobileServiceTable<ToDoItem> mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="binding"></a>如何将数据绑定到用户界面
数据绑定涉及到三个组件：

* 数据源
* 屏幕布局
* 将两者关联起来的适配器。

以下示例代码将移动应用 SQL Azure 表 **ToDoItem** 中的数据返回到一个数组中。 该活动是数据应用程序经常使用的一种模式。  数据库查询通常将客户端所获得的行集合返回到列表或数组中。 在此示例中，该数组就是数据源。

代码将指定屏幕布局，用于定义设备中显示的数据视图。  这两者与适配器绑定在一起，在此代码中，适配器是 **ArrayAdapter&lt;ToDoItem&gt;** 类的扩展。

#### <a name="layout"></a>如何定义布局
布局由多个 XML 代码段定义。 以某个现有布局为例，我们假设以下代码表示了要在其中填充服务器数据的 **ListView**。

    <ListView
        android:id="@+id/listViewToDo"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        tools:listitem="@layout/row_list_to_do" >
    </ListView>

在前面的代码中，*listitem* 属性指定列表中单个行的布局 ID。 此代码指定复选框及其关联文本，并针对列表中的每项实例化一次。 此布局不显示 **ID** 字段，如果使用更复杂的布局，则会在屏幕中指定更多字段。 以下代码摘自 **row_list_to_do.xml** 文件。

    <?xml version="1.0" encoding="utf-8"?>
    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="horizontal">
        <CheckBox
            android:id="@+id/checkToDoItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@string/checkbox_text" />
    </LinearLayout>


#### <a name="adapter"></a>如何定义适配器
由于视图的数据源是 **ToDoItem** 的数组，因此我们需要基于 **ArrayAdapter&lt;ToDoItem&gt;** 类子类化适配器。 此子类将使用 **row_list_to_do** 布局为每个 **ToDoItem** 生成一个视图。

在代码中，我们可以定义以下类作为 **ArrayAdapter&lt;E&gt;** 类的扩展：

    public class ToDoItemAdapter extends ArrayAdapter<ToDoItem> {

替代适配器的 **getView** 方法。 例如：

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        View row = convertView;

        final ToDoItem currentItem = getItem(position);

        if (row == null) {
            LayoutInflater inflater = ((Activity) mContext).getLayoutInflater();
            row = inflater.inflate(R.layout.row_list_to_do, parent, false);
        }

        row.setTag(currentItem);

        final CheckBox checkBox = (CheckBox) row.findViewById(R.id.checkToDoItem);
        checkBox.setText(currentItem.getText());
        checkBox.setChecked(false);
        checkBox.setEnabled(true);

        checkBox.setOnClickListener(new View.OnClickListener() {

            @Override
            public void onClick(View arg0) {
                if (checkBox.isChecked()) {
                    checkBox.setEnabled(false);
                    if (mContext instanceof ToDoActivity) {
                        ToDoActivity activity = (ToDoActivity) mContext;
                        activity.checkItem(currentItem);
                    }
                }
            }
        });


        return row;
    }

在活动中创建此类的实例，如下所示：

    ToDoItemAdapter mAdapter;
    mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);

ToDoItemAdapter 构造函数的第二个参数是对布局的引用。 我们现在可以实例化 **ListView** 并将适配器分配到 **ListView**。

    ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
    listViewToDo.setAdapter(mAdapter);

### <a name="api"></a>API 结构
移动应用表操作和自定义 API 调用是异步的。 针对异步方法（包括查询、插入、更新和删除）请使用 [Future] 和 [AsyncTask] 对象。 使用 future 可以更方便地在后台线程上执行多个操作，而无需处理多个嵌套回调。

有关示例，请查看 [Azure 门户]中 Android 快速入门项目的 **ToDoActivity.java** 文件。

#### <a name="use-adapter"></a>如何使用适配器
现在，你可以使用数据绑定了。 以下代码演示了如何获取表中的项，并使用返回项填充本地适配器。

    public void showAll(View view) {
        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final List<ToDoItem> results = mToDoTable.execute().get();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (ToDoItem item : results) {
                                mAdapter.add(item);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        };
        runAsyncTask(task);
    }

每当修改 **ToDoItem** 表时，请调用该适配器。 由于修改是按记录完成的，因此要处理的是单个行而不是一个集合。 插入项时，需要对适配器调用 **add** 方法；删除项时，需要调用 **remove** 方法。

## <a name="querying"></a>如何查询移动应用后端中的数据
本部分介绍如何向包含以下任务的移动应用后端发出查询：

* [返回所有项]
* [筛选返回的数据]
* [对返回的数据进行排序]
* [在页中返回数据]
* [选择特定列]
* [连接查询方法](#chaining)

### <a name="showAll"></a>如何返回表中的所有项
以下查询返回 **ToDoItem** 表中的所有项。

    List<ToDoItem> results = mToDoTable.execute().get();

*results* 变量以列表形式返回查询中的结果集。

### <a name="filtering"></a>如何筛选返回的数据
以下查询执行从 **complete** 等于 **false** 的 **ToDoItem** 表返回所有项目。

    List<ToDoItem> result = mToDoTable.where()
                                .field("complete").eq(false)
                                .execute().get();

**mToDoTable** 是对前面创建的移动服务表的引用。

对表引用使用 **where** 方法调用定义筛选器。 然后，依次执行 **where** 方法、**field** 方法和用于指定逻辑谓词的方法。 可能的谓词方法包括 **eq**（等于）、**ne**（不等于）、**gt**（大于）、**ge**（大于或等于）、**lt**（小于）、**le**（小于或等于）。 使用这些方法可将数字和字符串字段与特定的值相比较。

可以按日期筛选。 使用以下方法可以比较整个日期字段或日期的某些部分：**year**、**month**、**day**、**hour**、**minute** 和 **second**。 以下示例针对 *due date* 等于 2013 的项添加一个筛选器。

    mToDoTable.where().year("due").eq(2013).execute().get();

以下方法支持对字符串字段使用复杂筛选器：**startsWith**、**endsWith**、**concat**、**subString**、**indexOf**、**replace**、**toLower**、**toUpper**、**trim** 和 **length**。 以下示例筛选 *text* 列以“PRI0”开头的表行。

    mToDoTable.where().startsWith("text", "PRI0").execute().get();

支持对数字字段使用以下运算符方法：**add**、**sub**、**mul**、**div**、**mod**、**floor**、**ceiling** 和 **round**。 以下示例筛选其中的 **duration** 为偶数的表行。

    mToDoTable.where().field("duration").mod(2).eq(0).execute().get();

可将谓词与以下逻辑方法相组合：**and**、**or** 和 **not**。 以下示例组合前面的两个示例。

    mToDoTable.where().year("due").eq(2013).and().startsWith("text", "PRI0")
                .execute().get();

组合与嵌套逻辑运算符：

    mToDoTable.where()
                .year("due").eq(2013)
                    .and
                (startsWith("text", "PRI0").or().field("duration").gt(10))
                .execute().get();

有关筛选操作的更详细介绍和示例，请参阅 [Exploring the richness of the Android client query model](http://hashtagfail.com/post/46493261719/mobile-services-android-querying)（探索 Android 客户端查询模型的丰富功能）。

### <a name="sorting"></a>如何为返回的数据排序
下面的代码返回 **ToDoItems** 表中的所有项目，并按 *text* 字段升序排列。 *mToDoTable* 是对前面创建的后端表的引用：

    mToDoTable.orderBy("text", QueryOrder.Ascending).execute().get();

**orderBy** 方法的第一个参数是与要排序的字段名称相同的字符串。 第二个参数使用 **QueryOrder** 枚举来指定是按升序还是按降序排序。  如果使用 ***where*** 方法筛选，则必须在调用 ***orderBy*** 方法之前调用 ***where*** 方法。

### <a name="paging"></a>如何在页中返回数据
第一个示例演示了如何选择表中的前 5 项。 此查询返回 **ToDoItems** 表中的项。 **mToDoTable** 是对前面创建的后端表的引用：

    List<ToDoItem> result = mToDoTable.top(5).execute().get();


以下查询跳过前 5 项，返回后 5 项：

    mToDoTable.skip(5).top(5).execute().get();

### <a name="selecting"></a>如何选择特定的列
以下代码演示了如何返回 **ToDoItems** 表中的所有项，但只显示 **complete** 和 **text** 字段。 **mToDoTable** 是对前面创建的后端表的引用。

    List<ToDoItemNarrow> result = mToDoTable.select("complete", "text").execute().get();

select 函数的参数是要返回的表列的字符串名称。

**select** 方法需要接在 **where** 和 **orderBy** 等方法的后面。 它可以后接 **top** 等分页方法。

### <a name="chaining"></a>如何连接查询方法
用于查询后端表的方法是可以连接的。 链接查询方法使你能够选择已排序并分页的筛选行的特定列。 可以创建复杂的逻辑筛选器。
每个查询方法将返回一个查询对象。 若要结束方法序列并真正运行查询，可以调用 **execute** 方法。 例如：

    mToDoTable.where()
        .year("due").eq(2013)
        .and().startsWith("text", "PRI0")
        .or().field("duration").gt(10)
        .orderBy(duration, QueryOrder.Ascending)
        .select("id", "complete", "text", "duration")
        .top(20)
        .execute().get();

必须按照以下顺序排序已链接的查询方法：

1. 筛选 (**where**) 方法。
2. 排序 (**orderBy**) 方法。
3. 选择 (**select**) 方法。
4. 分页（**skip** 和 **top**）方法。

## <a name="inserting"></a>如何将数据插入后端
实例化 *ToDoItem* 类的实例并设置该实例的属性。

    ToDoItem item = new ToDoItem();
    item.text = "Test Program";
    item.complete = false;

然后，使用 **insert()** 插入对象：

    ToDoItem entity = mToDoTable.insert(item).get();

返回的实体将匹配插入后端表的数据，包括 ID 和后端上设置的任何其他值。

移动应用表需要名为 **ID** 的主键列。 默认情况下，此列是字符串。 ID 列的默认值是 GUID。  可以提供其他唯一值，如电子邮件地址或用户名。 如果没有为插入的记录提供字符串 ID 值，后端将生成新的 GUID。

字符串 ID 值提供以下优势：

* 无需往返访问数据库即可生成 ID。
* 更方便地合并不同表或数据库中的记录。
* ID 值能够更好地与应用程序的逻辑集成。

对于脱机同步支持，字符串 ID 值为 **REQUIRED**。

## <a name="updating"></a>如何更新移动应用中的数据
若要更新表中的数据，请将新对象传递给 **update()** 方法。

    mToDoTable.update(item).get();

在此示例中，*item* 是对 *ToDoItem* 表中某个行的引用，该表包含一些更改。
已更新具有相同 **ID** 的行。

## <a name="deleting"></a>如何删除移动应用中的数据
以下代码演示如何通过指定数据对象来删除表中的数据。

    mToDoTable.delete(item);

也可以通过指定要删除的行的 **ID** 字段来删除项。

    String myRowId = "2FA404AB-E458-44CD-BC1B-3BC847EF0902";
       mToDoTable.delete(myRowId);

## <a name="lookup"></a>如何查找特定的项
使用 **lookUp()** 方法查找具有特定 **ID** 字段的项：

    ToDoItem result = mToDoTable
                        .lookUp("0380BAFB-BCFF-443C-B7D5-30199F730335")
                        .get();

## <a name="untyped"></a>如何处理非类型化数据
非类型化编程模型可以提供对 JSON 序列化的完全控制。  但有时可能想要使用非类型化编程模型，这种情况也是比较常见的。 例如，如果后端表包含很多列，并且只需引用列的子集。  类型化模型需要在数据类中定义移动应用表的所有列。  

用于访问数据的大多数 API 调用都与类型化编程调用类似。 主要差别在于，在非类型化模型中，要对 **MobileServiceJsonTable** 对象而不是 **MobileServiceTable** 对象调用方法。

### <a name="json_instance"></a>如何创建非类型化表的实例
与使用类型化模型相似，首先需要获取表引用，不过，此时该引用的是一个 **MobileServicesJsonTable** 对象。 对客户端的实例调用 **getTable** 方法可获取该引用：

    private MobileServiceJsonTable mJsonToDoTable;
    //...
    mJsonToDoTable = mClient.getTable("ToDoItem");

创建 **MobileServiceJsonTable** 的实例后，它几乎具有与类型化编程模型相同的可用 API。 在某些情况下，这些方法采用非类型化参数而不是类型化参数。

### <a name="json_insert"></a>如何插入到非类型化表中
以下代码演示了如何执行插入。 第一步是创建属于 [gson][3] 库的 [JsonObject][1]。

    JsonObject jsonItem = new JsonObject();
    jsonItem.addProperty("text", "Wake up");
    jsonItem.addProperty("complete", false);

然后，使用 **insert()** 将非类型化对象插入表。

    mJsonToDoTable.insert(jsonItem).get();

如果需要获取所插入对象的 ID，请使用 **getAsJsonPrimitive()** 方法调用。

    jsonItem.getAsJsonPrimitive("id").getAsInt());

### <a name="json_delete"></a>如何从非类型化表中删除数据
以下代码演示了如何删除一个实例，在本例中，该实例就是我们在前一个 *insert* 示例中创建的 **JsonObject** 的实例。 该代码与类型化案例相同，但方法具有不同的签名，因为它引用了 **JsonObject**。

         mToDoTable.delete(jsonItem);

还可以使用某个实例的 ID 来直接删除该实例：

         mToDoTable.delete(ID);

### <a name="json_get"></a>如何返回非类型化表中的所有行
以下代码演示了如何检索整个表。 由于使用的是 Json 数据表，你可以选择性地只检索某些表的列。

    public void showAllUntyped(View view) {
        new AsyncTask<Void, Void, Void>() {
            @Override
            protected Void doInBackground(Void... params) {
                try {
                    final JsonElement result = mJsonToDoTable.execute().get();
                    final JsonArray results = result.getAsJsonArray();
                    runOnUiThread(new Runnable() {

                        @Override
                        public void run() {
                            mAdapter.clear();
                            for (JsonElement item : results) {
                                String ID = item.getAsJsonObject().getAsJsonPrimitive("id").getAsString();
                                String mText = item.getAsJsonObject().getAsJsonPrimitive("text").getAsString();
                                Boolean mComplete = item.getAsJsonObject().getAsJsonPrimitive("complete").getAsBoolean();
                                ToDoItem mToDoItem = new ToDoItem();
                                mToDoItem.setId(ID);
                                mToDoItem.setText(mText);
                                mToDoItem.setComplete(mComplete);
                                mAdapter.add(mToDoItem);
                            }
                        }
                    });
                } catch (Exception exception) {
                    createAndShowDialog(exception, "Error");
                }
                return null;
            }
        }.execute();
    }

类型化模型使用的相同筛选设置（筛选和分页方法）也适用于非类型化模型。

## <a name="custom-api"></a>如何：调用自定义 API
自定义 API 可让你定义自定义终结点，这些终结点将会公开不映射到插入、更新、删除或读取操作的服务器功能。 使用自定义 API 能够以更大的力度控制消息传送，包括读取和设置 HTTP 消息标头，以及定义除 JSON 以外的消息正文格式。

从 Android 客户端调用 **invokeApi** 方法，以调用自定义 API 终结点。 以下示例演示了如何调用名为 **completeAll** 的 API 终结点，从而返回名为 **MarkAllResult** 的集合类。

    public void completeItem(View view) {

        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll", MarkAllResult.class );

            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }

                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();
                }
            });
        }

**invokeApi** 方法在客户端上调用，该客户端向新的自定义 API 发送 POST 请求。 与任何错误相同，自定义 API 返回的结果也显示在消息对话框中。 使用其他版本的 **invokeApi** 可以选择性地在请求正文中发送对象、指定 HTTP 方法，以及随请求一起发送查询参数。 此外还提供了非类型化的 **invokeApi** 版本。

## <a name="authentication"></a>如何将身份验证添加到应用
教程已详细说明如何添加这些功能。

应用服务支持使用各种外部标识提供者[对应用用户进行身份验证](app-service-mobile-android-get-started-users.md)，这些提供者包括：Facebook、Google、Microsoft 帐户、Twitter 和 Azure Active Directory。 你可以在表中设置权限，以便将特定操作的访问权限限制给已经过身份验证的用户。 你还可以在后端中使用已经过身份验证的用户的标识来实施授权规则。

支持两种身份验证流：**服务器**流和**客户端**流。 服务器流依赖于标识提供者 Web 界面，因此可提供最简便的身份验证体验。  实现服务器流身份验证不需要任何其他 SDK。 服务器流身份验证不提供与移动设备的深入集成，因此建议仅用于验证概念方案。

客户端流依赖于标识提供者提供的 SDK，因此允许与设备特定的功能（例如单一登录）进行更深入的集成。  例如，可以将 Facebook SDK 集成到移动应用程序。  移动客户端切换到 Facebook 应用，确保登录后再切换回移动应用。

在应用中启用身份验证需要执行以下四个步骤：

* 使用标识提供者注册应用，以进行身份验证。
* 配置应用服务后端。
* 在应用服务后端上，仅为经过身份验证的用户授予表权限。
* 将身份验证代码添加到应用。

你可以在表中设置权限，以便将特定操作的访问权限限制给已经过身份验证的用户。 还可以使用已经过身份验证的用户的 SID 来修改请求。  有关详细信息，请参阅[身份验证入门]和“如何使用服务器 SDK”文档。

### <a name="caching"></a>如何向应用程序添加身份验证代码
以下代码使用 Google 提供程序启动服务器流登录过程：

    MobileServiceUser user = mClient.login(MobileServiceAuthenticationProvider.Google);

可以使用 **getUserId** 方法从 **MobileServiceUser** 获取已登录用户的 ID。 有关如何使用 Futures 调用异步登录 API 的示例，请参阅[身份验证入门]。

### <a name="caching"></a>如何缓存身份验证令牌
缓存身份验证令牌需要将用户 ID 和身份验证令牌存储在设备本地。 下一次启动应用程序时，只需检查缓存，如果这些值存在，则可以跳过登录过程，并使用这些数据重新进入客户端。 但是，这些数据是敏感的，为安全起见，应该以加密形式存储，以防手机失窃。

可以在[缓存身份验证令牌][7]部分中了解有关如何缓存身份验证令牌的完整示例。

尝试使用过期的令牌时，将收到“401 未授权”响应。 可以使用筛选器处理身份验证错误。  筛选器可截获到应用服务后端的请求。 此时，筛选器代码将测试 401 响应，根据需要触发登录进程，然后恢复生成 401 响应的请求。

## <a name="adal"></a>如何使用 Active Directory 身份验证库对用户进行身份验证
可以借助 Active Directory 身份验证库 (ADAL) 使用 Azure Active Directory 将用户登录到应用程序。 使用客户端流登录通常比使用 `loginAsync()` 方法更有利，因为它提供更直观的 UX 风格，并允许进行其他自定义。

1. 根据[如何为 Active Directory 登录配置应用服务](app-service-mobile-how-to-configure-active-directory-authentication.md)教程的说明，为 AAD 登录配置移动应用。 请务必完成注册本机客户端应用程序的可选步骤。
2. 可通过修改 build.gradle 文件并包含以下定义来安装 ADAL：

```
repositories {
    mavenCentral()
    flatDir {
        dirs 'libs'
    }
    maven {
        url "YourLocalMavenRepoPath\\.m2\\repository"
    }
}
packagingOptions {
    exclude 'META-INF/MSFTSIG.RSA'
    exclude 'META-INF/MSFTSIG.SF'
}
dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    compile('com.microsoft.aad:adal:1.1.1') {
        exclude group: 'com.android.support'
    } // Recent version is 1.1.1
    compile 'com.android.support:support-v4:23.0.0'
}
```

1. 将以下代码添加到应用程序并进行以下替换：

* 将 **INSERT-AUTHORITY-HERE** 替换为在其中预配应用程序的租户的名称。 格式应为 https://login.windows.net/contoso.onmicrosoft.com。 可以在 [Azure 经典门户] 中从 Azure Active Directory 的“域”选项卡复制此值。
* 将 **INSERT-RESOURCE-ID-HERE** 替换移动应用后端的客户端 ID。 可以在门户中“Azure Active Directory 设置”下面的“高级”选项卡获取此客户端 ID。
* 将 **INSERT-CLIENT-ID-HERE** 替换为从本机客户端应用程序复制的客户端 ID。
* 使用 HTTPS 方案将 **INSERT-REDIRECT-URI-HERE** 替换为站点的 */.auth/login/done* 终结点。 此值应类似于 *https://contoso.azurewebsites.net/.auth/login/done*。

        private AuthenticationContext mContext;

        private void authenticate() {
            String authority = "INSERT-AUTHORITY-HERE";
            String resourceId = "INSERT-RESOURCE-ID-HERE";
            String clientId = "INSERT-CLIENT-ID-HERE";
            String redirectUri = "INSERT-REDIRECT-URI-HERE";
            try {
                mContext = new AuthenticationContext(this, authority, true);
                mContext.acquireToken(this, resourceId, clientId, redirectUri, PromptBehavior.Auto, "", callback);
            } catch (Exception exc) {
                exc.printStackTrace();
            }
        }

        private AuthenticationCallback<AuthenticationResult> callback = new AuthenticationCallback<AuthenticationResult>() {
            @Override
            public void onError(Exception exc) {
                if (exc instanceof AuthenticationException) {
                    Log.d(TAG, "Cancelled");
                } else {
                    Log.d(TAG, "Authentication error:" + exc.getMessage());
                }
            }

            @Override
            public void onSuccess(AuthenticationResult result) {
                if (result == null || result.getAccessToken() == null
                        || result.getAccessToken().isEmpty()) {
                    Log.d(TAG, "Token is empty");
                } else {
                    try {
                        JSONObject payload = new JSONObject();
                        payload.put("access_token", result.getAccessToken());
                        ListenableFuture<MobileServiceUser> mLogin = mClient.login("aad", payload.toString());
                        Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                            @Override
                            public void onFailure(Throwable exc) {
                                exc.printStackTrace();
                            }
                            @Override
                            public void onSuccess(MobileServiceUser user) {
                                Log.d(TAG, "Login Complete");
                            }
                        });
                    }
                    catch (Exception exc){
                        Log.d(TAG, "Authentication error:" + exc.getMessage());
                    }
                }
            }
        };

        @Override
        protected void onActivityResult(int requestCode, int resultCode, Intent data) {
            super.onActivityResult(requestCode, resultCode, data);
            if (mContext != null) {
                mContext.onActivityResult(requestCode, resultCode, data);
            }
        }

## <a name="how-to-add-push-notification-to-your-app"></a>如何将推送通知添加到应用
可以[阅读概述][6]，其中介绍了 Microsoft Azure 通知中心如何支持各种推送通知。  在[本教程][5]中，每次插入一条记录，都会向所有设备发送一条推送通知。

## <a name="how-to-add-offline-sync-to-your-app"></a>如何将脱机同步添加到应用
快速入门教程包含可实现脱机同步的代码。 查找带有如下注释的代码：

    // Offline Sync

取消注释以下代码行，即可实现脱机同步，并且可将类似的代码添加到其他移动应用代码。

## <a name="customizing"></a>如何自定义客户端
可以通过多种方法自定义客户端的默认行为。

### <a name="headers"></a>如何自定义请求标头
配置 **ServiceFilter**，以向每个请求添加自定义 HTTP 标头：

    private class CustomHeaderFilter implements ServiceFilter {

        @Override
        public ListenableFuture<ServiceFilterResponse> handleRequest(
                    ServiceFilterRequest request,
                    NextServiceFilterCallback next) {

            runOnUiThread(new Runnable() {

                @Override
                public void run() {
                    request.addHeader("My-Header", "Value");                    }
            });

            SettableFuture<ServiceFilterResponse> result = SettableFuture.create();
            try {
                ServiceFilterResponse response = next.onNext(request).get();
                result.set(response);
            } catch (Exception exc) {
                result.setException(exc);
            }
        }

### <a name="serialization"></a>如何自定义序列化
客户端假设后端上的表名称、列名称和数据类型都与客户端中定义的数据对象完全匹配。 在许多情况下，服务器上和客户端上的名称并不匹配。 在方案中，可能需要执行类似于下面的自定义操作：

* 应用服务表中使用的列名称与客户端中使用的名称不匹配。
* 使用一个应用服务表，其名称不同于该表在客户端中映射到的类。
* 启用属性自动大写。
* 向对象中添加复杂属性。

### <a name="columns"></a>如何映射不同的客户端名称和服务器名称
假设 Java 客户端代码为 **ToDoItem** 对象属性使用了类似于下面属性的标准 Java 样式名称：

* mId
* mText
* mComplete
* mDuration

将客户端名称序列化为与服务器上 **ToDoItem** 表的列名称匹配的 JSON 名称。 以下代码使用 [gson][3] 库批注属性：

    @com.google.gson.annotations.SerializedName("text")
    private String mText;

    @com.google.gson.annotations.SerializedName("id")
    private int mId;

    @com.google.gson.annotations.SerializedName("complete")
    private boolean mComplete;

    @com.google.gson.annotations.SerializedName("duration")
    private String mDuration;

### <a name="table"></a>如何在客户端与后端之间映射不同的表名称
使用 [getTable()][4] 方法的替代，将客户端表名称映射到其他移动服务表名称：

    mToDoTable = mClient.getTable("ToDoItemBackup", ToDoItem.class);

### <a name="conversions"></a>如何自动执行列名称映射
可以使用 [gson][3] API，指定适用于每个列的转换策略。 在将数据发送到 Azure 应用服务之前，Android 客户端库会在后台使用 [gson][3] 将 Java 对象序列化为 JSON 数据。  下面的代码使用 **setFieldNamingStrategy()** 方法设置策略。 此示例删除初始字符（“m”），然后将每个字段名称的下一个字符小写。 例如，它将“mId”变为“id”。

    client.setGsonBuilder(
        MobileServiceClient
        .createMobileServiceGsonBuilder()
        .setFieldNamingStrategy(new FieldNamingStrategy() {
            public String translateName(Field field) {
                String name = field.getName();
                return Character.toLowerCase(name.charAt(1))
                    + name.substring(2);
                }
            });

在使用 **MobileServiceClient** 前必须执行此代码。

### <a name="complex"></a>如何将对象或数组属性存储到表中
到目前为止，序列化示例包括基元类型，如整数和字符串。  基元类型可以轻松序列化为 JSON。  如果想要将不能自动序列化的复杂对象添加到 JSON，需要提供 JSON 序列化方法。  若要查看有关如何提供自定义 JSON 序列化的示例，请阅读博客文章[在移动服务 Android 客户端中使用 gson 库自定义序列化][2]。

<!-- Anchors. -->

[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[The API structure]: #api
[How to: Query data from a mobile service]: #querying
[返回所有项]: #showAll
[筛选返回的数据]: #filtering
[对返回的数据进行排序]: #sorting
[在页中返回数据]: #paging
[选择特定列]: #selecting
[How to: Concatenate query methods]: #chaining
[How to: Bind data to the user interface]: #binding
[How to: Define the layout]: #layout
[How to: Define the adapter]: #adapter
[How to: Use the adapter]: #use-adapter
[How to: Insert data into a mobile service]: #inserting
[How to: update data in a mobile service]: #updating
[How to: Delete data in a mobile service]: #deleting
[How to: Look up a specific item]: #lookup
[How to: Work with untyped data]: #untyped
[How to: Authenticate users]: #authentication
[Cache authentication tokens]: #caching
[How to: Handle errors]: #errors
[How to: Design unit tests]: #tests
[How to: Customize the client]: #customizing
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next Steps]: #next-steps
[Setup and Prerequisites]: #setup

<!-- Images. -->

<!-- URLs. -->
[Get started with Azure Mobile Apps]: app-service-mobile-android-get-started.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Mobile Services SDK for Android]: http://go.microsoft.com/fwlink/p/?LinkID=717033
[Azure 门户]: https://portal.azure.com
[身份验证入门]: app-service-mobile-android-get-started-users.md
[1]: http://google-gson.googlecode.com/svn/trunk/gson/docs/javadocs/com/google/gson/JsonObject.html
[2]: http://hashtagfail.com/post/44606137082/mobile-services-android-serialization-gson
[3]: http://go.microsoft.com/fwlink/p/?LinkId=290801
[4]: http://go.microsoft.com/fwlink/p/?LinkId=296840
[5]: app-service-mobile-android-get-started-push.md
[6]: ../notification-hubs/notification-hubs-push-notification-overview.md#integration-with-app-service-mobile-apps
[7]: app-service-mobile-android-get-started-users.md#cache-tokens
[8]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/table/MobileServiceTable.html
[9]: http://azure.github.io/azure-mobile-apps-android-client/com/microsoft/windowsazure/mobileservices/MobileServiceClient.html
[10]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[11]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[12]: http://azure.github.io/azure-mobile-apps-android-client/
[13]: app-service-mobile-android-get-started.md#create-a-new-azure-mobile-app-backend
[14]: http://go.microsoft.com/fwlink/p/?LinkID=717034
[15]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller
[16]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Future]: http://developer.android.com/reference/java/util/concurrent/Future.html
[AsyncTask]: http://developer.android.com/reference/android/os/AsyncTask.html

