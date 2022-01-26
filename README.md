# grackle

## What is Gracle?

This project is a tool to visualize the program repair process of an Automated Program Repair tool written in Java for Java, [kGenProg](https://github.com/kusumotolab/kGenProg).
It focuses specifically on the visualization of **suspicious** values.

## How to use

First, let kGenProg, a modified version of the original that can output source code and suspicious values as JSON files do the automatic program modification.
To do so, place the program and unit test program in the kgenprog-jar folder and execute the following command
For example, run a command like the following.
```
java -jar kgenprog-jar/kGenProg-1.8.2.jar -r ./kgenprog-jar -s kgenprog-jar/CloseToZero.java -t kgenprog-jar/CloseToZeroTest.java --history-record
```
If you run the above command, you should see kgenprog-out output.<br>
Then, set up the project.
```
npm install
```
In addition, compiles and hot-reloads for development.
```
npm run serve
```
Then, access the [local host on port 8080](http://localhost:8080/) with a browser and select the JSON file generated earlier (maybe kgenprog-out/history.json) in the selection form on the upper right.<br>
Then you will be able to see the automatic modification process of kGenProg.
## Project setup
```
npm install
```

### Compiles and hot-reloads for development
```
npm run serve
```

### Compiles and minifies for production
```
npm run build
```

### Lints and fixes files
```
npm run lint
```

### Customize configuration
See [Configuration Reference](https://cli.vuejs.org/config/).
