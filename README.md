もにこさん
=====

### 実装方法
+ botはcronで5分おきに起動
+ botに対するメンションとユーザからの設定情報はSQLiteに保存

### モデル

#### mentions table
|カラム名 | tweet_id | user_id | screen_name | message |
|:-:|:-:|:-:|:-:|:-:|
|**型** | text | text | text | text |
|**意味** | メンションのツイートID | メンションしたユーザのID | メンションしたユーザの表示名 | メンション内容 |

#### calls table
|カラム名 | user_id | screen_name | call_time | tweet_id | status |
|:-:|:-:|:-:|:-:|:-:|:-:|
|**型** | text | text | text(datetime) | text | integer |
|**意味** | 起こすユーザのID | 起こすユーザの表示名 | 起こす時間 | 設定を行ったメンションのID | 起こすアクションの進行状況 |

### 実行ステップ
1. mentionsテーブル中最新のものより新しいメンションの一覧を取得してmentionsテーブルに格納する。取得するメンションがなければ終了
2. 新しく取得したメンションの中から時間を指定する単語と「起こして」などが入っているものを選び、callsテーブルに設定情報を追加する
3. 新しく取得したメンションの中から「起きたよ」などが入っているものを選び、callsテーブルに現在時刻とcall_timeの時刻が近く、user_idがそのユーザと等しいデータがあれば、そのユーザに褒めるメンションを送り、callsテーブルから該当データを削除する
4. callsテーブルの中で現在時刻からcall_timeの時刻が近いデータを取得し、user_idに示されているユーザに起きることを促すメンションを送信する
5. callsテーブルの中から現在時刻に対してcall_timeの時刻が大幅に過ぎていてかつ2回以上起こすメンションを行ったデータを取得し、user_idに示されているユーザとそのユーザのフォロワー数名の@関連が付いた、設定ユーザを罵倒するようなツイートを投稿する
