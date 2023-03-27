podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: cloud-sdk
        image: google/cloud-sdk
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
        - name: google-cloud-key
          mountPath: /var/secrets/google
        env:
        - name: GOOGLE_APPLICATION_CREDENTIALS
          value: /var/secrets/google/week9project-381822-b2929df8ca6e.json
      restartPolicy: Never
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: google-cloud-key
        secret:
          secretName: sdk-key
''') {
  node(POD_LABEL) {
    stage('smoke test') {
      container('cloud-sdk') {
        stage('smoke test') {
                    sh '''
                    gcloud auth login --cred-file=$GOOGLE_APPLICATION_CREDENTIALS
                    gcloud container clusters get-credentials hello-cluster --region us-east1 --project week9project-381822
                    git 'https://github.com/StrayCat96/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition'
                    cd Chapter 08/sample1
                    chmod +x gradlew
                    + ./gradlew smokeTest -Dcalculator.url=http://calculator-service.devops-tools.svc.cluster.local:8080
                    '''
        }
        stage('Deploy to staging') {
                    sh "kubectl apply -f hazelcast.yaml -n devops-tools"
                    sh "kubectl apply -f calculator.yaml -n devops-tools"
               }
          }
      }
     }
    }
