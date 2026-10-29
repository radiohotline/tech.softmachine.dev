# Binary choice ranker

I needed a ranker that accepts a plaintext list instead of individually entered items, so I made one. It was a fun challenge, because I still remembered various sorting algorithms I learned, but the manual selection was an interesting twist. This ranker uses merge sort, for which I referenced the pseudocode on Wikipedia, except instead of automatically comparing the value of items as you would with numbers, the user determines the sort order by selecting the "smaller" item themselves, which gets pushed into the sorted array (and so on for each sub-array until the list is sorted). The algorithm itself was easy, but async was new to me, as well some other Javascript details that had to do with the UI.

I haven't implemented keyboard controls yet.

## Demonstration

It can be found at [https://softmachine.dev/sort](https://softmachine.dev/sort).

## Source

### Script

    <script>
        function getChoice(item1, item2) {
            return new Promise((resolve) => {
                const button1 = document.getElementById('choice1');
                const button2 = document.getElementById('choice2');

                button1.innerText = item1;
                button2.innerText = item2;

                button1.onclick = () => resolve(true);
                button2.onclick = () => resolve(false);
            });
        }

        async function merge(left, right) {
            let result = [];

            while (left.length > 0 && right.length > 0) {
                const leftwins = await getChoice(left[0], right[0]);

                if (leftwins) {
                    result.push(left.shift());
                }
                else {
                    result.push(right.shift());
                }
            }

            return result.concat(left).concat(right);
        }

        async function sort(list) {
            if (list.length <= 1) {
                return list;
            }

            let left = [];
            let right = [];

            for (let i = 0; i < list.length; i++) {
                if (i < (list.length/2)) {
                    left.push(list[i]);
                }
                else {
                    right.push(list[i]);
                }
            }

            left = await sort(left);
            right = await sort(right);

            return await merge(left, right);
        }

        async function start() {
            const input = document.getElementById("userList").value;
            let list = input.split('\n').filter(item => item.trim() !== "");
            const isNumbered = document.getElementById("useNumbers").checked;

            document.getElementById("input").style.display = "none";
            document.getElementById("ranker").style.display = "block";

            let ranked = await sort(list);

            document.getElementById("ranker").style.display = "none";

            let output = "";
            if (isNumbered) {
                output = ranked.map((item, index) => `${index + 1}. ${item}`).join("<br>");
            }
            else {
               output = ranked.join("<br>");
            }

            document.getElementById("result").innerHTML = "<h2>Results:</h2> <p>" + output + "</p> <button class='button' onclick='restart()'>Restart</button>";
        }

        async function restart() {
            document.getElementById("input").style.display = "block";
            document.getElementById("result").innerHTML = "";
        }
    </script>

### HTML

    <h1>Binary Choice Ranker</h1>

    <div id="input" style="display: block;">
        <label for="userList">Enter the list to be ranked as items separated by newlines:</label>
        <br>
        <textarea id="userList" name="userList" placeholder="item 1&#10;item 2&#10;item 3&#10;..." rows="10" cols="25"></textarea>
        <br>
        <input type="button" class="button" value="Start" onclick="start()" />

        <p style="font-size: 75%;">This is a simple ranker that uses merge sort. Enter a list of items, then choose
        your preferred item from each given pair. The program will output your preferences in a ranked list.</p>

        <h3>Options:</h3>
        <input type="checkbox" id="useNumbers" name="numbered ranking" />
        <label for="useNumbers">Use numbered ranking</label>
    </div>

    <div id="ranker" style="display: none;">
        <h2>Choose:</h2>
        <div class="flex">
            <button class="button" id="choice1"></button>
            <img src="img/vs.png" alt=" VS ">
            <button class="button" id="choice2"></button>
        </div>
        <p>Click on your preferred choice,<br>or press A for the left choice or L for the right choice.</p>
    </div>

    <div id="result"></div>
