---
layout: post
title:  "iOS约束布局动态计算view高度"
date:   2018-08-08 10:16:01 +0800
categories: iOS
tags:
  - 约束
  - iOS
  - 布局
---
在一些场景我们需要指定高度的场景, 比如`tableheaderview` , 如果用约束布局, 那么用以下方法计算即可.
>view的细节跟动态布局的cell和scrollview一样, 约束必须明确每个view的高度并且距离边界不能模糊.

**核心代码**
```objectivec
- (CGFloat)contentViewFittingSize:(UIView *)contentView {
    // 获得父容器的宽度，我这里是获取控制器View的宽度
    CGFloat contentViewWidth = CGRectGetWidth(self.view.frame);
    // 新建一个宽度约束
    NSLayoutConstraint *widthFenceConstraint = [NSLayoutConstraint constraintWithItem:contentView attribute:NSLayoutAttributeWidth relatedBy:NSLayoutRelationEqual toItem:nil attribute:NSLayoutAttributeNotAnAttribute multiplier:1.0 constant:contentViewWidth];
    // 添加宽度约束
    [contentView addConstraint:widthFenceConstraint];
    // 获取约束后的size
    CGSize fittingSize = [contentView systemLayoutSizeFittingSize:UILayoutFittingCompressedSize];
    // 记得移除
    [contentView removeConstraint:widthFenceConstraint];
    return fittingSize.height;
}
```