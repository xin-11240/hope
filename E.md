
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>賓果遊戲</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            text-align: center;
        }
        .bingo-board {
            display: grid;
            grid-template-columns: repeat(5, 1fr);
            gap: 5px;
            max-width: 300px;
            margin: 0 auto;
        }
        .bingo-cell {
            width: 50px;
            height: 50px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 18px;
            border: 1px solid #333;
            cursor: pointer;
        }
        .marked {
            background-color: #90ee90;
            color: #fff;
            cursor: default;
        }
        .disabled {
            background-color: #f0f0f0;
            cursor: not-allowed;
        }
        #message {
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <h1>賓果遊戲</h1>
    <p>目標是達成 12 條連線。</p>
    <div class="bingo-board" id="bingoBoard"></div>
    <div id="message"></div>

    <script>
        const questions = {
            1: ["寄生生物必須依賴中間宿主來完成其生活史嗎？", "否"],
            2: ["試問下列對「寄生」的敘述，何者錯誤？\n(A)在寄生關係中，被寄生的生物經常因寄生生物的取食或附著行為而受到傷害\n(B)宿主可能因為寄生生物的感染影響其生存與繁殖\n(C)臺灣獼猴身上的蝨子會吸取其血液，因此兩者為寄生的關係\n(D)鳥巢蕨依附於其他樹木上以獲取所需的養分和水分，因此兩者為寄生的關係", "D"],
            3: ["中間宿主在寄生生物的生活史中扮演的主要角色是什麼？\n(A)寄生生物的最終棲息地\n(B)幫助寄生生物在不同宿主間移動並完成部分生活週期\n(C)阻止寄生生物的繁殖\n(D)提供寄生生物的唯一感染途徑", "B"],
            4: ["下列何者對「肺炎雙球菌引起人類肺炎」的生物交互作用最相似？\n(A)蟑螂小蜂產卵於蟑螂的蟲卵中\n(B)根瘤菌和豆科植物的根細胞組成根瘤\n(C)小丑魚附在珊瑚群中\n(D)山蘇附生於喬木上", "A"],
            5: ["下列「心絲蟲與狗」與下列何種生物之間的關係相同？\n(A)菟絲子與大樹\n(B)獅子與牛羚\n(C)藍鯨與磷蝦\n(D)螞蟻與蚜蟲", "A"],
            6: ["試問下列對「Microparasites」的敘述，何者錯誤？\n(A)體型小\n(B)發育及繁殖速度快\n(C)可直接透過空氣或食物傳播\n(D)跳蚤屬於Microparasites", "D"],
            7: ["試問下列對「Microparasites(微型寄生蟲)」與「Macroparasites(大型寄生蟲)」的比較，何者正確？\n(A)Microparasites通常具有較長的世代時間，並且不會在單一宿主內完成整個生命週期\n(B)Microparasites包括病毒、細菌和原生生物\n(C)Macroparasites的傳播可以是直接或間接的\n(D)Macroparasites在宿主內迅速繁殖，以提高傳播效率", "B"],
            8: ["下列哪一項屬於間接傳播的例子？\n(A)Microparasite\n(B)Macroparasites\n(C)真菌\n(D)昆蟲媒介", "D"],
            9: ["試問下列對「寄生植物」的敘述，何者正確？\n(A)所有寄生植物均無法進行光合作用，因此完全依賴宿主提供的養分\n(B)半寄生植物（Hemiparasites）具有葉綠素，可進行光合作用，但需要從宿主植物獲取水和無機養分\n(C)全寄生植物（Holoparasites）的養分來源僅限於宿主的木質部（xylem），而非韌皮部（phloem）\n(D)半寄生植物（Hemiparasites）依賴宿主植物的韌皮部來獲取碳和其他必需養分","B"],
            10: ["試問下列對「全寄生植物（Holoparasites）」與「半寄生植物（Hemiparasites）」的敘述，何者錯誤？\n(A)全寄生植物缺乏葉綠素，因此無法進行光合作用\n(B)半寄生植物可自行合成部分有機養分，但仍會從宿主獲取水和無機營養\n(C)全寄生植物透過特化根從宿主的木質部和韌皮部中獲取養分\n(D)榭寄生屬於全寄生植物；菟絲子屬於半寄生植物","D"],
            11: ["關於寄生物在宿主體內外的寄生特性及其傳播機制，下列敘述何者正確？\n(A)體內寄生物（endoparasites）能夠隨意進出宿主體內以增加傳播的機會，特別是成熟階段的寄生物\n(B)體外寄生物(如:跳蚤)會利用宿主間的接觸進行傳播\n(C)蟎蟲屬於體內寄生蟲\n(D)體外寄生物（ectoparasites）僅依附於宿主表皮層，不會隱藏在毛髮或羽毛等部位","B"],
            12: ["寄生蟲可透過改變宿主的行為或特徵來促進自身的生存與繁殖。(回答是或否)","是"],
            13: ["以下關於 Ectomycorrhizae（外生菌根）的敘述，哪一項正確？\n(A)寄生於細胞內部形成樹狀結構\n(B)在根的外部形成鞘，根會變短且增厚\n(C)主要存在於草本植物上\n(D)不改變根的形狀或外觀","B"],
            14: ["內生菌根（endomycorrhizae）和外生菌根（ectomycorrhizae）如何利用結構區別？\n(A)內生菌根穿透植物根細胞，而外生菌根僅在根細胞表面形成結構\n(B)內生菌根在根表面形成鞘層\n(C)外生菌根穿透植物根細胞並形成叢枝\n(D)內生菌根與植物根細胞無直接接觸","A"],
            15: ["下列哪一個因素會讓寄生物對宿主的繁殖成功率造成影響？\n(A)消耗資源\n(B)增加宿主的行為活躍性\n(C)減少宿主的感染能力\n(D)提升宿主的吸引力","A"],
            16: ["哪一項正確描述了 Rhizobium bacteria（根瘤菌）和豆科植物的關係對生態系統的好處？\n(A)減少氮氧化物的排放\n(B)增加氮的固定，提高土壤肥力，減少對合成肥料的依賴\n(C)減少土壤中的磷含量\n(D)增加植物光合作用效率","B"],
            17: ["關於寄生蟲對宿主族群動態的影響，下列哪一項描述是正確的？\n(A)寄生蟲只會影響宿主個體，對整體族群結構無顯著影響。\n(B)當宿主族群密度較高時，寄生蟲會減少傳播速度，從而有助於族群穩定。\n(C)寄生蟲的選擇性致死可以調控宿主族群的結構，通常會影響年幼或免疫力較弱的宿主個體。\n(D)寄生蟲的局部滅絕通常發生於宿主族群密度低的情況下。","C"],
            18: ["根據研究，瘧疾（malaria）主要透過受感染的蚊子叮咬傳播。在亞馬遜地區，森林砍伐對瘧疾傳播有何影響？\n(A)減少瘧原蟲數量\n(B)促進蚊子的繁殖並增加人類接觸風險\n(C)使瘧疾風險降低，因為棲息地減少\n(D)提高蜱數量，造成瘧疾傳播上升","B"],
            19: ["Rhizobium bacteria在豆科植物中的存在讓植物能夠不依賴土壤氮肥便能生長茁壯。這主要是因為什麼原因？\n(A)植物可以自行合成氮化合物\n(B)Rhizobium 能將大氣氮（N₂）轉化為植物可吸收的氨（NH₃）\n(C)Rhizobium 幫助植物根部進行光合作用\n(D)根瘤菌在根部形成能儲存氮的細胞","B"],
            20: ["以下關於 Endomycorrhizae（內生菌根）和 Ectomycorrhizae（外生菌根）寄主範圍的比較，哪一項正確？\n(A)內生菌根範圍窄，主要寄生於木本植物\n(B)外生菌根範圍廣，多為草本植物\n(C)內生菌根寄主範圍廣，多寄生於草本植物\n(D)兩者的寄主範圍皆相同","C"],
            21: ["California killifish的寄生蟲感染如何影響其行為，進而影響其生存率？\n(A)增加警覺性\n(B)減少食欲\n(C)增加暴露於掠食者的風險\n(D)減少繁殖能力","C"],
            22: ["Endomycorrhizae（內生菌根）與 Ectomycorrhizae（外生菌根）各自增強了植物對什麼物質的吸收？\n(A)內生菌根增強水分吸收，外生菌根增強光合作用\n(B)內生菌根延伸吸收氮、磷，外生菌根增強養分和水分吸收收\n(C)內生菌根增強水分吸收，外生菌根延伸氮、磷吸\n(D)兩者均增強二氧化碳吸收","B"],
            23: ["小明正在研究 Rhizobium bacteria（根瘤菌）與豆科植物的共生關係。他發現 Rhizobium 會感染植物的根部，形成一種特殊的結構。請問這種結構是什麼？\n(A)鞘層\n(B)根瘤\n(C)叢枝\n(D)體囊","B"],
            24: ["寄生蟲使California killifish出現異常行為是延伸表型的例子。(回答是或否)","是"],
            25: ["為什麼 Borrelia burgdorferi 無法在沒有蜱的情況下直接在人類之間傳播？\n(A)Borrelia 需要空氣中的氧氣來存活\n(B)它無法穿透人類皮膚，需要蜱叮咬創造的傷口進入宿主\n(C)病原體只能在人類皮膚表面繁殖\n(D)病原體會在短時間內被宿主的免疫系統消滅","B"]
        };

        let incorrectAttempts = {};
        let totalLines = 0;

        function shuffle(array) {
            for (let i = array.length - 1; i > 0; i--) {
                const j = Math.floor(Math.random() * (i + 1));
                [array[i], array[j]] = [array[j], array[i]];
            }
            return array;
        }

        function generateRandomBoard() {
            let numbers = Array.from({ length: 25 }, (_, i) => i + 1);
            numbers = shuffle(numbers);
            const board = [];
            for (let i = 0; i < 5; i++) {
                board.push(numbers.slice(i * 5, i * 5 + 5));
            }
            return board;
        }

        const bingoBoard = generateRandomBoard();

        function displayBoard() {
            const boardContainer = document.getElementById("bingoBoard");
            boardContainer.innerHTML = '';
            bingoBoard.flat().forEach((num) => {
                const cell = document.createElement("div");
                cell.classList.add("bingo-cell");
                cell.textContent = num;
                cell.onclick = () => handleCellClick(num, cell);
                if (typeof num === "string" && num === "V") {
                    cell.classList.add("marked");
                    cell.onclick = null;
                } else if (incorrectAttempts[num] >= 2) {
                    // 禁用點擊，顯示已達錯誤次數上限
                    cell.classList.add("disabled");
                    cell.onclick = null;
                }
                boardContainer.appendChild(cell);
            });
        }

        function handleCellClick(choice, cell) {
            if (incorrectAttempts[choice] >= 2) {
                alert(`此題已經答錯 2 次，無法再作答！`);
                return;
            }

            const userAnswer = prompt(questions[choice][0]);
            if (userAnswer === questions[choice][1]) {
                alert("答對了！");
                markBoard(choice);
                totalLines = countBingoLines();
                document.getElementById("message").innerText = `當前連線數：${totalLines}`;
                if (totalLines >= 12) {
                    document.getElementById("message").innerText = "恭喜！你達成了 12 條連線！";
                    return;
                }
            } else {
                alert("答錯了！");
                incorrectAttempts[choice] = (incorrectAttempts[choice] || 0) + 1;
                if (incorrectAttempts[choice] >= 2) {
                    alert(`此題已經答錯 2 次，無法再作答！`);
                }
            }
            displayBoard();
        }

        function markBoard(choice) {
            for (let i = 0; i < 5; i++) {
                for (let j = 0; j < 5; j++) {
                    if (bingoBoard[i][j] === choice) {
                        bingoBoard[i][j] = "V";
                    }
                }
            }
        }

        function countBingoLines() {
            let lines = 0;
            for (let i = 0; i < 5; i++) {
                if (bingoBoard[i].every(val => val === "V")) lines++;
                if (bingoBoard.every(row => row[i] === "V")) lines++;
            }
            if (bingoBoard.every((row, idx) => row[idx] === "V")) lines++;
            if (bingoBoard.every((row, idx) => row[4 - idx] === "V")) lines++;
            return lines;
        }

        displayBoard();
    </script>
</body>
</html>
