# Golf.se CSV Export

The WEB page [Golf.se](https://www.golf.se) do not support "bulk" 
download of historical results. 

The script below can be used to download all results as a CSV file.

## Instruction

1. Navigate to [Golf.se](https://www.golf.se) in a Google Chrome Browser
2. Open the page "Min handicap"
3. Scroll down and select show 100 rounds.
4. Right click the page and select "Inspect"
5. Copy the script below and paste it into the "Console" window and press ENTER
6. A csv file will be downloaded

The script use "Swedish CSV format", i.e. the file is separated with ; and 
numbers use comma (,) as decimal point. The international standard use , as 
separator and . as decimal point (check comments in the code).

## Script

```js
let CSV_SEPARATOR = ";"; 
// Replace above with below if . is used instead of , for numbers (non-swedish format)
//let CSV_SEPARATOR = ","; 
let headings = [
  "Round",
  "Date",
  "Time",
  "Club",
  "Course Name",
  "Holes",
  "Slope",
  "Course Rating",
  "Par",
  "Gross",
  "Points",
  "HCP adjusted",
  "New HCP",
  "Marker"
]
let csv = [headings.join(CSV_SEPARATOR)];
function text(e,query) {
  let r = e.querySelectorAll(query);
  if (r.length > 0) {
    return r[0].innerText;
  }
  return "";
}
let rows = document.getElementsByClassName("list-item");
for (let row of rows) {
  let cols = [
    text(row,'[class="round-value"]'),
    text(row,'[class="date"]'),
    text(row,'[class="time"]'),
    text(row,'div.club span'),
    text(row,'div.list-item-extended-row div.club span:nth-of-type(2)'),
    text(row,'[class="played-holes"]'),
    text(row,'div.slope span:nth-of-type(2)'),
    text(row,'div.course-rating span:nth-of-type(2)'),
    text(row,'div.par span:nth-of-type(2)'),
    text(row,'[class="adjusted-gross"]'),
    text(row,'[class="points"]'),
    text(row,'[class="hcp-result-adjusted"]'),
    text(row,'[class="new-hcp"]'),
    text(row,'div.marker span:nth-of-type(2)')];
  // Uncomment below if . should be used instead of , for numbers
  if (CSV_SEPARATOR == ",") {
    cols = cols.map(function(item,index){
        return item.replaceAll(',','.')
    });
  }
  csv.push(cols.join(CSV_SEPARATOR));
}
const downloadLink = document.createElement('a');
downloadLink.href = `data:text/plain;charset=utf-8, ${encodeURIComponent(csv.join("\n"))}`;
downloadLink.download = 'golfexport.csv';
document.body.appendChild(downloadLink);
downloadLink.click();
```

