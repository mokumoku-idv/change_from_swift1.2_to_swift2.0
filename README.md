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

try catchはcatchの中に何も書かないとどのエラータイプにも属さないものを返すっぽい
```
do {
            
            
            let realm = try Realm()
            let items = realm.objects(Favorite).sorted("register_date", ascending: true)
            
            if items.count > 50{
                //ループ回数を取得
                let loopcount = items.count-50
                for var i = 0 ; i < loopcount ; i++ {
                    let fav = items[i] as Favorite
                    deleteFav(fav,realm:realm)
                }
                
            }
            
        } catch{
            // Handle any errors
            print("this error is error by creating realm")
        }
```


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

```
//get responseData, feed, entries
var responseData = dict["ResultSet"] as! NSDictionary
```
エラー
```
Cannot subscript a value of type 'NSDictionary?' with an index of type 'String'
```

以下はOK
```
let resultset_key:String = "ResultSet"
let responseData:NSDictionary = (dict?.objectForKey(resultset_key))! as! NSDictionary
```

## Intのcast

```
totalStr?.toInt()
```
ダメ

以下はOK
```
Int(totalStr!)
```

## 文字数のカウント

以下はエラー
```
count(val_name)
```

以下は動く

```
tmpcode.characters.count
```

### reference
https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html#//apple_ref/doc/uid/TP40014097-CH7-ID297



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

## kvoのメソッドのoverride

swift1.2から変化
エラー
```
override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject : AnyObject], context: UnsafeMutablePointer<Void>) {
        
    }  
```

以下は動く
```
override func observeValueForKeyPath(keyPath: String?, ofObject object: AnyObject?, change: [String : AnyObject]?, context: UnsafeMutablePointer<Void>) {
        
    }
```


## NSURLSessionがhttpのリクエストを受け付けない
appleのセキュリティポリシー的な問題で、受け付けないようです。
httpsで叩いたら、うまくいきました

## BITCODE
デフォでYesになってるので、とりあえず、NO
http://stackoverflow.com/questions/30848208/new-warnings-in-ios9

# UITableViewCellが何も表示されない時
これで解決した

https://forums.developer.apple.com/thread/14003
