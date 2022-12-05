String githubUrl = "https://github.com/M-Abdelalem/Microservices/"

String projectName = "Microservices\\Services\\Order\\OrderApi"

String publishedPath = "Services\\Order\\OrderApi\\bin\\Release\\net6.0"

String iisApplicationName = "Microservices"

String iisApplicationPath = "D:\\Microservices_Publish"

String targetServerIP = "0.0.0.0"

String iisApplicationBackup = "D:\\Microservices_Backup"

node () {
    stage('Checkout') {
        checkout([
            $class: 'GitSCM', 
            branches: [[name: 'main']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[url: """ "${githubUrl}" """]]])
    }
    stage('Build') {
        bat """
        cd ${projectName}
        dotnet build -c Release /p:Version=${BUILD_NUMBER}
        dotnet publish -c Release --no-build
        """
    }
stage('Backup') 
{
        bat """
        powershell Compress-Archive -Path ${iisApplicationPath}\\* -DestinationPath ${iisApplicationBackup}\\Micro_Backup.zip -force
        """
}
   // stage('Deploy'){
   //    withCredentials([usernamePassword(credentialsId: 'iis-credential', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) { bat """ "C:\\Program Files (x86)\\IIS\\Microsoft Web Deploy V3\\msdeploy.exe" -verb:sync -source:iisApp="${WORKSPACE}\\${publishedPath}" -enableRule:AppOffline -dest:iisApp="${iisApplicationName}",ComputerName="https://${targetServerIP}:8172/msdeploy.axd",UserName="$USERNAME",Password="$PASSWORD",AuthType="Basic" -allowUntrusted"""}
   // }
stage('Upload')
{

    ftpPublisher alwaysPublishFromMaster: true, continueOnError: false, failOnError: false, publishers: [
        [configName: 'ftp', transfers: [
            [asciiMode: false, cleanRemote: false, excludes: '',
          flatten: false, makeEmptyDirs: false, noDefaultExcludes: false,
 patternSeparator: '[, ]+', remoteDirectory: "\\", 
remoteDirectorySDF: false, removePrefix: '', sourceFiles: '/Services/Order/OrderApi/bin/Release/net6.0/']
        ], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true]
    ]
        bat """
        xcopy ${iisApplicationPath}\\${publishedPath}\\ ${iisApplicationPath}\\ /S   /Y 
        rmdir ${iisApplicationPath}\\${publishedPath}\\  /q /s

        """}   
}
