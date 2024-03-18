## Diagram infrastructure
...
"devDependencies": {
      "cdk-dia": "^0.10.0",
      ...

and specify a new build target:  

...
"scripts": {
    "gen-diagram": "cd build && npx cdk-dia —stacks YourStackNameGoesHere AndAnotherStackHere",
    "visualize-stack": "brazil-build app run gen-diagram && open build/diagram.png"

