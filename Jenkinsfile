node('guoxl-jnlp') {
    stage('Clone'){
        echo "1. Clone Stage"
       // git url: "https://github.com/k8svip/jenkins-demo.git"
        checkout scm
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
            if (env.BRANCH_NAME != 'master') {
                build_tag = "${env.BRANCH_NAME}-${build_tag}"
            }
        }
    }
    
    stage('Test'){
        echo "2. Test Stage"
    }
    
    stage('Build'){
        echo "3. Build Docker Image Stage"
        sh "docker build -t k8svip/jenkins-demo:${build_tag} ."
    }
    stage('Push'){
        echo "4. Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'hubDockerAuth', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
            sh "docker login -u ${dockerHubUser} -p ${dockerHubPassword}"
            sh "docker push k8svip/jenkins-demo:${build_tag}"
        }
    }
    
    stage('Deploy'){
    echo "5. Change YAML FILE Stage"
    //  def userInput = input(
    //     id: 'userInput',
    //     message: 'Choose a deploy environment',
    //     parameters: [
    //         [
    //             $class: 'ChoiceParameterDefinition',
    //             choices: "Dev\nQA\nProd",
    //             name: 'Env'
    //         ]
    //     ]
    // )
    if (env.BRANCH_NAME == 'master') {
        input "确认要部署线上环境吗？"
    }
//    echo "This is a deploy step to ${userInput} env."
    sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
    sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
    sh "kubectl get pods"
    sh "cat k8s.yaml"
    sh "kubectl apply -f k8s.yaml --validate=false"
    }

}
