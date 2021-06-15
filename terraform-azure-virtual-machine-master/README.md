Terraform Module to create Virtual Machine in Microsoft Azure
Tools Used
- Terraform: Version 0.12.29
- Azurerm provider: Version v2.20.0

#### Parameters to pass

| Parameters | Need | Description
| ------ | ------ | ------ |
source |(Required)|source of this module
name|(Required)|name of the Virtual Machine
resource_group_name|(Required)|name of the Resorce Group
vnet_name|(Reqiured)|The name of the virtual network
network_interface_name|(Required)|The name of NIC
size_vm|(Required)|The size of the VM
os_profile_username|(Reqiured)|The username of the VM
os_profile_password|(Required)|The password of the VM
env|(Optional)|name of the environment
team_tag|(Optional)|tag a team
creator|(Optional)|tag a creator

#### Usage:
###### Import existing Resource Group
 terraform
 provider "azurerm" {
  version = "=2.20.0"
  features {}
}

module "az_virtual_machine" {
  source                 = "sneha/virtual-machine/azure"
  name                   = "sneha-vm"
  resource_group_name    = module.az_resource_group.az_rg_name
  network_interface_name = module.az_network_interface.az_network_interface_name
  size_vm                = "Standard_D2_v2"
  os_disk_name           = "sneha-vm-disk"
  os_profile_name        = "sneha-vm-host"
  os_profile_username    = "devops"
  os_profile_password    = "**********"
  env                    = "dev"
  team_tag               = "DevOps"
  creator                = "sneha"
}
```

###### Create new Virtual Machine using module
```terraform
provider "azurerm" {
  version = "=2.20.0"
  features {}
}

module "az_resource_group" {
  source   = "sneha/resource-group/azure"
  name     = "ankesh-workspace"
  location = "westeurope"
  team_tag = "DevOps"
  creator  = "sneha"
}

module "az_virtual_network" {
  source              = "sneha/virtual-network/azure"
  name                = "ankesh-vnet"
  resource_group_name = module.az_resource_group.az_rg_name
  address_space       = "10.0.2.0/24"
  env                 = "dev"
  team_tag            = "DevOps"
  creator             = "sneha"
}

module "az_subnet" {
  source              = "sneha/subnet/azure"
  name                = "sneha-subnet"
  resource_group_name = module.az_resource_group.az_rg_name
  vnet_name           = module.az_virtual_network.az_vnet_name
  address_prefix      = "10.0.2.0/26"
}

module "az_security_group" {
  source                              = "sneha/security-group/azure"
  name                                = "sneha-security-group"
  resource_group_name                 = module.az_resource_group.az_rg_name
  security_rule_name                  = "sneha-security-rule"
  security_priority                   = "101"
  security_direction                  = "Inbound"
  security_access                     = "Allow"
  security_protocol                   = "Tcp"
  security_source_port                = "*"
  security_destination_port           = ["80", "22", "443"]
  security_source_address_prefix      = "*"
  security_destination_address_prefix = "*"
  env                                 = "dev"
  team_tag                            = "DevOps"
  creator                             = "sneha"
}

module "az_public_ip" {
  source              = "sneha/public-ip/azure"
  name                = "ankesh-public-ip"
  resource_group_name = module.az_resource_group.az_rg_name
  allocation          = "Static"
  ip_version          = "IPv4"
  env                 = "dev"
  team_tag            = "DevOps"
  creator             = "sneha"
}

module "az_network_interface" {
  source                = "sneha/network-interface/azure"
  name                  = "sneha-network-interface"
  resource_group_name   = module.az_resource_group.az_rg_name
  vnet_name             = module.az_virtual_network.az_vnet_name
  subnet_name           = module.az_subnet.az_subnet_name
  private_ip_allocation = "Dynamic"
  public_ip_id          = module.az_public_ip.az_public_ip_id
  public_ip_name        = module.az_public_ip.az_public_ip_name
}

module "az_virtual_machine" {
  source                 = "sneha/virtual-machine/azure"
  name                   = "sneha-vm"
  resource_group_name    = module.az_resource_group.az_rg_name
  network_interface_name = module.az_network_interface.az_network_interface_name
  size_vm                = "Standard_D2_v2"
  os_disk_name           = "sneha-vm-disk"
  os_profile_name        = "sneha-vm-host"
  os_profile_username    = "devops"
  os_profile_password    = "**********"
  env                    = "dev"
  team_tag               = "DevOps"
  creator                = "sneha"
}
```

#### Terraform Execution:
###### To initialize Terraform:
```sh
terraform init
```

###### To execute Terraform Plan:
```sh
terraform plan
```

###### To apply Terraform changes:
```sh
terraform apply
```


