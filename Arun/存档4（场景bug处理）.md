不将SceneLoad标记为Isaveable，而是用协程的方式想办法在场景加载完后再加载进度



```c#

//保证在加载之前读取进度
[DefaultExecutionOrder(-100)]
public class DataManager : MonoBehaviour
{
    //存档
    public void Save()
    {
        foreach (ISaveable item in saveableList)
        {
            item.SaveData(data);
        }

        data.SaveGameScene(sceneLoad.currentLoadScene);

        var resultPath = jsonFolder + "data.sav";
        //将数据转成json文件
        var jsonData = JsonConvert.SerializeObject(data);
        //创建路径
        if(!File.Exists(resultPath))
            Directory.CreateDirectory(jsonFolder); //路径
        File.WriteAllText(resultPath, jsonData);

    }

    //读档
    public void Load()
    {
        ReadSaveData();
        sceneToGo = data.GetSaveScene();
        StartCoroutine(LoadDataAndScene());
    }

    IEnumerator LoadDataAndScene()
    {
        bool isLoaded = false;

        // 使用 AddListener 订阅事件
        sceneLoad.OnSceneLoaded.AddListener(() =>
        {
            isLoaded = true;
        });

        // 触发场景加载
        sceneLoad.OnLoadRequestEvent(sceneToGo, transform.position, true);

        // 等待直到场景加载完成
        yield return new WaitWhile(() => !isLoaded);

        // 清理监听器（重要！避免重复订阅导致的内存泄漏）
        sceneLoad.OnSceneLoaded.RemoveListener(() =>
        {
            isLoaded = true;
        });

        // 加载数据
        foreach (var item in saveableList)
        {
            item.LoadData(data);
        }
    }


}

```



```c#

public class SceneLoad : MonoBehaviour //没有Isaveable
{
    [Header("广播")]
    public UnityEvent OnSceneLoaded; //这个也是场景加载后的的事件

    //当场景已经加载好后
    private void OnLoadedCompleted(AsyncOperationHandle<SceneInstance> handle)
    {
        //场景加载完后执行的事件
        afterLoadedScene.RaiseEvent();
        OnSceneLoaded?.Invoke();

    }

}

```

