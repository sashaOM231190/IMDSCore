# IMDSCore – Azure Managed Identity Diagnostics 

IMDSCore is a diagnostic and health monitoring utility designed to validate Azure Managed Identity and DefaultAzureCredential token acquisition behavior via the IMDS (Instance Metadata Service) endpoint. It optionally supports packet-level network tracing and proxy probing to help identify connectivity or authentication issues.

This tool is especially useful for diagnosing identity and token failures in environments such as:

Azure Virtual Machines or App Services using Managed Identity

Azure Kubernetes Service (AKS) with Workload Identity

Azure-hosted containers or Service Fabric applications

# Core Features

Managed Identity Token Probing
Uses ManagedIdentityCredential (--mode mic) or DefaultAzureCredential (--mode dac) to repeatedly request tokens and log results.

Failure Threshold Monitoring
Automatically exits after N consecutive token acquisition failures.

Network Trace Collection
Captures IMDS traffic using netsh (Windows) or tcpdump (Linux) to help debug connectivity issues.

Proxy Configuration Diagnostics
Optionally logs proxy settings and diagnostics using --proxyprob.

Flexible Logging
Supports custom log directories, log file size can be dynamically set (default unit: 250MB), and up to 4 rotated files.

[Download IMDSCore for Windows and Linux](https://github.com/sashaOM231190/IMDSCore/releases/tag/v1.0.0)

IMDSCore.exe --mode mic|dac --identity sys|user 
             [--clientid [id]] 
             [--maxfailures [n]] 
             [--logdir [path]] 
             [--overridepath [path]] 
             [--logfilesize [size]] 
             [--test] 
             [--printtoken] 
             [--nettrace]
             [--proxyprob]

| Switch           | Description                                                                                |
| ---------------- | ------------------------------------------------------------------------------------------ |
| `--mode`         | Credential type: `mic` (ManagedIdentityCredential) or `dac` (DefaultAzureCredential)       |
| `--identity`     | Identity type: `sys` (system-assigned) or `user` (user-assigned)                           |
| `--clientid`     | Required if `--identity` is `user`; specifies the Client ID of the user-assigned identity  |
| `--maxfailures`  | Stop execution after N consecutive token failures (default: unlimited; max recommended: 5) |
| `--logdir`       | Custom directory for logs; defaults to OS-specific location                                |
| `--overridepath` | (Linux only) Override the default internal log path                                        |
| `--logfilesize`  | Max size of each log file in **MB** (default: 250MB)                          |
| `--test`         | Run a single token test and exit (dry-run mode)                                            |
| `--printtoken`   | Print acquired token into log file (recommended with `--test`)                             |
| `--nettrace`     | Enable network tracing using `netsh` (Windows) or `tcpdump` (Linux)                        |
| `--proxyprob`    | Print current proxy configuration and test its impact on IMDS traffic                      |


💡 Examples
## Basic Token Probe with System Identity
IMDSCore.exe --mode mic --identity sys

## Probe with Proxy Diagnostics
IMDSCore.exe --mode mic --identity sys --proxyprob

## Use MIC with User Identity and Print Token
IMDSCore.exe --mode mic --identity user --clientid [ClientId] --printtoken

## Run a Single Dry Test
IMDSCore.exe --mode dac --identity user --clientid [ClientId] --test

## Enable Network Trace and Custom Logging
IMDSCore.exe --mode mic --identity sys --nettrace --logdir "C:\Logs"

## On Linux: Override Default Log Path (/tmp/imdstrace)
   ### Note : With network trace prefer using default log path (don't use --logdir), else it may fail with permission error, 
   
./IMDSCore --mode mic --identity user --clientid [ClientId] --logdir /tmp/customlogs --overridepath

## On Linux/Ubuntu containers, install tcpdump and pkill, to make network trace work.








