#第三版数据模型

# Basic Model #

```sql


-- 消息表
-- 消息表存放消息本身
CREATE TABLE t_statuses (
_id         INTEGER PRIMARY KEY,
status_id   TEXT    UNIQUE NOT NULL,
author_id   TEXT,
text        TEXT,
source      TEXT,
created_at  INT,
truncated   INT DEFAULT 0,
favorited   INT DEFAULT 0,
pic_thumbnail   TEXT,
pic_middle      TEXT,
pic_original    TEXT,
in_reply_to_status_id   TEXT,
in_reply_to_user_id     TEXT,
in_reply_to_screen_name TEXT
);

-- 消息属性表
-- 每一条消息所属类别、所有者等信息
CREATE TABLE t_statuses_property (
_id         INTEGER PRIMARY KEY,
status_id   TEXT NOT NULL,
owner_id    TEXT,
type        INT,
sequence_flag   INT,
load_time    TIMESTAMP default (DATETIME('now', 'localtime'))
--消息ID(外键)
--所有者(随便看看的所有者为空，XX的消息的所有者为自己)
--消息类别(随便看看/首页(自己及自己好友)-需要连续性判断/XX的消息-需要连续性判断/收藏/照片/搜索结果)
);
CREATE INDEX status_property_idx ON t_statuses_property (
type, owner_id, status_id
);

-- User表
-- User的基本信息(由一次基本查询(例如针对消息的查询)可以直接获得的)
CREATE TABLE t_user (
_id         INTEGER PRIMARY KEY,
user_id     TEXT    UNIQUE NOT NULL,
user_name   TEXT    UNIQUE NOT NULL,
screen_name TEXT,
location    TEXT,
description TEXT,
url         TEXT,
protected   INT DEFAULT 0,
profile_image_url   TEXT,
followers_count     INT,
friends_count       INT,
favourites_count    INT,
statuses_count      INT,
created_at          INT,
following           INT DEFAULT 0,
notifications       INT DEFAULT 0,
utc_offset          TEXT,
load_time            TIMESTAMP default (DATETIME('now', 'localtime'))
);

-- User扩展表（与用户表合并）
-- User的扩展信息(需要一次专门的针对User信息的查询)
--CREATE TABLE t_user_extend (
--    user_id             TEXT PRIMARY KEY,
--    friends_count       INT,
--    favourites_count    INT,
--    statuses_count      INT,
--    created_at          INT,
--    following           INT DEFAULT 0,
--    notifications       INT DEFAULT 0,
--    utc_offset          TEXT,
--    last_status         TEXT（这个去消息表找）
--);

-- 私信表
-- 私信的基本信息
create table t_direct_message
(
_id          INTEGER PRIMARY KEY,
msg_id       TEXT    UNIQUE NOT NULL,
text         TEXT,
sender_id    TEXT,
recipinet_id TEXT,
created_at   INT,
sequence_flag   INT,
load_time    TIMESTAMP default (DATETIME('now', 'localtime'))
);

/* TODO */

-- 搜索结果表（去掉这个表在消息表添加一种搜索结果的type）
-- 暂存每一次的搜索结果(随时可能被清空)
--create table t_search_result
--(
-- 消息ID(外键)
-- 搜索关键字
--);


-- Follow关系表
-- 某个特定用户的Follow关系(User1 following User2, 查找关联某人好友只需限定User1或者User2)
create table t_follow_relationship
(
user1_id TEXT,
user2_id TEXT,
load_time    TIMESTAMP default (DATETIME('now', 'localtime'))
);


-- 热门话题表
-- 热门话题
create table t_trend
(
name             TEXT,
query            TEXT,
url              TEXT,
load_time    TIMESTAMP default (DATETIME('now', 'localtime'))  --(记录API请求热门话题的时间，以便于保存历史[部分])
);

-- 保存搜索表
--
create table t_saved_search
(
query_id         INT,(这个ID在API删除保存搜索词时使用)
query            TEXT,
name             TEXT,
created_at       INT,
load_time    TIMESTAMP default (DATETIME('now', 'localtime'))  --(记录API请求保存话题的时间，以便于保存历史[部分])
);

```