# c7-deployment-aware

Example configuration of deployment-aware Camunda 7

## Usage

- Start app1 (http://localhost:8080)
- Start the process with id example=process. It will find the logger bean, but create an incident (*java.lang.RuntimeException: HA!*). 
- Stop app1.
- Start app2 (http://localhost:8088) 
- Try to resolve the incident by incrementing retries. Wait for a few seconds until the job executor of app2 attempts the retry. You will see that app2 cannot resolve the bean name (*org.camunda.bpm.engine.ProcessEngineException: Unknown property used in expression: ${logger}. Cause: Cannot resolve identifier 'logger'*).
- Stop app2
- Change app2's [application.yaml](/app2/src/main/resources/application.yaml). In line 11 set deployment-aware to **true**.
- Start app2 again and try to resolve the incident again. You will see that the incident is marked as closed, but the process ha snot moved on.
This time app2's job executor is staying away from the retry job belonging to the process of app1.
- Stop app2
- Start app1 and see how after a few seconds app1 picks up the retry job (and fails again with *java.lang.RuntimeException: HA!*)

