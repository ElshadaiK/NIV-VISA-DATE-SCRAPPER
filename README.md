# US-NIV-VISA-DATE-SCRAPPER

Please note that this script only works for the Ethiopian NIV VISA appointment setting website.

If you're using it for another country, go to reschedule appointment -> inspect -> look for xhr call that looks like
19.json?appointments[expedite]=false (the 19 is Addis Ababa's visa center code in this case). Also replace the en-et from the
url <https://ais.usvisa-info.com/en-et/niv/schedule/> below by your country's code. (the et part is for Ethiopia)

To use this script, do the following:

1. Log in to <https://ais.usvisa-info.com>
2. Click on the "Continue" button to go to the actions page
3. Open up Console on your browser
4. Copy and paste this script into the console and press Enter to run the script

```javascript
'use strict';

// Enter the desired date (to find any earlier appointments from it) in yyyy-mm-dd format
var desiredDate = new Date('2023-06-14');

var path = window.location.pathname,
    id = path.split('/')[4];

var scrapeInterval = setInterval(function () {
    $.getJSON('https://ais.usvisa-info.com/en-et/niv/schedule/' + id + '/appointment/days/19.json?appointments[expedite]=false', function (data) {
        if (data.length === 0) {
            console.log('Not available');
        }
        if (data.length > 0) {
            var earliestDate = new Date(data[0].date);
            if (earliestDate.getTime() <= desiredDate.getTime()) {
                window.alert('Earliest date available: ' + earliestDate.toDateString());
            } else {
                console.log('No date available');
            }
        }
    });
}, 10000);
