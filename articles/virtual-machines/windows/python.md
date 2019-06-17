---
title: Tworzenie i zarządzanie nimi maszyny Wirtualnej z systemem Windows na platformie Azure przy użyciu języka Python | Dokumentacja firmy Microsoft
description: Dowiedz się tworzyć i zarządzać nimi maszyny Wirtualnej z systemem Windows na platformie Azure przy użyciu języka Python.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 748bc08e003d398e96ef55493e4f3b0bf6b7da28
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61034760"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Tworzenie i zarządzanie nimi Windows maszyn wirtualnych na platformie Azure przy użyciu języka Python

[Maszyny wirtualnej platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) wymaga kilku pomocnicze zasoby platformy Azure. W tym artykule opisano tworzenie i usuwanie zasobów maszyny Wirtualnej przy użyciu języka Python, zarządzanie nimi. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Instalowanie pakietów
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonaj te czynności trwa około 20 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze nie, zainstaluj [programu Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz **programowania w języku Python** na stronie obciążeń, a następnie kliknij **zainstalować**. Podsumowując, możesz zobaczyć, że **Python 3 64-bitowy (3.6.0)** jest automatycznie wybrana. Jeśli zainstalowano już program Visual Studio, możesz dodać obciążenie języka Python za pomocą składnika Uruchamianie programu Visual Studio.
2. Po zainstalowaniu i uruchamianie programu Visual Studio, kliknij przycisk **pliku** > **New** > **projektu**.
3. Kliknij przycisk **szablony** > **Python** > **aplikację w języku Python**, wprowadź *myPythonProject* dla nazwy Projekt, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-packages"></a>Instalowanie pakietów

1. W Eksploratorze rozwiązań w obszarze *myPythonProject*, kliknij prawym przyciskiem myszy **środowiska Python**, a następnie wybierz pozycję **Dodaj środowisko wirtualne**.
2. Na ekranie Dodaj środowisko wirtualne Zaakceptuj domyślną nazwę *env*, upewnij się, że *środowiska Python 3.6 (64-bitowy)* wybrano podstawowy interpreter, a następnie kliknij przycisk **Utwórz** .
3. Kliknij prawym przyciskiem myszy *env* środowisko, które zostały utworzone, kliknij przycisk **zainstaluj pakiet języka Python**, wprowadź *azure* w polu wyszukiwania, a następnie naciśnij klawisz Enter.

Powinny zostać wyświetlone w oknie danych wyjściowych pakietów systemu azure zostały pomyślnie zainstalowane. 

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz [jednostki usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). W kolejnym kroku, należy zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, który należy.

1. Otwórz *myPythonProject.py* pliku, który został utworzony, a następnie dodaj ten kod, aby umożliwić aplikacji do uruchomienia:

    ```python
    if __name__ == "__main__":
    ```

2. Aby zaimportować kod, który jest potrzebny, należy dodać te instrukcje na górze plik py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Następnie w plik PY należy dodać zmienne po instrukcje importowania, aby określić wartości typowych używana w kodzie:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Zastąp **identyfikator subskrypcji** z identyfikatorem Twojej subskrypcji.

4. Aby utworzyć poświadczenia usługi Active Directory, które należy wprowadzić żądań, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Zastąp **identyfikator aplikacji**, **klucz uwierzytelniania**, i **identyfikator dzierżawy** wartościami, które wcześniej zebrane podczas tworzenia usługi Azure Active Directory podmiot zabezpieczeń.

5. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Tworzenie zasobów
 
### <a name="initialize-management-clients"></a>Inicjowanie zarządzania klientami

Zarządzanie klientami są potrzebne do tworzenia zasobów i zarządzanie nimi przy użyciu zestawu SDK języka Python na platformie Azure. Aby utworzyć klienci zarządzania, Dodaj następujący kod, w obszarze **Jeśli** instrukcji końcem następnie plik py:

```python
resource_group_client = ResourceManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials, 
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Tworzenie maszyny Wirtualnej i zasoby pomocnicze

Wszystkie zasoby muszą być zawarte w [grupy zasobów](../../azure-resource-manager/resource-group-overview.md).

1. Aby utworzyć grupę zasobów, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Zestawy dostępności](tutorial-availability-sets.md) ułatwienia obsługi maszyn wirtualnych używanych przez aplikację.

1. Aby utworzyć zestaw dostępności, Dodaj tę funkcję po zmiennych w plik py:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) umożliwia komunikację z maszyną wirtualną.

1. Aby utworzyć publiczny adres IP dla maszyny wirtualnej, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi być w podsieci [sieć wirtualna](../../virtual-network/virtual-networks-overview.md).

1. Aby utworzyć sieć wirtualną, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Aby dodać podsieć do sieci wirtualnej, należy dodać tę funkcję po zmiennych w plik py:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi przejść do interfejsu sieciowego komunikacji w sieci wirtualnej.

1. Aby utworzyć interfejsu sieciowego, należy dodać tę funkcję, po zmiennych w plik py:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teraz, gdy utworzono pomocnicze zasoby, możesz utworzyć maszynę wirtualną.

1. Aby utworzyć maszynę wirtualną, należy dodać tę funkcję, po zmiennych w plik py:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Ten samouczek umożliwia utworzenie maszyny wirtualnej z wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania obrazów innych, zobacz [wybierz obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i wiersza polecenia platformy Azure i Navigate](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można tworzyć kod, aby zautomatyzować powtarzalne lub złożone zadania.

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie Wirtualnej

1. Aby uzyskać informacje o maszynie wirtualnej, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zapewnić jej ustawienia, ale nadal będą naliczane opłaty dla niego lub można zatrzymać maszynę wirtualną i cofnąć jej przydział. Po cofnięciu przydziału maszyny wirtualnej, wszystkie zasoby skojarzone z nim są również zakończenia przydział zostanie cofnięty i rozliczeń dla niego.

1. Aby zatrzymać maszynę wirtualną bez dealokowanie go, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Jeśli chcesz cofnąć alokację maszyny wirtualnej, należy zmienić wywołanie power_off ten kod:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

1. Można uruchomić maszyny wirtualnej, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Zmień rozmiar maszyny Wirtualnej

Wiele aspektów wdrożenia należy rozważyć podczas ustawiania rozmiaru maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](sizes.md).

1. Aby zmienić rozmiar maszyny wirtualnej, należy dodać tę funkcję po zmiennych w plik py:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Dodaj dysk danych do maszyny Wirtualnej

Maszyny wirtualne mogą mieć co najmniej jeden [dysków z danymi](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , są przechowywane jako pliki VHD.

1. Aby dodać dysk danych do maszyny wirtualnej, należy dodać tę funkcję po zmiennych w plik py: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ opłaty są naliczane za zasoby używane w systemie Azure, zawsze jest dobrym rozwiązaniem, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie pomocnicze zasoby, trzeba będzie usunąć grupę zasobów.

1. Aby usunąć grupę zasobów i wszystkie zasoby, należy dodać tę funkcję po zmiennych w plik py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Aby wywołać funkcję, które zostały wcześniej dodane, Dodaj następujący kod, w obszarze **Jeśli** instrukcji na końcu plik py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Zapisz *myPythonProject.py*.

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start** w programie Visual Studio.

2. Naciśnij klawisz **Enter** po zwróceniu stanu każdego zasobu. Informacje o stanie powinien być **Powodzenie** stan inicjowania obsługi. Po utworzeniu maszyny wirtualnej, masz szansę sprzedaży, aby usunąć wszystkie zasoby, które tworzysz. Przed naciśnięciem **Enter** zacząć usuwanie zasobów może potrwać kilka minut na sprawdzenie ich utworzenia w witrynie Azure portal. W przypadku witryny Azure portal Otwórz trzeba będzie odświeżyć bloku, aby zobaczyć nowe zasoby.  

    Aby zakończyć powinno zająć około pięciu minut, zanim ta aplikacja konsoli uruchomić zupełnie od początku. Może upłynąć kilka minut po aplikacji została ukończona przed wszystkie zasoby i grupy zasobów zostaną usunięte.


## <a name="next-steps"></a>Kolejne kroki

- Jeśli wystąpiły problemy dotyczące wdrożenia, następnym krokiem powinno być zapoznanie się z artykułem [Troubleshooting resource group deployments with Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md) (Rozwiązywanie problemów z wdrożeniami grup zasobów za pomocą witryny Azure Portal).
- Dowiedz się więcej o [biblioteki Python platformy Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

