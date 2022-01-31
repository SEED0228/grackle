# grackle

## Requirements
- JDK11+
- npm 8.1.4
- gradle 6.5 or 7.3.1

## What is Grackle?

This project is a tool to visualize the program repair process of an Automated Program Repair tool written in Java for Java, [kGenProg](https://github.com/kusumotolab/kGenProg).
It focuses specifically on the visualization of **suspicious** values.

## How to use

Use the following command to clone the project. Since this project contains submodules, the submodules will not be downloaded without the "--recursive" option.

```
git clone --recursive https://github.com/SEED0228/grackle.git
```

First,build the kGenProg project. Go to the kGenProg directory to do that.

```
cd kGenProg
```

Build with the gradle command. At this point, please make sure you have version 6.5.1 or 7.3.1 of gradle installed on your PC.

```
gradle build
```

Secondly, let kGenProg, a modified version of the original that can output source code and suspicious values as JSON files do the automatic program modification.
To do so, place the program and unit test program in the kgenprog-jar folder and execute the following command. For more information about the kGenProg command, please see [here](https://github.com/SEED0228/kGenProg?organization=SEED0228&organization=SEED0228#usage). Note that you will need to use the "--history-record" option to output JSON to use this project.<br>

For example, run a command like the following. At this point, please make sure you have version 11 of jdk installed on your PC.

```
java -jar build/libs/kGenProg-1.8.2.jar -r ./ -s example/CloseToZero04/src/example/CloseToZero.java -t example/CloseToZero04/src/example/CloseToZeroTest.java --history-record
```

If you run the above command, you should see kgenprog-out output.<br>
Then, return to the directory of this project.

```
cd ..
```

Finally, set up the project.

```
npm install
```

In addition, compiles and hot-reloads for development.

```
npm run serve
```

Then, access the [local host on port 8080](http://localhost:8080/) with a browser and select the JSON file generated earlier (maybe kGenProg/kgenprog-out/history.json) in the selection form on the upper right.<br>
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
