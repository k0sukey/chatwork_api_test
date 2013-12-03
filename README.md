chatwork_api_test
=================

### GitHub の Post-Receive Hooks でマイチャットにメッセージを送信してみるテスト

受け取るサーバは Appcelerator の Node.ACS で無料運用

#### まずは Node.ACS が使えるように下準備

1. Appcelerator のウェブサイトでアカウントを登録
2. node.js / npm を使えるように
3. ```$ npm install acs -g``` で acs コマンドを使えるように
4. ```$ acs new hoge``` で Node.ACS プロジェクトを作成

#### Node.ACS で GitHub から Post-Receive Hooks を受け取る

hoge/controllers/github.js を作成

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

hoge/config.json を編集

/github 〜の一行を追加します

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

hoge/package.json を編集

dependencies に request モジュールを追加

	"dependencies": {
		"request": "*"
	},
