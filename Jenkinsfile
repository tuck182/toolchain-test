def builds = [
  belkin: [ image:'i386/debian:8', toolchain: 'hndtools-arm-linux-2.6.36-uclibc-4.5.3' ],
  symantec: [ image:'debian:8', toolchain: 'arm_cortex-a7_gcc-5.2.0_uClibc-1.0.14_eabi' ],
]

def createBuildStages(builds) {
  builds.collectEntries { name, definition ->
    [ "${name}": createBuildStage(name, definition) ]
  }
}

def createBuildStage(name, definition) {
  return {
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

def getToolchain(builds, name) {
  builds[name].toolchain
}

def buildStages = builds.collectEntries { name, definition ->
  [ "${name}": createBuildStage(name, definition) ]
}

pipeline {
  agent none
  stages {
    stage('Build') {
      parallel buildStages
    }
  }
}
