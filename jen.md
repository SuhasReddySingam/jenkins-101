Got it! You want a **full Jenkins pipeline setup** that:

1. Monitors a **Git repository** via **webhooks**.
2. Builds a **Maven project** into a `.war` file.
3. Deploys it to a **Tomcat server running in Eclipse**.

Here’s a complete step-by-step guide.

---

## **1️⃣ Setup Jenkins to Connect to Git via Webhooks**

### Steps:

1. Go to **Jenkins → Manage Jenkins → Configure System**.
2. Under **Git**, make sure you have Git installed or point Jenkins to the Git executable.
3. Create a new **Freestyle or Pipeline job**:

   * Under **Source Code Management**, select **Git**.
   * Enter the repository URL.
4. Enable **Build Triggers → GitHub hook trigger for GITScm polling**.
5. In your **GitHub repo** (or GitLab/Bitbucket):

   * Go to **Settings → Webhooks → Add webhook**.
   * Payload URL: `http://<JENKINS_URL>/github-webhook/`
   * Content type: `application/json`
   * Trigger: `Push events`

✅ Now Jenkins will automatically start a build whenever code is pushed.

---

## **2️⃣ Configure Maven and JDK in Jenkins**

* Go to **Manage Jenkins → Global Tool Configuration**.
* Add:

  * **JDK** (e.g., Java 11)
  * **Maven** (e.g., Maven 3.9)
* Give them names like `Java11` and `Maven3` to reference in the pipeline.

---

## **3️⃣ Configure Tomcat in Eclipse for Deployment**

1. In Eclipse:

   * Window → Preferences → Server → Runtime Environments → Add → Apache Tomcat (choose version).
   * Make sure Tomcat is running on **localhost**, default `8080`.
2. Edit `tomcat-users.xml` (inside `Eclipse/Tomcat/conf/`) to add a manager user:

```xml
<tomcat-users>
  <role rolename="manager-gui"/>
  <role rolename="manager-script"/>
  <user username="admin" password="admin123" roles="manager-gui,manager-script"/>
</tomcat-users>
```

3. Restart Tomcat.

---

## **4️⃣ Jenkins Pipeline for Maven WAR Deployment**

Here’s a **Declarative Pipeline** example:

```groovy
pipeline {
    agent any

    tools {
        maven 'Maven3'       // Jenkins Maven tool name
        jdk 'Java11'         // Jenkins JDK tool name
    }

    environment {
        TOMCAT_URL = 'http://localhost:8080/manager/text'
        TOMCAT_USER = 'admin'
        TOMCAT_PASSWORD = 'admin123'
        CONTEXT_PATH = '/myapp'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/your-repo.git'
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                script {
                    def warFile = "target/myapp.war"
                    sh """
                    curl -u $TOMCAT_USER:$TOMCAT_PASSWORD \
                         -T $warFile "$TOMCAT_URL/deploy?path=$CONTEXT_PATH&update=true"
                    """
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}
```

**Notes:**

* Replace `myapp.war` and `https://github.com/your-repo.git` with your project details.
* Tomcat manager URL: `http://localhost:8080/manager/text` (scripted deployment via HTTP PUT/POST).
* The `update=true` parameter allows redeployment of an existing WAR.

---

## **5️⃣ Trigger via Webhook**

* Push code to GitHub → webhook triggers Jenkins → Jenkins runs the pipeline → Maven builds → WAR deployed to Eclipse Tomcat.

---

## **6️⃣ Optional Tips**

* If `curl` is not installed, install it or use Jenkins **HTTP Request Plugin**.
* Make sure Jenkins can access Eclipse Tomcat port (localhost:8080).
* For production, consider using **standalone Tomcat** instead of Eclipse for better deployment automation.

---

If you want, I can also write a **version that uses a Dockerized Maven agent**, so your Jenkins machine doesn’t need Maven or Java installed globally and the build is fully containerized. This is more robust and closer to CI/CD best practices.

Do you want me to do that?
