![Demo](https://github.com/nalydadad/ClipperForUIView/blob/master/demo.png)

```swift
//: Playground - noun: a place where people can play

import UIKit
import PlaygroundSupport

class PullBar: UIView {

    final let minWidth:CGFloat = 10
    final let minHeight:CGFloat = 40
    final let middleRectWidth:CGFloat = 2
    final let middleRectHeight:CGFloat = 30
    
    enum Direction {
        case left, right
        func corners() -> UIRectCorner {
            switch self {
            case .left:
                return [UIRectCorner.topLeft, UIRectCorner.bottomLeft]
            case .right:
                return [UIRectCorner.topRight, UIRectCorner.bottomRight]
            }
        }
    }
    
    private var _direction: Direction = .left
    var direction: Direction {
        get {
            return _direction
        }
        set {
            _direction = newValue
            self.setNeedsDisplay()
        }
    }
    
    override init(frame: CGRect) {
        var checkFrame = frame
        if checkFrame.width < minWidth {
            checkFrame.size.width = minWidth
        }
        if checkFrame.height < minHeight {
            checkFrame.size.height = minHeight
        }
        super.init(frame: checkFrame)
        backgroundColor = UIColor.white.withAlphaComponent(0)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
    
    override func draw(_ rect: CGRect) {
        let path = UIBezierPath(roundedRect: rect, byRoundingCorners: _direction.corners(), cornerRadii: CGSize(width: rect.width, height:rect.height))
        UIColor.yellow.set()
        path.stroke()
        path.fill()
        path.close()
        
        let widthPadding = (rect.width - middleRectWidth) / 2;
        let heightPadding = (rect.height - middleRectHeight) / 2;
        let insetsRect = UIEdgeInsetsInsetRect(rect, UIEdgeInsets(top: heightPadding, left: widthPadding, bottom: heightPadding, right: widthPadding))
        let path2 = UIBezierPath(rect: insetsRect)
        UIColor.darkGray.set()
        path2.fill()
        path2.close()
    }

}

class BoundingRect: UIView {

    final let minWidth: CGFloat = 50
    
    var leftPullBar: PullBar
    var rightPullBar: PullBar

    private var _leftCursor: CGFloat
    var leftCursor: CGFloat {
        get {
            return _leftCursor
        }
        set {
            if _leftCursor > _rightCursor - minWidth {
                return
            }
            _leftCursor = newValue
            self.setNeedsDisplay()
        }
    }
    private var _rightCursor: CGFloat
    var rightCursor: CGFloat {
        get {
            return _rightCursor
        }
        set {
            if newValue < _leftCursor + minWidth {
                return
            }
            _rightCursor = newValue
            self.setNeedsDisplay()
        }
    }
    
    
    override init(frame: CGRect) {
        let initPullBarFrame = CGRect(x: 0, y: 0, width: 0, height: frame.height)
        leftPullBar = PullBar(frame: initPullBarFrame)
        leftPullBar.direction = .left
        
        
        rightPullBar = PullBar(frame: initPullBarFrame)
        rightPullBar.direction = .right
        
        _leftCursor = 0 + leftPullBar.frame.width
        _rightCursor = frame.width - rightPullBar.frame.width
        
        super.init(frame: frame)
        self.addSubview(leftPullBar)
        self.addSubview(rightPullBar)
        backgroundColor = UIColor.white.withAlphaComponent(0)
    }
    
    override func draw(_ rect: CGRect) {
        leftPullBar.frame = CGRect(x: _leftCursor - leftPullBar.frame.width, y: 0, width: leftPullBar.frame.width, height: leftPullBar.frame.height)
        
        rightPullBar.frame = CGRect(x: _rightCursor, y: 0, width: rightPullBar.frame.width, height: rightPullBar.frame.height)
        
        let upPath = UIBezierPath()
        upPath.move(to: CGPoint(x: _leftCursor, y: 0))
        upPath.addLine(to: CGPoint(x: _rightCursor, y: 0))
        UIColor.yellow.set()
        upPath.stroke()
        upPath.close()
        
        let downPath = UIBezierPath()
        downPath.move(to: CGPoint(x: _leftCursor, y: rect.height))
        downPath.addLine(to: CGPoint(x: _rightCursor, y: rect.height))
        UIColor.yellow.set()
        downPath.stroke()
        downPath.close()
    }

    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }

}


let canvas = UIView(frame: CGRect(x: 0, y: 0, width: 400, height: 200))

let demoUIView = UIView(frame: CGRect(x: 0, y: 75, width: 400, height: 49))
demoUIView.backgroundColor = UIColor.white.withAlphaComponent(0.5)
canvas.addSubview(demoUIView)

let br = BoundingRect(frame: CGRect(x: 130, y: 75, width: 160, height: 50))
canvas.addSubview(br)

PlaygroundPage.current.liveView = canvas

```

