---
layout: post
title: "Expandable UITextView"
excerpt: "Create a delightful custom Expandable UITextView to use in our apps."
tags: [NSLayoutAnchor, NSLayoutConstraint, UITextView, UIToolbar, Expandable]
image:
  feature: expandable.jpg
---

> **Abstract**: NSLayoutAnchor, NSLayoutConstraint, UITextView, UIToolbar, Expandable TextView

<figure>
    <a href="https://raw.githubusercontent.com/MoralAlberto/ExpandableTextView/develop/demoImages/expandable.gif"><img src="https://raw.githubusercontent.com/MoralAlberto/ExpandableTextView/develop/demoImages/expandable.gif"></a>
</figure>

Recently, I created a repo on Github called [ExpandableTextView](https://github.com/MoralAlberto/ExpandableTextView). The main purpose of this repo is to add a UITextView within a UIToolbar and expand dynamically the height of this UIToolbar depending on the UITextView’s height. The UITextView’s content size may vary depends on the text introduced. To achieve that, **I used manual layout, with NSLayoutAnchor and NSLayoutConstraint**.

<br/>
**NSLayoutGuide** and **NSLayoutAnchor** were introduced with iOS 9, and the last one is a fluent API to use NSLayoutConstraints. Mention that, whether you understand constraints, they have the same difficulty to create them with code or with Interface Builder.

<br/>
The next snippet shows, how I add the leading and trailing constraint from the UIToolbar to the superview. And the UIToolbar’s bottom to the superview’s bottom. **The heightConstraint was created as a mutable property, we are gonna change this value each time that the keyboard appears (and disappears.)**


> Tip: First of all, you need to add the subviews before you add any constraint, if not, your app will crash at runtime.

{% highlight swift %}
//  Move the container at different heigh of your view. Put 300 instead of 0
heightConstraint = toolBar.bottomAnchor.constraintGreaterThanOrEqualToAnchor(view.bottomAnchor, constant: 0)
heightConstraint.active = true

//  ToolBar Constraints, attach the leading and trailing constraint to our container.
NSLayoutConstraint.activateConstraints([
        toolBar.leadingAnchor.constraintEqualToAnchor(view.leadingAnchor),
        toolBar.trailingAnchor.constraintEqualToAnchor(view.trailingAnchor),
])
{% endhighlight %}

Once the UIToolbar is added and correctly positioned at the leading, trailing and bottom from his superview, you can add the UITextView and a UIButton. Within your UIToolbar subclass:

{% highlight swift %}
//  Add textfield
translatesAutoresizingMaskIntoConstraints = false
addSubview(textView)

//  Add rightButton
rightButton.translatesAutoresizingMaskIntoConstraints = false
rightButton.layer.cornerRadius = 6;
rightButton.setTitle("Send", forState: .Normal)
rightButton.setTitleColor(UIColor.blueColor(), forState: .Normal)
addSubview(rightButton)
{% endhighlight %}

Then, **you apply manual layout to distribute the two UIViews (UITextView and UIButton) inside the UIToolbar.**

{% highlight swift %}
//  UIBUTTON Constraints, we want the button to be centered with the toolbar and with a trailing space, the standard one (8).
NSLayoutConstraint.activateConstraints([
      rightButton.trailingAnchor.constraintEqualToAnchor(trailingAnchor, constant: -8),
      rightButton.bottomAnchor.constraintEqualToAnchor(bottomAnchor, constant: -8)
])

//  TEXTVIEW Constraints, this is the last one, we need to attach the TEXTVIEW to the TOOLBAR, so they has the same leading, top and bottom constraints, and a trailing space between the trailing textfield and the leading button.
NSLayoutConstraint.activateConstraints([
      textView.leadingAnchor.constraintEqualToAnchor(layoutMarginsGuide.leadingAnchor),
      textView.trailingAnchor.constraintEqualToAnchor(rightButton.leadingAnchor, constant: -8),    
      textView.topAnchor.constraintEqualToAnchor(topAnchor, constant: 8),
      textView.bottomAnchor.constraintEqualToAnchor(bottomAnchor, constant: -8)
])
{% endhighlight %}

Here appears something new, you can add a constraint to the margins' view. The UITextView has been attached to the UIToolbar margins leading. It means that, the constraint has a little gap (standard value: 8) from the UIToolbar leading. Also, the UITextView  trailing is attached to UIButton leading (with a constant to separate between them). The Bottom and Top are attached to the UIToolbar.

{% highlight swift %}
//  We decide to create a public var to modify the toolBar heigh constant, it could increase depending on the TEXTVIEW's heigh
toolBarHeighConstraint = NSLayoutConstraint(item: self, attribute: NSLayoutAttribute.Height, relatedBy: .GreaterThanOrEqual, toItem: nil, attribute: NSLayoutAttribute.NotAnAttribute, multiplier: 1.0, constant: 44)
toolBarHeighConstraint.active = true
{% endhighlight %}

One of the most important constraints, is the **toolbarHeightConstraint, when the UITextView content size change, we need to update this constraint to expand or shrink**. Once, we create the NSLayoutConstraint, we need to activate in order to use.

<br/>
In my UIViewController subclass, there is a delegate method:

{% highlight swift %}
//MARK: TextView Delegate
func notifyParentView(withHeigh: CGFloat) {
    toolBar.toolBarHeighConstraint.constant = (withHeigh > 39.0) ? withHeigh + 16.0 : 39.0;

    UIView.animateWithDuration(0.5, delay: 0, options: .TransitionCurlDown, animations: {
        //  With layoutIfNeeded we can see the transition, if we don't add this method, you will only see a jump to the specified position.
        self.view.layoutIfNeeded()
    }, completion: nil)
}
{% endhighlight %}

**This method is called every time the content size of our UITextView did change.** Because a new height has to be calculated, to add or remove a new line in our UITextView, To calculate this content size I use the next method:

{% highlight swift %}
func didChangeTextViewText(notification: NSNotification) {
    let textView = notification.object as? UITextView
    let numberOfLines = self.numberOfLines()

    guard ((textView?.isEqual(self)) == nil) || numberOfLines <= maxNumberOfLines else {
        return
    }

    if self.frame.height > minimumHeight || numberOfLines > 2 {
        var ownHeight = self.contentSize.height
        if numberOfLines == 1 {
            let size = CGSizeMake(CGRectGetWidth(self.bounds), 10000)
            ownHeight = self.sizeThatFits(size).height
        }

        var ownFrame = self.frame
        ownFrame.size = CGSizeMake(CGRectGetWidth(self.frame), ownHeight)
        self.frame = ownFrame
        self.scrollRangeToVisible(NSMakeRange(self.text.characters.count, 0))

        //  Notify to the delegate only when is needed
        self.customDelegate?.notifyParentView(self.frame.height)
    }
}
{% endhighlight %}

**The next step will be create a new repo with UICollectionViewLayout, simulating a chat conversation.**

> Tip: example using NSLayoutConstraint:

{% highlight swift %}
NSLayoutConstraint(item: textView, attribute: NSLayoutAttribute.Height, relatedBy: .GreaterThanOrEqual, toItem: nil, attribute: NSLayoutAttribute.NotAnAttribute, multiplier: 1.0, constant: 39).active = true
{% endhighlight %}

In the previous NSLayoutConstraints you need to fill a lot of input parameters (a lot of them), and then activate every NSLayoutConstraint. With the next snippet you reduce the amount of code:

{% highlight swift %}
NSLayoutConstraint.activateConstraints([
        textView.leadingAnchor.constraintEqualToAnchor(toolBar.layoutMarginsGuide.leadingAnchor),
        textView.trailingAnchor.constraintEqualToAnchor(rightButton.leadingAnchor, constant: -8),
        textView.topAnchor.constraintEqualToAnchor(toolBar.topAnchor, constant: 8),
        textView.bottomAnchor.constraintEqualToAnchor(toolBar.bottomAnchor, constant: -8)
])
{% endhighlight %}

As you can see, with NSLayoutAnchor you can add and activate multiple NSLayoutConstraints, with not as many parameters as with NSLayoutConstraint. It’s a quite awesome solution if you had many constraints to add in your view (This fluent API was introduced with iOS 9).

<br/>
The UIToolbar is added at the bottom, and inside it there’s a custom UITextView and UIButton. At first we add UILayoutGuide and inside it we add the UIToolbar.

<br/>
A user can customize the number of maximum lines. I’m gonna show and example in a bit.
There’re three basic components: UIToolbar, UITextView and UIButton.
The InputViewController is the base class, you can inherit from it, like you do with UIViewControllers.
