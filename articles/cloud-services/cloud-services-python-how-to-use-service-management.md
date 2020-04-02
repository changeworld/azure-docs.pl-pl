---
title: Użyj interfejsu API zarządzania usługami (Python) — przewodnik po funkcjach
description: Dowiedz się, jak programowo wykonywać typowe zadania zarządzania usługami z języka Python.
services: cloud-services
documentationcenter: python
author: lmazuel
manager: wpickett
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: lmazuel
ms.openlocfilehash: ae44ce77ce480cc1bc56ead5547e003d7d4bb39c
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80547579"
---
# <a name="use-service-management-from-python"></a>Korzystanie z zarządzania usługami z języka Python
W tym przewodniku pokazano, jak programowo wykonywać typowe zadania zarządzania usługami z języka Python. **ServiceManagementService** Klasy w [azure SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python) obsługuje dostęp programowy do większości funkcji związanych z zarządzaniem usługami, który jest dostępny w [witrynie Azure portal][management-portal]. Za pomocą tej funkcji można tworzyć, aktualizować i usuwać usługi w chmurze, wdrożenia, usługi zarządzania danymi i maszyny wirtualne. Ta funkcja może być przydatna w tworzeniu aplikacji, które wymagają programowego dostępu do zarządzania usługami.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Co to jest zarządzanie usługami?
Interfejs API zarządzania usługami azure zapewnia programowy dostęp do dużej części funkcji zarządzania usługami dostępnych za pośrednictwem [witryny Azure portal.][management-portal] Za pomocą narzędzia Azure SDK dla języka Python można zarządzać usługami w chmurze i kontami magazynu.

Aby korzystać z interfejsu API zarządzania usługami, należy [utworzyć konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Pojęcia
Zestaw Azure SDK dla języka Python zawija [interfejs API zarządzania usługami,][svc-mgmt-rest-api]który jest interfejsem API REST. Wszystkie operacje interfejsu API są wykonywane za pośrednictwem protokołu TLS i wzajemnie uwierzytelnione przy użyciu certyfikatów X.509 v3. Usługa zarządzania można uzyskać z poziomu usługi uruchomionej na platformie Azure. Można również uzyskać dostęp bezpośrednio przez Internet z dowolnej aplikacji, która może wysłać żądanie HTTPS i otrzymać odpowiedź HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalacja
Wszystkie funkcje opisane w tym artykule są dostępne w `azure-servicemanagement-legacy` pakiecie, który można zainstalować za pomocą pip. Aby uzyskać więcej informacji na temat instalacji (na przykład, jeśli jesteś nowy w Pythonie), zobacz [Instalowanie języka Python i pakietu Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Łączenie się z zarządzaniem usługami
Aby połączyć się z punktem końcowym zarządzania usługami, potrzebujesz identyfikatora subskrypcji platformy Azure i prawidłowego certyfikatu zarządzania. Identyfikator subskrypcji można uzyskać za pośrednictwem [witryny Azure portal][management-portal].

> [!NOTE]
> Teraz można używać certyfikatów utworzonych za pomocą OpenSSL podczas uruchamiania w systemie Windows. Python 2.7.4 lub nowsze jest wymagane. Zaleca się używanie openssl zamiast .pfx, ponieważ obsługa certyfikatów .pfx prawdopodobnie zostanie usunięta w przyszłości.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certyfikaty zarządzania w systemie Windows/Mac/Linux (OpenSSL)
Do utworzenia certyfikatu zarządzania można użyć [openssl.](https://www.openssl.org/) Należy utworzyć dwa certyfikaty, jeden dla `.cer` serwera (plik) i `.pem` jeden dla klienta (plik). Aby utworzyć `.pem` plik, wykonaj:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Aby utworzyć `.cer` certyfikat, wykonaj:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Aby uzyskać więcej informacji na temat certyfikatów platformy Azure, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](cloud-services-certs-create.md). Pełny opis parametrów OpenSSL można znaleźć [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html)w dokumentacji w punkcie .

Po utworzeniu tych plików `.cer` przekaż plik na platformę Azure. W [witrynie Azure portal][management-portal]na karcie **Ustawienia** wybierz pozycję **Przekaż**. Zwróć uwagę, `.pem` gdzie plik został zapisany.

Po uzyskaniu identyfikatora subskrypcji utwórz certyfikat i `.cer` przekaż plik na platformę Azure, połącz się z punktem końcowym zarządzania platformą Azure. Połącz się, przekazując identyfikator subskrypcji `.pem` i ścieżkę do pliku do **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

W poprzednim przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** Klasa jest podstawową klasą używaną do zarządzania usługami platformy Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certyfikaty zarządzania w systemie Windows (MakeCert)
Certyfikat zarządzania z podpisem własnym można `makecert.exe`utworzyć na komputerze za pomocą programu . Otwórz **wiersz polecenia programu Visual Studio** jako **administrator** i użyj następującego polecenia, zastępując *narzędzie AzureCertificate* nazwą certyfikatu, której chcesz użyć:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Polecenie tworzy `.cer` plik i instaluje go w magazynie certyfikatów **osobistych.** Aby uzyskać więcej informacji, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](cloud-services-certs-create.md).

Po utworzeniu certyfikatu `.cer` przekaż plik na platformę Azure. W [witrynie Azure portal][management-portal]na karcie **Ustawienia** wybierz pozycję **Przekaż**.

Po uzyskaniu identyfikatora subskrypcji utwórz certyfikat i `.cer` przekaż plik na platformę Azure, połącz się z punktem końcowym zarządzania platformą Azure. Połącz się, przekazując identyfikator subskrypcji i lokalizację certyfikatu w magazynie certyfikatów **osobistych** do **servicemanagementservice** (ponownie zastąp *narzędzie AzureCertificate* nazwą certyfikatu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

W poprzednim przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** Klasa jest podstawową klasą używaną do zarządzania usługami platformy Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Lista dostępnych lokalizacji
Aby wyświetlić listę lokalizacji dostępnych dla usług hostingowych, użyj metody **lokalizacji listy.\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Podczas tworzenia usługi w chmurze lub usługi magazynu, należy podać prawidłową lokalizację. Metoda **\_lokalizacji listy** zawsze zwraca aktualną listę aktualnie dostępnych lokalizacji. W chwili pisania tego tekstu dostępne lokalizacje to:

* Europa Zachodnia
* Europa Północna
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Środkowe stany USA
* Północno-środkowe stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA
* Wschodnie stany USA
* Japonia Wschodnia
* Japonia Zachodnia
* Brazylia Południowa
* Australia Wschodnia
* Australia Południowo-Wschodnia

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Tworzenie usługi w chmurze
Podczas tworzenia aplikacji i uruchamiania jej na platformie Azure kod i konfiguracja razem są nazywane [usługą w chmurze][cloud service]platformy Azure. (Był znany jako *usługa hostowana* we wcześniejszych wersjach platformy Azure.) Można użyć metody **\_tworzenia\_usługi hosta,** aby utworzyć nową usługę hosta. Utwórz usługę, podając nazwę usługi hostowanej (która musi być unikatowa na platformie Azure), etykietę (automatycznie zakodowaną do base64), opis i lokalizację.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Możesz wyświetlić listę wszystkich usług hostowanych dla subskrypcji za pomocą metody **usług\_hostowanych\_listy.**

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Aby uzyskać informacje o określonej usługi hostowane, przekazać nazwę usługi hostowane do **get\_hosted\_metody właściwości usługi.\_**

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po utworzeniu usługi w chmurze, wdrożyć kod do usługi z metody **wdrażania tworzenia.\_**

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Usuwanie usługi w chmurze
Usługę w chmurze można usunąć, przekazując nazwę usługi do metody **usuwania\_usługi hostowanego.\_**

    sms.delete_hosted_service('myhostedservice')

Przed usunięciem usługi należy najpierw usunąć wszystkie wdrożenia usługi. Aby uzyskać więcej informacji, zobacz [Usuwanie wdrożenia](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Usuwanie wdrożenia
Aby usunąć wdrożenie, użyj metody **wdrażania\_usuwania.** W poniższym przykładzie pokazano, `v1`jak usunąć wdrożenie o nazwie:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Tworzenie usługi magazynowania
[Usługa magazynu](../storage/common/storage-create-storage-account.md) zapewnia dostęp do [obiektów blob](../storage/blobs/storage-python-how-to-use-blob-storage.md)platformy Azure, [tabel](../cosmos-db/table-storage-how-to-use-python.md)i [kolejek.](../storage/queues/storage-python-how-to-use-queue-storage.md) Aby utworzyć usługę magazynu, potrzebujesz nazwy usługi (od 3 do 24 małych liter i unikatowych na platformie Azure). Potrzebny jest również opis, etykieta (do 100 znaków, automatycznie zakodowana w base64) i lokalizacja. W poniższym przykładzie pokazano, jak utworzyć usługę magazynu, określając lokalizację:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

W poprzednim przykładzie stan operacji **\_tworzenia\_konta magazynu** można pobrać, przekazując wynik zwrócony przez **\_utworzenie konta magazynu\_** do metody stanu operacji **\_\_get.** 

Za pomocą metody **kont\_magazynu\_** listy można wyświetlić listę kont magazynu i ich właściwości.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Usuwanie usługi magazynowania
Aby usunąć usługę magazynu, należy przekazać nazwę usługi magazynu do metody **konta magazynu\_usuwania.\_** Usunięcie usługi magazynu powoduje usunięcie wszystkich danych przechowywanych w usłudze (obiektów blob, tabel i kolejek).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Lista dostępnych systemów operacyjnych
Aby wyświetlić listę systemów operacyjnych dostępnych dla usług hostingowych, użyj metody **systemów\_operacyjnych\_listy.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternatywnie można użyć metody **rodzin\_systemów\_operacyjnych\_listy,** która grupuje systemy operacyjne według rodziny.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Tworzenie obrazu systemu operacyjnego
Aby dodać obraz systemu operacyjnego do repozytorium obrazów, użyj metody **dodaj\_obraz systemu\_operacyjnego.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Aby wyświetlić listę obrazów systemu operacyjnego, które są dostępne, użyj metody **\_\_obrazów systemu operacyjnego listy.** Zawiera wszystkie obrazy platformy i obrazy użytkowników.

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Usuwanie obrazu systemu operacyjnego
Aby usunąć obraz użytkownika, użyj metody **usuwania\_\_obrazu systemu operacyjnego.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Tworzenie maszyny wirtualnej
Aby utworzyć maszynę wirtualną, należy najpierw utworzyć usługę w [chmurze](#CreateCloudService). Następnie utwórz wdrożenie maszyny wirtualnej przy użyciu metody **wdrażania maszyny\_wirtualnej.\_\_**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Usuwanie maszyny wirtualnej
Aby usunąć maszynę wirtualną, należy najpierw usunąć wdrożenie przy użyciu metody **wdrażania\_usuwania.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Usługę w chmurze można następnie usunąć przy użyciu metody **usuwania\_usługi hosta.\_**

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Tworzenie maszyny wirtualnej na podstawie przechwyconego obrazu maszyny wirtualnej
Aby przechwycić obraz maszyny Wirtualnej, należy najpierw wywołać metodę **\_\_obrazu maszyny wirtualnej przechwytywania.**

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Aby upewnić się, że obraz został pomyślnie przechwycony, użyj interfejsu API **\_\_obrazów maszyn wirtualnych listy.** Upewnij się, że obraz jest wyświetlany w wynikach.

    images = sms.list_vm_images()

Aby ostatecznie utworzyć maszynę wirtualną przy użyciu przechwyconego obrazu, należy użyć metody **wdrażania maszyny\_wirtualnej,\_\_** jak poprzednio, ale tym razem przekaż w vm_image_name zamiast tego.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Aby dowiedzieć się więcej o tym, jak przechwycić maszynę wirtualną systemu Linux w klasycznym modelu wdrażania, zobacz [Przechwytywanie maszyny wirtualnej systemu Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Aby dowiedzieć się więcej o przechwytywaniu maszyny wirtualnej systemu Windows w klasycznym modelu wdrażania, zobacz [Przechwytywanie maszyny wirtualnej systemu Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>Następne kroki
Teraz, gdy poznasz podstawy zarządzania usługami, możesz uzyskać dostęp do [dokumentacji odwołania do pełnego interfejsu API dla narzędzia Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) i łatwo wykonywać złożone zadania, aby zarządzać aplikacją języka Python.

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów Python](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
