必须设置的三个数据代理：

```swift
extension ViewController: UITableViewDataSource{

    //返回一共有多少组,返回0的时候后面的方法都不会继续执行

    func numberOfSectionsInTableView(tableView: UITableView) -> Int{
        return dataArray != nil ? dataArray!.count : 0
    }
    
    //返回当前section一共有多少行
    func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int{
    return dataArray![section].car!.count
    }
    
    //返回当前行的tableViewCell
    func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell{
    let cell = tableview.dequeueReusableCellWithIdentifier(Indentifier.mainCell, forIndexPath: indexPath)
        cell.textLabel?.text = dataArray![indexPath.section].car![indexPath.row]
        return cell
    }
}

```