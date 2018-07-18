def builds = [
  belkin: [ image:'i386/debian:8', toolchain: 'hndtools-arm-linux-2.6.36-uclibc-4.5.3' ],
  symantec: [ image:'debian:8', toolchain: 'arm_cortex-a7_gcc-5.2.0_uClibc-1.0.14_eabi' ],
]

def buildDockerImage(name) {
  // Exit early if the docker image is up to date, based on commit hashes of this file and
  // the docker image directory
  def dockerPath = ".docker/${name}"
  def imageName = "${name}"
  def imageHash = [
    "Jenkinsfile",
    dockerPath
  ].collect {
    sh(script: "git rev-list -1 --abbrev-commit HEAD ${it}", returnStdout: true).trim()
  }.join('_')

  def imageDetails = sh(
    script: "docker image inspect 'tuck182/${imageName}:${imageHash}' || true",
    returnStdout: true).trim()
  if (imageDetails == "[]") {
    docker.build("tuck182/${imageName}:${imageHash}", dockerPath)
  } else {
    println "${imageName}:${imageHash} already built; skipping (imageDetails:${imageDetails})"
  }
}

def createBuildStages(builds) {
  builds.collectEntries { name, definition ->
    [ "${name}": createBuildStage(name, definition) ]
  }
}

def createBuildStage(name, definition) {
  return {
    stage(name) {
      node ('docker-jenkins') {
        sh 'echo building $JENKINS_PLATFORM using $TOOLCHAIN; uname -a; dpkg --print-architecture; getconf LONG_BIT; arch'
        sh 'sleep 20'
        sh 'echo build complete'
      }
    }
  }
}

pipeline {
  agent any
  stages {
    stage('PrepareDocker') {
      agent any
      steps {
        checkout scm
        script {
          buildDockerImage('docker-jenkins')
        }
      }
    }

    stage('Build') {
      steps {
        script {
          parallel createBuildStages(builds)
        }
      }
    }
  }
}
