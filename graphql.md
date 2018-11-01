# GraphQL

* [documentation](https://graphql.github.io/learn/)
* [editor](https://github.com/graphql/graphiql) for GraphQL
* [example](https://medium.com/codingthesmartway-com-blog/creating-a-graphql-server-with-node-js-and-express-f6dddc5320e1) how to use GraphQL with NodeJS

## Example

These example are taken from the article above \(they are also little bit updated\).

    const express = require('express');
    const express_graphql = require('express-graphql');
    const {buildSchema} = require('graphql');
    // GraphQL schema
    const schema = buildSchema(`
        type Query {
            message: String
        }
    `);
    // Root resolver
    const root = {
      message: () => 'Hello World!'
    };
    // Create an express server and a GraphQL endpoint
    const app = express();
    app.use('/graphql', express_graphql({
      schema: schema,
      rootValue: root,
      graphiql: true
    }));
    app.listen(4000, () => console.log('Express GraphQL Server Now Running On localhost:4000/graphql'));

Now lets call the API using this UI [http://localhost:4000/graphql](http://localhost:4000/graphql).

```
{
    message
}
```

Let's do another more complex example. 

    const express = require('express');
    const express_graphql = require('express-graphql');
    const { buildSchema } = require('graphql');
    // GraphQL schema
    const schema = buildSchema(`
        type Query {
            course(id: Int!): Course
            courses(topic: String): [Course]
        },
        type Course {
            id: Int
            title: String
            author: String
            description: String
            topic: String
            url: String
        }
    `);
    const coursesData = [
      {
        id: 1,
        title: 'The Complete Node.js Developer Course',
        author: 'Andrew Mead, Rob Percival',
        description: 'Learn Node.js by building real-world applications with Node, Express, MongoDB, Mocha, and more!',
        topic: 'Node.js',
        url: 'https://codingthesmartway.com/courses/nodejs/'
      },
      {
        id: 2,
        title: 'Node.js, Express & MongoDB Dev to Deployment',
        author: 'Brad Traversy',
        description: 'Learn by example building & deploying real-world Node.js applications from absolute scratch',
        topic: 'Node.js',
        url: 'https://codingthesmartway.com/courses/nodejs-express-mongodb/'
      },
      {
        id: 3,
        title: 'JavaScript: Understanding The Weird Parts',
        author: 'Anthony Alicea',
        description: 'An advanced JavaScript course for everyone! Scope, closures, prototypes, this, build your own framework, and more.',
        topic: 'JavaScript',
        url: 'https://codingthesmartway.com/courses/understand-javascript/'
      }
    ]
    const getCourse = function(args) {
      const id = args.id;
      return coursesData.filter(course => {
        return course.id == id;
      })[0];
    }
    const getCourses = function(args) {
      if (args.topic) {
        const topic = args.topic;
        return coursesData.filter(course => course.topic === topic);
      } else {
        return coursesData;
      }
    }
    const root = {
      course: getCourse,
      courses: getCourses
    };
    // Create an express server and a GraphQL endpoint
    const app = express();
    app.use('/graphql', express_graphql({
      schema: schema,
      rootValue: root,
      graphiql: true
    }));
    app.listen(4000, () => console.log('Express GraphQL Server Now Running On localhost:4000/graphql'));

Now we can play with this in the graphql ui. 

```
query getSingleCourse($courseID: Int!) {
    course(id: $courseID) {
        title
        author
        description
        topic
        url
    }
}
# parameters
{ 
    "courseID":1
}
```

Multiple parameters.

```
query getCourseWithFragments($courseID1: Int!, $courseID2: Int!) {
  course1: course(id: $courseID1) {
    ...courseFields
  }
  course2: course(id: $courseID2) {
    ...courseFields
  }
}

fragment courseFields on Course {
  title
  author
  description
  topic
  url
}

# parameters
{ 
  "courseID1":1,
  "courseID2":2
}


```

Mutation of data on server. 

    // GraphQL schema
    const schema = buildSchema(`
        type Query {
            course(id: Int!): Course
            courses(topic: String): [Course]
        },
        type Mutation {
            updateCourseTopic(id: Int!, topic: String!): Course
        }
        type Course {
            id: Int
            title: String
            author: String
            description: String
            topic: String
            url: String
        }
    `);


    const updateCourseTopic = function({id, topic}) {
        coursesData.map(course => {
            if (course.id === id) {
                course.topic = topic;
                return course;
            }
        });
        return coursesData.filter(course => course.id === id) [0];
    }
    const root = {
      course: getCourse,
      courses: getCourses,
      updateCourseTopic: updateCourseTopic
    };

    # query
    mutation updateCourseTopic($id: Int!, $topic: String!) {
      updateCourseTopic(id: $id, topic: $topic) {
        ... courseFields
      }
    }
    # parameters{
      "id": 1,
      "topic": "JavaScript"
    }



