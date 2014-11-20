# Hubot for Slack with XMPP

HubotをXMPP経由でSlackに連携させる用のミニマムなファイル群です。  
HubotはHerokuにデプロイする想定です。  
PRIVATE GROUPにおいてもHubotが反応します。

※[hubot-xmpp](https://github.com/markstory/hubot-xmpp)を利用しております。

## 事前準備

### 1. Hubot用のユーザーをSlackで用意しておく

このユーザーがSlack上で反応するので、アイコンとかユーザ名とかbotにあったものにしくといいかもです。

### 2. (任意)Hubot用のユーザーをPRIVATE GROUPに所属させておく

PRIVATE GROUPでもhubotに反応してもらいたい場合はPRIVATE GROUPに所属させておきましょう。

## 設定方法

### 1. このリポジトリをForkする

このリポジトリをForkしてください。

### 2. Forkしたファイルをローカルにgit cloneする

    $ git clone https://github.com/blue-goheimochi/hubot-slack-xmpp-base.git

※カレントディレクトリをCloneしたファイルのあるディレクトリに移動しておいてください。

### 3. Procfileを編集する

    $ vi Procfile
    web: bin/hubot -a xmpp -n hubot-xmpp

Procfireに記述する -n [文字列]で指定した文字列（上の例で言うとhubot-xmpp）を監視してHubotが反応してくれます。  

※Slack上のユーザー名で反応するわけではないので注意  
※Slack上のユーザー名とあわせておくのが吉

### 4. Heroku appを作成する

    $ heroku create yourname-hubot-slack

※適当な名前でHeroku appを作成します。  
※herokuコマンドを使用するためには[Heroku ToolBelt](https://toolbelt.heroku.com/)をインストールしておく必要があります。  
※herokuにログインしていない場合は`heroku login`のコマンドでログインしてください。

### 5. Herokuにソースコードをデプロイする

    $ git push heroku master
    $ heroku ps:scale web=1

※ここでワーカープロセスも指定しておきます。

### 6. HerokuのAddonを追加する

    $ heroku addons:add rediscloud

※redisのAddonを追加します。

### 7. Herokuのconfig設定する

    $ heroku config:add HEROKU_URL=http://XXXXX.herokuapp.com

※4. で作成したHeroku appのURLを指定します。

### 8. HubotとSlackの連携の設定をする

※こちらの[URL](blue-goheimochi.hatenablog.com/entry/2014/10/19/SlackとHubotを連携させてPRIVATE_GROUPでも動くようにしてみた#HubotAndSlackSetting)でも解説しておりますのであわせてご確認いただければ幸いです。※

#### ■SlackのXMPPのGatewayを有効にする

https://my.slack.com/admin/settings#permissions  
**管理者権限を持ったユーザ**で上記URLにアクセスし、  
「Gateways」の「expand」で設定項目を開き、  
「Enable XMPP gateway (SSL only) 」のチェックボックスをONにして「Save Setting」で保存する。

#### ■Hubot用ユーザの認証情報を確認する。

https://my.slack.com/account/gateways  
**Hubot用のユーザ**で上記URLにアクセスし、  
「Getting Started: XMPP」の項目で認証情報を確認する。

#### ■Hubot用ユーザの認証情報をHerokuのconfigで設定する

    $ heroku config:add HUBOT_XMPP_USERNAME="hubot-xmpp@your-team.xmpp.slack.com"
    $ heroku config:add HUBOT_XMPP_PASSWORD="HUBOT_XMPP_PASSWORD"
    $ heroku config:add HUBOT_XMPP_ROOMS="test@conference.your-team.xmpp.slack.com"

**・HUBOT_XMPP_USERNAME**  
認証情報の確認の1.で表示されている、  
「Your ID is `hubot-xmpp@your-team.xmpp.slack.com`」の部分を指定する。  

**・HUBOT_XMPP_PASSWORD**  
認証情報の確認の3.で表示されている、  
「`HUBOT_XMPP_PASSWORD`as your password」の部分を指定する。

**・HUBOT_XMPP_ROOMS**  
`[Channel名]@conference.your-team.xmpp.slack.com`で指定する。
「`conference.your-team.xmpp.slack.com` for the server (i.e. put "conference." at the beginning of your XMPP host name)」の部分を@以降の文字列として指定。  
複数のChannelを対象にしたい場合は、  
`test@conference.your-team.xmpp.slack.com,test2@conference.your-team.xmpp.slack.com`  
と、「,(カンマ)」区切りで指定する。

### 9. SlackでHUBOT_SLACK_BOTNAME宛てにpingを送る

※3.で指定したbot名で、

> hubot-xmpp ping

or

> @hubot-xmpp ping

`HUBOT_XMPP_ROOMS`で指定したチャンネルで投稿。

> PONG

とHubot用のユーザーから返ってくる。

以上。
