🔧 IMDSCore – Azure Managed Identity Diagnostics & IMDS Health Probe
IMDSCore is a diagnostic and health monitoring utility that validates Azure Managed Identity and DefaultAzureCredential token acquisition behavior via the IMDS (Instance Metadata Service) endpoint. It optionally supports packet-level network tracing to assist in identifying connectivity or authentication issues.

This tool is especially useful for debugging identity and token issues in environments like:

Azure Virtual Machines or App Services using Managed Identity

Azure Kubernetes Service (AKS) with Workload Identity

Azure-hosted containers or Service Fabric apps

# Core Features

- Managed Identity Token Probing
Repeatedly acquires tokens using either ManagedIdentityCredential (--mode mic) or DefaultAzureCredential (--mode dac) and logs successes or failures.

- Failure Threshold Monitoring
Automatically exits if a configurable number of consecutive failures is reached.

- Network Trace Collection
Collects IMDS traffic using netsh (on Windows) or tcpdump (on Linux) to troubleshoot endpoint reachability and delays.

- Flexible Command-Line Interface
Intuitive and robust CLI with named switches for mode, identity type, log directory, test mode, and tracing options.


IMDSCore.exe --mode mic|dac --identity sys|user 
             [--clientid <id>] 
             [--maxfailures <n>] 
             [--logdir <path>] 
             [--overridepath <path>] 
             [--test] 
             [--printtoken] 
             [--nettrace]


| Switch           | Description                                                                          |
| ---------------- | ------------------------------------------------------------------------------------ |
| `--mode`         | Credential type: `mic` (ManagedIdentityCredential) or `dac` (DefaultAzureCredential) |
| `--identity`     | Identity type: `sys` (system-assigned) or `user` (user-assigned)                     |
| `--clientid`     | Required for user-assigned identities (`--identity user`)                            |
| `--maxfailures`  | Stop after N consecutive token acquisition failures  (max 5)                         |
| `--logdir`       | Custom log output directory (default: OS-specific), log file max size 250 MB (max number of files 4, threshold)                                  
| `--overridepath` | (Linux only) Override the default log path                                           |
| `--test`         | Run a single test and exit (for dry-run checks)                                      |
| `--printtoken`   | Print the acquired token to the log file, run in test setup                          |
| `--nettrace`     | Enable network trace collection using `netsh` or `tcpdump`                           |

Note : When --maxfailures or max number of log files are reached tool will stop itself.

[Download IMDSCore for windows and linux](https://github.com/sashaOM231190/IMDSCore/releases/tag/v1.0.0)

Usage:
   IMDSCore.exe --mode mic|dac --identity sys|user [--clientid <id>] [--maxfailures <n>] [--logdir <path>] [--test] [--printtoken] [--nettrace]



Examples:
  IMDSCore.exe --mode mic --identity sys
  
  IMDSCore.exe --mode mic --identity user --clientid <ClientId> --test --printtoken
  
  IMDSCore.exe --nettrace --logdir
  
  Use --overridepath in Linux falvours to change the default log path


# Use ManagedIdentityCredential with system-assigned identity
IMDSCore.exe --mode mic --identity sys --maxfailures [n]

# Use MIC with user-assigned identity, print token in log file
IMDSCore.exe --mode mic --identity user --clientid [ClientId] --printtoken

# Run a single token test in dry-run mode
IMDSCore.exe --mode dac --identity user --clientid [ClientId] --test

# Enable network tracing and log to a specific folder
IMDSCore.exe --mode mic --identity sys --nettrace --logdir "C:\Logs"

# On Linux, override default log directory
./IMDSCore --mode mic --identity user --clientid [ClientId] --overridepath /tmp/customlogs


