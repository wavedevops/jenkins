If your **Jenkins Shared Library** is in **one repository**, and you want to call it from a **Jenkinsfile in another repository**, follow these steps:

---

## **📌 Step 1: Configure Shared Library in Jenkins**
1. **Go to Jenkins Dashboard** → **Manage Jenkins** → **Configure System**.
2. Scroll to **Global Pipeline Libraries**.
3. Click **Add** and set:
    - **Name:** `my-shared-library`
    - **Default Version:** `main` (or specify a branch/tag)
    - **Repository URL:** (GitHub/GitLab repo URL)
    - **Retrieval Method:** (e.g., Git)
    - **Check "Load Implicitly"** if you want the library available in all pipelines.
4. **Save the changes**.

---

## **📌 Step 2: Structure of the Shared Library Repo**
Your shared library repository should have the following structure:

```
my-shared-library/
├── vars/
│   ├── ciStage.groovy       # CI logic
│   ├── cdStage.groovy       # CD logic
│   ├── releaseStage.groovy  # Release logic
│   ├── utils.groovy         # Helper functions
├── resources/  # Optional
├── src/        # Optional for Java/Groovy classes
├── README.md
```

### **Example: `vars/ciStage.groovy`**
```groovy
def call() {
    echo "Executing CI Stage from Shared Library..."
    sh "echo 'CI stage from shared library'"
}
```

---

## **📌 Step 3: Use the Shared Library in Another Repository**
In the **Jenkinsfile** of the other repository, use the `@Library` annotation.

```groovy
@Library('my-shared-library') _  // Load the shared library

pipeline {
    agent { label 'workstation' }

    stages {
        stage('CI') {
            when {
                expression { env.BRANCH_NAME != env.TAG_NAME }
            }
            steps {
                ciStage()  // Calling function from Shared Library
            }
        }

        stage('CD') {
            when {
                expression { env.BRANCH_NAME != env.TAG_NAME }
            }
            steps {
                cdStage()  // Calling function from Shared Library
            }
        }

        stage('Release') {
            when {
                expression { env.BRANCH_NAME == env.TAG_NAME }
            }
            steps {
                releaseStage()  // Calling function from Shared Library
            }
        }
    }

    post {
        success {
            echo "Pipeline completed successfully."
        }
        failure {
            echo "Pipeline failed!"
        }
    }
}
```

---

## **📌 Step 4: If the Shared Library is NOT Configured Globally**
If the shared library is **not globally configured**, you can **dynamically load it** in your `Jenkinsfile`:

```groovy
@Library('my-shared-library@main') _  // Load specific branch or tag
```

OR load it explicitly within the script:

```groovy
library identifier: 'my-shared-library@main', retriever: modernSCM(
    [$class: 'GitSCMSource', remote: 'https://github.com/your-org/my-shared-library.git']
)
```

---

## **🚀 Now your Jenkins pipeline from another repo can use the shared library!**
This setup ensures modular, reusable, and maintainable pipeline scripts. Let me know if you need more help! 😊