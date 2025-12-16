# karaoke-form
<!doctype html>
<html lang="ja">
<head>
  <meta charset="utf-8">
  <title>カラオケ予約</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  https://static.line-scdn.net/liff/edge/2/sdk.js</script>
  <style>
    body { font-family: system-ui; margin: 16px; }
    label { display:block; margin-top:12px; }
    input, select { width:100%; padding:8px; }
    button { margin-top:16px; padding:10px 16px; }
  </style>
</head>
<body>
  <h1>カラオケ予約</h1>

  <label>予約日（YYYY-MM-DD）<input id="date" type="date" required></label>
  <label>開始時間（HH:mm）<input id="time" type="time" required></label>
  <label>利用時間（時間）<input id="hours" type="number" min="1" value="2" required></label>
  <label>人数<input id="people" type="number" min="1" value="2" required></label>
  <label>電話番号<input id="phone" type="tel" placeholder="090-1234-5678"></label>
  <label>備考<textarea id="note" rows="3"></textarea></label>

  <button id="submitBtn">申し込む</button>

  <script>
    const LIFF_ID = '（あなたのLIFF ID）'; // LINE Developersで取得

    async function init() {
      await liff.init({ liffId: LIFF_ID });
      if (!liff.isLoggedIn()) liff.login();

      // ユーザー情報
      const profile = await liff.getProfile(); // { userId, displayName, pictureUrl }
      const userId = profile.userId;
      const displayName = profile.displayName;

      document.getElementById('submitBtn').onclick = async () => {
        const payload = {
          lineUserId: userId,
          displayName,
          reserveDate: document.getElementById('date').value,
          startTime: document.getElementById('time').value,
          hours: Number(document.getElementById('hours').value),
          people: Number(document.getElementById('people').value),
          phone: document.getElementById('phone').value,
          note: document.getElementById('note').value
        };

        // Power Automate の HTTP 受信トリガーへ送信
        const flowEndpoint = 'https://prod-xx.japaneast.logic.azure.com/...'; // フローのURL
        const res = await fetch(flowEndpoint, {
          method: 'POST',
          headers: { 'Content-Type': 'application/json' },
          body: JSON.stringify(payload)
        });

        if (res.ok) {
          alert('申込を受け付けました。LINEに確認メッセージが届きます。');
          liff.closeWindow(); // LINEトークに戻る
        } else {
          alert('送信に失敗しました。時間をおいて再度お試しください。');
        }
      };
    }
    init();
  </script>
</body>
</html>
