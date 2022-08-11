# **BorysNie** - Code examples

```python
# Python

def main(args):
    """ This function sets up and runs the number of requests to be processed parallelly """
    log.info(f"Parent process Pid: {os.getpid()}")
    log.info(f"Starting with parameters: {args}")

    url = args['url']
    params = args['params']
    processes = []
    start_times = []
    end_times = []
    total_times = []

    if validate_url(url):
        for thread in range(args['threads']):
            log.info(f"Registering process {thread}")

            # If random is true, ?ref=<random> will be appended to the end of the URL.
            if args['random']:
                params = {'ref': ''.join(random.choices(ascii_lowercase + digits, k=8))}

            processes.append(Process(target=web_request, args=(url, params,)))

        for process in processes:
            log.info(f"Starting process: {process}")
            start_times.append(time())
            process.start()

        for process in processes:
            log.info(f"Joining process: {process}")
            process.join()
            end_times.append(time())

        for start in start_times:
            for end in end_times:
                total_times.append(end - start)

        delta = round(sum(total_times) / len(total_times), 2)

        log.info(f"Time Delta: {delta} seconds")
    else:
        log.error('Invalid URL')
```


```yaml
# YAML

AWSTemplateFormatVersion: 2010-09-09

Parameters:
  ProjectName:
    Type: String
  AvailabilityZone1:
    Type: String
    Default: "c"
    AllowedValues: ["a", "b", "c"]
  AvailabilityZone2:
    Type: String
    Default: "a"
    AllowedValues: ["a", "b", "c"]

Resources:
  NetworkStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      Parameters:
        AvailabilityZone1: !Ref AvailabilityZone1
        AvailabilityZone2: !Ref AvailabilityZone2
        ExportPrefix: !Sub "${ProjectName}-network-resources"
      Tags:
        - Key: Project
          Value: !Sub "${ProjectName}"
      TemplateURL: "https://s3-eu-west-1.amazonaws.com/<bucket>/<folder>/<file.yaml>"
      TimeoutInMinutes: '5'
    DeletionPolicy: Retain

  EcsStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      Parameters:
        ProjectName: !Ref ProjectName
        VpcId: !ImportValue NetworkResourcesVpcId
        PrimarySubnetId: !ImportValue NetworkResourcesPrimarySubnetId
        SecondarySubnetId: !ImportValue NetworkResourcesSecondarySubnetId
        ExportPrefix: !Sub "${ProjectName}-ecs-resources"
      Tags:
        - Key: Project
          Value: !Sub "${ProjectName}"
      TemplateURL: "https://s3-eu-west-1.amazonaws.com/<bucket>/<folder>/<file.yaml>"
      TimeoutInMinutes: '5'
    DeletionPolicy: Retain
```


```groovy
// Groovy

pipeline {
  agent any

  environment {
    KEYFILE = ''
  }

  parameters {
    string(name: 'INSTANCEIP', defaultValue: '', description: 'Internal EC2 IP address')
    string(name: 'ACCESSKEY', defaultValue: '', description: 'AWS Access Key')
    string(name: 'SECRETKEY', defaultValue: '', description: 'AWS Secret Key')
  }

  stages {
    stage('Validate parameters') {
      steps {
        script {
          if (!INSTANCEIP) {
            echo 'Error: internal IP address not set.'
            sh 'exit 1'
          }
          if (!ACCESSKEY) {
            echo 'Error: access key is not set.'
            sh 'exit 1'
          }
          if (!SECRETKEY) {
            echo 'Error: secret key is not set.'
            sh 'exit 1'
          }
        }
      }
    }
    stage('Deploy') {
      steps {
        script {
          sh """ansible-playbook deploy.yml -i ${params.INSTANCEIP}, -e "access_key=${params.ACCESSKEY} secret_key=${params.SECRETKEY}" --key-file ${env.KEYFILE}"""
        }
      }
    }
  }
}
```


```cpp
// C++

#pragma comment( lib, "user32.lib" )
#pragma comment( lib, "gdi32.lib" )

#include <windows.h>
#include <stdio.h>

INT iFormat = -1;
BOOL bAuto = TRUE;

const char ClipboardCaptureName[] = "myCaptureClass";

void WINAPI SetAutoView(HWND hwnd)
{
	static UINT auPriorityList[] = {
		CF_OWNERDISPLAY,
		CF_TEXT,
		CF_ENHMETAFILE,
		CF_BITMAP
	};

	iFormat = GetPriorityClipboardFormat(auPriorityList, 4);
	bAuto = TRUE;
	InvalidateRect(hwnd, NULL, TRUE);
	UpdateWindow(hwnd);
}
```


```sh
# Bash

function get_step {
    local etl_entry=$1
    local version=$2
    local client=$3
    local etl_array=(${etl_entry//+/ })
    local etl_name="${etl_array[0]}"
    local etl_type="${etl_array[1]}"
    local etl_in="${etl_array[2]}"
    local etl_out="${etl_array[3]}"
    local extra_parameters="${etl_array[4]}"

    if [ $etl_type = "bash" ]
    then
        echo $(cat step_bash_template.json \
            | sed "s~{ETL}~$etl_name~g" \
            | sed "s~{PARAM1}~$etl_in~g"
        )
    else
        echo $(cat step_template.json \
            | sed "s~{CLIENT}~$client~g" \
            | sed "s~{GCSC_VERSION}~${GCSC_VERSION}~g" \
            | sed "s~{ETL}~$etl_name~g" \
            | sed "s~{TYPE}~$etl_type~g" \
            | sed "s~{IN}~$(insert_spaces "${etl_in}")~g" \
            | sed "s~{OUT}~$(insert_spaces "${etl_out}")~g" \
            | sed "s~{VERSION}~$version~g" \
            | sed "s~{EXTRA_PARAMETERS}~$extra_parameters~g"
        )
    fi
}
```