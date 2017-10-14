```swift
class ViewController: UIViewController {
    
    var middleViewTopSpaceLayoutConstant: CGFloat!
    var middleViewOriginY: CGFloat!
    
    @IBOutlet weak var middleView: UIView!//中间可移动的view
    @IBOutlet weak var middleViewTopSpaceLayout: NSLayoutConstraint!//原本中间view与主view的上约束
    @IBOutlet var panGesture: UIPanGestureRecognizer!//拖入的手势
    override func viewDidLoad() {
        super.viewDidLoad()
        
        panGesture.addTarget(self, action: Selector("pan"))//添加手势
        middleViewTopSpaceLayoutConstant = middleViewTopSpaceLayout.constant//保存约束长度
        middleViewOriginY = middleView.frame.origin.y//保存view的Y坐标
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    func pan() {
        if panGesture.state == UIGestureRecognizerState.Ended {//当手势结束后
            UIView.animateWithDuration(0.4, delay: 0, options: UIViewAnimationOptions.CurveEaseInOut, animations: { () -> Void in
                self.middleView.frame.origin.y = self.middleViewOriginY
                }, completion: { (success) -> Void in
                    if success {
                        self.middleViewTopSpaceLayout.constant = self.middleViewTopSpaceLayoutConstant
                    }
            })
            return
        }
        //当手势向下拉与放开恢复的时候
        let y = panGesture.translationInView(self.view).y//获取改变的y值
        middleViewTopSpaceLayout.constant = middleViewTopSpaceLayoutConstant + y//设置变化值
    }

}
```

![Pic icon](https://staticonsae.sinaapp.com/images/1.gif)

