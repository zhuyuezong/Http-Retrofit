# Retrofit简单的网络请求框架搭建
## 接口定义 
```Java
public class K
{
    public static class host
    {
        public static final String HOST = "http://www.weather.com.cn";
    }
}
```
这里的定义和系统的R文件类似。
</br>
然后在ApiServers这个类下定义请求路径与参数，如天气预报接口定义
```Java
@GET("data/cityinfo/{city_id}.html")
Observable<ResponseModel> getWeathers(@Path("city_id") String cityId);
```
注意这里定义成Observable<ResponseModel>不用修改。

## Model定义

Model的定义必须实现BaseModel接口，并且实现parseJson方法，这里没有使用Gson解析，让Retrofit框架自己解析，是因为那样必须把参数定义成接口返回的字段，并且节点上也有很大的限制，这里考虑自己解析，能够提高代码的阅性，可以根据不同接口的是用来命名。

</br>比如demo中的WeatherInfoModel
```Java
public class WeatherInfoModel implements BaseModel
```

## DataProvider实现
直接上demo中代码
```Java
public class WeatherProvider extends NetworkDataProvider
{
    private WeatherInfoModel mModel;

    public WeatherInfoModel getWeatherData()
    {
        return mModel;
    }

    @Override
    public void loadData(OnLoadingListener loadingListener)
    {
        loadData(getServers().getWeathers("101230201"),loadingListener);
    }

    @Override
    void parseJson(JSONObject jsonObject)
    {
        mModel = new WeatherInfoModel();
        mModel.parseJson(jsonObject);
    }
}
```
可以看到代码很简短，只要配置需要调用的接口，和刚定义model进行解析。
##最后调用
最后只要在我们的进行网络请求的地方，实例化DataProvider，并执行loadData方法就ok了。数据的处理在loadData的监听器中实现。

```Java
@Override
public void onBefore()
{

}

@Override
public void onSuccess()
{
    Toast.makeText(this,mProvider.getWeatherData().toString(),Toast.LENGTH_LONG).show();
}

@Override
public void onFailure(Throwable error)
{

}
```
