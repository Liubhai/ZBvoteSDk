# 智播投票
###智播投票概述

智播直播间投票基于IM及时通讯，让主播端和观众端同步显示投票信息。依赖智播投票接口，对发起投票和投票数据进行处理，实现不同数据和界面的交互。

###投票功能描述（在直播间）

1.主播界面：发起投票及时显示投票信息
2.观众界面：获取、显示投票信息；根据投票的信息（选项）投票给某选项

###智播投票功能类（代理协议）和方法：
1.VoteView.h 投票信息里面每个投票选项视图

    1）代理方法
```python
    - (void)getGoldLabelWith:(CGFloat)with;//代理获取当前所有投票选项信息视图里面最大的宽度，用于在投票结束后创建收起按钮的时候设置按钮的横坐标
```

    2）代理方法
```python
- (void)showVoteListView;//当投票正在进行的时候，点击投票视图里面某个选项弹出观众投票选择视图
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
- (void)createVoteOngoingWithArray:(NSMutableArray *)optionArray flag:(BOOL)flag voteTime:(NSString *)time voteId:(NSString *)voteId;
```
    2）代理方法
```python
/**
 *  发送消息（主播编辑好投票内容后点击发起投票按钮，成功之后需要发消息）
 *
 *  @param message    消息内容
 *  @param chatId   直播间的聊天室的id
 */
- (void)sendMessageWithMessage:(ZBMessage *)message chaId:(NSNumber *)chatId;
```
		message的属性赋值：
			message.fromUserIdentity = _usid;//消息发送者id
            message.extend = data;//消息主要内容（主播请求发起投票的接口的所有数据）
            message.extendCode = 1201;//专属有新投票消息的的消息码
            message.messageType = ZBMessageTypeCoustomAvailable;//消息类型

3.AnchorVotingView.h当前投票正在进行视图

    1）代理方法:根据当前投票信息的VoteView来创建投票结束后的视图，在当前情况下（倒计时结束后）触发此代理。
```python
/**
 *  根据倒计时结束的时候创建投票结果视图
 *
 *  @param voteViewArr 当前投票倒计时结束的时候存在的VoteView数组（保存的是当前投票里面显示的所有VoteView）
 *  @param h           当前voteViewArr里面的所有VoteView的最大的宽度
 */
- (void)createVoteResult:(NSMutableArray *)voteViewArr MaximumWidth:(CGFloat)MaxWith;
```
    2）代理方法:观众界面弹起投票视图
```python
/**
 *  弹起观众投票列表视图
 *
 *  @param chooseArr 当前投票时候存在的VoteView数组（保存的是当前投票里面显示的所有VoteView）
 */
- (void)showVoteListChooseView:(NSMutableArray *)chooseArr;
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
- (void)createVotingViewWithArray:(NSMutableArray *)optionArray isflag:(BOOL)isflag voteTime:(NSString *)timestr originY:(CGFloat)y;
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
- (void)packUpView:(CGRect)frames;
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
- (void)createVoteResultView:(NSMutableArray *)arr resultViewOriginY:(CGFloat)y MaximumWidth:(CGFloat)MaxWith;
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

```python
/**
 *  观众投票送礼物的接口
 *
 *  @param vote_id      当前次投票的id
 *  @param chooseOption 选择投票给某个选项
 *  @param count        金币数量
 *  @param completion   请求成功返回data数据里面包含所有选项的数据
 *  @param error        请求失败返回nil
 */
- (void)getVoteGiftDataWithVoteID:(NSString *)vote_id
                       option_key:(NSString *)chooseOption
                            count:(NSInteger)count
                       completion:(void(^)(id data))completion
                            error:(void(^)(NSError *error))error;
```
```python
/**
 *  永久结束投票接口
 *
 *  @param vote_id    投票id
 *  @param completion 完成返回的当前结束投票的数据
 *  @param error      请求失败返回nil
 */
- (void)getCloseVoteDataWithVoteID:(NSString *)vote_id
                        completion:(void(^)(id data))completion
                             error:(void(^)(NSError *error))error;
```
```python
/**
 *  临时结束投票接口
 *
 *  @param vote_id    投票id
 *  @param completion 完成返回的当前关闭投票的数据
 *  @param error      请求失败返回nil
 */
- (void)getTemporaryCloseVoteDataWithVoteID:(NSString *)vote_id
                                 completion:(void(^)(id data))completion
                                      error:(void(^)(NSError *error))error;
```
```python
/**
 *  恢复临时结束投票接口
 *
 *  @param vote_id    投票id
 *  @param completion 完成返回的当前关闭投票的数据
 *  @param error      请求失败返回nil
 */
- (void)getStartTemporaryCloseVoteDataWithVoteID:(NSString *)vote_id
                                 completion:(void(^)(id data))completion
                                      error:(void(^)(NSError *error))error;
```
主播端实例代码以及处理逻辑详解
```python
主播端：主播开启直播的时候根据主播自己id，请求自己最新（上一次）发起的投票信息，如果请求成功并强行执行永久结束此次投票接口，用以保证主播开启直播的时候，之前的投票都是结束的。此时控制主播页面的发起投票按钮是否可点击。
分不同情况处理：	1）请求成功并且强制结束上一次投票成功，发起投票按钮可以点击。
					2）请求失败，但是返回的code是91404（此主播从未发起投票），此时发起投票按钮也是可以点击
- (void)getNewVotetestData{
    VoteGetNewData *newData = [[VoteGetNewData alloc] init];
    [newData getNewVoteDataWithVoteID:User_Info_Global.userInfo.basicInfo.usid completion:^(id data) {
        if (data && [data isKindOfClass:[NSDictionary class]]) {
            //强制结束投票
            [newData getCloseVoteDataWithVoteID:[data objectForKey:@"vote_id"] completion:^(id data) {
                //
            } error:^(NSError *error) {
                //
            }];
            _sponsorVoteButton.selected = YES;
            _sponsorVoteButton.enabled = YES;
        }
    } error:^(NSError *error) {
        NSLog(@"超时");
        vote_id = nil;
        if (error.code == 91404) {
            //从未发起过投票
            _sponsorVoteButton.enabled = YES;
            [_sponsorVoteButton setSelected:YES];
        }else{
            _sponsorVoteButton.enabled = NO;
            [_sponsorVoteButton setSelected:NO];
        }
    }];
}

创建投票编辑视图：首先需要判断是否加入聊天室成功，因为发起投票需要走IM及时通讯，如果加入聊天室失败则无法发起投票并且弹框提示。
- (void)createVote{
    if (canApplyVote) {
        _applyView = [[AnchorApplyView alloc] init];
        _applyView.usid = User_Info_Global.userInfo.basicInfo.usid;
        _applyView.chatId = [NSNumber numberWithInteger:_cidByIM];
        _applyView.delegate = self;
        _applyView.userInteractionEnabled = YES;
        [self.view addSubview:_applyView];
    }else{
        UIAlertView *alertView = [[UIAlertView alloc] initWithTitle:@"加入聊天室失败" message:@"无法发起投票!" delegate:self cancelButtonTitle:@"确定" otherButtonTitles:nil];
        alertView.delegate = self;
        [alertView show];
    }
}

主播端发起投票成功之后，创建正在投票的信息视图，此时需要将投票编辑视图、投票结果显示视图投票结果按钮移除掉（如果这些视图存在）
- (void)createVoteOngoingWithArray:(NSMutableArray *)optionArray flag:(BOOL)flag voteTime:(NSString *)time voteId:(NSString *)voteId{
    CGFloat y = 130.0;//视图y坐标
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
    _votingView.vote_id = voteId;
    [_votingView createVotingViewWithArray:optionArray isflag:flag voteTime:time originY:y];
    [self.view addSubview:_votingView];
    vote_id = voteId;
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

投票结束后，创建投票结果视图，只要投票结束，发起投票按钮就可以点击。
- (void)createVoteResult:(NSMutableArray *)voteViewArr MaximumWidth:(CGFloat)MaxWith{
    if (_votingView) {
        [_votingView removeFromSuperview];
        _votingView = nil;
    }
    _resultView = [[AnchorvoteResultView alloc] init];
    _resultView.delegate = self;
    [_resultView createVoteResultView:voteViewArr resultViewOriginY:130 MaximumWidth:MaxWith];
    [self.view addSubview:_resultView];
    _sponsorVoteButton.enabled = YES;
    [_sponsorVoteButton setSelected:YES];
}
```

观众端关于投票实例代码和相关处理逻辑
```python
//voteChooseView代理
在弹出的投票选项视图里面点击某个选项然后弹出送礼物界面（因为这里需要将原生的送礼物界面的送出按钮改成投票按钮，这里用isGift布尔值来判断）
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
//进入直播间需要请求数据：请求当前直播间主播最新的一次投票信息，如果是正在投票则需要执行创建正在投票信息视图，如果不是正在投票则不作处理（不作任何ui界面操作）。
- (void)getNewVotetestData{
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
            }else{
                [self createVoteOngoingWithArray:[data objectForKey:@"dataArr"] flag:[data objectForKey:@"flag"] voteTime:[data objectForKey:@"timeStr"] voteId:[data objectForKey:@"vote_id"]];
                //                [self createvoteView:[data objectForKey:@"dataArr"] :[data objectForKey:@"flag"] :[data objectForKey:@"timeStr"] :[data objectForKey:@"vote_id"]];
            }
        }
    } error:^(NSError *error) {
        //未发起过投票
    }];
}

//创建正在投票视图AnchorVotingView方法
处理逻辑：同主播端一样，如果结果视图和投票结果按钮存在那么就需要移除
- (void)createVoteOngoingWithArray:(NSMutableArray *)optionArray flag:(BOOL)flag voteTime:(NSString *)time voteId:(NSString *)voteId{
    CGFloat y = 120;//视图y坐标
    if (_resultView) {
        [_resultView removeFromSuperview];
        _resultView= nil;
    }
    if (_voteResult) {
        [_voteResult removeFromSuperview];
        _voteResult = nil;
    }
    _votingView = [[AnchorVotingView alloc] init];
    NSLog(@"=======================%@",_votingView);
    _votingView.delegate = self;
    _votingView.vote_id = voteId;
    [_votingView createVotingViewWithArray:optionArray isflag:flag voteTime:time originY:y];
    [self.view addSubview:_votingView];
    vote_id = voteId;
}
//AnchorVotingView代理
- (void)createVoteResult:(NSMutableArray *)voteViewArr MaximumWidth:(CGFloat)MaxWith{
    if (_votingView) {
        [_votingView removeFromSuperview];
        _votingView = nil;
    }
    _resultView = [[AnchorvoteResultView alloc] init];
    _resultView.delegate = self;
    //AnchorvoteResultView方法
    [_resultView createVoteResultView:voteViewArr resultViewOriginY:120 MaximumWidth:MaxWith];
    [self.view addSubview:_resultView];
}
//AnchorVotingView代理
- (void)showVoteListChooseView:(NSMutableArray *)chooseArr{
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
#关于收发消息消息处理
观众端：观众端收到消息里面如果message.extendCode是1201是代表是主播端发过来的发起投票的消息，如果message.extendCode是1202就是收到其他观众给主播投票的消息。对应的处理代码实例如下。
//收到发起投票消息
```python
 if (message.extendCode == 1201) {
        NSString *timeStr;
        NSDate *dat = [NSDate date];
        NSDate *voteDate = [NSDate dateWithTimeIntervalSince1970:[[message.extend objectForKey:@"create_time"] integerValue]];
        NSTimeInterval a = [dat timeIntervalSinceDate:voteDate];  //  *1000 是精确到毫秒，不乘就是精确到秒
        int t = [[message.extend objectForKey:@"time"] intValue] * 60 - (int)a;
        timeStr = [NSString stringWithFormat:@"%d",t];
        [self createVoteOngoingWithArray:[message.extend objectForKey:@"options"] flag:YES voteTime:timeStr voteId:[message.extend objectForKey:@"vote_id"]];
        //
        //        [self createVoteOngoingWithArray:[data objectForKey:@"dataArr"] flag:[data objectForKey:@"flag"] voteTime:[data objectForKey:@"timeStr"] voteId:[data objectForKey:@"vote_id"]];
    }
    //收投票消息
    if (message.extendCode == 1202) {
        NSMutableArray *voteDataArr = [[NSMutableArray alloc] init];
        [voteDataArr removeAllObjects];
        [voteDataArr addObjectsFromArray:[message.extend objectForKey:@"options"]];
        if (_votingView) {
            [_votingView setValue:voteDataArr];
        }
        [self getLocationChatInfoModelNickNameAndVerifiedWithUsid:message.fromUserIdentity successCallBack:^(id data) {
            //
            ChatInfoModel *locationChatInfo = data;
            ChatInfoModel *chatInfoModel = [[ChatInfoModel alloc] init];
            chatInfoModel.isVerified = locationChatInfo.isVerified;
            chatInfoModel.nickName = locationChatInfo.nickName;
            chatInfoModel.avatarDic = locationChatInfo.avatarDic;
            [self.giveGiftShowView showGiftWithGoldCode:[[message.extend objectForKey:@"voteDetail"] objectForKey:@"gift_code"] userName:chatInfoModel.nickName isVerified:chatInfoModel.isVerified uidByIM:[message.fromUserIdentity integerValue] avatarDic:chatInfoModel.avatarDic voteOption:[NSString stringWithFormat:@"投%@",[[message.extend objectForKey:@"voteDetail"] objectForKey:@"vote_key"]]];
        } failCallBack:^(NSString *err) {
            //
            DebugLog(@"%@", [err debugDescription]);
        }];
        return;
    }
```
主播端：收到投票消息后处理代码示例
```python
//收投票消息
    if (message.extendCode == 1202) {
        NSMutableArray *voteDataArr = [[NSMutableArray alloc] init];
        [voteDataArr removeAllObjects];
        [voteDataArr addObjectsFromArray:[message.extend objectForKey:@"options"]];
        if (_votingView) {
            [_votingView setValue:voteDataArr];
        }
        [self getLocationChatInfoModelNickNameAndVerifiedWithUsid:message.fromUserIdentity successCallBack:^(id data) {
            //
            ChatInfoModel *locationChatInfo = data;
            ChatInfoModel *chatInfoModel = [[ChatInfoModel alloc] init];
            chatInfoModel.isVerified = locationChatInfo.isVerified;
            chatInfoModel.nickName = locationChatInfo.nickName;
            chatInfoModel.avatarDic = locationChatInfo.avatarDic;
            [self.giveGiftShowView showGiftWithGoldCode:[[message.extend objectForKey:@"voteDetail"] objectForKey:@"gift_code"] userName:chatInfoModel.nickName isVerified:chatInfoModel.isVerified uidByIM:[message.fromUserIdentity integerValue] avatarDic:chatInfoModel.avatarDic voteOption:[NSString stringWithFormat:@"投%@",[[message.extend objectForKey:@"voteDetail"] objectForKey:@"vote_key"]]];
            for (ZBOldGiftModel *model in Initialize_Info.giftList) {
                if ([model.giftCode isEqualToString:message.extend[@"type"]]) {
                    self.goldCount += (int)model.gold;
                    return;
                }
            }
        } failCallBack:^(NSString *err) {
            //
            DebugLog(@"%@", [err debugDescription]);
        }];
        return;
    }
```

