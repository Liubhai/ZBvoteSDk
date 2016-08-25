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

    1）代理方法
```python
    - (void)GetWith:(CGFloat)with;//代理获取当前所有投票选项信息视图里面最大的宽度，用于在投票结束后创建收起按钮的时候设置按钮的横坐标
```

    2）代理方法
```python
- (void)showVoteView;//用于观众界面点击VoteView里面的视图层次最 上层的titleButton的时候触发此代理方法，然后触发AnchorVotingView.h里面的- (void)showVoteChooseView代理方法弹出观众投票视图
```

2.AnchorApplyView.h 主播界面发起（编辑）投票视图

    1）代理方法
```python
/**
 *  根据主播对发起的投票的编辑内容来创建投票视图
 *
 *  @param arr    投票内容选项
 *  @param flag   yes
 *  @param time   编辑的时候选择的时间单位是min
 *  @param voteid 此次编辑的投票id
 */
- (void)createvoteView :(NSMutableArray *)arr :(BOOL)flag :(NSString *)time :(NSString *)voteid;
```

3.AnchorVotingView.h当前投票正在进行视图

    1）代理方法:根据当前投票信息的VoteView来创建投票结束后的视图，在当前情况下（倒计时结束后）触发此代理。
```python
/**
 *  根据倒计时结束的时候创建投票结果视图
 *
 *  @param voteViewArr 当前投票倒计时结束的时候存在的VoteView数组（保存的是当前投票里面显示的所有VoteView）
 *  @param h           当前voteViewArr里面的所有VoteView的最大的宽度
 */
- (void)createVoteResult:(NSMutableArray *)voteViewArr :(CGFloat)h;
```
    2）代理方法:观众界面弹起投票视图
```python
/**
 *  弹起观众投票列表视图
 *
 *  @param chooseArr 当前投票时候存在的VoteView数组（保存的是当前投票里面显示的所有VoteView）
 */
- (void)showVoteChooseView:(NSMutableArray *)chooseArr;
```
    3）方法:通过传入参数投票信息数据里面的options（投票选项信息）数组、投票时长、以及您想设置的AnchorVotingView的Y坐标来创建AnchorVotingView视图。
```python
/**
 *  创建投票正在进行中的情况下的投票信息视图
 *
 *  @param arr     投票信息接口请求得到的关于所有选项的数据数组
 *  @param isflag  yes
 *  @param timestr 倒计时的时间
 *  @param y       在VC上面的纵坐标
 */
- (void)createVotingView:(NSMutableArray *)arr :(BOOL)isflag :(NSString *)timestr :(CGFloat)y;
```
    4）方法:根据投票信息数据接口请求到的数据里面的options（投票选项信息）数组来对当前AnchorVotingView视图里面的每个VoteView的各个属性赋值，以此来达到改变进度条的目的。
```python
/**
 *  为投票选项里面的所有voteView的属性赋值
 *
 *  @param arr 投票信息接口请求得到的关于所有选项的数据数组
 */
- (void)setValue:(NSMutableArray *)arr;
```
 4.AnchorvoteResultView.h 每轮投票结束后的投票结果视图
 
    1）代理方法:投票结果视图AnchorvoteResultView里面的”收起投票结果视图“按钮的点击事件代理方法。顾名思义就是点击后可以将投票结果视图收起来。
```python
/**
 *  收起投票结果视图目的是做动画效果
 *
 *  @param frames 当前投票结束视图的frame
 */
- (void)shouqiView:(CGRect)frames;
```
    2）方法:目的是处理当观众首次进入直播间的时候，投票已经结束但又没有新的投票正在进行，这样的情况下来创建投票结果视图AnchorvoteResultView，此方法目的是放回一个数组，数组里面元素是VoteView，VoteView的个数以及每个VoteView的属性信息依据要显示的那次投票数据信息里面的options（投票选项信息）数组来决定。
```python
/**
 *  通过请求到的当前投票信息的选项数据数组来创建每个VoteView，并存放在一个数组里面返回
 *
 *  @param arr 通过请求到的当前投票信息的选项数据数组
 *
 *  @return 存放有当前投票所有选项的对应的voteView
 */
- (NSMutableArray *)getdataArr:(NSMutableArray *)arr;
```
    3）方法:根据投票结果信息里面options（投票选项信息）数组所创建的，里面元素是VoteView的数组以及自己设置的结果视图的Y坐标来创建结果视图。
```python
/**
 *  根据当前投票所有选项的对应的voteView来创建投票结果视图
 *
 *  @param arr    存放有当前投票所有选项的对应的voteView的数组
 *  @param y      在VC上面的纵坐标
 *  @param height 当前投票voteView里面最大视图宽度
 */
- (void)createVoteResultView :(NSMutableArray *)arr :(CGFloat)y :(CGFloat)height;
```
5.VoteChooseView.h观众界面弹起的投票列表视图

    1)代理：选择送礼物给哪个选项
```python
/**
 *  选择投票（送礼物）给某个选项
 *
 *  @param option 选项key例如"A"
 */
- (void)chooseGift:(NSString *)option;
```
    2)方法：根据当前投票选项来创建观众投票列表视图
```python
/**
 *  根据当前的VC界面显示的所有选项创建观众投票选择列表视图
 *
 *  @param chooseArr 当前投票的选项数组
 */
- (void)createChooseVoteView:(NSMutableArray *)chooseArr;
```
6.VoteGetNewData.h里面接口说明
```python
/**
 *  进入直播间获取最新投票信息
 *
 *  @param voteID     当前直播间主播usid
 *  @param completion 获取结果的回调，如果正常的话返回data数据
 */
- (void)getNewVoteDataWithVoteID:(NSString *)voteID
                      completion:(void(^)(id data))completion
                           error:(void(^)(NSError *error))error;
```
| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| voteID     | 当前直播间主播usid |   nsstring     |
| data     |   请求成功后返回的数据   |   NSDictionary   |
| error        |    请求失败  |  nil  |

请求后返回的结果

| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| data     | 请求成功返回的数据 |      NSDictionary  |

data里面数据样式
```python
{
    "code":"00000",
    "data":{
        "vote_id":"v-qT3ZBDHZESsQmSonE",
        "create_time":"1471230882",
        "title":"",
        "time":0,
        "ballot":89,
        "status":1,
        "options":[
            {
                "option_key":"op_1",
                "option_value":"选项1",
                "option_ballot":15
            },
            {
                "option_key":"op_2",
                "option_value":"选项2",
                "option_ballot":74
            }
        ]
    }
}
```
| 字段	|类型|	中文描述|	附加信息|
| -----   | -----  | ----  |----|
|vote_id	|string|	创建的投票项目标识	|该标识已经采用动态加密|
|create_time|	string|	创建时间|	时间戳|
|title|	string|	创建的投票项目名|	
|time	|int|	投票持续时间|	单位:分钟|
|ballot	|int|	投票的总票数|	
|status	|int|	投票项目状态|	1:正在投票 2:投票临时关闭 0:投票结束|
|options|	array|	包含所有的投票选项	|
|option_key	|string	|投票选项key	|
|option_value	|string	|投票选项name	|
|option_ballot|	int|	投票数	|
```python
/**
 *  获取当前正在投票的投票信息
 *
 *  @param voteID     当前次投票的id
 *  @param completion 请求成功后返回数据data
 *  @param error      失败返回nil
 */
- (void)getVotingDataWithVoteID:(NSString *)voteID
                     completion:(void(^)(id data))completion
                          error:(void(^)(NSError *error))error;
```
| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| voteID     | 当前直播间投票id |   nsstring     |
| data     |   请求成功后返回的数据   |   NSMutableArray或者nsstring   |
| error        |    请求失败  |  nil  |

请求后返回的结果

| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| data     | 请求成功返回的数据 |      NSMutableArray或者nsstring  |
data里面数据样式
```python
"options":[
            {
                "option_key":"op_1",
                "option_value":"选项1",
                "option_ballot":15
            },
            {
                "option_key":"op_2",
                "option_value":"选项2",
                "option_ballot":74
            }
```
| 字段	|类型|	中文描述|	附加信息|
| -----   | -----  | ----  |----|
|status	|int|	投票项目状态|	1:正在投票 2:投票临时关闭 0:投票结束|
|options|	array|	包含所有的投票选项	|
|option_key	|string	|投票选项key	|
|option_value	|string	|投票选项name	|
|option_ballot|	int|	投票数	|

###以下是您所需添加主播端发起投票功能的VC里面实例代码
在m文件中包含以下头文件
```python
#import "VoteView.h"
#import "AnchorApplyView.h"
#import "AnchorVotingView.h"
#import "AnchorvoteResultView.h"
#import "VoteGetNewData.h"
#import "VoteResultButton.h"
```
然后遵循其代理
```python
AnchorApplyViewDelegate
AnchorVotingViewDelegate
AnchorvoteResultViewDelegate
```
需要在m文件里面设置以下变量
```python
NSTimer *commentTimer;//最新投票信息数据刷新定时器
NSString *vote_id;//投票id
```
然后设置属性
```python
@property (nonatomic, strong) UIButton *sponsorVoteButton;//弹出投票视图按钮
@property (nonatomic, strong) VoteResultButton *voteResult;//投票结果按钮
@property (nonatomic, strong) AnchorApplyView *applyView;
@property (nonatomic, strong) AnchorvoteResultView *resultView;
@property (nonatomic, strong) AnchorVotingView *votingView;
```
其次要在viewDidLoad里面添加以下代码（创建VC上的一个按钮来作为主播发起投票的触发事件）
```python
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
- (void)getNewVotetestData{
    VoteGetNewData *newData = [[VoteGetNewData alloc] init];
    [newData getNewVoteDataWithVoteID:User_Info_Global.userInfo.basicInfo.usid completion:^(id data) {
        if (data && [data isKindOfClass:[NSDictionary class]]) {
            if ([[data objectForKey:@"timeStr"] isEqualToString:@""]) {
                [self createvoteView:[data objectForKey:@"dataArr"] :[data objectForKey:@"flag"] :nil :[data objectForKey:@"vote_id"]];
            }else{
                [self createvoteView:[data objectForKey:@"dataArr"] :[data objectForKey:@"flag"] :[data objectForKey:@"timeStr"] :[data objectForKey:@"vote_id"]];
            }
        }
    } error:^(NSError *error) {
        NSLog(@"超时");
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
- (void)createVoteResult:(NSMutableArray *)voteViewArr :(CGFloat)h{
    if (_votingView) {
        [_votingView removeFromSuperview];
        _votingView = nil;
    }
    _resultView = [[AnchorvoteResultView alloc] init];
    _resultView.delegate = self;
    [_resultView createVoteResultView:voteViewArr :64.0 :h];
    [self.view addSubview:_resultView];
    if(commentTimer && commentTimer.isValid) {
        [commentTimer invalidate];
        commentTimer = nil;
    }
}
- (void)shouqiView:(CGRect)frames{
    [UIView animateWithDuration:0.4 animations:^{
        CGRect frame = frames;
        _resultView.resultViewframe = frames;
        CGFloat h = frame.size.height;
        _resultView.frame = CGRectMake(8.0, 64.0, 0.f, h);
    } completion:^(BOOL finished) {
        [_resultView removeFromSuperview];
        _voteResult = [[VoteResultButton alloc] initWithFrame:CGRectMake(0.f, 64.0, 20, 55)];
        [_voteResult addTarget:self action:@selector(showVoteResultView) forControlEvents:UIControlEventTouchUpInside];
        [self.view addSubview:_voteResult];
    }];
}
- (void)showVoteResultView{
    CGRect frame = _resultView.frame;
    CGFloat h = frame.size.height;
    [_voteResult removeFromSuperview];
    _voteResult = nil;
    [UIView animateWithDuration:0.4 animations:^{
        _resultView.frame = CGRectMake(8.0, 64.0, _resultView.resultViewframe.size.width, h);
        [self.view addSubview:_resultView];
    }];
}
- (void)changeVoteButtonState:(NSNotification *)notice{
    if ([[notice.userInfo objectForKey:@"flag"] isEqualToString:@"can"]) {
        _sponsorVoteButton.enabled = YES;
        [_sponsorVoteButton setBackgroundImage:[UIImage imageNamed:@"host_btn_vote_normal"] forState:UIControlStateNormal];
    }else if ([[notice.userInfo objectForKey:@"flag"] isEqualToString:@"no"]){
        _sponsorVoteButton.enabled = NO;
        [_sponsorVoteButton setBackgroundImage:[UIImage imageNamed:@"host_btn_vote_unable"] forState:UIControlStateNormal];
    }else{
        
    }
}
- (void)getNewData{
    NSDictionary *dict = @{@"vote_id":vote_id};
    VoteGetNewData *newDatas;
    if (newDatas) {
    }else{
        newDatas = [[VoteGetNewData alloc] init];
    }
    [newDatas getVotingDataWithVoteID:vote_id completion:^(id data) {
        if (data) {
            if ([data isKindOfClass:[NSMutableArray class]]) {
                if (_votingView) {
                    [_votingView setValue:data];
                }
            }else{
                if(commentTimer && commentTimer.isValid) {
                    [commentTimer invalidate];
                    commentTimer = nil;
                }
            }
        }
    } error:^(NSError *error) {
    }];
}
```
###在观众角色进入直播间的VC里面需要的操作（建议）
m文件里面需要包含以下头文件
```python
#import "VoteView.h"
#import "AnchorVotingView.h"
#import "AnchorvoteResultView.h"
#import "VoteResultButton.h"
#import "VoteGetNewData.h"
#import "VoteChooseView.h"
```
遵循的代理（协议）里面应该包含以下delegate
```python
VoteViewDelegate
AnchorVotingViewDelegate
AnchorvoteResultViewDelegate
VoteChooseViewDelegate
```
在m文件内部变量里面添加以下变量（建议，名字可修改，但是修改后需要代码里面涉及到的也要修改）
```python
NSTimer *commentTimer;//投票信息刷新定时器
NSTimer *lastVoteTimer;//最新的投票定时器
NSString *vote_id;//投票id
BOOL isGift;//yes投票no单纯送礼物
NSString *chooseOption;//观众选择投票的选项
```
在m文件内部属性里面添加以下属性
```python
@property (nonatomic, strong) AnchorvoteResultView *resultView;
@property (nonatomic, strong) AnchorVotingView *votingView;
@property (nonatomic, strong) VoteResultButton *voteResult;
@property (nonatomic, strong) VoteGetNewData *voteNewData;
@property (nonatomic, strong) VoteChooseView *voteChooseView;
```
在viewDidLoad里面添加以下代码
```python
isGift = NO;
[self getNewVotetestData];
```
在viewWillDisappear里面添加以下代码
```python
if(commentTimer && commentTimer.isValid) {
        [commentTimer invalidate];
        commentTimer = nil;
    }
    if(lastVoteTimer && lastVoteTimer.isValid) {
        [lastVoteTimer invalidate];
        lastVoteTimer = nil;
    }
    [[NSNotificationCenter defaultCenter] postNotificationName:@"STOPTIMER" object:nil];
```
然后在m文件代码块里面添加以下代码即可
```python
//voteChooseView代理
- (void)chooseGift:(NSString *)option{
    if ([option isEqualToString:@"投A"]) {
        NSLog(@"送礼物给A选项");
        chooseOption = @"A";
    }else if ([option isEqualToString:@"投B"]){
        NSLog(@"送礼物给B选项");
        chooseOption = @"B";
    }else if ([option isEqualToString:@"投C"]){
        NSLog(@"送礼物给C选项");
        chooseOption = @"C";
    }else if ([option isEqualToString:@"投D"]){
        NSLog(@"送礼物给D选项");
        chooseOption = @"D";
    }
    isGift = YES;
    [self showGiftView];
}
//进入直播间需要请求数据
- (void)getNewVotetestData{
    NSDictionary *dict = @{@"usid":self.liveListDataModel.basicInfo.usid};
    if (_voteNewData) {
        //
    }else{
        _voteNewData = [[VoteGetNewData alloc] init];
    }
    [_voteNewData getNewVoteDataWithVoteID:self.liveListDataModel.basicInfo.usid completion:^(id data) {
        //
        if (data && [data isKindOfClass:[NSDictionary class]]) {
            //
            if ([[data objectForKey:@"timeStr"] isEqualToString:@""]) {
                if (_resultView) {
                    //
                }else{
                    _resultView = [[AnchorvoteResultView alloc] init];
                    _resultView.delegate = self;
                    [_resultView createVoteResultView:[_resultView getdataArr:[data objectForKey:@"dataArr"]] :YY(self.starHeadView) + 10 :_resultView.with];
                    [self.view addSubview:_resultView];
                }
                if (!lastVoteTimer) {
                    lastVoteTimer = [NSTimer timerWithTimeInterval:5 target:self selector:@selector(getNewVotetestData) userInfo:nil repeats:YES];
                    [[NSRunLoop  currentRunLoop] addTimer:lastVoteTimer forMode:NSDefaultRunLoopMode];
                    [lastVoteTimer fire];
                }
            }else{
                //结束最新投票定时器
                if(lastVoteTimer && lastVoteTimer.isValid) {
                    [lastVoteTimer invalidate];
                    lastVoteTimer = nil;
                }
                [self createvoteView:[data objectForKey:@"dataArr"] :[data objectForKey:@"flag"] :[data objectForKey:@"timeStr"] :[data objectForKey:@"vote_id"]];
            }
        }
    } error:^(NSError *error) {
        //
        //未发起过投票
        if (!lastVoteTimer) {
            lastVoteTimer = [NSTimer timerWithTimeInterval:5 target:self selector:@selector(getNewVotetestData) userInfo:nil repeats:YES];
            [[NSRunLoop  currentRunLoop] addTimer:lastVoteTimer forMode:NSDefaultRunLoopMode];
            [lastVoteTimer fire];
        }
    }];
}

//创建正在投票视图AnchorVotingView方法
- (void)createvoteView:(NSMutableArray *)arr :(BOOL)isflag :(NSString *)time :(NSString *)voteID{
    CGFloat y = YY(self.starHeadView) + 10;//视图y坐标
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
//AnchorVotingView代理
- (void)createVoteResult:(NSMutableArray *)voteViewArr :(CGFloat)h{
    if (_votingView) {
        [_votingView removeFromSuperview];
        _votingView = nil;
    }
    _resultView = [[AnchorvoteResultView alloc] init];
    _resultView.delegate = self;
    //AnchorvoteResultView方法
    [_resultView createVoteResultView:voteViewArr :YY(self.starHeadView) + 10 :h];
    [self.view addSubview:_resultView];
    
    if(commentTimer && commentTimer.isValid) {
        [commentTimer invalidate];
        commentTimer = nil;
        //开启最新投票轮询
        if (!lastVoteTimer) {
            lastVoteTimer = [NSTimer timerWithTimeInterval:5 target:self selector:@selector(getNewVotetestData) userInfo:nil repeats:YES];
            [[NSRunLoop  currentRunLoop] addTimer:lastVoteTimer forMode:NSDefaultRunLoopMode];
            [lastVoteTimer fire];
        }
    }
}
//刷新数据轮询
- (void)getNewData{
    NSDictionary *dict = @{@"vote_id":vote_id};
    if (_voteNewData) {
        //
    }else{
        _voteNewData = [[VoteGetNewData alloc] init];
    }
    [_voteNewData getVotingDataWithVoteID:vote_id completion:^(id data) {
        //
        if (data) {
            if ([data isKindOfClass:[NSMutableArray class]]) {
                if (_votingView) {
                    [_votingView setValue:data];
                }
            }else{
                if(commentTimer && commentTimer.isValid) {
                    [commentTimer invalidate];
                    commentTimer = nil;
                }
            }
        }
    } error:^(NSError *error) {
        //
        NSLog(@"目测请求失败");
    }];
}
- (void)shouqiView:(CGRect)frames{
    CGRect frame = frames;
    _resultView.resultViewframe = frames;
    CGFloat h = frame.size.height;
    CGFloat height = frame.origin.y;
    [UIView animateWithDuration:0.4 animations:^{
        //
        _resultView.frame = CGRectMake(8.0, height, 0.f, h);
    } completion:^(BOOL finished) {
        //
        [_resultView removeFromSuperview];
        _voteResult = [[VoteResultButton alloc] initWithFrame:CGRectMake(0.f, height, 20, 55)];
        [_voteResult addTarget:self action:@selector(showVoteResultView) forControlEvents:UIControlEventTouchUpInside];
        [self.view addSubview:_voteResult];
    }];
}
- (void)showVoteResultView{
    CGRect frame = _resultView.frame;
    CGFloat h = frame.size.height;
    CGFloat height = frame.origin.y;
    [_voteResult removeFromSuperview];
    _voteResult = nil;
    [UIView animateWithDuration:0.4 animations:^{
        _resultView.frame = CGRectMake(8.0, height, _resultView.resultViewframe.size.width, h);
        [self.view addSubview:_resultView];
    }];
}
//AnchorVotingView代理
- (void)showVoteChooseView:(NSMutableArray *)chooseArr{
    if (_voteChooseView) {
        [_voteChooseView removeFromSuperview];
        _voteChooseView = nil;
    }
    _voteChooseView = [[VoteChooseView alloc] initWithFrame:CGRectMake(0.f, ScreenHeight, ScreenWidth, 1)];
    [_voteChooseView createChooseVoteView:chooseArr];
    _voteChooseView.delegate = self;
    [self.view addSubview:_voteChooseView];
}
```

由于观众角色进入的直播间界面的投票信息以及相关UI需要和礼物视图以及送礼物点击事件融合。以下是您的代码块里面需要注意和处理的内容：
1.在我提供的代码块里面的一个voteChooseView代理方法- (void)chooseGift:(NSString *)option，在这个方法里面代码最末里面弹出您的送礼物界面，并且在您的送礼物视图里面做相应处理
```python
//此时你必须把giveGiftView里面的givGiftButton要设成外部属性
    if (isGift) {
        [self.giveGiftView.givGiftButton setTitle:@"投票" forState:UIControlStateNormal];
    }else{
        [self.giveGiftView.givGiftButton setTitle:@"送出" forState:UIControlStateNormal];
    }
```
2.在您的送礼物按钮的点击事件里面需要请求下面的接口
```python
- (void)getVoteGiftDataWithVoteID:(NSString *)vote_id
                       option_key:(NSString *)chooseOption
                            count:(NSInteger)count
                       completion:(void(^)(id data))completion
                            error:(void(^)(NSError *error))error;
```
建议按一下代码处理点击送礼物按钮事件里面的代码(此处用了全局变量 bool类型的isGift来传递判断当前点击事件是单纯送礼物还是投票，isGift为yes的时候就需要请求上面的接口)需要传递的参数有
| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| vote_id     | 当前次投票的id |   nsstring     |
| chooseOption        |   观众投票时候选择的投给某个选项   |   nsstring   |
| count        |    礼物数量（金币数量）    |  nsinteger  |

请求后返回的结果

| 参数名称  |  描述 |  类型  |
| -----   | -----  | ----  |
| data     | 请求成功返回的数据 |   id（NSMutableArray）     |
| error        |   请求失败   |   nil   |

data里面的数据
```python
"options":[
            {
                "option_key":"op_1",
                "option_value":"选项1",
                "option_ballot":15
            },
            {
                "option_key":"op_2",
                "option_value":"选项2",
                "option_ballot":74
            }
```
```python
if (isGift) {
        //请求投票接口
        if (!_voteNewData) {
            _voteNewData = [[VoteGetNewData alloc] init];
        }
        [_voteNewData getVoteGiftDataWithVoteID:vote_id option_key:chooseOption count:giftCount completion:^(id data) {
            //
            if (data && [data isKindOfClass:[NSMutableArray class]]) {
                //
                if (_votingView) {
                    [_votingView setValue:data];
                }
            }
        } error:^(NSError *error) {
            //
            NSLog(@"网络请求失败");
        }];
    }
```
