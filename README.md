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

## Script without score card

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
    text(row,'.round-value'),
    text(row,'.date'),
    text(row,'.time'),
    text(row,'.club span'),
    text(row,'.list-item-extended-row .club span:nth-of-type(2)'),
    text(row,'.played-holes'),
    text(row,'.slope span:nth-of-type(2)'),
    text(row,'.course-rating span:nth-of-type(2)'),
    text(row,'.par span:nth-of-type(2)'),
    text(row,'.adjusted-gross'),
    text(row,'.points'),
    text(row,'.hcp-result-adjusted'),
    text(row,'.new-hcp'),
    text(row,'.marker span:nth-of-type(2)')];
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

## Script with score card

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
  "Marker",
];
for (let i = 1 ; i <= 18 ; i++) {
  headings.push(`${i} index`);
  headings.push(`${i} par`);
  headings.push(`${i} strokes`);
  headings.push(`${i} adjusted-gross`);
  headings.push(`${i} points`);
}
let csv = [headings.join(CSV_SEPARATOR)];
function text(e,query) {
  let r = e.querySelectorAll(query);
  if (r.length > 0) {
    return r[0].innerText;
  }
  return "";
}
// Expand all scorecards
let rows = document.getElementsByClassName("list-item");
for (let row of rows) {
    if (row.querySelectorAll('.scorecard-rows').length == 0) {
        const e = row.querySelectorAll('.toggle-scorecard');
        if (e.length > 0 && e[0].hasAttribute('onclick')) {
            e[0].click();
        } 
    }
}
// Wait until all score cards are expanded until fetching data
setTimeout(() => {
    let rows = document.getElementsByClassName("list-item");
    for (let row of rows) {
        let cols = [
          text(row,'.round-value'),
          text(row,'.date'),
          text(row,'.time'),
          text(row,'.club span'),
          text(row,'.list-item-extended-row .club span:nth-of-type(2)'),
          text(row,'.played-holes'),
          text(row,'.slope span:nth-of-type(2)'),
          text(row,'.course-rating span:nth-of-type(2)'),
          text(row,'.par span:nth-of-type(2)'),
          text(row,'.adjusted-gross'),
          text(row,'.points'),
          text(row,'.hcp-result-adjusted'),
          text(row,'.new-hcp'),
          text(row,'.marker span:nth-of-type(2)')];
        const sr = row.querySelectorAll('.scorecard-rows');
        if (sr.length > 0) {
            let sc = sr[0];
            eIndex = sc.querySelectorAll('.index')[0].querySelectorAll('.cell-inner');
            ePar = sc.querySelectorAll('.par')[0].querySelectorAll('.cell-inner');
            eStrokes = sc.querySelectorAll('.strokes')[0].querySelectorAll('.cell-inner');
            eAGross = sc.querySelectorAll('.adjusted-gross')[0].querySelectorAll('.cell-inner');
            ePoints= sc.querySelectorAll('.points')[0].querySelectorAll('.cell-inner');
            for (let i = 0 ; i < 19 ; i++) {
                if (i != 9) {
                    cols.push(eIndex[i].innerText);
                    cols.push(ePar[i].innerText);
                    cols.push(eStrokes[i].innerText);
                    cols.push(eAGross[i].innerText);
                    cols.push(ePoints[i].innerText);
                } 
            }
        } 
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
}, 2000);
```

