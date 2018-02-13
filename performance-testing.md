# Performance Testing

We usually want to generate a load for our services to measure the performance. Usually we want to do a certain set of operations every second, 5th second, minute or so.

We are going to create script that will try to call REST API 5 times every second. 

    const _ = require('lodash');
    const cron = require('node-cron');
    const axios = require('axios');

    let sent = 0;
    let received = 0;
    let inProgress = 0;

    console.log("Starting...");
    const reportStatus = function (values) {
        let report = '';
        values.forEach(response => {
            if (_.has(response, 'status')) {
                report += response.status + ', ';
            } else if (_.has(response, 'code')) {
                report += response.code + ', ';
            } else {
                report += response.response.status + ', ';
            }
        })
        console.log(`Report: ${report}`);
        received += values.length;
        inProgress -= values.length;
        console.log(`Sent: ${sent}, Received: ${received}, InProgress: ${inProgress}`);
    };
    const createTasks = function () {
        let howMany = 5;
        const tasks = _.range(0, howMany).map(it => {
            return axios.get('https://api.github.com/search/repositories?q=github&page=1&per_page=1')
        });
        return tasks;
    };
    const task = cron.schedule('* * * * * * *', function () {
        console.log(new Date());
        const tasks = createTasks();
        let howMany = tasks.length;
        sent += howMany;
        inProgress += howMany;
        console.log(`Sent: ${sent}, Received: ${received}, InProgress: ${inProgress}`);

        Promise.all(tasks.map(p => p.catch(e => e)))
            .then(function (values) {
                reportStatus(values);
            });
    });

    task.start();

When we run the code, we can observe that couple of requests and with 200 but the rest will end with 403, becuase github will refuse too many requests. 

> You can authenticate to make more requests.

```
Starting...
2018-02-13T22:17:17.825Z
Sent: 5, Received: 0, InProgress: 5
Report: 200, 200, 200, 200, 200, 
Sent: 5, Received: 5, InProgress: 0
2018-02-13T22:17:18.830Z
Sent: 10, Received: 5, InProgress: 5
Report: 200, 200, 200, 200, 200, 
Sent: 10, Received: 10, InProgress: 0
2018-02-13T22:17:19.833Z
Sent: 15, Received: 10, InProgress: 5
Report: 403, 403, 403, 403, 403, 
Sent: 15, Received: 15, InProgress: 0
2018-02-13T22:17:20.837Z
Sent: 20, Received: 15, InProgress: 5
Report: 403, 403, 403, 403, 403, 
Sent: 20, Received: 20, InProgress: 0
```



