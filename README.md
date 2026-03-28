# jenkins-python-greetings-pipeline

Šajā repozitorijā glabājas Jenkins pipeline definīcija Python mikroservisa piegādes konveijeram.

## Fails

-`Jenkinsfile`- Jenkins pipeline definīcija

## Kā palaist
1. Atvērt Jenkins
2. Izveidot jaunu `Pipeline` job
3. Izvēlēties `Pipeline script from SCM`
4. Norādīt šo repozitoriju kā Git avotu
5. Norādīt branch: `main`
6. Norādīt script path: `Jenkinsfile`
7. Palaist `Build Now`

## Pipeline stages
- install-pip-deps
- deploy-to-dev
- tests-on-dev
- deploy-to-stg
- tests-on-stg
- deploy-to-preprod
- tests-on-preprod
- deploy-to-prod
- tests-on-prod
