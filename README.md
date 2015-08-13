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
```
do {
            
            
            let dict = try NSJSONSerialization.JSONObjectWithData(connectionsession.connectedData!, options:NSJSONReadingOptions.AllowFragments) as? NSDictionary
            
            let responseData:NSArray = dict!["results"] as! NSArray
            if responseData.count > 0{
                
                let version_key:String = "version"
                
                let version_dic:NSDictionary = responseData[0] as! NSDictionary
                
                if let latest = version_dic[version_key] as? String{
                    self.latest_str = latest
                }
            }
            
        } catch let error as NSError {
            // Handle any errors
            print(error)
        }
```
はOK

### reference
* https://www.bignerdranch.com/blog/error-handling-in-swift-2/
* 

## Dictionaryのキー

以下はエラー
```
let latest:String = responseData[0]["version"] as? String
```

```
Cannot subscript a value of type 'AnyObject?' with an index of type 'String'
```

以下はOK
```
let version_key:String = "version"
let version_dic:NSDictionary = responseData[0] as! NSDictionary
let latest = version_dic[version_key] as? String                
```

## realmの移行

自分でフレームワークをビルドしないといけない(cocoapodsとかcarthageは調べてない)
http://ja.stackoverflow.com/questions/13165/swift2%E3%81%A7realm%E3%82%92%E4%BD%BF%E3%81%84%E3%81%9F%E3%81%84

## tableviewのreloadcell

以下エラー
```
self.menuview.beginUpdates()
self.menuview.reloadRowsAtIndexPaths(ary_index as [AnyObject], withRowAnimation: UITableViewRowAnimation.None)
self.menuview.endUpdates()
```

```
Cannot invoke 'reloadRowsAtIndexPaths' with an argument list of type '([AnyObject], withRowAnimation: UITableViewRowAnimation)'
```


以下動いたコード
```
let reload_ary = indexPaths.copy() as! NSArray

self.workview.beginUpdates()
self.workview.insertRowsAtIndexPaths(reload_ary as! [NSIndexPath], withRowAnimation: UITableViewRowAnimation.Fade)
self.workview.endUpdates()
```
