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
    3）方法- (void)createVoteResultView :(NSMutableArray *)arr :(CGFloat)y;根据投票结果信息里面options（投票选项信息）数组所创建的，里面元素是VoteView的数组以及自己设置的结果视图的Y坐标来创建结果视图。

###以下是您所需添加主播端发起投票功能的VC里面实例代码
在m文件中包含以下头文件
```python
#import "AnchorApplyView.h"
#import "AnchorVotingView.h"
#import "AnchorvoteResultView.h"
```
然后遵循其代理
```python
AnchorApplyViewDelegate
AnchorVotingViewDelegate
AnchorvoteResultViewDelegate
```
需要在m文件里面设置以下变量
```python
NSTimer *commentTimer;//评论信息刷新定时器
CGFloat VoteBGViewWith;
NSMutableArray *dataArr;
BOOL flag;//no是当前第一次进入直播间的时候的计时器 yes不是
NSString *vote_id;//投票id
NSString *timeStr;//当前选择的时长
```
然后设置属性
```python
@property (nonatomic, strong) AnchorApplyView *applyView;
@property (nonatomic, strong) AnchorvoteResultView *resultView;
@property (nonatomic, strong) AnchorVotingView *votingView;
@property (nonatomic, strong) UIButton *sponsorVoteButton;//弹出投票视图按钮
@property (nonatomic, strong) UIButton *voteResult;//投票结果按钮
```
其次要在viewDidLoad里面添加以下代码
```python
    VoteBGViewWith = 0;
    dataArr = [[NSMutableArray alloc] init];
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(changeVoteButtonState:) name:@"VOTEBUTTONCANCLICK" object:nil];
    _sponsorVoteButton = [[UIButton alloc] initWithFrame:CGRectMake(ScreenWidth - 40 - 40, ScreenHeight - 40, 40, 40)];
    [_sponsorVoteButton.layer setMasksToBounds:YES];
    _sponsorVoteButton.layer.cornerRadius = 20.0;
    [_sponsorVoteButton setBackgroundImage:[UIImage imageNamed:@"host_btn_vote_normal"] forState:UIControlStateNormal];
    [_sponsorVoteButton addTarget:self action:@selector(createVote) forControlEvents:UIControlEventTouchUpInside];
    [self.view addSubview:_sponsorVoteButton];
    [self getNewVotetestData];
```
再在viewWillDisappear里面添加以下代码
```python
if(commentTimer && commentTimer.isValid) {
        [commentTimer invalidate];
        commentTimer = nil;
    }
    [[NSNotificationCenter defaultCenter] postNotificationName:@"STOPTIMER" object:nil];
```
将以下代码拷贝进m文件里面
```python
#pragma mark -- VoteViewDelegate
- (void)GetWith:(CGFloat)with{
    if (VoteBGViewWith<with) {
        VoteBGViewWith = with;
    }
}

- (void)getNewVotetestData{
    NSDictionary *dict = @{@"usid":User_Info_Global.userInfo.basicInfo.usid};
    [ZBCloudData getZBCloudDataWithApi:@"ZBCloud_Apps_Vote_GetUserLastVote" parameter:dict success:^(id data) {
        //
        if (data && [data isKindOfClass:[NSDictionary class]]) {
            NSLog(@"最后投票信息%@",data);
            [dataArr removeAllObjects];
            [dataArr addObjectsFromArray:[data objectForKey:@"options"]];
            vote_id = [data objectForKey:@"vote_id"];
            if ([[data objectForKey:@"status"] integerValue] == 0) {
                //投票结束timeStr传空值
                timeStr = nil;
            }else if ([[data objectForKey:@"status"] integerValue] == 1){
                //投票正在进行timeStr传 请求到的剩余时间(投票总时长（秒）- （当前本地时间 - 创建投票时候的本地时间）)
                NSDate *dat = [NSDate date];
                NSDate *voteDate = [NSDate dateWithTimeIntervalSince1970:[[data objectForKey:@"create_time"] integerValue]];
                NSTimeInterval a = [dat timeIntervalSinceDate:voteDate];  //  *1000 是精确到毫秒，不乘就是精确到秒
                int t = [[data objectForKey:@"time"] intValue] * 60 - (int)a;
                timeStr = [NSString stringWithFormat:@"%d",t];
            }else{
                //投票临时关闭
            }
            flag = YES;
            [self createvoteView:dataArr :flag :timeStr :vote_id];
        }
    } fail:^(NSError *fail) {
        //
        
    }];
}
- (void)createVote{
    _applyView = [[AnchorApplyView alloc] init];
    _applyView.delegate = self;
    _applyView.userInteractionEnabled = YES;
    [self.view addSubview:_applyView];
}
- (void)createvoteView:(NSMutableArray *)arr :(BOOL)isflag :(NSString *)time :(NSString *)voteID{
    CGFloat y = 64.0;//视图y坐标
    if (_applyView) {
        [_applyView removeFromSuperview];
        _applyView = nil;
    }
    if (_resultView) {
        [_resultView removeFromSuperview];
        _resultView= nil;
    }
    if (_voteResult) {
        [_voteResult removeFromSuperview];
        _voteResult = nil;
    }
    _votingView = [[AnchorVotingView alloc] init];
    _votingView.delegate = self;
    [_votingView createVotingView:arr :isflag :time :y];
    [self.view addSubview:_votingView];
    vote_id = voteID;
    if (!commentTimer) {
        commentTimer = [NSTimer timerWithTimeInterval:5 target:self selector:@selector(getNewData) userInfo:nil repeats:YES];
        [[NSRunLoop  currentRunLoop] addTimer:commentTimer forMode:NSDefaultRunLoopMode];
        [commentTimer fire];
    }
}
- (void)createVoteResult:(NSMutableArray *)voteViewArr{
    if (_votingView) {
        [_votingView removeFromSuperview];
        _votingView = nil;
    }
    _resultView = [[AnchorvoteResultView alloc] init];
    _resultView.delegate = self;
    [_resultView createVoteResultView:voteViewArr :64.0];
    [self.view addSubview:_resultView];
    if(commentTimer && commentTimer.isValid) {
        [commentTimer invalidate];
        commentTimer = nil;
    }
}
- (void)shouqiView:(CGRect)frames{
    [UIView animateWithDuration:0.4 animations:^{
        //
        CGRect frame = frames;
        CGFloat h = frame.size.height;
        _resultView.frame = CGRectMake(8.0, 64.0, 0.f, h);
    } completion:^(BOOL finished) {
        //
        [_resultView removeFromSuperview];
        _voteResult = [[UIButton alloc] initWithFrame:CGRectMake(0.f, 64.0, 20, 55)];
        [_voteResult.layer setMasksToBounds:YES];
        _voteResult.layer.cornerRadius = 2.5;
        _voteResult.backgroundColor = [UIColor colorWithWhite:0 alpha:0.6];
        [_voteResult setTitle:[NSString stringWithFormat:@"\n投\n票\n结\n果\n"] forState:UIControlStateNormal];
        [_voteResult setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
        _voteResult.titleLabel.font = [UIFont systemFontOfSize:10];
        _voteResult.titleLabel.numberOfLines = _voteResult.titleLabel.text.length;
        [_voteResult addTarget:self action:@selector(showVoteResultView) forControlEvents:UIControlEventTouchUpInside];
        [self.view addSubview:_voteResult];
    }];
}
- (void)showVoteResultView{
    CGRect frame = _resultView.frame;
    CGFloat h = frame.size.height;
    CGFloat w = 10 + 120.0 + 5 + 10 + 3.0 + VoteBGViewWith + 5 + 25.0;
    [_voteResult removeFromSuperview];
    _voteResult = nil;
    [UIView animateWithDuration:0.4 animations:^{
        _resultView.frame = CGRectMake(8.0, 64.0, w, h);
        [self.view addSubview:_resultView];
    }];
}
- (void)changeVoteButtonState:(NSNotification *)notice{
    
    if ([[notice.userInfo objectForKey:@"flag"] isEqualToString:@"can"]) {
        //
        _sponsorVoteButton.enabled = YES;
        [_sponsorVoteButton setBackgroundImage:[UIImage imageNamed:@"host_btn_vote_normal"] forState:UIControlStateNormal];
    }else if ([[notice.userInfo objectForKey:@"flag"] isEqualToString:@"no"]){
        //
        _sponsorVoteButton.enabled = NO;
        [_sponsorVoteButton setBackgroundImage:[UIImage imageNamed:@"host_btn_vote_unable"] forState:UIControlStateNormal];
    }else{
        
    }
}
- (void)getNewData{
    NSDictionary *dict = @{@"vote_id":vote_id};
    [ZBCloudData getZBCloudDataWithApi:@"ZBCloud_Apps_Vote_GetInfo" parameter:dict success:^(id data) {
        //
        if (data && [data isKindOfClass:[NSDictionary class]]) {
            NSLog(@"最新投票信息%@",data);
            [dataArr removeAllObjects];
            [dataArr addObjectsFromArray:[data objectForKey:@"options"]];
            vote_id = [data objectForKey:@"vote_id"];
            if ([[data objectForKey:@"status"] integerValue] == 0) {
                //投票结束timeStr传空值
                timeStr = nil;
                if(commentTimer && commentTimer.isValid) {
                    [commentTimer invalidate];
                    commentTimer = nil;
                }
            }else if ([[data objectForKey:@"status"] integerValue] == 1){
                if (_votingView) {
                    [_votingView setValue:dataArr];
                }
            }else{
                //投票临时关闭
                if(commentTimer && commentTimer.isValid) {
                    [commentTimer invalidate];
                    commentTimer = nil;
                }
            }
        }
    } fail:^(NSError *fail) {
        //
    }];
}
```
