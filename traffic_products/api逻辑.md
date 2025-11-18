登录---验证用户是否有效---更新登录时间---创建stripe customer api---根据user_type判断用户订阅是否过期（03过期）

注册---验证用户是否已经注册---新增用户=

创建绑卡意图---验证用户是否存在有效---创建setup intent api---更新customer表last_setup_intent_id---返回client_secret setup_intent_id

查询账单---验证用户是否存在|有效|付款记录---数据库查询invoice

查询账单列表---验证用户是否存在|有效|付款记录---数据库查询invoice list

获取用户绑卡详情---验证用户是否存在|有效|绑卡---查询卡详情api---返回卡详情

创建订阅---验证用户是否存在|有效|绑卡|SetupIntent---创建订阅api---插入membership表---获取账单api---插入invoice表

webhook订阅成功后---验证用户是否存在|有效|付款记录---查询membership表period_end---更新user表主账号is_first_free='1'|user_type='02'|subscription_status='1'|subscription_expiration_time=period_end---同步user表子账号subscription_status|subscription_expiration_time|is_first_free

webhook订阅失败后---验证用户是否存在|有效|付款记录---无处理

取消会员订阅---验证用户是否存在|有效|有订阅---取消订阅api---更新membership表status为cancelling
webhook订阅删除后(同时也是过期了)---invoice表获取invoice详情---更新membership表status="canceled"

创建子账号---同步主账号订阅状态

franchisee.info---获取user表数据

get_current_user---验证用户是否登录---子账号同步主账号状态---若free=0|aub_status=0|过期了---将用户is_first_free='1'|user_type='03'|role_id=2
---若free=1|aub_status=0|过期了|user_type!=03---将用户is_first_free='1'|user_type='03'|role_id=2

试用期未过期---/franchisee/info---subscription_status='0'|subscription_expiration_time>now|is_first_free='0'
试用期已过期---/franchisee/info---subscription_status='0'|subscription_expiration_time<now|is_first_free='1'
已退订未过期---/franchisee/info---subscription_status='1'|subscription_expiration_time>now|is_first_free='1'
已退订已过期---/franchisee/info---subscription_status='0'|subscription_expiration_time<now|is_first_free='1'
未退订未过期---/franchisee/info---subscription_status='1'|subscription_expiration_time>now|is_first_free='1'
