#!groovy

import groovy.json.JsonOutput

stage('Run') {
    node {

        currentBuild.result = "SUCCESS"

        try {
            checkout scm

            sh 'curl https://www.vaccinespotter.org/api/v0/states/NE.json > NE.json'
            sh 'jq \'.features[].properties | select(.provider_brand_id==1338) | select(.city=="Lincoln") | select(.appointments_available_all_doses==true)\' NE.json > results.txt'
            def hyVeeLincoln = readFile(file: 'results.txt')
            if (hyVeeLincoln != '') {
                error(hyVeeLincoln)
            }

            sh 'jq \'.features[].properties | select(.provider_brand_id==1338) | select(.city=="Omaha") | select(.appointments_available_all_doses==true)\' NE.json > results.txt'
            def hyVeeOmaha = readFile(file: 'results.txt')
            if (hyVeeOmaha != '') {
                error(hyVeeOmaha)
            }

            sh 'jq \'.features[].properties | select(.provider_brand_id==70) | select(.city=="Omaha") | select(.appointments_available_all_doses==true)\' NE.json > results.txt'
            def walmartOmaha = readFile(file: 'results.txt')
            if (walmartOmaha != '') {
                error(walmartOmaha)
            }
        } catch (err) {
            currentBuild.result = "FAILURE"

            def build = "${env.JOB_NAME} - #${env.BUILD_NUMBER}".toString()
            def emailAddress = "${env.EMAIL}".toString()

            def email = [to: emailAddress, from: emailAddress, subject: "$build failed!", body: "${err.message}"]

            emailext body: email.body, recipientProviders: [[$class: 'DevelopersRecipientProvider']], subject: email.subject, to: "${env.EMAIL}"

            throw err
        }
    }
}
