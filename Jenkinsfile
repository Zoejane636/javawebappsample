import groovy.json.JsonSlurper

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=79174a24-ac34-481a-9d0b-5c31d13571a6',
        'AZURE_TENANT_ID=b72002ad-1aae-48a5-9724-9ef88906e4ed']) {

    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'jenkins-webapp-yiding2025'  // 你的 Azure Web App 名称

      // 使用 Azure Service Principal 登录
      withCredentials([usernamePassword(credentialsId: 'AzureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
        sh '''
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
      }

      // 直接使用 Azure CLI 部署 .war 文件
      sh "az webapp deploy --resource-group ${resourceGroup} --name ${webAppName} --src-path target/calculator-1.0.war --type war"

      // 退出 Azure CLI
      sh 'az logout'
    }
  }
}
