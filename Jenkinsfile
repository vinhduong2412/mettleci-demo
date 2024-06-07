pipeline {

    agent {
        label 'mettleci'
    }
    stages {
        stage("Tests") {
            stages {

                stage("Compliance Tests") {

                    agent {
                        label "mettleci"
                    }

                    steps {
                        bat label: "Export ISX Assets for Compliance Check", script: "C:/MettleCI/cli/mettleci isx export -domain 10.1.102.37:9446 -server IIS-DATASTAGE -username hieu.vc -password 12345678 -project test1 -jobname .*t1.* -location C:/MettleCi/cli/export-files" 
                        bat label: "Run Compliance Check on Jobs", script: "C:/MettleCI/cli/mettleci compliance test -rules C:/Users/hieu.vc/Downloads/repo_MettleCI_Demo-main/MB_MettleCI_Demo-main/compliance/compliance-mettleci -assets C:/MettleCi/cli/export-files/Jobs -report C:/MettleCi/cli/compliance_report_warn.xml -junit -include-job-in-test-name"
                    }
                }
                /*stage("Check Test Results") {
                    steps {
                        script {
                            def failed = bat(returnStdout: true, script: '''
                               @echo off
                               setlocal enabledelayedexpansion
                               set "xmlFile=C:\\MettleCi\\cli\\compliance_report_warn.xml"
                               set "failCount=0"
                        
                               for /f "tokens=*" %%A in ('type "!xmlFile!" ^| findstr "<failure>"') do (
                                    set /a failCount+=1
                                )
                        
                                if !failCount! gtr 0 (
                                    echo FAIL
                                ) else (
                                    echo PASS
                                )
                            ''').trim()

                            if (failed == "FAIL") {
                                error("Test cases failed. Stopping the pipeline.")
                            } else {
                                echo "All test cases passed. Continuing the pipeline."
                            }
                        }
                    }
                }*/
                stage("Unit Tests") {

                    agent {
                        label "mettleci"
                    }

                    steps {
                        bat label: "Generate Unit Test Specs", script: "C:/MettleCI/cli/mettleci unittest generate -assets C:/MettleCi/cli/export-files/Jobs -specs C:/MettleCi/cli/test-specs"
                        bat label: "Run Unit Tests", script: "cd C:/IBM/InformationServer/Clients/Classic & C:/MettleCI/cli/mettleci unittest test -domain 10.1.102.37:9446 -server IIS-DATASTAGE -username hieu.vc -password 12345678 -project test1 -specs C:/MettleCi/cli/test-specs -reports C:/MettleCi/cli/test-report -project-cache -ignore-test-failures"  
                    }
                }
            }
        }
        
        stage("Import") {

            parallel {

                stage("Import") {

                    agent {
                        label "mettleci"
                    }

                    steps {   
                        bat label: "Import Datastage Jobs", script: "& cd C:/IBM/InformationServer/Clients/istools/cli & C:/MettleCI/cli/mettleci isx import -domain 10.1.102.28:9446 -username hieu.vc -password 12345678 -server CICD3 -project dstage1 -location C:/MettleCi/cli/export-files"

                    }
                }
            }
        }
    }
}
