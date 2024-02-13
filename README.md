<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>제품 옵션 선택</title>
    <link href="https://fonts.googleapis.com/css2?family=Roboto:wght@400;500&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Roboto', sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
        }
        #container {
            background-color: #ffffff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.2);
            width: 80%;
            max-width: 600px;
        }
        .step, #completeButton, #backButton, .prevButton {
            display: none;
        }
        button {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 10px 20px;
            margin: 5px;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        .selected {
            background-color: #28a745;
        }
        #completeButton {
            background-color: #dc3545;
            display: block; /* 위치 조정 필요 없으므로 항상 표시 */
            margin: 20px 0;
        }
        #backButton {
            background-color: #ffc107;
            display: block; /* 마찬가지로 위치 조정 필요 없음 */
            margin: 20px 0;
        }
        .prevButton {
            background-color: #6c757d;
            display: inline-block; /* 이전 단계 버튼은 인라인으로 표시 */
            margin: 20px 3px 20px 0;
        }
        #statusBarContainer {
            width: 100%;
            background-color: #e0e0e0;
            height: 20px;
            border-radius: 10px;
            position: relative;
            margin-bottom: 20px;
        }
        #statusBar {
            height: 100%;
            background-color: #4caf50;
            width: 0%;
            border-radius: 10px;
        }
        #statusText {
            position: absolute;
            width: 100%;
            text-align: center;
            font-weight: bold;
            color: #ffffff;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100%;
        }
    </style>
</head>
<body>
<div id="container">
    <div id="statusBarContainer">
        <div id="statusBar"></div>
        <div id="statusText">Step 1 of 4</div>
    </div>
    <div id="colorStep" class="step">
        <h2>색상을 선택하세요.</h2>
        <button onclick="saveChoice('color', '빨강')">빨강</button>
        <button onclick="saveChoice('color', '파랑')">파랑</button>
        <button onclick="saveChoice('color', '녹색')">녹색</button>
    </div>
    <div id="sizeStep" class="step">
        <h2>사이즈를 선택하세요.</h2>
        <button onclick="saveChoice('size', '소')">소</button>
        <button onclick="saveChoice('size', '중')">중</button>
        <button onclick="saveChoice('size', '대')">대</button>
        <button class="prevButton" onclick="updateStep(0)">이전 단계</button>
    </div>
    <div id="featuresStep" class="step">
        <h2>추가 기능을 선택하세요. (여러 개 선택 가능)</h2>
        <button id="featureWaterproof" onclick="toggleFeature('방수', this)">방수</button>
        <button id="featureShockproof" onclick="toggleFeature('충격 방지', this)">충격 방지</button>
        <button id="featureWirelessCharging" onclick="toggleFeature('무선 충전', this)">무선 충전</button>
        <button class="prevButton" onclick="updateStep(1)">이전 단계</button>
    </div>
    <button id="completeButton" onclick="updateStep(3)">완료</button>
    <div id="finalPriceStep" class="step">
        <h2>선택한 옵션에 따른 최종 가격:</h2>
        <div id="finalPrice"></div>
    </div>
    <button id="backButton" onclick="resetAndStartOver()">돌아가기</button>
</div>

<script>
    const selections = {
        color: null,
        size: null,
        features: []
    };
    const optionPrices = {
        color: {"빨강": 500, "파랑": 500, "녹색": 500},
        size: {"소": 0, "중": 1000, "대": 2000},
        features: {"방수": 3000, "충격 방지": 4000, "무선 충전": 5000}
    };
    const steps = ['color', 'size', 'features', 'finalPrice'];
    document.getElementById('colorStep').style.display = 'block';

    function saveChoice(step, choice) {
        selections[step] = choice;
        const nextStepIndex = steps.indexOf(step) + 1;
        updateStep(nextStepIndex);
    }

    function toggleFeature(feature, element) {
        const featureIndex = selections.features.indexOf(feature);
        if (featureIndex > -1) {
            selections.features.splice(featureIndex, 1);
            element.classList.remove('selected');
        } else {
            selections.features.push(feature);
            element.classList.add('selected');
        }
    }

    function updateStep(stepIndex) {
        document.querySelectorAll('.step').forEach((el, index) => {
            el.style.display = index === stepIndex ? 'block' : 'none';
        });
        document.getElementById('completeButton').style.display = stepIndex === 2 ? 'block' : 'none';
        document.getElementById('backButton').style.display = stepIndex === 3 ? 'block' : 'none';
        document.querySelectorAll('.prevButton').forEach((btn, index) => {
            btn.style.display = index === stepIndex - 1 ? 'block' : 'none';
        });
        updateStatusBar(stepIndex);
    }

    function resetAndStartOver() {
        selections.color = null;
        selections.size = null;
        selections.features = [];
        updateStep(0); // Reset to first step
    }

    function calculateFinalPrice() {
        let finalPrice = 10000;
        finalPrice += optionPrices.color[selections.color];
        finalPrice += optionPrices.size[selections.size];
        selections.features.forEach(feature => {
            finalPrice += optionPrices.features[feature];
        });
        return finalPrice;
    }

    function updateStatusBar(stepIndex) {
        const percentage = ((stepIndex + 1) / steps.length) * 100;
        document.getElementById('statusBar').style.width = `${percentage}%`;
        document.getElementById('statusText').innerText = `Step ${stepIndex + 1} of ${steps.length}`;
    }
</script>
</body>
</html>
