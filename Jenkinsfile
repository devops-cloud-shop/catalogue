@Library('jenkins-shared-library') _

def configMap = [
    PROJECT : "roboshop",
    COMPONENT: "catalogue"
]

if(! env.BRANCH_NAME.equalsIgnoreCase('main')){
    nodeJSEKSPipeline(configMap)
}
else {
    echo "Please follow the CR process"
}