```swift
func AlamofireForData() {
        
        Alamofire.request(.GET, "http://m2.qiushibaike.com/article/list/text?").responseJSON{ (JSONData) -> Void in
            
            guard let JsonStr = JSONData.result.value
                else{ return }
            let JsonValue = JSON(JsonStr)
            let result = JsonValue["items"]
            for (st,Dic) in result
            {
                let model = HomePageDataModel()
                model.login = Dic["user","login"].string
                model.id = Dic["id"].string
                model.up = Dic["votes","up"].int
                model.content = Dic["content"].string
                model.comments_count = Dic["comments_count"].int
                self.dataArray.append(model)
                
            }
            print(self.dataArray)
            self.tableView.reloadData()
        }
        
    }
```

要写在请求的范围里面，因为这是另外的线程 ，如果写在最外面的{}里，那么在请求的同时就立即执行了reloadData函数，然而函数需要依赖请求中的数据，会报错有解包会nil的情况