podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
        containers:
      - name: gradle
        image: gradle:6.3-jdk14
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt 
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
   {node(POD_LABEL) {
    stage('smoke test') {
      git 'https://github.com/StrayCat96/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
      container('gradle') {
        stage('Build a gradle project') {
          sh '''
          cd /home/jenkins/agent/workspace/Chapter08/sample1
          chmod +x gradlew
          ./gradlew build
          mv ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
          ./gradlew smokeTest -Dcalculator.url=http://calculator-service.devops-tools.svc.cluster.local:8080
          
          '''
        }
      }
    }
    stage('Deploying to prod') {
      container('cloud-sdk') {
        stage('Deploy calculator') {
          sh "kubectl apply -f hazelcast.yaml -n devops-tools"
          sh "kubectl apply -f calculator.yaml -n devops-tools"
                    '''
               }
          }
      }
     }
    }
