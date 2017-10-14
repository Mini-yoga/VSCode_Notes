```swift
    private func addTimer() {
        timer = NSTimer(timeInterval: 2, target: self, selector: "scrollToNextPhoto", userInfo: nil, repeats: true)
        NSRunLoop.mainRunLoop().addTimer(timer!, forMode: NSDefaultRunLoopMode)//循环
    }
    
    private func scrollToNextPhoto() {
        collectionView.scrollToItemAtIndexPath( ++currentIndex, atScrollPosition: UICollectionViewScrollPosition.Left, animated: true)
    }
    
    private func removeTimer(){
        timer?.invalidate()
    }
```