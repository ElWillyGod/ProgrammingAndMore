# RED HAT CERTIFIED OPENSHIFT APPLICATION DEVELOPER EX288

# Preparation

Este es un kpo, tiene videos que le pegan pila a los ejercicios del examen:

https://www.youtube.com/@techtejendra4782

Start the exam login into every account needed, openshift, podman, quay, etc.

- [[Image Stream, Authenticating OpenShift to Private Registries]]
- [[Expose registry]]

![[Pizarron EX288.png]]

> [!info]- Transcripción del pizarrón
> 1. Deploy an application customizing Helm Chart (P3)
> 2. Customizing Build / Add a python3 script post-build (P1)
> 3. Expose internal registry and pull an image (P1)
> 4. Health monitoring / Applying Readiness & Liveness Probes (P2)
> 5. Deploy an application / Broken git code / JSON Troubleshooting ✓
> 6. Customizing and Deploying with S2I ✓
> 7. Customizing Template ✓
> 8. ???
> 9. ???
>
> **1. Create helm chart**
> Customize: name, project, liveness / readiness (Probes `/healthz`), image, source code, chart version
>
> **4. Apply to a running app a health check probe**
> Readiness, Liveness → `oc set probe`, service expose, curl check

---

## JSON troubleshooting

Build, deploy & troubleshoot

1. Create the project and check you are working within the project

    ```bash
    oc new-project example
    oc project example
    ```

2. Create the application
   In this exercise you are asked to use specific data for the application including configuring a variable with the npm_config_registry they provide on it.

    ```bash
    oc new-app --name appexample --build-env npm_config_registry=given_url image~repo#branch --context-dir=directory_given

    oc get pods                # to check the app, here you are going to see an error on the pod
    oc logs -f bc/appexample   # check the logs to recognise the error

    # here we recognize the error as a json parse error
    ```

3. Troubleshoot the problem
    1. Clone the repository
    2. Check out to the correct branch

        ```bash
        git clone repo
        git checkout branch
        ```

    3. Look for the file that has the json error on it

        ```bash
        cd repo/directory                  # in the error log we can see the path to the failing file
        python3 -m json.tool package.json  # this command helps recognizing what is wrong in the file
        vi package.json                    # fix the problem, probably a fucking } or :
        ```

    4. Upload the changes made

        ```bash
        git add package.json
        git commit -m "fix package.json"
        git push
        ```

    5. Build again

        ```bash
        oc start-build --follow bc/appexample  # with this command we can see the live logs
        oc get all                             # to check the build again, if u did it right it will be no problems
        ```

4. Create the route and check the application is working

    ```bash
    oc expose service appexample
    oc get route
    curl app_url

    # if all this works and the app responds you did it right. This exercise ends here.
    ```

---

## Customizing S2I Builds

1. Clone the repository and checkout to the correct branch

    ```bash
    git clone repo
    git checkout branch
    cd repo/.s2i/bin   # files to modify are inside this directory
    ```

2. Start customising

    ```bash
    vi assemble   # file to customize
    ```

    ```bash
    #!/bin/bash

    set -e

    source ${HTTPD_CONTAINER_SCRIPTS_PATH}/common.sh

    echo "---> Enabling s2i support in httpd24 image"

    config_s2i

    ######## CUSTOMIZATION STARTS HERE ############

    echo "---> Installing application source"
    ## do something ##

    cp -Rf /tmp/src/*.html ./

    DATE=`date "+%b %d, %Y @ %H:%M %p"`

    echo "---> Creating info page"
    echo "Page built on $DATE" >> ./info.html
    echo "Proudly served by Apache HTTP Server version $HTTPD_VERSION" >> ./info.html

    ######## CUSTOMIZATION ENDS HERE ############

    if [ -d ./httpd-cfg ]; then
      echo "---> Copying httpd configuration files..."
      if [ "$(ls -A ./httpd-cfg/*.conf)" ]; then
        cp -v ./httpd-cfg/*.conf "${HTTPD_CONFIGURATION_PATH}"
        rm -rf ./httpd-cfg
      fi
    else
      if [ -d ./cfg ]; then
        echo "---> Copying httpd configuration files from deprecated './cfg' directory, use './httpd-cfg' instead..."
        if [ "$(ls -A ./cfg/*.conf)" ]; then
          cp -v ./cfg/*.conf "${HTTPD_CONFIGURATION_PATH}"
          rm -rf ./cfg
        fi
      fi
    fi

    # Fix source directory permissions
    fix-permissions ./
    ```

3. Go back to the root dir and push the changes

    ```bash
    cd ../..
    git status
    git add .
    git commit -m "customize s2i assemble"
    git push
    ```

4. Configuracion en cluster

    ```bash
    oc new-project example
    oc new-app --name=example image~repo#branch --context-dir dir
    ```

5. Check if everything is alright

    ```bash
    oc get pods   # check the logs and check everything works
    ```

6. Expose the service

    ```bash
    oc expose service example
    curl route
    curl route/info.html   # check the changes and bash commands.
    ```

---

## Helm Chart Build

1. Creating helm chart and inspect it:

    ```bash
    helm create example
    cd example
    tree .
    ```

2. Configure the details needed

    ```bash
    vi values.yaml                # here edit: image, registry/repository, tag, pullPolicy
    vi templates/deployment.yaml  # go to containers section, edit container port
    vi Chart.yaml                 # at last we add dependencies, format below
    ```

    ```yaml
    dependencies:
    - name: mariadb
      version: 11.0.13
      repository: https://charts.bitnami.com/bitnami
    ```

    ```bash
    helm dependency update   # to apply the changes and update the chart
    ```

3. Once applied the dependencies we need to edit values.yaml again

    ```bash
    vi values.yaml
    # move to the end and will find the dependencies section, make sure to fix it
    # add in the end the env variables needed

    vi templates/deployment.yaml
    # add the env section for the chart to work, use the following format
    ```

    Inside `containers`, at the same level as `image:`:

    ```yaml
              env:
                {{- range .Values.env }}
                - name: {{ .name }}
                  value: {{ .value | quote }}
                {{- end }}
    ```

    And in `values.yaml`:

    ```yaml
    env:
      - name: DB_USER
        value: user
      - name: DB_PORT
        value: 3306
    ```

4. Finally

    ```bash
    oc new-project project_example
    helm install example .   # execute this from inside of the chart

    oc get all   # to verify the status of the deployment

    # if something go wrong the easiest way to start again will be
    oc delete project project_example
    # repeat the process, create the project and install helm chart again
    # this way will save time and effort on the exam, dont try to fix the broken deployment
    # you will consume too much time

    oc expose service example
    curl route
    ```

---

## Monitoring Application health - Probes

Readiness & Liveness Probe

There are 3 methods:

- Http checks
- Container Execution checks
- TCP socket checks

```bash
oc new-project example

oc new-app --name probes --context-dir dir --build-env npm_config_registry=registry_url image~repo

oc get all   # check pods

oc expose service probes
oc get route
curl route
curl route/ready -i
curl route/healthz -i

# applying readiness probe
oc set probe deployment probes --readiness --get-url=http://:8080/ready \
  --initial-delay-seconds=2 --timeout-seconds=2

curl route/ready -i
oc get pods

oc set probe --help   # if you forget smth

# applying liveness probe
oc set probe deployment probes --liveness --get-url=http://:8080/healthz \
  --initial-delay-seconds=2 --timeout-seconds=2

oc describe deployment probes   # this should show you the probes applied to the deployment

curl route/healthz -i

curl route/flip?op=kill   # switch the app to unhealthy
oc get pods               # to check

curl route/healthz -i     # check the app go back to OK 200 state
```

---

## Docker build and deploy

Deploy image

image:tag

expose the internal registry and connect it with the user devops from the workspace and pull an image from it

error manifest unknown

customize build add to the build a script that executes when the build finishes python3 script file

### BUILD config post-build script

```bash
oc set build-hook bc/mybc --post-commit --command -- python script.py
```
