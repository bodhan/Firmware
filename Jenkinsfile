pipeline {
  agent {
    docker {
      image 'px4io/px4-dev-simulation:2017-09-26'
      args '--env=CCACHE_DISABLE --env=CI'
    }
    
  }
  stages {
    stage('Quality Checks') {
      steps {
        sh '''make check_format'''
      }
    }
    stage('Build') {
      steps {
        sh '''make distclean;
make posix_sitl_default;'''
      }
    }
    stage('Generate Metadata') {
      parallel {
        stage('airframe') {
          steps {
            sh 'make airframe_metadata'
            archiveArtifacts 'airframes.md, airframes.xml'
          }
        }
        stage('parameters') {
          steps {
            sh 'make parameters_metadata'
            archiveArtifacts 'parameters.md, parameters.xml'
          }
        }
        stage('modules') {
          steps {
            sh 'make module_documentation'
            archiveArtifacts 'modules/*.md'
          }
        }
      }
    }
    stage('Test') {
      steps {
        sh '''make tests'''
      }
    }
    stage('Deploy') {
      parallel {
        stage('User Guide Update') {
          steps {
            sh 'git clone https://github.com/PX4/px4_user_guide.git'
          }
        }
        stage('Dev Guide Update') {
          steps {
            sh 'git clone https://github.com/PX4/Devguide.git'
          }
        }
      }
    }
  }
  environment {
    CI = '1'
    CCACHE_DISABLE = '1'
  }
}