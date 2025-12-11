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
