```swift
tableView.estimatedRowHeight = 200//估算高度
tableView.rowHeight = UITableViewAutomaticDimension
```

---

### 

### `​`​`​`​驱动 UITableViewCell 适应 Label 内容：

### 使用 estimatedHeightForRowAtIndexPath 替代 heightForRowAtIndexPath

### estimate`​`dHeightForRowAtIndexPath 是 iOS 7 推出的新 API。如果列表行数有一万行，那么 heightForRowAtIndexPath 就会列表显示之前计算一万次，而 estimatedHeightForRowAtIndexPath 只会计算当前屏幕中显示着的几行，会大大提高数据量很大时候的性能。

```swift
override func tableView(tableView: UITableView, estimatedHeightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
    let cell = prototypeCell
    cell.firstLabel.text = labelArray[indexPath.row]
    return cell.contentView.systemLayoutSizeFittingSize(UILayoutFittingCompressedSize).height + 1
}

```

<https://lvwenhan.com/ios/449.html>

![pic icon](https://staticonsae.sinaapp.com/images/6.gif)