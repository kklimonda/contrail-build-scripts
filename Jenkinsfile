def pipeline(branch, system) {
  ws("${BRANCH_NAME}_trusty") {
  docker.image("10.10.2.112:5000/contrail-builder:${system}").inside {
    stage ("checkout contrail-vnc") {
      checkout([$class: 'RepoScm', currentBranch: true,
        manifestBranch: "${branch}", manifestFile: 'default.xml',
        manifestRepositoryUrl: 'https://github.com/kklimonda/contrail-vnc',
        quiet: false])
    }
    stage ("fetch third-party modules") {
        sh "cd third_party && python fetch_packages.py"
    }
    stage ("build source packages") {
        withEnv(["USER=jenkins"]) {
            sh("""export USER
            echo $USER
            make -f packages.make source-all""")
        }
    }
    stage ("build binary packages") {
        sh """
        KVERS=`dpkg -l |grep -Po "linux-headers-\\K.*-generic"` make -f packages.make all
        """
    }
  }
  }
}

node {
  parallel (
    "trusty": pipeline("master", "trusty"),
    "xenial": pipeline("master", "xenial")
  )
}
