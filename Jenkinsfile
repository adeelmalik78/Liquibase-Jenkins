#!/usr/bin/env groovy
// Liquibase declarative pipeline
//
//
pipeline {
agent any
  environment {
    PROJ="projectName"
    GITURL="https://github.com/<username>"
    ENVIRONMENT_STEP="${params.step}"
    BRANCH="${params.pipeline}"
    PATH="/path/to/liquibase:$PATH"
  }
  stages {

    stage ('Precheck') {
		steps {
			sh '''
        { set +x; } 2>/dev/null
        echo "Current project: "$PROJ
        echo "Current scm branch: "$BRANCH
        echo "Current environment: "$ENVIRONMENT_STEP
			'''
		} // steps
	} // stage 'precheck'

    stage ('Checkout') {
      steps {
        // checkout Liquibase project from repo
        sh '''
          { set +x; } 2>/dev/null
          cd /localPath/to/workspaceFolder
          if [ -d "$PROJ" ]; then rm -Rf $PROJ; fi
          git clone ${GITURL}/${PROJ}.git
          cd ${PROJ}
          git checkout $BRANCH
          git status
          '''
      } // steps for checkout stages
    } // stage 'checkout'

   stage ('liquibase commands'){
      steps {
        sh '''
          { set +x; } 2>/dev/null
          cd /localPath/to/workspaceFolder/${PROJ}/${ENVIRONMENT_STEP}
          liquibase --version
          echo "------------------------------------"
          echo "----------liquibase status----------"
          echo "------------------------------------"
          liquibase --url=${URL} --password=${PASSWORD} --contexts=$ENVIRONMENT_STEP status
          echo "---------------------------------------------"
          echo "----------liquibase rollbackCount=2----------"
          echo "---------------------------------------------"
          liquibase --url=${URL} --password=${PASSWORD} --contexts=$ENVIRONMENT_STEP rollbackCount 2
          echo "---------------------------------------"
          echo "----------liquibase updateSQL----------"
          echo "---------------------------------------"
          liquibase --url=${URL} --password=${PASSWORD} --contexts=$ENVIRONMENT_STEP updateSQL
          echo "------------------------------------"
          echo "----------liquibase update----------"
          echo "------------------------------------"
          liquibase --url=${URL} --password=${PASSWORD} --contexts=$ENVIRONMENT_STEP update
        '''
      } // steps
    }   // Environment stage

        stage ('Deleting project workspace'){
           steps {
             sh '''
               { set +x; } 2>/dev/null
               echo "Deleting project workspace..."
               cd /localPath/to/workspaceFolder && rm -r ${PROJ}
             '''
           } // steps
         }   // Deleting project workspace
  } // stages
}  // pipeline