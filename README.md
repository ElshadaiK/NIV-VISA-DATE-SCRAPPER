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
const checkAppointments = async () => {
    const url = `https://ais.usvisa-info.com/en-et/niv/schedule/${appointmentId}/appointment/days/19.json?appointments[expedite]=false`;
    
    try {
        const response = await fetch(url);
        const data = await response.json();
        
        if (data.length === 0) {
            console.log('No appointments available');
            return;
        }

        const earliestDate = new Date(data[0].date);
        if (earliestDate <= desiredDate) {
            alert(`Earliest date available: ${earliestDate.toDateString()}`);
            // To use desktop notification instead of alert, comment out the line above and uncomment the line below
            // notifyUser(`Earliest date available: ${earliestDate.toDateString()}`);
        } else {
            console.log('No earlier date available');
        }
    } catch (error) {
        console.error('Error fetching appointment data:', error);
    }
};

// Set interval to check appointments every minute (adjustable)
const setScrapeInterval = (intervalInMinutes) => {
    const intervalInMilliseconds = intervalInMinutes * 60 * 1000;
    return setInterval(checkAppointments, intervalInMilliseconds);
};

// Example usage: Set the interval to check every 1 minute (can be adjusted)
const scrapeInterval = setScrapeInterval(1);
```
