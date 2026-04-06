# Using holmesGPT as IA agent feature in AKS

[[_TOC_]]

## Summary

The **_HolmesGPT_** is an AI agent purpose-built for production observability and incident response. **The SRE tool**, Open-source SRE agent for investigating production incidents across any infrastructure — Kubernetes, VMs, cloud services, databases, and more.There is many option for installation.In this document we will explore the python SDK, meaning not consumming resources in your cluster, just using the cli running on your pc.Using the **.kube/config** to the python communicate with kubernetes api.

  

## What you will need

1. LLM configured

- Create: azure portal>Microsoft Foundry>Use with Foundry>Create or using [ai foundry portal](https://ai.azure.com/foundry "azure foundry ai portal") >Create
- Get the information(Name+Api version: Going to [ai foundry portal](https://ai.azure.com/foundry "azure foundry ai portal") >My assests> Models+Endpoints 

2. Python  

3. Python libraries manager [install poetry](https://python-poetry.org/docs/#installing-with-the-official-installer "installing poetry")
- ```curl -sSL https://install.python-poetry.org | python3 - ```  (easy)

4. Git to clone the HolmesGPT repository and install the requirement python packages
- ```git clone https://github.com/HolmesGPT/holmesgpt.git```
- ```cd holmesgpt```
- ```poetry install --no-root```

5. Validate holmesGTP instalation:

```bash
   poetry run holmes ask --help
```

6. How to use the provider: [azure openai](https://holmesgpt.dev/latest/ai-providers/azure-openai/ "how to use the provider")

```bash
   export AZURE_API_VERSION="2024-02-15-preview"
   export AZURE_API_BASE="https://your-resource.openai.azure.com"
   export AZURE_API_KEY="your-azure-api-key"
```

7. Validate pod configuration is accurate: --model="provider/name-of-model"

```bash
poetry run holmes ask "what pods are failing?" --model="azure/gpt-mister"
```

  

## Confirm one real scenario: The node were deleted, but i dont know what/why was deleted!

```bash
14:49:48_$ $VENV_PATH/bin/poetry run holmes ask "why my node aks-agentpool-40890303-vmss000005 was deleted " --model="azure/gpt-4.1"
```

Output:

```console
Retrying request to /chat/completions in 45.000000 seconds
╭─ AI Response ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│  Node aks-agentpool-40890303-vmss000005 was deleted due to autoscaler scale down. 
Events show:                                                                             
│  • 14m ago: Node marked as toBeDeleted/unschedulable (ScaleDown)                                                               
│   • 13m ago: Node status changed to NotReady                                           
│   • 5m ago: Node deleted because it did not exist in cloud provider                    
│  Node pool agentpool is configured with autoscaling enabled and scale down mode set to Delete. No pods were running on the deleted node at time of deletion.               
│  Root cause: Cluster autoscaler removed node to reduce capacity.                       
╰──────────────────────────────────────────────────────────────────────────
```


> **NOTE:** the console stay actived till **crtl+c** to exit

```bash
User: which node and associated processes had OOMkill events
```
Output:
```console

Thinking... (press escape to interrupt)
╭─ AI Response ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│                                                                                                                                                                           
│  Node aks-agentpool-21576910-vmss000007 had OOMKill events for these processes:                                                                                            │
│                                                                                                                                                                            
│   • stress (PID 207419, 207416, 207417)                                                                                                                                    │
│   • bash (PID 199315)                                                                                                                                                                                                                                                                                                                                
│  All OOMKill events occurred on this node.                                                                                                                                 │
│                                                                                                                                                                            │
╰────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

```bash
User: what is the average of  memory consumption of this aks

Thinking... (press escape to interrupt)
```
Output:
```console
╭─ AI Response ──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮                                                                                                                                                                            
│  Current average node memory usage in this AKS cluster:                                                                                                                    │                                                                                                                                                                          
│   • aks-agentpool-21576910-vmss000006: 3746Mi (27%)                                                                                                                        
│   • aks-agentpool-21576910-vmss000007: 2110Mi (15%)                                                                                                                        
│                                                                                                                                                                            
│  Average = (3746 + 2110) / 2 = 2928Mi                                                                                                                                      
│                                                                                                                                                                            
│  So, average node memory consumption is 2928Mi.                                                                                                                            
│                                                                                                                                                                            
╰────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```


## References
- 

## Owner and Contributors

**Owner:** Mario Chaves <mariochaves@microsoft.com>

**Contributors:**
- Mario Chaves <mariochaves@microsoft.com>
