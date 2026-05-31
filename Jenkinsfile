@Library('jenkins-shared-library') _

def configMap = [
    PROJECT  : "roboshop",
    COMPONENT: "catalogue"
]

if (env.BRANCH_NAME != "main") {
    echo "Running pipeline on branch: ${env.BRANCH_NAME}"
    nodejsPipeline(configMap)
} else {
    echo "Skipping pipeline on main branch"
}
