pipeline {
    // TODO: update agent if necessary
    agent any

    environment {
        // config
        // TODO: set configuration
        MASTER_BRANCH_NAME      = "master"              //? master branch name
        DEVELOP_BRANCH_NAME     = "develop"             //? develop branch name
        RELEASE_BRANCH_NAME     = "release"             //? release branch name
        QFE_BRANCH_NAME         = "hotfix"              //? qfe branch name

        CI_TAG                  = "ci"                  //? continuous integration
        RC_TAG                  = "rc"                  //? release candidate
        QFE_TAG                 = "qfe"                 //? quick-fix engineering

        GIT_CRED_ID             = "github_access_token" //? git credential id

        DOCKER_IMAGE_URI        = ""                    //? full Docker image uri

        STAGE_COMMIT_MESSAGE    = "STAGED"
        RELEASE_COMMIT_MESSAGE  = "RELEASED"
        // end of config
    }

    stages{
        stage('Checkout') {
            steps {
                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh 'git fetch --all'
                }
            }
        }

        stage('Version') {
            steps {
                script {
                    def parts = sh(
                        returnStdout: true,
                        script: 'git tag --sort=-v:refname --list | grep -E \'^v(0|[0-9]+)\\.(0|[0-9]+)\\.(0|[0-9]+)\$\' | head -n 1'
                    ).trim().substring(1).tokenize('.')

                    def major = parts[0].toInteger()
                    def minor = parts[1].toInteger()
                    def patch = parts[2].toInteger()

                    if (env.BRANCH_NAME == env.DEVELOP_BRANCH_NAME) {
                        minor = minor + 1
                        patch = 0
                    } else if (env.BRANCH_NAME == env.QFE_BRANCH_NAME) {
                        patch = patch + 1
                    }

                    env.VERSION = "${major}.${minor}.${patch}"
                }
            }
        }

        stage('Integrate') {
            steps {
                script {
                    def hash = sh(
                        returnStdout: true,
                        script: 'git rev-parse --short HEAD'
                    ).trim()

                    if (env.BRANCH_NAME == env.DEVELOP_BRANCH_NAME) {
                        env.TAG = "${VERSION}-${CI_TAG}.${hash}"
                    } else if (env.BRANCH_NAME == env.QFE_BRANCH_NAME) {
                        env.TAG = "${VERSION}-${QFE_TAG}.${hash}"
                    }

                    env.GIT_TAG = "v${TAG}"
                }

                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh "git tag ${GIT_TAG}"
                    sh "git push origin ${GIT_TAG}"
                }
            }

        }

        stage('Build') {
            steps {
                script {
                    env.DOCKER_TAG = "${TAG}"
                }

                // section_1: build Docker image and push to repository/hub
                //! Docker image with ${DOCKER_IMAGE_URI}:${DOCKER_TAG} MUST be pulled to machine if not

                // TODO

                // end of section_1
            }
        }

        stage('Test') {
            steps {
                echo "Testing..."

                // section_2: run test

                // TODO

                // end of section_2
            }
        }

        stage('Stage') {
            when {
                branch env.DEVELOP_BRANCH_NAME
            }

            steps {
                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh "git checkout ${RELEASE_BRANCH_NAME}"
                    sh "git merge --no-ff ${GIT_TAG} -m '${STAGE_COMMIT_MESSAGE}'"
                    sh "git push origin ${RELEASE_BRANCH_NAME}"
                }

                script {
                    def hash = sh(
                        returnStdout: true,
                        script: 'git rev-parse --short HEAD'
                    ).trim()

                    env.TAG = "${VERSION}-${RC_TAG}.${hash}"
                    env.GIT_TAG = "v${TAG}"
                }

                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh "git tag ${GIT_TAG}"
                    sh "git push origin ${GIT_TAG}"
                }

                // section_3: attach RC-tag and push to repository/hub

                // TODO

                // end of section_3



                // section_4: attach `stage` tag and push to repository/hub

                // TODO

                // end of section_4



                // section_5: deploy staged

                // TODO

                // end of section_5
            }
        }

        stage('Approve') {
            steps {
                input message: "Do you want to release ${TAG}?", ok: "Release"
            }
        }

        stage('Release') {
            steps {
                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh "git checkout ${MASTER_BRANCH_NAME}"
                    sh "git merge --no-ff ${GIT_TAG} -m '${RELEASE_COMMIT_MESSAGE}'"
                    sh "git push origin ${MASTER_BRANCH_NAME}"
                }

                script {
                    env.TAG = "${VERSION}"
                    env.GIT_TAG = "v${TAG}"
                }

                withCredentials(bindings: [
                    gitUsernamePassword(credentialsId: env.GIT_CRED_ID)
                ]) {
                    sh "git tag ${GIT_TAG}"
                    sh "git push origin ${GIT_TAG}"
                }

                // section_6: attach version-tag and push to repository/hub

                // TODO

                // end of section_6



                // section_7: attach `latest` tag and push to repository/hub

                // TODO

                // end of section_7
            }
        }

        stage('Deploy') {
            steps {
                // section_8: deploy released

                // TODO

                // end of section_8
            }
        }
    }
}