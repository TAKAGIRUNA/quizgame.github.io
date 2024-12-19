## APIを使ってクイズゲームを作る
今回は、Open Trivia Database APIを使用して、シンプルなクイズゲームを作成します。このゲームでは、ランダムに出題される問題に答えていき、最後にスコアを表示します。

---

### やること

##### 1. APIから問題を取得
Open Trivia Databaseからランダムなクイズの問題を取得します。

##### 2. 問題と選択肢を表示
ユーザーが答えを選ぶための選択肢を表示します。

##### 3. 答え合わせ
ユーザーが選んだ答えが正解か不正解かを判定し、メッセージを表示します。

---

### 必要な準備
##### 1. APIを使用
ここでは、無料で使えるOpen Trivia Database APIを使用します。公式サイト（https://opentdb.com/）でAPIにアクセスできます。特に設定は不要で、URLにリクエストを送るだけで簡単にクイズ問題を取得できます。

##### 2. HTMLとJavaScriptを使用
HTMLでページの構造を作り、JavaScriptでAPIとやりとりし、ゲームのロジックを実装します。

---

### コードの解説
以下が、実際に動作するクイズゲームのコードです。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>クイズゲーム</title>
    <style>
        body {
            font-family: 'Poppins', sans-serif;
            background-color: #f7e8e9;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            color: #444;
        }
        .quiz-container {
            text-align: center;
            padding: 30px;
            background-color: #ffffff;
            border-radius: 15px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
            width: 320px;
            max-width: 100%;
            transition: transform 0.2s;
        }
        .quiz-container:hover {
            transform: scale(1.05);
        }
        h1 {
            font-size: 2rem;
            color: #ff6b6b;
            font-weight: bold;
            margin-bottom: 20px;
        }
        .message {
            margin-top: 20px;
            font-size: 18px;
            font-weight: bold;
            color: #4caf50;
            margin-bottom: 15px;
        }
        .quiz-container button {
            margin-top: 10px;
            padding: 12px;
            font-size: 16px;
            cursor: pointer;
            background-color: #ff6b6b;
            color: white;
            border: none;
            border-radius: 50px;
            width: 100%;
            transition: background-color 0.3s, transform 0.2s;
        }
        .quiz-container button:hover {
            background-color: #ff4757;
            transform: scale(1.1);
        }
        .quiz-container button:active {
            transform: scale(0.95);
        }
        .quiz-container button:focus {
            outline: none;
        }
        #choices button {
            margin: 10px 0;
            padding: 10px;
            background-color: #f7a1c8;
            border-radius: 25px;
            font-size: 16px;
            width: 80%;
            cursor: pointer;
            transition: background-color 0.3s, transform 0.2s;
        }
        #choices button:hover {
            background-color: #ff6b6b;
            color: white;
        }
        #choices button:active {
            transform: scale(0.95);
        }
    </style>
</head>
<body>
    <div class="quiz-container">
        <h1>クイズゲーム</h1>
        <div id="question"></div>
        <div id="choices"></div>
        <div id="message" class="message"></div>
        <button onclick="nextQuestion()">次の問題</button>
    </div>

    <script>
        let currentQuestion = 0;
        let score = 0;
        let questions = [];

        // クイズのAPIからデータを取得
        async function fetchQuestions() {
            try {
                const response = await fetch('https://opentdb.com/api.php?amount=5&type=multiple');
                const data = await response.json();
                questions = data.results;
                showQuestion(currentQuestion);
            } catch (error) {
                console.error("APIエラー:", error);
            }
        }

        // 質問と選択肢を表示
        function showQuestion(index) {
            const question = questions[index];
            const questionElement = document.getElementById('question');
            const choicesElement = document.getElementById('choices');
            const messageElement = document.getElementById('message');
            messageElement.textContent = '';

            questionElement.textContent = question.question;
            choicesElement.innerHTML = ''; // 既存の選択肢をクリア

            // シャッフルして選択肢を表示
            const allChoices = [...question.incorrect_answers, question.correct_answer];
            const shuffledChoices = shuffle(allChoices);

            shuffledChoices.forEach(choice => {
                const button = document.createElement('button');
                button.textContent = choice;
                button.onclick = () => checkAnswer(choice, question.correct_answer);
                choicesElement.appendChild(button);
            });
        }

        // シャッフル関数
        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        // 答えをチェック
        function checkAnswer(selected, correct) {
            const messageElement = document.getElementById('message');
            if (selected === correct) {
                score++;
                messageElement.textContent = "正解！";
            } else {
                messageElement.textContent = "不正解。正解は: " + correct;
            }
            currentQuestion++;
            if (currentQuestion < questions.length) {
                setTimeout(() => showQuestion(currentQuestion), 2000);
            } else {
                setTimeout(() => {
                    alert("ゲーム終了！あなたのスコアは: " + score);
                    resetGame();
                }, 2000);
            }
        }

        // 次の問題へ
        function nextQuestion() {
            if (currentQuestion < questions.length) {
                showQuestion(currentQuestion);
            }
        }

        // ゲームリセット
        function resetGame() {
            currentQuestion = 0;
            score = 0;
            fetchQuestions(); // 新しいクイズを取得
        }

        // ページロード時にAPIからクイズを取得
        fetchQuestions();
    </script>
</body>
</html>
```

---

### 動作確認
このクイズゲームは、ブラウザで直接開くことができます。ゲームが進むごとに問題と選択肢がランダムに表示され、正解・不正解が判定されます。全問終了後、スコアが表示されます。
![画面](./アドベントカレンダー/クイズ画面.png)

---
### まとめ
今回クイズゲームを作ってみて、簡単にゲームを作れることがわかりました。どんなゲームがあるのか調べてみると聞いたことがないゲームがたくさんあったので、色々チャレンジできて、面白いと思います。ぜひ皆さんもやってみてください!!