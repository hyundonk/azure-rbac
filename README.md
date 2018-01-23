## Azure Role-Based Access Control

Azure Active Directory는 Active Directory에 등록된 사용자, 그룹, 어플리케이션이 Azure 상의 리소스들에 대한 CRUD(Creation, Delete, Update, Delete) operation 권한을 제어하는 강력한 Role-Based Access Control기능을 제공한다. 
RBAC role은 어떤 리소스(들)에 대해 어떤 권한을 가지는지를 정의한다. 
특정 scope(개별 리소스 level, 또는 리소스 그룹 level, 또는 전체 subscription 레벨)에 대해 Active Directory에 등록된 사용자, 그룹, 또는 어플리케이션에 대해 특정 RBAC role을 할당하는 방법으로 RBAC 권한을 지정한다. 

Azure는 다음과 같은 built-in role을 제공한다. 
- Owner
- Contributor
- Reader
- VM Contributor  
...

예를 들어 특정 사용자에게 가상네트워크, 서브넷, NSG 등의 설정에 대한 변경 권한을 주지 않고 특정 서브넷 내에서 가상 머신을 생성할 수 있는 권한을 주는 경우 "*VM Contributor*" role을 줄 수 있다. 
Built-in role인 VM Contributor role은 아래와 같은 권한을 같는다.  

    $ azure role show --name "Virtual Machine Contributor"
    info:    Executing command role show
    + Searching for role definitions
    data:    Name             : Virtual Machine Contributor
    data:    Id               : 9980e02c-c2be-4d73-94e8-173b1dc7cf3c
    data:    Description      : Lets you manage virtual machines, but not access to them, and not the virtual network or storage account they�re connected to.
    data:    AssignableScopes : 0=/
    data:    Actions          : 0=Microsoft.Authorization/*/read, 1=Microsoft.Compute/availabilitySets/*, 2=Microsoft.Compute/locations/*, 3=Microsoft.Compute/virtualMachines/*, 4=Microsoft.Compute/virtualMachineScaleSets/*, 5=Microsoft.DevTestLab/schedules/*, 6=Microsoft.Insights/alertRules/*, 7=Microsoft.Network/applicationGateways/backendAddressPools/join/action, 8=Microsoft.Network/loadBalancers/backendAddressPools/join/action, 9=Microsoft.Network/loadBalancers/inboundNatPools/join/action, 10=Microsoft.Network/loadBalancers/inboundNatRules/join/action, 11=Microsoft.Network/loadBalancers/probes/join/action, 12=Microsoft.Network/loadBalancers/read, 13=Microsoft.Network/locations/*, 14=Microsoft.Network/networkInterfaces/*, 15=Microsoft.Network/networkSecurityGroups/join/action, 16=Microsoft.Network/networkSecurityGroups/read, 17=Microsoft.Network/publicIPAddresses/join/action, 18=Microsoft.Network/publicIPAddresses/read, 19=Microsoft.Network/virtualNetworks/read, 20=Microsoft.Network/virtualNetworks/subnets/join/action, 21=Microsoft.RecoveryServices/locations/*, 22=Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read, 23=Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read, 24=Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write, 25=Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write, 26=Microsoft.RecoveryServices/Vaults/backupPolicies/read, 27=Microsoft.RecoveryServices/Vaults/backupPolicies/write, 28=Microsoft.RecoveryServices/Vaults/read, 29=Microsoft.RecoveryServices/Vaults/usages/read, 30=Microsoft.RecoveryServices/Vaults/write, 31=Microsoft.ResourceHealth/availabilityStatuses/read, 32=Microsoft.Resources/deployments/*, 33=Microsoft.Resources/subscriptions/resourceGroups/read, 34=Microsoft.Storage/storageAccounts/listKeys/action, 35=Microsoft.Storage/storageAccounts/read, 36=Microsoft.Support/*
    data:    NotActions       :
    data:    IsCustom         : false
    data:
    info:    role show command OK

다음과 같은 방법으로 VM Contributor role을 활용할 수 있다. 
1. 해당 사용자에게 가상 머신을 위치할 Virtual Network 리소스의 "*Reader*" role로 할당하고 대상 subnet의 사용자 관리 메뉴에서는 해당 사용자를 "*Virtual Macihne Contributor*" role로 할당
2. 할당된 Public IP address 리소스 및 Network Security Group 리소스에 대해서도 해당 사용자를 "*Virtual Macihne Contributor*" role로 할당
3. Boot diagnostics를 위해 storage group 사용시 해당 storage group에 대해서도 해당 사용자를 "*Virtual Macihne Contributor*" role로 할당

Built-in VM Contributor role은 Image를 생성하거나 Data Disk를 생성할 수 없다. 필요 시 기존 VM Contributor role 권한에 아래의 리소스 종류에 대한 권한을 추가한 custom role을 생성할 수 있다. ('vmcontributor3.json' 참조)

    “Microsoft.Compute/images/write”  
    "Microsoft.Compute/disks/*"

Custom role은 role definition (json) 파일 생성 후 아래의 Azure CLI 명령으로 추가할 수 있다.  

    $ azure role create --inputfile vmcontributor3.json
    

