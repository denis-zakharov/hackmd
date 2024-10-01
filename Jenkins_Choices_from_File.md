Yes, it's possible to create a file on a Jenkins master node and use it as a source for a build parameter with multiple choices. Here’s a step-by-step guide on how to achieve this:

### 1. **Create the File on the Jenkins Master Node**
   - First, create a file on the Jenkins master node (or the node where the Jenkins job will run) that contains the options for your build parameter. Each option should be on a new line.

   Example file (`/var/lib/jenkins/parameter_options.txt`):
   ```
   option1
   option2
   option3
   ```

### 2. **Install the `Extended Choice Parameter` Plugin (if not installed)**
   Jenkins provides a plugin called [Extended Choice Parameter Plugin](https://plugins.jenkins.io/extended-choice-parameter/) that allows you to use a file as the source for a choice parameter.

   - Go to **Manage Jenkins** > **Manage Plugins**.
   - Search for **Extended Choice Parameter Plugin** and install it if it's not already installed.

### 3. **Configure the Job with an Extended Choice Parameter**
   - Go to the job where you want to add the build parameter.
   - Click on **Configure**.
   - In the **Build Environment** section, add a new parameter by clicking on **Add Parameter** and select **Extended Choice Parameter**.

### 4. **Set the Parameter Properties**
   Configure the Extended Choice Parameter to use the file as the source for the choices:

   - **Name**: Give your parameter a name (e.g., `MY_CHOICES`).
   - **Parameter Type**: Select **Multi-Select** or **Single-Select** based on your requirement.
   - **Value**: In the **Property File** field, enter the path to the file created on the Jenkins master node (`/var/lib/jenkins/parameter_options.txt`).
   - **Quote Value**: If needed, you can quote the value, but this is optional depending on your use case.

### 5. **Use the Parameter in the Build**
   - The parameter is now available for selection when you trigger a build.
   - You can reference the parameter value in your build steps as `$MY_CHOICES`.

### 6. **Example Script Usage**
   If you're using the parameter in a shell script or pipeline, you can reference it like so:

   **For a Freestyle job:**
   ```bash
   echo "Selected option: $MY_CHOICES"
   ```

   **For a Pipeline job:**
   ```groovy
   pipeline {
       agent any
       parameters {
           choice(name: 'MY_CHOICES', choices: readFile('/var/lib/jenkins/parameter_options.txt').split('\n'), description: 'Choose an option')
       }
       stages {
           stage('Build') {
               steps {
                   script {
                       echo "Selected option: ${params.MY_CHOICES}"
                   }
               }
           }
       }
   }
   ```

This setup allows you to manage build parameter options from a file on the Jenkins master node and dynamically update them as needed.

You can update a file on the Jenkins master node using another Jenkins pipeline by writing a pipeline script that accesses the file system on the master node and modifies the file. Below is an example of how to achieve this using a Jenkins pipeline.

### Steps to Update a File on the Jenkins Master Node Using a Pipeline:

1. **Use the `master` node label in the pipeline script** to ensure that the script runs on the master node, where the file is located.
2. **Write to the file using a shell command** or a Groovy script within the pipeline.

Here is a basic pipeline script to update the file located on the master node.

### Example: Updating the File on the Jenkins Master Node

```groovy
pipeline {
    agent { label 'master' }  // Ensure the pipeline runs on the master node
    parameters {
        string(name: 'NEW_OPTION', defaultValue: 'option4', description: 'Enter a new option to add to the file')
    }
    stages {
        stage('Update Parameter File') {
            steps {
                script {
                    def filePath = '/var/lib/jenkins/parameter_options.txt'  // Path to the file
                    def newOption = params.NEW_OPTION
                    echo "Adding new option: ${newOption}"

                    // Append the new option to the file
                    sh """
                    echo '${newOption}' >> ${filePath}
                    """

                    echo "Updated file with new option."
                }
            }
        }
    }
}
```

### Explanation:
1. **Agent with `master` label**: The pipeline is set to run on the master node using `agent { label 'master' }`. This ensures that the file located on the master node will be modified.

2. **Pipeline Parameter (`NEW_OPTION`)**: The pipeline accepts a new string parameter `NEW_OPTION`. This allows you to pass the new option from the Jenkins UI when running the pipeline.

3. **File Path (`filePath`)**: The file located on the Jenkins master node is referenced using a file path (e.g., `/var/lib/jenkins/parameter_options.txt`).

4. **Shell Command (`sh`)**: The pipeline uses the `sh` step to run a shell command that appends the new option to the file:
   ```bash
   echo '${newOption}' >> /var/lib/jenkins/parameter_options.txt
   ```

### Running the Pipeline:
- When you trigger the pipeline job, you will be prompted to enter the new option (e.g., `option4`).
- The pipeline will then append the new option to the file `parameter_options.txt`.

### Verifying the Update:
After the pipeline completes, you can verify that the file has been updated by checking the contents of `/var/lib/jenkins/parameter_options.txt`. Each new option will be appended on a new line.

### Example File After Update:
```
option1
option2
option3
option4
```

### Additional Considerations:

- **File Permissions**: Ensure that the Jenkins user has write permissions to the file on the master node. You may need to adjust file permissions using `chmod` or similar methods if Jenkins cannot modify the file.

- **Avoid Duplicates**: If you want to avoid adding duplicate options, you can add logic in the pipeline to check if the option already exists in the file. This can be done with a simple shell command or Groovy logic.

### Example to Avoid Duplicate Entries:
```groovy
pipeline {
    agent { label 'master' }
    parameters {
        string(name: 'NEW_OPTION', defaultValue: 'option4', description: 'Enter a new option to add to the file')
    }
    stages {
        stage('Update Parameter File') {
            steps {
                script {
                    def filePath = '/var/lib/jenkins/parameter_options.txt'
                    def newOption = params.NEW_OPTION

                    // Check if the option already exists before adding it
                    def exists = sh(script: "grep -Fxq '${newOption}' ${filePath}", returnStatus: true) == 0

                    if (exists) {
                        echo "Option '${newOption}' already exists in the file."
                    } else {
                        echo "Adding new option: ${newOption}"
                        sh """
                        echo '${newOption}' >> ${filePath}
                        """
                        echo "Updated file with new option."
                    }
                }
            }
        }
    }
}
```

This pipeline checks if the option already exists in the file using `grep` and only adds it if it's not present.

To run a Jenkins pipeline periodically, you can use the **"Build Periodically"** feature by specifying a cron-like schedule in your pipeline configuration. Here's how to configure a pipeline job to run periodically:

### 1. **Add a Cron Schedule to the Pipeline**

In a Jenkins pipeline, you can use the `triggers` block to set up a periodic schedule with a cron expression.

Here's an example pipeline that will run periodically:

```groovy
pipeline {
    agent { label 'master' }

    triggers {
        cron('H 0 * * *')  // Runs once every day at midnight
    }

    parameters {
        string(name: 'NEW_OPTION', defaultValue: 'option4', description: 'Enter a new option to add to the file')
    }

    stages {
        stage('Update Parameter File') {
            steps {
                script {
                    def filePath = '/var/lib/jenkins/parameter_options.txt'
                    def newOption = params.NEW_OPTION

                    // Append the new option to the file
                    echo "Adding new option: ${newOption}"
                    sh """
                    echo '${newOption}' >> ${filePath}
                    """
                    echo "Updated file with new option."
                }
            }
        }
    }
}
```

### 2. **Understanding the Cron Syntax**

The `cron` syntax in Jenkins is similar to the standard Unix cron syntax. It consists of five fields that represent different time intervals:

```
MINUTE HOUR DOM MONTH DOW
```

Here’s what each field represents:
- **MINUTE**: 0–59
- **HOUR**: 0–23
- **DOM** (Day of Month): 1–31
- **MONTH**: 1–12
- **DOW** (Day of Week): 0–7 (0 or 7 is Sunday)

### Examples of Cron Expressions:
- `H 0 * * *`: Runs once every day at midnight. The `H` is a hash that allows Jenkins to distribute the load by choosing a random minute for execution.
- `H/15 * * * *`: Runs every 15 minutes.
- `0 12 * * 1-5`: Runs at 12:00 PM (noon) Monday through Friday.
- `0 0 * * 1`: Runs at midnight every Monday.

### 3. **Setting Periodic Builds from the Jenkins UI (Optional)**

If you're not defining the cron schedule in the pipeline script, you can configure it from the Jenkins UI for **Freestyle** jobs or **Pipeline** jobs as follows:

1. Go to the Jenkins job you want to schedule.
2. Click **Configure**.
3. Scroll to the **Build Triggers** section.
4. Check the box **Build periodically**.
5. Enter your cron expression in the **Schedule** field.

For example, to run every day at midnight, enter:
```
H 0 * * *
```

### 4. **Using the `@daily` Syntax (Alternative)**

Instead of using the full cron format, you can also use shorthand like:
- `@hourly`: Runs once per hour.
- `@daily`: Runs once a day (midnight).
- `@weekly`: Runs once a week.
- `@monthly`: Runs once a month.

### Conclusion:

Using the `triggers { cron(...) }` block in a pipeline, you can easily schedule your pipeline to run periodically. Alternatively, you can configure it through the Jenkins UI if needed. The flexibility of cron expressions allows you to set specific schedules for your job based on your needs.
