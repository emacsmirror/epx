pipeline {
    agent {
        docker {
            image 'alpine:edge'
            args '-u root'
        }
    }

    environment {
        PACKAGE_LINT_DIR = 'package-lint'
    }

    stages {
        stage('Install dependencies') {
            steps {
                sh 'apk add --no-cache emacs-nox git'
            }
        }

        stage('Setup') {
            steps {
                sh 'git clone --depth 1 https://github.com/purcell/package-lint.git "$PACKAGE_LINT_DIR"'
            }
        }

        stage('Byte compile') {
            steps {
                sh '''
                    emacs --batch \
                      --eval "(progn \
                                (setq byte-compile-error-on-warn t) \
                                (byte-compile-file \\"epx.el\\"))"
                '''
            }
        }

        stage('Checkdoc') {
            steps {
                sh '''
                    emacs --batch \
                      --eval "(progn \
                                (require 'checkdoc) \
                                (checkdoc-file \\"epx.el\\"))"
                '''
            }
        }

        stage('Package lint') {
            steps {
                sh '''
                    emacs --batch \
                      --load "$WORKSPACE/$PACKAGE_LINT_DIR/package-lint.el" \
                      --funcall package-lint-batch-and-exit "epx.el"
                '''
            }
        }
    }
}
