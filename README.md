# US-NIV-VISA-DATE-SCRAPPER

Please note that this script only works for the US Non-immigrant Visa appointment setting website in Ethiopia.

If you're using it for another country, go to reschedule appointment -> inspect -> look for xhr call that looks like
19.json?appointments[expedite]=false (the 19 is Addis Ababa's visa center code in this case). Also replace the en-et from the
url <https://ais.usvisa-info.com/en-et/niv/schedule/> below by your country's code. (the et part is for Ethiopia)

To use this script, do the following:

1. Log in to <https://ais.usvisa-info.com>
2. Click on the "Continue" button to go to the actions page
3. Open up Console on your browser
4. Copy and paste this script into the console and press Enter to run the script

The script will then run automatically every 60 seconds, checking for earlier appointment dates than the specified desired date. If an earlier date is found, the script will display an alert with the earliest available date.

## Customization

### Desired Date

You can customize the desired date by modifying the following line in the script:

```javascript
'use strict';

// Enter the desired date (to find any earlier appointments from it) in yyyy-mm-dd format
var desiredDate = new Date('2024-04-30');

// Audio and Notification setup
const audioSource = 'https://cdn.freesound.org/previews/533/533869_5828667-lq.mp3';
const audio = new Audio(audioSource);

var path = window.location.pathname,
    id = path.split('/')[4];

function notifyUser(message) {
    await audio.play();
    if (!("Notification" in window)) {
        alert("This browser does not support desktop notifications.");
    } else if (Notification.permission === "granted") {
        new Notification(message);
    } else if (Notification.permission !== "denied") {
        Notification.requestPermission().then(function(permission) {
            if (permission === "granted") {
                new Notification(message);
            }
        });
    }
}

var scrapeInterval = setInterval(function () {
    $.getJSON('https://ais.usvisa-info.com/en-et/niv/schedule/' + id + '/appointment/days/19.json?appointments[expedite]=false', function (data) {
        if (data.length === 0) {
            console.log('Not available');
        }
        if (data.length > 0) {
            var earliestDate = new Date(data[0].date);
            if (earliestDate.getTime() <= desiredDate.getTime()) {
                window.alert('Earliest date available: ' + earliestDate.toDateString());
                // if you're busy using another window and would like to be notified if an appointment is found, 
                // comment the line above and uncomment the line below
                // notifyUser('Earliest date available: ' + earliestDate.toDateString());
            } else {
                console.log('No date available');
            }
        }
    });
}, 60000);
