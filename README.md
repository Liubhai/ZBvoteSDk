# ZBvoteSDk
###ZBvoteSDk 概述

ZBvoteSDk是基于ZBSmartLiveSDK的0.1.18版本的一个适用于iOS平台快速集成直播发起投票和投票功能的SDK,配合智播云后台数据可及时获取+投票信息。ZBvoteSDk提供了定制化的UI，UI相对的坐标调整。(备注:该SDK只支持在真机环境下调试发布,仅支持ARC环境和object-c工程)

###开发环境配置
首先需要导入依赖库ZBSmartLiveSDK的0.1.18版本，您的工程里面的frameworks 需要导入UIKit库。然后将ZBvoteSDK里面的所有文件手动下载到本地，再手动将所有文件导入到您的工程里面即可。

###功能描述（在直播间）

1.主播界面：发起投票及时显示投票信息
2.观众界面：获取、显示投票信息；根据投票的信息（选项）投票给某选项

###ZBvoteSDK内部文件提供的类（代理协议）和方法：
1.VoteView.h 投票信息里面每个投票选项视图

    1）代理方法- (void)GetWith:(CGFloat)with;用于获取自适应宽度的VoteView的宽度

    2）代理方法- (void)showVoteView;用于观众界面点击VoteView里面的视图层次最 上层的titleButton的时候触发此代理方法，然后触发AnchorVotingView.h里面的- (void)showVoteChooseView代理方法弹出观众投票视图

2.AnchorApplyView.h 主播界面发起（编辑）投票视图

    1）代理方法- (void)createvoteView :(NSMutableArray *)arr :(BOOL)flag :(NSString *)time :(NSString *)voteid;此代理方法是在主播在AnchorApplyView（主播发起投票编辑视图）里面编辑好了投票内容信息后点击发起投票并且接口请求成功之后执行此代理方法，目的是创建主播成功发起投票之后的投票信息视图。

3.AnchorVotingView.h当前投票正在进行视图

    1）代理方法- (void)createVoteResult:(NSMutableArray *)voteViewArr;根据当前投票信息的VoteView来创建投票结束后的视图，在当前情况下（倒计时结束后）触发此代理。
    2）代理方法- (void)showVoteChooseView;观众界面弹起投票视图
    3）方法- (void)createVotingView:(NSMutableArray *)arr :(BOOL)isflag :(NSString *)timestr :(CGFloat)y;通过传入参数投票信息数据里面的options（投票选项信息）数组、投票时长、以及您想设置的AnchorVotingView的Y坐标来创建AnchorVotingView视图。
    4）方法- (void)setValue:(NSMutableArray *)arr;根据投票信息数据接口请求到的数据里面的options（投票选项信息）数组来对当前AnchorVotingView视图里面的每个VoteView的各个属性赋值，以此来达到改变进度条的目的。
 4.AnchorvoteResultView.h 每轮投票结束后的投票结果视图
 
    1）代理方法- (void)shouqiView:(CGRect)frames;投票结果视图AnchorvoteResultView里面的”收起投票结果视图“按钮的点击事件代理方法。顾名思义就是点击后可以将投票结果视图收起来。
    2）方法- (NSMutableArray *)getdataArr:(NSMutableArray *)arr;目的是处理当观众首次进入直播间的时候，投票已经结束但又没有新的投票正在进行，这样的情况下来创建投票结果视图AnchorvoteResultView，此方法目的是放回一个数组，数组里面元素是VoteView，VoteView的个数以及每个VoteView的属性信息依据要显示的那次投票数据信息里面的options（投票选项信息）数组来决定。
    3）方法- (void)createVoteResultView :(NSMutableArray *)arr :(CGFloat)y;根据投票结果信息里面options（投票选项信息）数组所创建的，里面元素是VoteView的数组以及自己设置的结果视图的Y坐标来创建结果视图
