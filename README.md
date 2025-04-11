# calendario-trabajo1

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Calendario con Notas 1.0</title>
<style>
    body {
        font-family: Arial, sans-serif;
        background:rgb(33, 116, 168);
        display: flex;
        justify-content: center;
        align-items: center;
        height: 100vh;
        margin: 0;
    }

    .calendar-container {
        background: rgb(23, 86, 81);
        border-radius: 8px;
        box-shadow: 1 4px 14px rgba(0, 0, 0, 0.1);
        padding: 20px;
        width: 3000px;
    }

    .header {
        background:rgb(151, 190, 53);
        color: rgb(239, 190, 190);
        padding: 10px;
        border-radius: 80px;
        text-align: center;
        font-weight: bold;
    }

    .controls {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 10px;
    }

    .controls button {
        background: none;
        border: none;
        color: #080b18;
        font-size: 18px;
        cursor: pointer;
    }

    .days {
        display: grid;
        grid-template-columns: repeat(7, 1fr);
        gap: 5px;
        text-align: center;
        margin-top: 10px;
    }

    .day, .date {
        font-weight: bold;
        padding: 8px;
    }

    .day {
        color: #3F51B5;
    }

    .date {
        background: #f0f0f0;
        border-radius: 50%;
        cursor: pointer;
        font-size: 14px;
        position: relative;
    }

    .date:hover {
        background: #c5cae9;
    }

    .selected {
        background: #3F51B5;
        color: white;
    }

    .footer {
        display: flex;
        justify-content: space-between;
        margin-top: 15px;
    }

    button {
        padding: 6px 12px;
        border: none;
        background: #3F51B5;
        color: white;
        border-radius: 4px;
        cursor: pointer;
        font-size: 14px;
    }

    button:hover {
        background: #303F9F;
    }

    #selected-date {
        font-size: 16px;
        margin-top: 10px;
        font-weight: normal;
    }

    .note-popup {
        position: fixed;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%);
        background: rgb(113, 45, 45);
        padding: 15px;
        border-radius: 10px;
        box-shadow: 0 4px 10px rgba(133, 25, 25, 0.2);
        z-index: 10;
        display: none;
    }

    .note-popup textarea {
        width: 10%;
        height: 60px;
        margin-bottom: 10px;
    }

    .note-indicator {
        position: absolute;
        bottom: 2px;
        right: 6px;
        font-size: 12px;
        color: red;
    }

    .overlay {
        position: fixed;
        top: 0; left: 0;
        width: 100vw;
        height: 100vh;
        background: rgba(0, 0, 0, 0.4);
        z-index: 99;
        display: none;
    }
    </style>
</head>
<body>

<div class="calendar-container">
    <div class="header" id="selected-date">marzo del 2025</div>
    <div class="controls">
    <button onclick="changeMonth(-1)">◀</button>
    <div id="month-year"></div>
    <button onclick="changeMonth(1)">▶</button>
</div>
<div class="days">
    <div class="day">D</div>
    <div class="day">L</div>
    <div class="day">M</div>
    <div class="day">M</div>
    <div class="day">J</div>
    <div class="day">V</div>
    <div class="day">S</div>

        <li class="day">1</li>
        <li class="day">2</li>
        <li class="day">3</li>
        <li class="day">4</li>
        <li class="day">5</li>
        <li class="day">6</li>
        <li class="day">7</li>
        <li class="day">8</li>
        <li class="day">9</li>
        <li class="day">10</li>
        <li class="day">11</li>
        <li class="day">12</li>
        <li class="day">13</li>
        <li class="day">14</li>
        <li class="day">15</li>
        <li class="day">16</li>
        <li class="day">17</li>
        <li class="day">18</li>
        <li class="day">19</li>
        <li class="day">20</li>
        <li class="day">21</li>
        <li class="day">22</li>
        <li class="day">23</li>
        <li class="day">24</li>
        <li class="day">25</li>
        <li class="day">26</li>
        <li class="day">27</li>
        <li class="day">28</li>
        <li class="day">29</li>
        <li class="day">30</li>
        <li class="day">31</li>
</div>
<div class="days" id="dates"></div>
<div class="footer">
    <button onclick="cancelDate()">Cancelar</button>
    <button onclick="okDate()">OK</button>
</div>
</div>

<!-- Ventana emergente para escribir nota -->
<div class="overlay" id="overlay"></div>
<div class="note-popup" id="note-popup">
    <h4>Nota para el día <span id="note-date"></span></h4>
    <textarea id="note-text"></textarea>
    <button onclick="saveNote()">Guardar</button>
    <button onclick="closeNote()">Cerrar</button>
</div>

<script>
    const monthYear = document.getElementById("month-year");
    const datesContainer = document.getElementById("dates");
    const selectedDateElem = document.getElementById("selected-date");
    const notePopup = document.getElementById("note-popup");
    const noteText = document.getElementById("note-text");
    const noteDate = document.getElementById("note-date");
    const overlay = document.getElementById("overlay");

    let currentDate = new Date();
    let selectedDate = null;
   let notes = {}; // Guardamos notas con formato YYYY-MM-DD

function renderCalendar() {
    const year = currentDate.getFullYear();
    const month = currentDate.getMonth();
    const firstDay = new Date(year, month, 1).getDay();
    const lastDate = new Date(year, month + 1, 0).getDate();

    monthYear.textContent = currentDate.toLocaleString("es-ES", {
        month: "long",
        year: "numeric"
    });

    datesContainer.innerHTML = "";

    for (let i = 0; i < firstDay; i++) {
        datesContainer.innerHTML += <div></div>;
    }

    for (let day = 1; day <= lastDate; day++) {
        const dateKey = ${yearasync (params) => {
            }-${
        }month + 1}-${day};
        const isSelected = selectedDate &&
        day === selectedDate.getDate() &&
        month === selectedDate.getMonth() &&
        year === selectedDate.getFullYear();

        const div = document.createElement("div");
        div.className = "date" + (isSelected ? " selected" : "");
        div.textContent = day;
        div.onclick = () => selectDate(day);

      // Indicador de nota
        if (notes[dateKey]) {
        const indicator = document.createElement("span");
        indicator.className = "note-indicator";
        indicator.textContent = "✎";
        div.appendChild(indicator);
    }

    datesContainer.appendChild(div);
    }
}

function selectDate(day) {
    selectedDate = new Date(currentDate.getFullYear(), currentDate.getMonth(), day);
    selectedDateElem.textContent = selectedDate.toLocaleDateString("es-ES", {
        weekday: "short",
        year: "numeric",
        month: "long",
        day: "numeric"
    });

    const key = getDateKey(selectedDate);
    noteText.value = notes[key] || "";
    noteDate.textContent = selectedDate.toLocaleDateString("es-ES");
    overlay.style.display = "block";
    notePopup.style.display = "block";
}

function getDateKey(date) {
    return ${date.getFullYear(20)}-${date.getMonth(2025) + 1}-${date.getDate(l)};
}

function saveNote() {
    const key = getDateKey(selectedDate);
    const note = noteText.value.trim();
    if (note) {
        notes[key] = note;
    } else {
        delete notes[key];
    }
    closeNote();
    renderCalendar();
}

function closeNote() {
    overlay.style.display = "none";
    notePopup.style.display = "none";
}

function changeMonth(offset) {
    currentDate.setMonth(currentDate.getMonth() + offset);
    renderCalendar();
}

function cancelDate() {
    selectedDate = null;
    selectedDateElem.textContent = "Selecciona una fecha";
    renderCalendar();
}

function okDate() {
    if (selectedDate) {
        const key = getDateKey(selectedDate);
        alert("Fecha: " + selectedDate.toLocaleDateString("es-ES") +
        (notes[key] ? "\nNota: " + notes[key] : "\n(Sin nota)"));
    } else {
        alert("No has seleccionado ninguna fecha.");
    }
}

</script>

</body>
</html>