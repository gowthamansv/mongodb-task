# Mongodb-Task

## Collection

## Aggregations

### sample data

users 
```javascript
{
  "_id": {
    "$oid": "673cc0c54af790c4dfd9c92c"
  },
  "userId": 1,
  "userName": "gowthaman",
  "userEmail": "gowthaman@gmail.com",
  "mentorId": 2,
  "info": [
    {
      "date": "2020-10-09",
      "topic": "HTML",
      "task_status": "submitted",
      "problems_solved": 8,
      "status": "present"
    },
    {
      "date": "2020-10-10",
      "topic": "TailWind-CSS",
      "task_status": "submitted",
      "problems_solved": 9,
      "status": "present"
    },
    {
      "date": "2020-10-15",
      "topic": "JS",
      "task_status": "submitted",
      "problems_solved": 10,
      "status": "present"
    },
    {
      "date": "2020-10-16",
      "topic": "Axios",
      "task_status": "submitted",
      "problems_solved": 6,
      "status": "present"
    },
    {
      "date": "2020-10-20",
      "topic": "React",
      "task_status": "submitted",
      "problems_solved": 18,
      "status": "present"
    },
    {
      "date": "2020-10-22",
      "topic": "React-Router",
      "task_status": "submitted",
      "problems_solved": 12,
      "status": "present"
    },
    {
      "date": "2020-10-30",
      "topic": "MySQL",
      "task_status": "submitted",
      "problems_solved": 25,
      "status": "present"
    },
    {
      "date": "2020-11-01",
      "topic": "MongoDB",
      "task_status": "submitted",
      "problems_solved": 6,
      "status": "present"
    }
  ]
}
```
topics
```javascript
{
  "_id": {
    "$oid": "673cfc044af790c4dfd9c984"
  },
  "topicId": 1,
  "topic": "HTML",
  "topicDate": "2020-10-9"
}
```
tasks
```javascript
{
  "_id": {
    "$oid": "673cfe2e4af790c4dfd9c98e"
  },
  "taskId": 1,
  "topicId": 1,
  "dueDate": "2020-10-09"
}
```
mentors
```javascript
{
  "_id": {
    "$oid": "673cc1564af790c4dfd9c93c"
  },
  "mentorId": 1,
  "mentorName": "ramesh",
  "mentorEmail": "ramesh@gmail.com"
}
```
Company_drivers
```javascript
{
  "_id": {
    "$oid": "673ce3bf4af790c4dfd9c941"
  },
  "company": "IBM",
  "driveDates": "2020-09-04"
}
```
codekata
```javascript
{
  "_id": {
    "$oid": "673d05ff4af790c4dfd9c99e"
  },
  "topic": "HTML",
  "question": 20
}
```
attendance
```javascript
{
  "_id": {
    "$oid": "673d08f54af790c4dfd9c9b7"
  },
  "topic": "Axios",
  "date": "2020-10-16",
  "users": [
    {
      "userId": 22,
      "status": "absent"
    },
    {
      "userId": 28,
      "status": "absent"
    },
    {
      "userId": 41,
      "status": "absent"
    }
  ]
}
```
Questions

### 1. Find all the topics and tasks which are thought in the month of October

```javascript
//for topics
db.getCollection('topics').aggregate(
  [
    {
      $match: {
        topicDate: {
          $gte: '2020-10-01',
          $lte: '2020-10-31'
        }
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);

//for tasks
db.getCollection('tasks').aggregate(
  [
    {
      $match: {
        dueDate: {
          $gte: '2020-10-01',
          $lte: '2020-10-31'
        }
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
output

### 2. Find all the company drives which appeared between 15 oct-2020 and 31-oct-2020
```javascript
db.getCollection('company_drives').aggregate(
  [
    {
      $match: {
        driveDates: {
          $gte: '2020-10-15',
          $lte: '2020-10-31'
        }
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
output

### 3. Find all the company drives and students who are appeared for the placement.
```javascript

```
output

### 4. Find the number of problems solved by the user in codekata
```javascript
db.getCollection('users').aggregate(
  [
    { $unwind: '$info' },
    {
      $group: {
        _id: '$userName',
        totalProblemsSolved: {
          $sum: '$info.problems_solved'
        }
      }
    }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
output

### 5. Find all the mentors with who has the mentee's count more than 15
```javascript
db.getCollection('users').aggregate(
  [
    {
      $group: {
        _id: '$mentorId',
        menteeCount: { $sum: 1 }
      }
    },
    { $match: { menteeCount: { $gt: 15 } } }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
output

### 6. Find the number of users who are absent and task is not submitted  between 15 oct-2020 and 31-oct-2020
```javascript
db.getCollection('attendance').aggregate(
  [
    {
      $match: {
        date: {
          $gte: '2020-10-15',
          $lte: '2020-10-31'
        },
        'users.status': {
          $in: ['absent', 'not submitted']
        }
      }
    },
    { $unwind: '$users' },
    {
      $match: {
        'users.status': {
          $in: ['absent', 'not submitted']
        }
      }
    },
    { $group: { _id: null, count: { $sum: 1 } } },
    { $project: { _id: 0, count: 1 } }
  ],
  { maxTimeMS: 60000, allowDiskUse: true }
);
```
