@Library('jenkins-shared-library') _

def configMap = [
    PROJECT : "roboshop",
    COMPONENT: "catalogue"
]
// if branch name not equals to main then run CI pipeline//
if(! env.BRANCH_NAME.equalsIgnoreCase('main')){
    nodeJSEKSPipeline(configMap)
}
else {
    echo "Please follow the CR process"
}