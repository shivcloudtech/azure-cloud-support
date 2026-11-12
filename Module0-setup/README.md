# Module 0: Setup and Cost Control

## Goal
Set up an Azure subscription with cost guardrails, and practice creating, tagging and deleting resource groups using the Azure portal and Azure CLI in Bash.

## What I built
- A monthly budget with alerts at 50%, 80%, and 100%
- Azure Cloud Shell (Bash)
- Tagged resource groups, created and deleted with Bash loops
- A cleanup routine and a cost log

## Steps

### 1. Subscription and credit
Checked my subscription offer and credit expiry date: Free trial

![screenshots](screenshots/1.png)

![screenshots](screenshots/2.png)

![screenshots](screenshots/3.png)

### 2. Budget alert and cost analysis
Created a budget called `budget-portfolio` of $1 with alerts at 50%, 80%, and 100%.

![screenshots](screenshots/4.png)

![screenshots](screenshots/5.png)

![screenshots](screenshots/6.png)

![screenshots](screenshots/7.png)

Search cost analysis and set the scope:-

![screenshots](screenshots/8.png)

![screenshots](screenshots/9.png)

![screenshots](screenshots/10.png)

![screenshots](screenshots/11.png)

![screenshots](screenshots/12.png)

![screenshots](screenshots/13.png)

### 3. Cloud Shell (Bash)
Opened Cloud Shell in Bash and confirmed I was signed in:
```
az account show -o table
```
![screenshots](screenshots/14.png)

List subscriptions and resource groups:
![screenshots](screenshots/15.png)

### 4. Create and tag a resource group
```
az group create -n rg-test2 -l canadacentral --tags purpose=portfolio module=0
az group show -n rg-test2 -o table
```
![screenshots](screenshots/23.png)

### 5. Delete resource group through CLI
```
az group delete -n rg-test1 --yes --no-wait
```
![screenshots](screenshots/17.png)

### 6. Delete with a loop
```
for g in $(az group list --query "[?starts_with(name,'rg-test')].name" -o tsv); do az group delete -n $g --yes --no-wait; done 
az group list -o table
```
![screenshots](screenshots/18.png)

## Troubleshooting log

1. 
What I did : Created a group in canadcentral 

Error : (LocationNotAvailableForResourceGroup) The provided location 'canadcentral' is not available for resource group. 

Cause : Typ error in the region name 

How I Found : Ran az account list-locations -o table and compared the spelling 

Fix : Used correct spelling - canadacentral 

![screenshots](screenshots/16.png)

2.
What I did :Created rg-test1 in canadacentral, then again in eastus 

Error : InvalidResourceGroupLocation: The Resource group already exists in location 'canadacentral'. 

Cause : A resource group’s region can’t be changed 

How I Found : Ran az group show -n rg-test1 -o table and saw the original region  

Fix : Reused canadacentral, or deleted the group and recreated it 

![screenshots](screenshots/20.png)

3.
What I did : Deleted rg_test 

Error : (ResourceGroupNotFound) Resource group 'rg_test' could not be found. 

Cause : The group doesn’t exist: wrong name 

How I Found : Ran az group list -o table and compared names 

Fix : Used the exact name from the list and delete it 

![screenshots](screenshots/21.png)

4.
What I did : Added a CanNotDelete lock, then tried to delete rg-test1 

Error : ScopeLocked: The scope  cannot perform delete operation because following scope(s) are locked. Please remove the lock and try again. 

Cause : A lock protects the group to delete 

How I Found : Ran az lock list -g rg-test1 -o table and saw the lock 

Fix : Ran az lock delete --name lock-test --resource-group rg-test1, then deleted the group 

![screenshots](screenshots/22.png)


## What I learned
- The Azure hierarchy is management group, subscription, resource group, resource.
- Budgets only warn. Deleting or deallocating resources is what actually controls cost.
- Most errors name the problem. Checking names, region, subscription, and locks solves most of them.

## Cleanup
Deleted all test resource groups. Cost shown afterward: 

![screenshots](screenshots/25.png)

![screenshots](screenshots/26.png)

