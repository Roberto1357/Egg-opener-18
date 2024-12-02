# Egg-opener-18
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Egg Simulator</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #f0f0f0;
            overflow: hidden;
        }

        .container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
        }

        #openEggButton {
            padding: 10px 20px;
            font-size: 18px;
            margin: 20px;
            cursor: pointer;
        }

        .table-container {
            width: 80%;
            margin: 20px auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th, td {
            border: 1px solid #ddd;
            text-align: center;
            padding: 8px;
        }

        th {
            background-color: #f4f4f4;
        }

        #inventoryButton {
            position: absolute;
            top: 20px;
            left: 20px;
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }

        #inventory {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: white;
            display: none;
            flex-wrap: wrap;
            justify-content: center;
            align-items: center;
            overflow: auto;
            padding: 20px;
        }

        #inventory.active {
            display: flex;
        }

        .pet-box {
            width: 150px;
            height: 150px;
            border: 1px solid #ddd;
            margin: 10px;
            text-align: center;
            display: flex;
            flex-direction: column;
            justify-content: center;
            align-items: center;
        }

        .pet-box .name {
            font-size: 14px;
        }

        .pet-box .count {
            font-size: 18px;
            font-weight: bold;
        }

        #popupContainer {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            background: rgba(0, 0, 0, 0.8);
            display: none;
            justify-content: center;
            align-items: center;
        }

        #popupContainer.zoom-effect {
            animation: zoomOut 1s forwards;
        }

        #petPopup {
            font-size: 36px;
            font-weight: bold;
            color: white;
            opacity: 0;
        }

        @keyframes zoomOut {
            from {
                transform: scale(1);
            }
            to {
                transform: scale(0.7);
            }
        }

        .line-effect {
            position: absolute;
            width: 100%;
            height: 100%;
            pointer-events: none;
            animation: linesWiggle 1.5s infinite;
        }

        .line {
            position: absolute;
            width: 2px;
            height: 50px;
            background: yellow;
            transform-origin: center;
        }

        .line.top { top: 20px; left: 50%; }
        .line.bottom { bottom: 20px; left: 50%; }

        @keyframes linesWiggle {
            0%, 100% { transform: rotate(0); }
            50% { transform: rotate(5deg); }
        }

        .close-button {
            position: absolute;
            top: 10px;
            right: 10px;
            font-size: 24px;
            cursor: pointer;
            border: none;
            background: transparent;
        }
    </style>
</head>
<body>
    <div id="popupContainer">
        <div class="line-effect">
            <div class="line top"></div>
            <div class="line bottom"></div>
        </div>
        <div id="petPopup"></div>
    </div>

    <div id="inventory" class="inventory">
        <button class="close-button" onclick="toggleInventory()">×</button>
    </div>

    <div class="container">
        <button id="inventoryButton" onclick="toggleInventory()">Inventar</button>
        <button id="openEggButton" onclick="openEgg()">Open Egg</button>

        <div class="table-container">
            <table>
                <thead>
                    <tr>
                        <th>Pet</th>
                        <th>Probability</th>
                        <th>Collected</th>
                    </tr>
                </thead>
                <tbody id="petTableBody"></tbody>
            </table>
        </div>
    </div>

    <script>
        // Pets mit Wahrscheinlichkeiten
        const pets = [
            { name: 'Common Cat', probability: 0.6 },
            { name: 'Common Dog', probability: 0.2 },
            { name: 'Uncommon Bird', probability: 0.1 },
            { name: 'Rare Fox', probability: 0.05 },
            { name: 'Epic Dragon', probability: 0.025 },
            { name: 'Legendary Unicorn', probability: 0.01 },
            { name: 'Mythic Phoenix', probability: 0.005 },
            { name: 'Divine Tiger', probability: 0.0025 },
            { name: 'Godly Wolf', probability: 0.001 },
            { name: 'Celestial Whale', probability: 0.0005 },
            { name: 'Stellar Griffin', probability: 0.0002 },
            { name: 'Galactic Serpent', probability: 0.0001 },
            { name: 'Astral Beast', probability: 0.00005 },
            { name: 'Ethereal Spirit', probability: 0.00001 },
            { name: 'Ultimate Egglord', probability: 0.000001 }
        ];

        let inventory = {};
        pets.forEach(pet => {
            inventory[pet.name] = 0;
        });

        function openEgg() {
            const random = Math.random();
            let accumulatedProbability = 0;

            for (const pet of pets) {
                accumulatedProbability += pet.probability;
                if (random <= accumulatedProbability) {
                    inventory[pet.name]++;
                    if (pet.name === 'Rare Fox') {
                        rareFoxAnimation();
                    } else {
                        displayPetPopup(pet.name);
                    }
                    updateTable();
                    break;
                }
            }
        }

        function updateTable() {
            const tableBody = document.getElementById('petTableBody');
            tableBody.innerHTML = '';
            pets.forEach(pet => {
                const row = document.createElement('tr');
                row.innerHTML = `<td>${pet.name}</td><td>${(pet.probability * 100).toFixed(5)}%</td><td>${inventory[pet.name]}</td>`;
                tableBody.appendChild(row);
            });
        }

        function rareFoxAnimation() {
            const popupContainer = document.getElementById('popupContainer');
            popupContainer.style.display = 'block';
            popupContainer.classList.add('zoom-effect');

            setTimeout(() => {
                popupContainer.style.display = 'none';
                displayPetPopup('Rare Fox');
            }, 2000);
        }

        function displayPetPopup(petName) {
            const petPopup = document.getElementById('petPopup');
            petPopup.innerText = petName;
            petPopup.style.opacity = 1;
            setTimeout(() => petPopup.style.opacity = 0, 1000);
        }

        function toggleInventory() {
            const inventory = document.getElementById('inventory');
            inventory.classList.toggle('active');
            displayInventory();
        }

        function displayInventory() {
            const inventory = document.getElementById('inventory');
            inventory.innerHTML = '<button class="close-button" onclick="toggleInventory()">×</button>';
            pets.forEach(pet => {
                const petBox = document.createElement('div');
                petBox.classList.add('pet-box');
                petBox.innerHTML = `<div class="count">${inventory[pet.name]}</div><div class="name">${pet.name}</div>`;
                inventory.appendChild(petBox);
            });
        }

        updateTable();
    </script>
</body>
</html>
