
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
