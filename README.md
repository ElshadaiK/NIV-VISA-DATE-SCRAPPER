# US-NIV-VISA-DATE-SCRAPER

This script checks for earlier available appointment dates on the US Non-immigrant Visa appointment setting website for Ethiopia. 

**Note:** If you are using it for another country, follow the instructions in the **Customization** section.

## How to Use

1. Log in to [US Visa Appointment](https://ais.usvisa-info.com).
2. Click on the "Continue" button to go to the actions page.
3. Open the Console on your browser (usually F12 or right-click -> Inspect -> Console).
4. Copy and paste the provided script into the console and press Enter to run it.

The script will run every 60 seconds, checking for earlier appointment dates than the specified desired date. If an earlier date is found, the script will display an alert with the earliest available date.

## Customization

### Country and Visa Center Code

To adapt this script for another country:

1. Inspect the network calls in your browser while on the reschedule appointment page.
2. Look for an XHR call similar to `19.json?appointments[expedite]=false`. Replace `19` with the appropriate visa center code for your country.
3. Replace `en-et` in the URL `https://ais.usvisa-info.com/en-et/niv/schedule/` with your country's code (e.g., `en-us` for the USA).

### Desired Date

Modify the desired date by changing the following line in the script:

```javascript
const desiredDate = new Date('2024-04-30');
```

### Check Interval

You can adjust how often the script checks for new appointments by modifying the interval in minutes:

```javascript
const scrapeInterval = setScrapeInterval(1); // Check every 1 minute
```

## Script

Copy and paste the following script into the browser console:

```javascript
'use strict';

// Desired date to find any earlier appointments (yyyy-mm-dd format)
const desiredDate = new Date('2024-04-30');

// Audio and Notification setup
const audioSource = 'https://cdn.freesound.org/previews/533/533869_5828667-lq.mp3';
const audio = new Audio(audioSource);

// Extract appointment ID from URL path
const getAppointmentId = () => {
    const pathSegments = window.location.pathname.split('/');
    return pathSegments[4];
};

const appointmentId = getAppointmentId();

// Notify user with audio and desktop notification
const notifyUser = async (message) => {
    await audio.play();
    if (!("Notification" in window)) {
        alert("This browser does not support desktop notifications.");
    } else if (Notification.permission === "granted") {
        new Notification(message);
    } else if (Notification.permission !== "denied") {
        const permission = await Notification.requestPermission();
        if (permission === "granted") {
            new Notification(message);
        }
    }
};

// Check for available appointments and notify user if an earlier date is found

const checkAppointments = setInterval(function () {
    $.getJSON('https://ais.usvisa-info.com/en-et/niv/schedule/' + appointmentId + '/appointment/days/19.json?appointments[expedite]=false', function (data) {
        if (data.length === 0) { console.log('No appointments available'); }
        if (data.length > 0) {
            const earliestDate = new Date(data[0].date);
            if (earliestDate.getTime() <= desiredDate.getTime()) {
                // window.alert('Earliest date available: ' + earliestDate.toDateString());
                // if you're busy using another window and would like to be notified if an appointment is found, 
                // comment the line above and uncomment the line below
                notifyUser('Earliest date available: ' + earliestDate.toDateString());
            } else {
                console.log('No date available');
            }
        }
    });
}, 60000);

// Set interval to check appointments every minute (adjustable)
const setScrapeInterval = (intervalInMinutes) => {
    const intervalInMilliseconds = intervalInMinutes * 60 * 1000;
    return setInterval(checkAppointments, intervalInMilliseconds);
};

// Example usage: Set the interval to check every 1 minute (can be adjusted)
const scrapeInterval = setScrapeInterval(1);
```

### Additional Notes

- Ensure your browser supports desktop notifications.
- You can replace `window.alert` with `notifyUser` for desktop notifications by uncommenting the relevant line in the script.
- This script uses `fetch` for making network requests and `async/await` for asynchronous operations, ensuring modern and clean code practices.
