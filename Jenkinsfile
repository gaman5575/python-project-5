pipeline {
    agent any

    stages {
        stage('Git Checkout') {
            steps {
                // Checkout your source code repository
                git branch: 'main',
                    url: 'https://github.com/gaman5575/python-project-5.git'
            }
        }

        stage('Clone Repositories and Update Version.yaml') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'git-credentials', usernameVariable: 'GIT_CREDENTIALS_USR', passwordVariable: 'GIT_CREDENTIALS_PSW'),
                                 usernamePassword(credentialsId: 'docker_credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    script {
                        def new_version = ""
                        sh """
                            echo "Cloning the repository"

                            git clone https://${GIT_CREDENTIALS_USR}:${GIT_CREDENTIALS_PSW}@github.com/${GIT_CREDENTIALS_USR}/python-project-5.git
                            cd python-project-5

                            echo "Configuring Git"

                            git config user.email '${GIT_CREDENTIALS_USR}@gmail.com'
                            git config user.name '${GIT_CREDENTIALS_USR}'

                            echo "Finding Version in version.yml and Updating"

                            current_version=\$(grep -oP '(?<=version: )\\K[\\d\\.]+' version.yml)
                            new_major_version=\$(echo "\$current_version" | awk -F. '{print \$1 + 1}')
                            new_version="\${new_major_version}.0.0"
                            new_branch="\${new_version}-release"

                            echo "Git checkout to new branch"

                            git checkout -b "\${new_branch}"
                            git branch

                            echo "Adding new version to version.yml"

                            sed -i "s/version: .*/version: \${new_version}/" version.yml
                            cat version.yml

                            echo "Add and commit in git "
                            git add .
                            git commit -m "Changed version to \${new_version}"

                            echo "Pushing to git repository"
                            git push -u https://${GIT_CREDENTIALS_USR}:${GIT_CREDENTIALS_PSW}@github.com/${GIT_CREDENTIALS_USR}/python-project-5.git "\${new_branch}"

                            echo "Building the Docker image"
                            docker build -t "${DOCKER_USERNAME}/python-project-5:\${new_version}" .

                            

                            echo "Pushing the Docker image to Docker Hub"
                            docker login -u "${DOCKER_USERNAME}" -p "${DOCKER_PASSWORD}"
                            docker push "${DOCKER_USERNAME}/python-project-5:\${new_version}"

                            cd ..
                            rm -rf python-project-5
                        """
                    }
                }
            }
        }
    }
}
