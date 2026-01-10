<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Kotlin Flows Learning Catalogue</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.3.0/papaparse.min.js"></script>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }

        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            min-height: 100vh;
            padding: 40px 20px;
        }

        .container {
            max-width: 1200px;
            margin: 0 auto;
        }

        h1 {
            text-align: center;
            color: white;
            font-size: 2.5rem;
            margin-bottom: 50px;
            text-shadow: 2px 2px 4px rgba(0,0,0,0.3);
        }

        .upload-section {
            text-align: center;
            margin-bottom: 40px;
        }

        .upload-btn {
            background: white;
            color: #667eea;
            border: none;
            padding: 15px 40px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 1.1rem;
            font-weight: 600;
            box-shadow: 0 4px 15px rgba(0,0,0,0.2);
            transition: transform 0.3s ease;
        }

        .upload-btn:hover {
            transform: scale(1.05);
        }

        #csvInput {
            display: none;
        }

        .card {
            background: white;
            border-radius: 15px;
            overflow: hidden;
            margin-bottom: 30px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
            transition: transform 0.3s ease;
            display: grid;
            grid-template-columns: 1fr 1fr;
            min-height: 400px;
        }

        .card:hover {
            transform: translateY(-5px);
        }

        .card-image {
            background-size: cover;
            background-position: center;
            display: flex;
            align-items: center;
            justify-content: center;
            position: relative;
        }

        .card-image::before {
            content: '';
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: linear-gradient(135deg, rgba(102, 126, 234, 0.3) 0%, rgba(118, 75, 162, 0.3) 100%);
        }

        .card-content {
            padding: 30px;
            display: flex;
            flex-direction: column;
        }

        @media (max-width: 768px) {
            .card {
                grid-template-columns: 1fr;
            }
            
            .card-image {
                min-height: 200px;
            }
        }

        .card-title {
            font-size: 1.8rem;
            color: #333;
            margin-bottom: 15px;
        }

        .card-blurb {
            color: #666;
            line-height: 1.6;
            margin-bottom: 20px;
            font-size: 1rem;
        }

        .quiz-toggle {
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            color: white;
            border: none;
            padding: 12px 30px;
            border-radius: 25px;
            cursor: pointer;
            font-size: 1rem;
            font-weight: 600;
            transition: all 0.3s ease;
            box-shadow: 0 4px 15px rgba(102, 126, 234, 0.4);
        }

        .quiz-toggle:hover {
            transform: scale(1.05);
            box-shadow: 0 6px 20px rgba(102, 126, 234, 0.6);
        }

        .quiz-section {
            max-height: 0;
            overflow: hidden;
            transition: max-height 0.4s ease;
            margin-top: 20px;
        }

        .quiz-section.active {
            max-height: 800px;
        }

        .quiz-question {
            background: #f8f9fa;
            padding: 20px;
            border-radius: 10px;
            margin-bottom: 15px;
        }

        .quiz-question h3 {
            color: #667eea;
            margin-bottom: 15px;
            font-size: 1.1rem;
        }

        .quiz-option {
            display: block;
            padding: 12px 15px;
            margin: 8px 0;
            background: white;
            border: 2px solid #e0e0e0;
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
        }

        .quiz-option:hover {
            border-color: #667eea;
            background: #f0f4ff;
        }

        .quiz-option input[type="radio"] {
            margin-right: 10px;
            cursor: pointer;
        }

        .submit-quiz {
            background: #28a745;
            color: white;
            border: none;
            padding: 10px 25px;
            border-radius: 20px;
            cursor: pointer;
            font-size: 1rem;
            margin-top: 15px;
            transition: background 0.3s ease;
        }

        .submit-quiz:hover {
            background: #218838;
        }

        .quiz-result {
            margin-top: 15px;
            padding: 15px;
            border-radius: 8px;
            font-weight: 600;
            display: none;
        }

        .quiz-result.show {
            display: block;
        }

        .quiz-result.correct {
            background: #d4edda;
            color: #155724;
            border: 1px solid #c3e6cb;
        }

        .quiz-result.incorrect {
            background: #f8d7da;
            color: #721c24;
            border: 1px solid #f5c6cb;
        }

        .loading {
            text-align: center;
            color: white;
            font-size: 1.2rem;
            padding: 20px;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1>🌊 Kotlin Flows Learning Catalogue</h1>

        <div class="upload-section">
            <input type="file" id="csvInput" accept=".csv">
            <button class="upload-btn" onclick="document.getElementById('csvInput').click()">
                📁 Upload CSV File
            </button>
        </div>

        <div id="content"></div>
    </div>

    <script>
        const defaultCSV = `title,blurb,image_url,quiz_q1,quiz_q1_a,quiz_q1_b,quiz_q1_c,quiz_q1_correct,quiz_q2,quiz_q2_a,quiz_q2_b,quiz_q2_c,quiz_q2_correct
TEST,"Kotlin Flow is a powerful asynchronous stream processing library built on coroutines. It enables reactive programming by emitting multiple values sequentially over time, making it perfect for handling continuous data streams efficiently and elegantly.",https://drive.google.com/uc?export=view&id=1_VWWwAq_Tm5-2tMtkA1kHVADvdR7s7L6,What is the main advantage of using Kotlin Flow?,It only works with synchronous operations,It can emit multiple values asynchronously over time,It replaces all coroutines functionality,b,What is Flow built on top of?,Threads,Coroutines,RxJava,b
Flow Builders and Operators,"Creating flows is straightforward with various builders like flow, flowOf, and asFlow. Operators such as map, filter, and transform allow you to manipulate data streams declaratively, providing powerful composition capabilities for complex asynchronous workflows.",https://picsum.photos/800/600?random=2,Which is NOT a valid Flow builder?,flow { },flowOf(),createFlow(),c,What does the map operator do?,Filters values from the flow,Transforms each emitted value,Collects all values into a list,b
Cold vs Hot Flows,"Understanding flow temperature is crucial. Cold flows start emitting values only when collected, creating independent streams for each collector. Hot flows like SharedFlow and StateFlow emit values regardless of collectors, enabling shared state and event broadcasting patterns.",https://picsum.photos/800/600?random=3,When does a cold flow start emitting values?,Immediately when created,Only when collected,After a delay,b,Which is an example of a hot flow?,Regular Flow,StateFlow,flowOf(),b
Flow Collection and Terminal Operators,"Terminal operators like collect, toList, and first trigger flow execution and consume emitted values. Understanding collection contexts and cancellation is essential for proper resource management and avoiding memory leaks in your Kotlin applications.",https://picsum.photos/800/600?random=4,What is a terminal operator?,An operator that transforms flow values,An operator that triggers flow execution and consumes values,An operator that creates new flows,b,Which is a terminal operator?,map,filter,collect,c
Error Handling in Flows,"Robust error handling ensures application stability. The catch operator intercepts exceptions upstream, while retry and retryWhen provide automatic recovery mechanisms. Proper exception handling prevents crashes and creates resilient data processing pipelines in production environments.",https://picsum.photos/800/600?random=5,What does the catch operator do?,Stops the flow immediately,Intercepts exceptions from upstream,Filters error values,b,Which operator allows automatic recovery attempts?,catch,retry,collect,b`;

        function loadCSV(csvText) {
            Papa.parse(csvText, {
                header: true,
                skipEmptyLines: true,
                complete: function(results) {
                    renderContent(results.data);
                }
            });
        }

        function renderContent(data) {
            const contentDiv = document.getElementById('content');
            contentDiv.innerHTML = '';

            data.forEach((row, index) => {
                const card = document.createElement('div');
                card.className = 'card';
                
                const quizId = `quiz${index}`;
                
                card.innerHTML = `
                    <div class="card-image" style="background-image: url('${row.image_url}')"></div>
                    <div class="card-content">
                        <h2 class="card-title">${row.title}</h2>
                        <p class="card-blurb">${row.blurb}</p>
                        <button class="quiz-toggle" onclick="toggleQuiz('${quizId}')">Take the Quiz 📝</button>
                        
                        <div id="${quizId}" class="quiz-section">
                            <div class="quiz-question">
                                <h3>Question 1: ${row.quiz_q1}</h3>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_1" value="a">
                                    ${row.quiz_q1_a}
                                </label>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_1" value="b">
                                    ${row.quiz_q1_b}
                                </label>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_1" value="c">
                                    ${row.quiz_q1_c}
                                </label>
                            </div>
                            <button class="submit-quiz" onclick="checkAnswer('q${index}_1', '${row.quiz_q1_correct}', 'result${index}_1')">Submit Answer</button>
                            <div id="result${index}_1" class="quiz-result"></div>

                            <div class="quiz-question">
                                <h3>Question 2: ${row.quiz_q2}</h3>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_2" value="a">
                                    ${row.quiz_q2_a}
                                </label>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_2" value="b">
                                    ${row.quiz_q2_b}
                                </label>
                                <label class="quiz-option">
                                    <input type="radio" name="q${index}_2" value="c">
                                    ${row.quiz_q2_c}
                                </label>
                            </div>
                            <button class="submit-quiz" onclick="checkAnswer('q${index}_2', '${row.quiz_q2_correct}', 'result${index}_2')">Submit Answer</button>
                            <div id="result${index}_2" class="quiz-result"></div>
                        </div>
                    </div>
                `;
                
                contentDiv.appendChild(card);
            });
        }

        function toggleQuiz(quizId) {
            const quiz = document.getElementById(quizId);
            quiz.classList.toggle('active');
        }

        function checkAnswer(questionName, correctAnswer, resultId) {
            const selected = document.querySelector(`input[name="${questionName}"]:checked`);
            const resultDiv = document.getElementById(resultId);
            
            if (!selected) {
                resultDiv.className = 'quiz-result show incorrect';
                resultDiv.textContent = 'Please select an answer!';
                return;
            }
            
            if (selected.value === correctAnswer) {
                resultDiv.className = 'quiz-result show correct';
                resultDiv.textContent = '✓ Correct! Well done!';
            } else {
                resultDiv.className = 'quiz-result show incorrect';
                resultDiv.textContent = '✗ Incorrect. Try again!';
            }
        }

        document.getElementById('csvInput').addEventListener('change', function(e) {
            const file = e.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    loadCSV(e.target.result);
                };
                reader.readAsText(file);
            }
        });

        // Load default content
        loadCSV(defaultCSV);
    </script>
</body>
</html>
