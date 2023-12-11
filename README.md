# madoi-tutorial-usermanagement

WebSocket通信ライブラリ Madoi のチュートリアルです。 Madoiの機能を使って、チャットアプリケーションを作成しています。
ユーザの入退室も監視し、ログに出力しています。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
<meta charset="utf8">
<style>
#logDiv {
    overflow: scroll;
    resize: vertical;
    height: 400px;
    border: solid 1px;
    padding: 4;
    border-radius: 4px;
}
</style>
</head>
<body>
<form id="chatForm">
    <input id="name" size="8" type="text" value="匿名">
    <input id="message" size="30" type="text" placeholder="送信するメッセージ。enterで送信。">
    <button type="submit">送信</button>
</form>
<div id="logDiv">
</div>
<script src="https://fungo.kcg.edu/madoi-20231023/js/madoi.js"></script>
<script>
window.addEventListener("load", ()=>{
    // Madoiを作成しサーバに接続する。引数はルームのID。アプリケーション毎に異なるIDを使用する。
    const m = new madoi.Madoi("chat-usermanagement-ldfngslkkeg");

    const chatForm = document.getElementById("chatForm");
    const logDiv = document.getElementById("logDiv");

    chatForm.addEventListener("submit", ()=>{
        // フォームのsubmit時の処理
        event.preventDefault();
        const name = document.getElementById("name");
        const message = document.getElementById('message');
        // メッセージを送る。第一引数はメッセージタイプ、第二引数はメッセージの内容。
        m.send("chat", `${name.value}: ${message.value}`);
        message.value = "";
    });

    // "chat"タイプのメッセージを受信した際の処理を登録する。
    m.setHandler("chat", message=>{
        // m.getCurrentSenderPeerIdで、メッセージを送信したユーザのIDが取得できる。
        logDiv.innerHTML += `(${m.getCurrentSenderPeerId()}) ${message}<br>`;
        logDiv.scrollTop = logDiv.scrollHeight;
    });

    // ルームに入出した際の処理を登録する。
    m.addEventListener("enterRoomAllowed", ({detail})=>{
        const {selfPeer, otherPeers} = detail;
        logDiv.innerHTML += `[入室しました。あなたのIDは ${selfPeer.id} です。]<br/>`;
        for(let p of otherPeers){
            logDiv.innerHTML += `[${p.id}が入室しています。]<br/>`;
        }
        logDiv.scrollTop = logDiv.scrollHeight;
    });

    // ルームに他のユーザが入出した際の処理を登録する。
    m.addEventListener("peerEntered", ({detail: {peer: {id}}})=>{
        logDiv.innerHTML += `[${id} が入室しました。]<br/>`;
        logDiv.scrollTop = logDiv.scrollHeight;
    });

    // ルームから他のユーザが退出した際の処理を登録する。
    m.addEventListener("peerLeaved", ({detail: {peerId}})=>{
        logDiv.innerHTML += `[${peerId} が退室しました。]<br/>`;
        logDiv.scrollTop = logDiv.scrollHeight;
    });
});
</script>
</body>
</html>
```
