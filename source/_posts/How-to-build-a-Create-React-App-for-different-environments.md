---
title: How to build a Create-React-App for different environments
date: 2019-09-27 15:01:16
tags:
---

When developing the React app with back-end API, we often need to define environment variables for different environments. For example, in my project, I have two environments: `test` and `prod`, which are connected to different serverless backend. This is to make sure that the developer can work in an environment that is isolated from the production environment.

Suppose we are going to deploy our React app to the **testing** and **production** server using **test** and **prod** environment variables and we do not want to eject the project.

# Create Environment Variable Files
Create files called `.env.test` and `.env.prod` at the root of your project.

Set environment variables in `.env.test` and `.env.prod`files.

For example, in the `.env.test` we have `REACT_APP_API_URL=http://api-test.example.com`, in the .env.prod we have `REACT_APP_API_URL=http://api.example.com`.

According to the Create React App document, we must create custom environment variables beginning with REACT_APP_.

# Install env-cmd
This is a simple node program for executing commands using an environment from an env file.
```bash
$ npm install env-cmd --save
```

Update scripts
Add some new scripts to your package.json.
```json
{
  "scripts": {
    "build:test": "env-cmd -f .env.test npm run build"
    "build:prod": "env-cmd -f .env.prod npm run build"
  }
}
```
# Usage
for example:
```javascript
var url = process.env.REACT_APP_API_URL + '/sms?key=' + this.state.currentFileName;
```
# Deploy
```bash
# build for test server
$ npm run build:test

# build for prod server
$ npm run build:prod
```
