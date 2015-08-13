# このドキュメントについて
このドキュメントは、自分の会社のプロジェクトのswift1.2からswift2.0への移行時のメモです

## reflect関数が使えない
自身のクラスを取得するreflect関数が使えなくなっています（主にAnalytics部分で使用）
以下に置き換えました
```
sendEvent(NSStringFromClass(self.dynamicType), str_action: "select_location_kodawari")
```

## try-catch
swift2.0からtry-catchの概念が導入された？みたいです。例えば、
```
var error: NSError?
if let dict = NSJSONSerialization.JSONObjectWithData(connectionsession.connectedData!, options:NSJSONReadingOptions.AllowFragments , error: &error) as? NSDictionary{
}            
```
みたいな書き方はダメで、


### reference
* https://www.bignerdranch.com/blog/error-handling-in-swift-2/
