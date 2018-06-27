def builds = [
  belkin: [ image:'i386/debian:8', toolchain: 'hndtools-arm-linux-2.6.36-uclibc-4.5.3' ],
  symantec: [ image:'debian:8', toolchain: 'arm_cortex-a7_gcc-5.2.0_uClibc-1.0.14_eabi' ],
]

def createBuildStages(builds) {
  builds.each { name, definition ->
    stage(name) {
      agent {
        docker { image definition.image }
      }
      environment {
        JENKINS_PLATFORM = name
        TOOLCHAIN = definition.toolchain
      }
      steps {
        sh 'echo building $JENKINS_PLATFORM using $TOOLCHAIN'
        sh 'uname -a'
        sh 'sleep 20'
        sh 'echo build complete'
      }
    }
  }
}

def getToolchain(builds, name) {
  return builds[name].toolchain
}

pipeline {
  agent none
  stages {
    stage('Build') {
      parallel {
        stage('belkin') {
          agent {
            docker { image builds.belkin.image }
          }
          environment {
            JENKINS_PLATFORM = 'belkin'
            TOOLCHAIN = getToolchain(builds, 'belkin')
          }
          steps {
            sh 'echo building $JENKINS_PLATFORM using $TOOLCHAIN'
            sh 'uname -a'
            sh 'sleep 20'
            sh 'echo build complete'
          }
        }
        stage('symantec') {
          agent {
            docker { image builds.symantec.image }
          }
          environment {
            JENKINS_PLATFORM = 'symantec'
            TOOLCHAIN = getToolchain(builds, 'symantec')
          }
          steps {
            sh 'echo building $JENKINS_PLATFORM using $TOOLCHAIN'
            sh 'uname -a'
            sh 'sleep 20'
            sh 'echo build complete'
          }
        }
      }
    }
  }
}
