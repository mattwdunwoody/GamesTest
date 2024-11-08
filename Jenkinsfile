
node('appserver')
{
    def app
    stage("Cloning Git")
    {
        checkout scm
    }

    stage("Snyk SCA SAST testing")
    {
        snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'snyk_id',
            severity: 'critical'
        )
    }

    stage("SonarQube-Analysis")
    {
        agent
        {
            label 'appserver'
        }
        steps
        {
            script
            {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('SonarQube')
                {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=gameapp \
                    --Dsonar.sources=."
                }
            }
        }
    }

    stage("Build-and-Tag")
    {
        app = docker.build('widmatg/snake-game-img')
    }

    stage("Post-to-Dockerhub")
    {
        docker.withRegistry('https://registry.hub.docker.com', 'docker_credentials')
        {
            app.push('latest')
        }
    }

    stage("Deploy")
    {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }
}
