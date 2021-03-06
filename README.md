# gcvp

This project is design to be a cron-job that stop (scale down) dev project. 
1 - It will look for all the deploy and deploymentconfig
2.1 - If label io.shyrka.gcvp/scaledown=false, then nothing
2.2 - If label current date is before the "io.shyrka.erebus/end-date" value,  then nothing
2.3 - otherwise 2.1 - If label io.shyrka.gcvp/scaledown=true and current date is after the "io.shyrka.erebus/end-date" then it scale down the dc or deploy

Sisyphus validate projet conformity
3. It will look for configmap with label io.shyrka.sisyphus/start


## Build

you need a docker (DOCKER_HOST environment variable) up in order to build the image

mvn io.fabric8:fabric8-maven-plugin:build

kubectl create configmap shyrka --from-literal=agent=true
kubectl run gcvp --image=kanedafromparis/gcvp:1.0-SNAPSHOT --restart=OnFailure


## LABELS

We use label on a shirka configmap instead of using info from that config, because it is simplier to manage then having those tag on the namespaces (due to openshift security management)

## Labels on shirka configmap

|  resource  |      Label name (pattern)                             | Description     |  Possible values    | Example    |
|------------|:-----------------------------------------------------:|-----------------|---------------------|-----------:|
| configmap  |  io.shyrka.erebus/pjt-name                            |                 |     string          | sample     |
| configmap  |  io.shyrka.erebus/product.owner                       |   user name     | firstname.lastname  | john.doe   |
| configmap  |  io.shyrka.erebus/product.owners.last.acknowledgement |   user name     | Date (ISO 8601)  | 2018-01-01 |


### Annotations on shirka configmap

|  resource  |      annotation name (pattern)                        | Description                           |  Possible values    | Example                                               |
|------------|:-----------------------------------------------------:|---------------------------------------|---------------------|------------------------------------------------------:|
| configmap  |  io.shyrka.erebus/product.owner.email                 |                                       |     email           | john.doe@yopmail.com                                  |
| configmap  |  io.shyrka.erebus/team.watchers                       |   email list for team mate in json    | JSON array of email | {0 : lerappoffi-4267@yopmail.com, 1 :....             |
| configmap  |  io.shyrka.erebus/contract.ref                        |   currently not use                   | URL                 |                                                       |
| configmap  |  io.shyrka.erebus/contract.ref.TYPE                   |   currently not use                   | URL                 |                                                       |
| configmap  |  io.shyrka.erebus/project-scope:${scope}              |   the scope of the project            | URL                 |                                                       |



### Labels on deploy and dc

|  resource        |      Label name (pattern)                          | Description     |  Possible values                                                     | Example    |
|------------------|:--------------------------------------------------:|-----------------|----------------------------------------------------------------------|-----------:|
| deploy\|dc       |  io.shyrka.gcvp/scaledown                          |                 |     true/false                                                       |            |
| deploy\|dc       |  io.shyrka.erebus/start-date                       |   no used       | Date (ISO 8601)                                                      | 2018-01-01 |
| deploy\|dc       |  io.shyrka.erebus/end-date                         |   used          | Date (ISO 8601)                                                      | 2018-01-01 |
| deploy\|dc       |  io.shyrka.erebus/framework:${name}                |   used          | .../framework:lamp                                                   | 2018-01-01 |
| deploy\|dc       |  io.shyrka.erebus/scope:${scope}                   |   string        | development \ production \ preproduction                             | production |
| deploy\|dc       |  io.shyrka.erebus/scope:${scope}                   |   string        | development \ production \ preproduction                             | production |
| deploy\|dc\|jobs |  io.shyrka.erebus.${framework}-info/role:${role}                    |   string        | lamp-info/role:database, lamp-info/role:application                  | production |
| deploy\|dc\|jobs |  io.shyrka.erebus.${framework}-info/${component}-version:${version} |   vernum        | lamp-info/mariadb-version:10.2.13, lamp-info/httpd-version: 2.4.23   | production |

--


