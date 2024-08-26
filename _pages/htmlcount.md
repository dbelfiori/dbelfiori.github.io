---
layout: page
title: HTML Tag Counter
permalink: /tagcount/
---

<style>
    #resultTable {
        width: 400px;
        border-collapse: collapse;
        margin-top: 20px;
        display: none; /* Hide the table initially */
    }
    th, td {
        border: 1px solid black;
        padding: 8px;
        text-align: left;
        font-family: Arial, Helvetica, sans-serif;
    }
    th {
        background-color: #f2f2f2;
    }
    .no-open-tags {
        background-color: #ffcccc; /* Light red color for rows with no open tags */
    }
</style>
    
    <h1>HTML Tag Counter</h1>
    <p>This will count HTML/XHTML tags to check for matching pairs of open/close tags, or missing tags (&lt;html&gt;, &lt;head&gt;, &lt;title&gt;, and &lt;body&gt;). This is particularly useful for finding errors in tables. No data is sent to the server—this is done locally on your computer. This is not as good as a <a href="https://validator.w3.org/" target="_blank">validator</a>, but is more forgiving of non-standard HTML.</p>
    <hr>
    <p>Note: This is a just a quick check for matched pairs of tags. It works for fairly simple HTML. If there are “<” or “>” characters within the text or tag attributes, the count may be incorrect.</p>
    <hr>
    <p>Paste the contents of an HTML or XHTML file here:</p>
    <textarea id="htmlInput" rows="10" cols="50" placeholder="Paste your HTML code here"></textarea><br>
    <button onclick="countTags()">Count Tags</button>
    <table id="resultTable">
        <thead>
            <tr>
                <th>Tag Name</th>
                <th>Open Tags</th>
                <th>Closed Tags</th>
            </tr>
        </thead>
        <tbody>
        </tbody>
    </table>

    <script>
        function countTags() {
            const htmlInput = document.getElementById('htmlInput').value;
            const tempElement = document.createElement('div');
            tempElement.innerHTML = htmlInput;

            const tags = ['a', 'body', 'head', 'html', 'img', 'span', 'table', 'td', 'title', 'tr'];
            const resultTableBody = document.getElementById('resultTable').getElementsByTagName('tbody')[0];
            resultTableBody.innerHTML = ''; // Clear previous results

            tags.forEach(tag => {
                let openTags = 0;
                let closedTags = 0;

                if (tag === 'img') {
                    const imgTags = htmlInput.match(/<img[^>]*>/g) || [];
                    openTags = imgTags.length;
                    closedTags = imgTags.filter(tag => tag.endsWith('/>')).length;
                } else {
                    openTags = (htmlInput.match(new RegExp(`<${tag}(\\s|>)`, 'g')) || []).length;
                    closedTags = (htmlInput.match(new RegExp(`</${tag}>`, 'g')) || []).length;
                }

                const row = resultTableBody.insertRow();
                if (openTags === 0) {
                    row.classList.add('no-open-tags');
                }
                const cell1 = row.insertCell(0);
                const cell2 = row.insertCell(1);
                const cell3 = row.insertCell(2);

                cell1.textContent = tag;
                cell2.textContent = openTags;
                cell3.textContent = closedTags;
            });

            // Show the table after counting tags
            document.getElementById('resultTable').style.display = 'table';
        }
    </script>