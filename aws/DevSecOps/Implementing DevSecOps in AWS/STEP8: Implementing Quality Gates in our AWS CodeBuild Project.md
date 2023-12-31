# Implementing Quality Gates in AWS CodeBuild Project

## Introduction
Welcome to this lecture where we'll implement a quality gate within our AWS CodeBuild project. A quality gate ensures that if SonarCloud identifies security or code quality issues within our source code, the CodeBuild project fails, allowing us to address and fix the issues before they impact production. In this demonstration, we'll be using SonarCloud, but similar principles apply to SonarQube.

## Prerequisites
Make sure you've completed the previous steps, including setting up AWS CodeBuild, integrating AWS Secrets Manager, and configuring SonarCloud.

## Implementing a Quality Gate in CodeBuild
To implement a quality gate in our CodeBuild pipeline, we need to make changes to our buildspec.yml file. So you buildspec.yml should look something like this:
```yaml
version: 0.1
env:
    secrets-manager:
      TOKEN: yoursecretname:yourkeyofthesecretvalue
phases:
  build:
    commands:
      - mvn verify sonar:sonar -Dsonar.projectKey=sonarcloudprojectkey -Dsonar.organization=sonarcloudorg -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=$TOKEN 
      - sleep 5
      - |- 
        quality_status=$(curl -s -u $TOKEN: https://sonarcloud.io/api/qualitygates/project_status?projectKey=sonarcloudprojectkey | jq -r '.projectStatus.status')
        echo "SonarCloud analysistatus is $quality_status"; 
        if [ $quality_status = "ERROR" ] ; then exit 1;fi
```

## Explanation of Changes:

• Added a sleep 5 command to allow time for SonarCloud to update after running the analysis.

• Introduced a multi-line command using |- for better readability.

• Used curl to query the SonarCloud API for the quality gate status.

• Checked if the quality gate status is "ERROR" and failed the build if true.

## Configuring Quality Gates in SonarCloud
1. Open SonarCloud and navigate to your organizat.

2. Click on "Quality Gates".

3. Create a custom quality gate, add a condition: 
   - Set `Where?` to on `Overall Code`
   - And `Quality Gate Fails When`, Operator: `Coverage` is less than `80`, then click `Save Condition`.

4. Make another rule within the quality gate and make it the exact same, however, just change the `operator` to `condition coverage`

4. Ensure the quality gate is set as default by clicking the three dots in the top-right and selecting `Set as Default`.

## Testing the Quality Gate
1. Trigger a new build in AWS CodeBuild.

2. Monitor the build logs for the SonarCloud analysis status.

3. Verify that the build fails, the quality gate status should be "ERROR." This shows that the SonarCloud Quality Gate Condition worked as the coverage was under 80%:)

4. Go back to the quality gate section and switch the default quality gate to the SonarWay quality gate.

## Conclusion🎉
Congratulations! You've successfully implemented a quality gate in your AWS CodeBuild project using SonarCloud. This ensures that your build fails if SonarCloud identifies code quality or security issues, allowing you to address them before deployment.
