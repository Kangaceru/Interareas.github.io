<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Feedback Anônimo</title>
    <style>
        /* Reset básico */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Arial', sans-serif;
        }

        body {
            background: linear-gradient(135deg, #1e3c72, #2a5298);
            color: #fff;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
        }

        h1 {
            text-align: center;
            margin-bottom: 20px;
            color: #e0e0e0;
        }

        #feedbackForm {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
            max-width: 500px;
            width: 100%;
            margin-bottom: 20px;
        }

        textarea, select, button {
            width: 100%;
            padding: 10px;
            margin-top: 10px;
            border: none;
            border-radius: 5px;
            font-size: 16px;
            background: rgba(255, 255, 255, 0.2);
            color: #fff;
        }

        textarea::placeholder, select option {
            color: #ccc;
        }

        textarea {
            height: 100px;
            resize: vertical;
        }

        button {
            background: #007bff;
            color: white;
            font-weight: bold;
            cursor: pointer;
            transition: 0.3s;
        }

        button:hover {
            background: #0056b3;
        }

        .feedback-container {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 15px;
            border-radius: 10px;
            margin-bottom: 10px;
            box-shadow: 0 4px 10px rgba(0, 0, 0, 0.2);
        }

        .feedback-container .feedback {
            font-size: 16px;
            margin-bottom: 10px;
            color: #e0e0e0;
        }

        .like-dislike-buttons {
            display: flex;
            justify-content: space-between;
        }

        .like-dislike-buttons button {
            flex: 1;
            margin: 5px;
            background: rgba(255, 255, 255, 0.2);
            color: #fff;
            transition: 0.3s;
        }

        .like-dislike-buttons button.active {
            background: #28a745;
        }

        .like-dislike-buttons button.dislike-active {
            background: #dc3545;
        }

        .feedback-sections {
            display: grid;
            grid-template-columns: repeat(4, 1fr);
            gap: 20px;
            width: 100%;
            max-width: 1200px;
        }

        .feedback-section {
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(10px);
            padding: 15px;
            border-radius: 10px;
            box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
        }

        .feedback-section h2 {
            text-align: center;
            margin-bottom: 10px;
            color: #e0e0e0;
        }
    </style>
</head>
<body>

    <h1>Feedback Anônimo</h1>

    <form id="feedbackForm">
        <textarea id="feedbackText" placeholder="Escreva seu feedback aqui..." required></textarea>
        <select id="feedbackArea" required>
            <option value="">Selecione a área...</option>
            <option value="RH">RH</option>
            <option value="TI">T.I.</option>
            <option value="Saude">Saúde</option>
            <option value="Geral">Geral</option>
        </select>
        <button type="submit">Enviar Feedback</button>
    </form>

    <!-- Seções para cada área de feedback -->
    <div class="feedback-sections">
        <div class="feedback-section">
            <h2>RH</h2>
            <div id="feedbacksRH"></div>
        </div>

        <div class="feedback-section">
            <h2>T.I.</h2>
            <div id="feedbacksTI"></div>
        </div>

        <div class="feedback-section">
            <h2>Saúde</h2>
            <div id="feedbacksSaude"></div>
        </div>

        <div class="feedback-section">
            <h2>Geral</h2>
            <div id="feedbacksGeral"></div>
        </div>
    </div>

    <script>
        // Lista de nomes bloqueados
        const blockedNames = ["João"]; // Adicione mais nomes aqui

        document.getElementById('feedbackForm').onsubmit = function(event) {
            event.preventDefault();
            const feedbackText = document.getElementById('feedbackText').value;
            const feedbackArea = document.getElementById('feedbackArea').value;

            // Verifica se o feedback contém algum nome bloqueado
            const containsBlockedName = blockedNames.some(name => feedbackText.toLowerCase().includes(name.toLowerCase()));

            if (containsBlockedName) {
                alert("O feedback contém um nome bloqueado e não pode ser enviado.");
                return; // Impede o envio do feedback
            }

            const feedback = { text: feedbackText, area: feedbackArea, likes: 0, dislikes: 0, liked: false, disliked: false };
            addFeedbackToDisplay(feedback);
            document.getElementById('feedbackForm').reset();
        };

        function addFeedbackToDisplay(feedback) {
            const feedbackElement = document.createElement('div');
            feedbackElement.className = 'feedback-container';
            feedbackElement.innerHTML = `
                <div class="feedback">${feedback.text}</div>
                <div class="like-dislike-buttons">
                    <button onclick="likeFeedback(this, ${feedback.likes})">👍 (${feedback.likes})</button>
                    <button onclick="dislikeFeedback(this, ${feedback.dislikes})">👎 (${feedback.dislikes})</button>
                </div>
            `;

            // Adiciona o feedback à seção correspondente
            switch (feedback.area) {
                case 'RH':
                    document.getElementById('feedbacksRH').appendChild(feedbackElement);
                    break;
                case 'TI':
                    document.getElementById('feedbacksTI').appendChild(feedbackElement);
                    break;
                case 'Saude':
                    document.getElementById('feedbacksSaude').appendChild(feedbackElement);
                    break;
                case 'Geral':
                    document.getElementById('feedbacksGeral').appendChild(feedbackElement);
                    break;
                default:
                    console.error("Área de feedback inválida.");
            }
        }

        function likeFeedback(button, likes) {
            const feedbackContainer = button.parentElement.parentElement;
            const dislikeButton = feedbackContainer.querySelector('.like-dislike-buttons button:nth-child(2)');

            if (!button.classList.contains('active')) {
                button.classList.add('active');
                button.textContent = `👍 (${likes + 1})`;
                dislikeButton.classList.remove('dislike-active');
                dislikeButton.textContent = `👎 (0)`;
            }
        }

        function dislikeFeedback(button, dislikes) {
            const feedbackContainer = button.parentElement.parentElement;
            const likeButton = feedbackContainer.querySelector('.like-dislike-buttons button:nth-child(1)');

            if (!button.classList.contains('dislike-active')) {
                button.classList.add('dislike-active');
                button.textContent = `👎 (${dislikes + 1})`;
                likeButton.classList.remove('active');
                likeButton.textContent = `👍 (0)`;
            }
        }
    </script>
</body>
</html>
