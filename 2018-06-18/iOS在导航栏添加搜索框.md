### 记录一个iOS小坑

> 在做一个二手交易平台的demo的时候，想在首页的导航栏加入一个搜索框的内容，结果发现里面的坑还是挺多的。 

#### UISearchBar

**UISearchBar**是Apple官方的一个搜索控件，在网上查了怎么在导航栏添加搜索框之后发现，可以在NavigationBar的子View`titleView`里面把自己创建的搜索框添加进去。 

但是，这里就有个问题了，如果直接把创建的**searchBar**添加到**titleView**里面的话，会发现，无论你怎么修改，都无法改变**searchBar**的位置，所以很不好看。

#### 解决办法

可以创建一个**UIview**来做**searchBar**的背景View，然后把让titleview = 这个View。这样做还有一个很大的问题就是，你直接让searchBar作为titleView的话是不会出现灰色的背景框的。但是这样做的话会出现灰色的背景框，就不能和导航栏融为一体了。通过多次修改，发现一个可行的办法，通过查看searchBar的结构，发现它是有一个背景View的，所以通过遍历把这个背景移除就行了。代码如下：

```objective-c
   for (UIView *view in self.mySearchBar.subviews) {
       
        if ([view isKindOfClass:NSClassFromString(@"UIView")] && view.subviews.count > 0) {
            [[view.subviews objectAtIndex:0] removeFromSuperview];
            break;
        }
    }
```

移除背景后，通过修改searchBar的位置就可以达到你想要的效果了，因为这是相对之前创建的背景View的位置。再将之前的背景View的颜色设置为透明色就行了。

#### 一些小细节

```objective-c
    [[UIBarButtonItem appearanceWhenContainedIn: [UISearchBar class], nil] setTintColor:[UIColor whiteColor]];
    [[UIBarButtonItem appearanceWhenContainedIn: [UISearchBar class], nil] setTitle:@"取消"];
```

通过这两行代码可以可以修改自带的取消按钮的颜色和名字。



```objective-c
- (void)searchBarCancelButtonClicked:(UISearchBar *)searchBar
{
    [searchBar setShowsCancelButton:NO animated:YES];
    [searchBar resignFirstResponder];
}

- (void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar {
    /* 点击button时以动画效果出现cancelButton */
    [searchBar setShowsCancelButton:YES animated:YES];
}
```

通过这两个代理方法则可以实现点击搜索框出现取消按钮，点击取消按钮还原。

最终效果如下：

![](https://wx1.sinaimg.cn/mw690/bda8daacgy1fruouiov6xj20ku112acb.jpg)

![](https://wx2.sinaimg.cn/mw690/bda8daacgy1fruouiqu2lj20ku11241p.jpg)