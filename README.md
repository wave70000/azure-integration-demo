Azure integration Demo

# Docs for the Azure Web Apps Deploy action: https://github.com/Azure/webapps-deploy
# More GitHub Actions for Azure: https://github.com/Azure/actions

name: Build and deploy JAR app to Azure Web App - spring-integration-demo

on:
push:
branches:
- main
workflow_dispatch:

jobs:
build:
runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2

      - name: Set up Java version
        uses: actions/setup-java@v1
        with:
          java-version: '11'

      - name: Build with Maven
        run: mvn clean install

      - name: Upload artifact for deployment job
        uses: actions/upload-artifact@v2
        with:
          name: java-app
          path: '${{ github.workspace }}/target/*.jar'

deploy:
runs-on: ubuntu-latest
needs: build
environment:
name: 'production'
url: ${{ steps.deploy-to-webapp.outputs.webapp-url }}

    steps:
      - name: Download artifact from build job
        uses: actions/download-artifact@v2
        with:
          name: java-app

      - name: Deploy to Azure Web App
        id: deploy-to-webapp
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'spring-integration-demo'
          slot-name: 'production'
          publish-profile: ${{ secrets.AzureAppService_PublishProfile_525a6a82b0f74408b931c7c0de3b6cdb }}
          package: '*.jar'