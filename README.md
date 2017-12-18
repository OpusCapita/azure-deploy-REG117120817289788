# azure-deploy-REG117120817289788

## initial deploy
open powershell and 
```Add-AzureRMAccount -SubscriptionID <your subscription id>```

checkout this repo and cd there

create test resource group
```New-AzureRmResourceGroup -Location westeurope -Name oc_shared_test_gr4per```

then run deployment using powershell
```New-AzureRmResourceGroupDeployment -Name genesis -ResourceGroupName oc_shared_test_gr4per -TemplateFile deploy_infra.json -TemplateParameterFile dev_params.json```

enter the public key phrase when prompted for parameter:
```ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEApLQ8FMtRdA8SyT1tYTqrz4W/8IUmqOVh80MnqU5BkqdUSwisz+8ITnSu18JIS2//Qzsotz1LGsYPzLelhCb0aK7SJyV3M17TuWanYBi6nJTCKm1Ithq/KoYHcLsBErWsyELOhFpYkqeLm/wzUg3WU6FL0feZMxlNgnWSt+RkB8+/SMMwi4uV8oBXXdWtqeJkWonC2I42XBYvZdPJHlaPXrNFJheUqIWwo6Z+WfqPmkipo1hbyWcGpr7wQnzhNbjPlHsIdF0Z/IEaa4axEvmvRxELrHYjCz9bmV/n+teyIBzcwVRVab+fn/G+HlCgz8ydEYiKHVB93s8F/mk3iPnxWw== rsa-key-20171218```

confirm vm via az cli
```
az login
az account set --subscription "<your subscription name>"
az vm show --name dev-postgres0 --resource-group oc_shared_test_gr4per --show-details | jq ".osProfile.linuxConfiguration```

confirm ssh to the machine using the azure-deploy-REG117120817289788.ppk key

## update the public key
```az vm user update --name dev-postgres0 --resource-group oc_shared_test_gr4per --username dm --ssh-key-value "ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAkNhbZ5l9irAHhFoT0wOPHHFAZg+rNDa1qMTE/aqsoIq6pK/HTVCR/5GRlnRGrnXfsg7Y9ZeDv29WNYlYA7ndpXWCFWsyPHqIM1UNsP1fCBRHbsYmfn+oT3RxxExE/jFaRyJr0rYFg9kEsD1DaY0IKkKYku3bvM9kb84syhAWHtM1Vs9FyUBfNgb3nP326PW7fXvA5dB6jYb+oMQZ9HTtvRq4svTJtt8QkctQ1uWdQZ0JUyjDHXLVNhRUwZLC2N6jdTJK4SY6b9Uv2yVZJoA46eRI6vzxOs+pK7vHhTltvB3/vonCJHrtPTDR+7LU9Ph9ZpA2Lm15qQSkACsDd9Lxaw== rsa-key-20171218"```

this says ok

## confirm login via ssh using new key

it does work

## inspect authorized_keys on target vm

this now has both keys: the old one and the new one EXPECTATION FAILED

## inspect vm details via az cli

this still shows the old key EXPECTATION FAILED

## rerun the original deployment
```New-AzureRmResourceGroupDeployment -Name genesis -ResourceGroupName oc_shared_test_gr4per -TemplateFile deploy_infra.json -TemplateParameterFile dev_params.json```

use the new public key this time:
ssh-rsa AAAAB3NzaC1yc2EAAAABJQAAAQEAkNhbZ5l9irAHhFoT0wOPHHFAZg+rNDa1qMTE/aqsoIq6pK/HTVCR/5GRlnRGrnXfsg7Y9ZeDv29WNYlYA7ndpXWCFWsyPHqIM1UNsP1fCBRHbsYmfn+oT3RxxExE/jFaRyJr0rYFg9kEsD1DaY0IKkKYku3bvM9kb84syhAWHtM1Vs9FyUBfNgb3nP326PW7fXvA5dB6jYb+oMQZ9HTtvRq4svTJtt8QkctQ1uWdQZ0JUyjDHXLVNhRUwZLC2N6jdTJK4SY6b9Uv2yVZJoA46eRI6vzxOs+pK7vHhTltvB3/vonCJHrtPTDR+7LU9Ph9ZpA2Lm15qQSkACsDd9Lxaw== rsa-key-20171218

Deployment fails: EXPECTATION FAILED
```
New-AzureRmResourceGroupDeployment : 12:33:41 - Resource Microsoft.Compute/virtualMachines 'dev-postgres0' failed with
message '{
  "error": {
    "code": "PropertyChangeNotAllowed",
    "target": "linuxConfiguration.ssh.publicKeys",
    "message": "Changing property 'linuxConfiguration.ssh.publicKeys' is not allowed."
  }
}'
```
