---
title: Tworzenie i zarządzanie nimi maszyny Wirtualnej systemu Windows na platformie Azure przy użyciu języka Python | Dokumentacja firmy Microsoft
description: Informacje dotyczące sposobu korzystania z języka Python do tworzenia i zarządzania maszyny Wirtualnej systemu Windows na platformie Azure.
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
ms.openlocfilehash: dbe8f1603433f381c3c28cb47d2dbda543b462e0
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Tworzenie i zarządzanie maszynami wirtualnymi systemu Windows na platformie Azure przy użyciu języka Python

[Maszyny wirtualnej Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM), wymaga kilku obsługi zasobów platformy Azure. W tym artykule omówiono tworzenia, zarządzania i usuwania zasobów maszyny Wirtualnej przy użyciu języka Python. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Instalowanie pakietów
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonaj te kroki trwa około 20 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli nie jest jeszcze zainstalować [programu Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz **programowania Python** na stronie obciążeń, a następnie kliknij **zainstalować**. Podsumowując, można stwierdzić, że **Python 3 64-bitowych (3.6.0)** jest automatycznie wybrana. Jeśli zainstalowano program Visual Studio można dodać obciążenia Python za pomocą składnika Uruchamianie programu Visual Studio.
2. Po zainstalowaniu i uruchomieniem programu Visual Studio, kliknij przycisk **pliku** > **nowy** > **projektu**.
3. Kliknij przycisk **szablony** > **Python** > **aplikacji Python**, wprowadź *myPythonProject* dla nazwy Projekt, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-packages"></a>Instalowanie pakietów

1. W Eksploratorze rozwiązań w obszarze *myPythonProject*, kliknij prawym przyciskiem myszy **środowiska Python**, a następnie wybierz **Dodaj środowisko wirtualne**.
2. Na ekranie Dodawanie środowiska wirtualnego, zaakceptuj domyślną nazwę *env*, upewnij się, że *3,6 języka Python (64-bitowy)* został wybrany do interpreter podstawowy, a następnie kliknij przycisk **Utwórz** .
3. Kliknij prawym przyciskiem myszy *env* środowisko, które zostały utworzone, kliknij przycisk **zainstaluj pakiet języka Python**, wprowadź *azure* w polu wyszukiwania, a następnie naciśnij klawisz Enter.

Powinien zostać wyświetlony w oknie danych wyjściowych pakietów azure zostały pomyślnie zainstalowane. 

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz [nazwy głównej usługi Active Directory](../../azure-resource-manager/resource-group-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i Identyfikatora dzierżawy, które są potrzebne w kolejnym kroku.

1. Otwórz *myPythonProject.py* pliku, który został utworzony, a następnie dodaj ten kod, aby umożliwić aplikację do uruchamiania:

    ```python
    if __name__ == "__main__":
    ```

2. Aby zaimportować kodu, który jest wymagany, Dodaj te instrukcje na początku pliku .py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Następnie w pliku PY i Dodaj zmienne po instrukcje importu, tak aby określić wartości typowych używane w kodzie:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Zastąp **identyfikator subskrypcji** z identyfikatorem subskrypcji.

4. Aby utworzyć poświadczenia usługi Active Directory, które są potrzebne do tworzenia żądań, Dodaj tej funkcji po zmiennych w pliku .py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Zastąp **identyfikator aplikacji**, **klucz uwierzytelniania**, i **identyfikator dzierżawcy** z wartościami, które wcześniej zebrane podczas tworzenia usługi Azure Active Directory podmiot zabezpieczeń.

5. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Tworzenie zasobów
 
### <a name="initialize-management-clients"></a>Inicjowanie zarządzania klientami

Klienci zarządzania są potrzebne do tworzenia i zarządzania zasobami na platformie Azure przy użyciu zestawu SDK Python. Aby utworzyć zarządzania klientami, Dodaj ten kod w obszarze **Jeśli** instrukcji następnie końcem pliku .py:

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

### <a name="create-the-vm-and-supporting-resources"></a>Utwórz maszynę Wirtualną i obsługi zasobów

Wszystkie zasoby muszą być zawarte w [grupy zasobów](../../azure-resource-manager/resource-group-overview.md).

1. Aby utworzyć grupę zasobów, należy dodać tej funkcji po zmiennych w pliku .py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Zestawy dostępności](tutorial-availability-sets.md) ułatwienia obsługi maszyn wirtualnych używanych przez aplikację.

1. Aby utworzyć zbiór dostępności, Dodaj tę funkcję po zmiennych w pliku .py:
   
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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

A [publicznego adresu IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) jest potrzebne do komunikowania się z maszyną wirtualną.

1. Aby utworzyć publiczny adres IP dla maszyny wirtualnej, należy dodać tej funkcji po zmiennych w pliku .py:

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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi należeć do podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Aby utworzyć sieć wirtualną, Dodaj tę funkcję po zmiennych w pliku .py:

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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Aby dodać podsieci do sieci wirtualnej, należy dodać tę funkcję po zmiennych w pliku .py:
    
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
        
4. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi komunikować się w sieci wirtualnej do interfejsu sieciowego.

1. Aby utworzyć interfejsu sieciowego, należy dodać tej funkcji po zmiennych w pliku .py:

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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teraz, gdy utworzono pomocnicze zasoby, można utworzyć maszyny wirtualnej.

1. Aby utworzyć maszynę wirtualną, Dodaj tę funkcję po zmiennych w pliku .py:
   
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
    > W tym samouczku tworzy maszynę wirtualną z wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej o wybieraniu innych obrazów, zobacz [Nawigacja i wybierz obrazów maszyny wirtualnej platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można utworzyć kod do automatyzowania zadań powtarzających się lub złożonych.

### <a name="get-information-about-the-vm"></a>Uzyskiwanie informacji o Maszynie wirtualnej

1. Aby uzyskać informacje dotyczące maszyny wirtualnej, należy dodać tej funkcji po zmiennych w pliku .py:

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
2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Należy zatrzymać maszynę wirtualną i zachować wszystkie jego ustawienia, ale nadal naliczane za jej lub można zatrzymać maszyny wirtualnej i jej cofnąć. Po cofnięciu przydziału maszyny wirtualnej, wszystkie zasoby skojarzone z nim są również zakończenia deallocated i rozliczeń dla niego.

1. Aby zatrzymać maszynę wirtualną bez dealokowanie go, należy dodać tej funkcji po zmiennych w pliku .py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Jeśli chcesz Cofnij Przydział maszyny wirtualnej, Zmień wywołanie power_off ten kod:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Uruchom maszynę Wirtualną

1. Można uruchomić maszyny wirtualnej, należy dodać tę funkcję po zmiennych w pliku .py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Zmień rozmiar maszyny Wirtualnej

Wiele aspektów wdrożenia należy uwzględnić przy podejmowaniu decyzji o rozmiarze dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [rozmiarów maszyn wirtualnych](sizes.md).

1. Aby zmienić rozmiar maszyny wirtualnej, należy dodać tę funkcję po zmiennych w pliku .py:

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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Dodaj dysk danych do maszyny Wirtualnej

Maszyny wirtualne mogą mieć jeden lub więcej [dysków z danymi](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) przechowywane jako wirtualne dyski twarde.

1. Aby dodać dysk z danymi do maszyny wirtualnej, należy dodać tej funkcji po zmiennych w pliku .py: 

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

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ naliczane są opłaty za zasoby używane na platformie Azure, zawsze jest dobrym rozwiązaniem, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyn wirtualnych i pomocnicze zasoby, musisz wykonać będzie usunąć grupę zasobów.

1. Aby usunąć grupę zasobów i wszystkie zasoby, Dodaj tę funkcję po zmiennych w pliku .py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Wywoływanie funkcji, które zostały wcześniej dodane, Dodaj ten kod w obszarze **Jeśli** instrukcji na końcu pliku .py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Zapisz *myPythonProject.py*.

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start** w programie Visual Studio.

2. Naciśnij klawisz **Enter** po zwróceniu stanu każdego zasobu. W informacjach dotyczących stanu powinna zostać wyświetlona **zakończyło się pomyślnie** stan inicjowania obsługi. Po utworzeniu maszyny wirtualnej, możesz mieć możliwość usunąć wszystkie zasoby, które należy utworzyć. Przed naciśnięciem przycisku **Enter** zacząć usuwanie zasobów może potrwać kilka minut, aby sprawdzić ich tworzenie w portalu Azure. Jeśli masz portalu Azure, Otwórz, może być konieczne odświeżyć bloku, aby zobaczyć nowe zasoby.  

    Zakończ powinno zająć około pięciu minut dla tej aplikacji konsoli uruchomić zupełnie od początku. Może upłynąć kilka minut po aplikacji zostało zakończone przed wszystkie zasoby i grupy zasobów są usuwane.


## <a name="next-steps"></a>Kolejne kroki

- Jeśli wystąpiły problemy dotyczące wdrożenia, następnym krokiem powinno być zapoznanie się z artykułem [Troubleshooting resource group deployments with Azure Portal](../../resource-manager-troubleshoot-deployments-portal.md) (Rozwiązywanie problemów z wdrożeniami grup zasobów za pomocą witryny Azure Portal).
- Dowiedz się więcej o [biblioteka języka Python platformy Azure](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

