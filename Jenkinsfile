node {
    stage ('Clone sources') {
        git url: 'https://github.com/emc-mongoose/mongoose.git'
    }
    
    stage('CLEAN') {
        sh './gradlew clean'
    }

    try {
        stage('Test') {
            sh './gradlew test --continue'
        }
        echo 'Test successful'
    } catch (err) {
        echo 'Something wrong with tests. Please check it'
        echo err
        throw err
    } finally {
        def currentResult = currentBuild.result ?: 'SUCCESS'
        if (currentResult == 'UNSTABLE') {
            echo 'This build was marked as unstable'
        }

        def previousResult = currentBuild.previousBuild?.result
        if (previousResult != null && previousResult != currentResult) {
            echo 'he Pipeline status has changed'
        }

        echo 'Final steps:'
        stage('Build') {
            sh './gradlew build'
        }
        stage('Test Results') {
            junit '**/build/test-results/test/*.xml'
        }
        stage('Package') {
            sh 'tar -czvf build/mongoose-${BUILD_ID}.tgz build/libs'
            archiveArtifacts 'build/*.tgz'
        }    
        
    }
}
