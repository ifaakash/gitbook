# Terraform Training

1. We have deployed resources in the Azure cloud using terraform.&#x20;
2. We first created a Resource group and then used that RG to create a Virtual netowrk, followed by a VM.

```hcl

resource "azurerm_resource_group" "firstRG"{
    name="terraform-test-RG"
    location = "West Europe"
}

```

3. If we have a resource deployed in the cloud already, then you can import that resource to use in your state using the "terraform import" command.

> Error: A resource with the ID "/subscriptions/d1740c6b-6728-46a4-9e2d-6707bb55d3a3/resourceGroups/terraform-test-RG" already exists
>
> * to be managed via Terraform this resource needs to be imported into the State. Please see the resource documentation for "azurerm\_resource\_group" for more information.

```
terraform import <resource_identifier>.<resource_name> <id of the resource>

terraform import azurerm_resource_group.firstRG /subscriptions/d1740c6b-6728-46a4-9e2d-6707bb55d3a3/resourceGroups/terraform-test-RG
```
