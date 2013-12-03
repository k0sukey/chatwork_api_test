chatwork_api_test
=================

### GitHub の Post-Receive Hooks でマイチャットにメッセージを送信してみるテスト

受け取るサーバは Appcelerator の Node.ACS で無料運用。

#### まずは Node.ACS が使えるように下準備

1. Appcelerator のウェブサイトでアカウントを登録
2. node.js / npm を使えるように
3. ```$ npm install acs -g``` で acs コマンドを使えるように
4. ```$ acs new hoge``` で Node.ACS プロジェクトを作成

#### Node.ACS で GitHub から Post-Receive Hooks を受け取る

以下の方法で payload のベタな JSON がマイチャットに送信されます。

##### hoge/controllers/github.js を作成

ChatWork の API トークンやマイチャットの room_id はご自分のものをどうぞ。

	var Request = require('request');

	function index(req, res) {
		Request.post({
			uri: 'https://api.chatwork.com/v1/rooms/{マイチャットの room_id}/messages',
			headers: {
				'X-ChatWorkToken': 'ChatWork の API トークン'
			},
			form: {
				body: req.body.payload
			},
			json: true
		}, function(error, response, body){

		});
	}

##### hoge/config.json を編集

/github 〜の一行を追加。

	{
		"routes":
		[
			{ "path": "/", "callback": "application#index" },
			{ "path": "/github", "method": "post", "callback": "github#index" }
		],
		"filters":
		[
			{ "path": "/", "callback": "" }
		],
		"websockets":
		[
			{ "event": "", "callback": ""}
		]
	}

##### hoge/package.json を編集

dependencies に request モジュールを追加。

	"dependencies": {
		"request": "*"
	},

##### Node.ACS へパブリッシュ

```$ acs publish``` でパブリッシュ。
2 回目以降は ```--force``` オプションを付けること。
パブリッシュが完了すると URL（ https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.cloudapp.appcelerator.com ）が出力されます。

##### GitHub に WebHook URL を設定

GitHub のレポジトリにある Settings → Service Hooks → WebHook URLs へ https://xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.cloudapp.appcelerator.com/github として追加します。

#### payload を加工して見栄えの良い物に
