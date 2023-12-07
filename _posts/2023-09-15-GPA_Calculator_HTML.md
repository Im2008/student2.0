---
toc: true
comments: true
layout: post
title: GPA Calculator
description: The GPA calculator helps calculate the overall grade of a single student that depends on the grades of other classes.
courses: { compsci: {'week': 4} }
type: hacks
---

<!-- Heading -->
<h1>Grade Calculator</h1>
<h2>Input scores, press tab to add each new number.</h2>
<!-- Totals -->
<h3>
    Total : <span id="total">0.0</span>
    Count : <span id="count">0.0</span>
    Average : <span id="average">0.0</span>
</h3>
<!-- Rows -->
<div id="scores">
    <!-- javascript generated inputs -->
</div>

<!-- Reset button -->
<button id="resetButton">Reset</button>

<script>
// Creates a new input box
function newInputLine(index) {

    // Add a label for each score element
    var title = document.createElement('label');
    title.htmlFor = index;
    title.innerHTML = index + ". ";    
    document.getElementById("scores").appendChild(title); // add to HTML

    // Setup score element and attributes
    var score = document.createElement("input"); // input element
    score.id =  index;  // id of input element
    score.onkeydown = calculator // Each key triggers event (using function as a value)
    score.type = "number"; // Use text type to allow typing multiple characters
    score.name = "score";  // name is used to group "score" elements
    score.style.textAlign = "right";
    score.style.width = "5em";
    document.getElementById("scores").appendChild(score);  // add to HTML

    // Create and add blank line after input box
    var br = document.createElement("br");  // line break element
    document.getElementById("scores").appendChild(br); // add to HTML

    // Set focus on the new input line
    document.getElementById(index).focus();
}

// Handles event and calculates totals
function calculator(event) {
    var key = event.key;
    // Check if the pressed key is the "Tab" key (key code 9) or "Enter" key (key code 13)
    if (key === "Tab" || key === "Enter") { 
        event.preventDefault(); // Prevent default behavior (tabbing to the next element)
   
        var array = document.getElementsByName('score'); // setup array of scores
        var total = 0;  // running total
        var count = 0;  // count of input elements with valid values

        for (var i = 0; i < array.length; i++) {  // iterate through array
            var value = array[i].value;
            if (parseFloat(value)) {
                var parsedValue = parseFloat(value);
                total += parsedValue;  // add to running total
                count++;
            }
        }

        // update totals
        document.getElementById('total').innerHTML = total.toFixed(2); // show two decimals
        document.getElementById('count').innerHTML = count;

        if (count > 0) {
            document.getElementById('average').innerHTML = (total / count).toFixed(2);
        } else {
            document.getElementById('average').innerHTML = "0.0";
        }

        // adds newInputLine, only if all array values satisfy parseFloat 
        if (count === document.getElementsByName('score').length) {
            newInputLine(count); // make a new input line
        }
    }
}

// Handles event and calculates totals
function calculator(event) {
    var key = event.key;
    // Check if the pressed key is the "Tab" key (key code 9) or "Enter" key (key code 13)
    if (key === "Tab" || key === "Enter") { 
        event.preventDefault(); // Prevent default behavior (tabbing to the next element)
   
        var array = document.getElementsByName('score'); // setup array of scores
        var total = 0;  // running total
        var count = 0;  // count of input elements with valid values

        for (var i = 0; i < array.length; i++) {  // iterate through array
            var value = array[i].value;
            if (/^[0-9]+$/.test(value)) { // Check if it's a valid integer
                var parsedValue = parseInt(value, 10); // Parse as an integer
                total += parsedValue;  // add to running total
                count++;
            }
        }

        // update totals
        document.getElementById('total').innerHTML = total;
        document.getElementById('count').innerHTML = count;

        if (count > 0) {
            document.getElementById('average').innerHTML = Math.round(total / count); // Round to the nearest integer
        } else {
            document.getElementById('average').innerHTML = "0";
        }

        // adds newInputLine, only if all array values satisfy the integer condition
        if (count === document.getElementsByName('score').length) {
            newInputLine(count); // make a new input line
        }
    }
}

// Function to convert a numeric average to a GPA grade
function calculateGPA(average) {
    if (average >= 90) {
        return 'A+';
    } else if (average >= 85) {
        return 'A';
    } else if (average >= 80) {
        return 'A-';
    } else if (average >= 75) {
        return 'B+';
    } else if (average >= 70) {
        return 'B';
    } else if (average >= 65) {
        return 'B-';
    } else if (average >= 60) {
        return 'C+';
    } else if (average >= 55) {
        return 'C';
    } else if (average >= 50) {
        return 'C-';
    } else if (average >= 45) {
        return 'D+';
    } else if (average >= 40) {
        return 'D';
    } else {
        return 'F';
    }
}

// Inside the calculator function, update the GPA display
document.getElementById('average').innerHTML = calculateGPA(Math.round(total / count));

// Creates 1st input box on Window load
newInputLine(0);

// Reset function
function resetCalculator() {
    var array = document.getElementsByName('score'); // get all input elements
    for (var i = 0; i < array.length; i++) {
        array[i].value = ''; // clear input values
    }
    // Reset totals
    document.getElementById('total').innerHTML = '0.0';
    document.getElementById('count').innerHTML = '0.0';
    document.getElementById('average').innerHTML = '0.0';
}

// Create and attach an event listener to the reset button
document.getElementById('resetButton').addEventListener('click', resetCalculator);

</script>