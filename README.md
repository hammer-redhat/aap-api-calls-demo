# AAP API Calls

This repository contains examples for launching Ansible Automation Platform (AAP) job templates via the API.

## Launching Job Templates

### Basic (Minimal)

```bash
curl -k -X POST \
  "https://aap.example.com/api/controller/v2/job_templates/<JOB TEMPLATE ID>/launch/" \
  -H "Authorization: Bearer YOUR_AAP_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{}'
```

### With Extra Variables

```bash
curl -k -X POST \
  "https://aap.example.com/api/controller/v2/job_templates/<JOB TEMPLATE ID>/launch/" \
  -H "Authorization: Bearer YOUR_AAP_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "extra_vars": {
      "variable1": "value1",
      "variable2": "value2"
    }
  }'
```

### With Additional Optional Parameters

```bash
curl -k -X POST \
  "https://aap.example.com/api/controller/v2/job_templates/<JOB TEMPLATE ID>/launch/" \
  -H "Authorization: Bearer YOUR_AAP_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "extra_vars": {
      "target_env": "production",
      "app_version": "1.2.3"
    },
    "job_tags": "deploy,configure",
    "skip_tags": "test",
    "limit": "web-servers",
    "verbosity": 2,
    "forks": 10
  }'
```

## Notes

- Replace `YOUR_AAP_TOKEN` with your actual AAP API token.
- The `-k` flag skips SSL verification (useful for lab environments with self-signed certificates).
- The request body can be empty `{}` if you don't need to pass any parameters.

## Optional Parameters

The following optional parameters can be included in the request body:

- **extra_vars**: Dictionary of variables to pass to the playbook
- **job_tags**: Comma-separated tags to run
- **skip_tags**: Comma-separated tags to skip
- **limit**: Host pattern to limit execution
- **verbosity**: Verbosity level (0-4)
- **forks**: Number of parallel processes
- **timeout**: Job timeout in seconds
- **credential**: Credential ID (if template allows override)
- **inventory**: Inventory ID (if template allows override)
- **execution_environment**: Execution environment ID
- **labels**: Array of label IDs
- **scm_branch**: SCM branch to use


## Using ansible.controller.job_launch module in a playbook

### Configure the files/controller_auth file
```
vim files/controller_auth
```

### Populate the file with your AAP connection info and credentials.
```
#Instead of username/password you can leverage oauth_tokens from AAP. 
[general]
host=https://aap.example.com
username= user
password= password
#oauth_token= <token>
verify_ssl=false
```

### Ensure the ansible.controller collection is installed
```
$ ansible-galaxy collection install ansible.controller
```

### Update the playbook with your desired job template.
```
vim job_launch.yml
```

```
---
- name: Launch a job in AAP from a playbook. 
  hosts: localhost
  tasks:
    - name: Launch a job with inventory and AAP credentials. 
      ansible.controller.job_launch:
        job_template: "ping"     #<------ Update the name 'ping' to the name of your desired job template. 
        controller_config_file: files/controller_auth
        wait: true
      register: job

    - name: Wait for job max 120s
      ansible.controller.job_wait:
        job_id: "{{ job.id }}"
        timeout: 120
        controller_config_file: files/controller_auth

    - name: Display id and status of the job. 
      ansible.builtin.debug:
        msg: "Job id {{ job.id }} has a status of {{ job.status }}" 
```

### Example output
```
$ ansible-playbook job_launch.yml

PLAY [Launch a job in AAP from a playbook.] **************************************************************************************************************************************************************************************************

TASK [Gathering Facts] ***********************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Launch a job with inventory and AAP credentials.] **************************************************************************************************************************************************************************************
changed: [localhost]

TASK [Wait for job max 120s] *****************************************************************************************************************************************************************************************************************
ok: [localhost]

TASK [Display id and status of the job.] *****************************************************************************************************************************************************************************************************
ok: [localhost] => {
    "msg": "Job id 12 has a status of successful"
}

PLAY RECAP ***********************************************************************************************************************************************************************************************************************************
localhost                  : ok=4    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
