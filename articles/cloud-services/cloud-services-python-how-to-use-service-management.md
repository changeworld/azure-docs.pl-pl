---
title: Użyj usługi Zarządzanie interfejsu API (Python) — Przewodnik po funkcji
description: Dowiedz się, jak programowo wykonywać typowe zadania zarządzania dla usługi w języku Python.
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
ms.openlocfilehash: b89f1aad46621d35728934ea068a5893ba674094
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29124803"
---
# <a name="use-service-management-from-python"></a>Za pomocą przystawki Zarządzanie usługi w języku Python
Ten przewodnik przedstawia, jak programowo wykonywać typowe zadania zarządzania dla usługi w języku Python. **ServiceManagementService** klasy w [zestaw Azure SDK for Python](https://github.com/Azure/azure-sdk-for-python) obsługuje dostęp programistyczny umożliwiający wiele usług związanych z zarządzaniem funkcji dostępnych w [Azure Portal][management-portal]. Ta funkcja służy do tworzenia, aktualizacji i usuwania usługi w chmurze, wdrożeń, dane usługi zarządzania i maszyn wirtualnych. Ta funkcja może być przydatne do tworzenia aplikacji, które muszą dostęp programistyczny umożliwiający zarządzanie usługą.

## <a name="WhatIs"></a>Co to jest usługa zarządzania?
Interfejs API zarządzania usługami Azure zapewnia dostęp programistyczny umożliwiający wiele funkcji zarządzania usługi dostępne za pośrednictwem [portalu Azure][management-portal]. Zestaw Azure SDK for Python służy do zarządzania usługi w chmurze i kont magazynu.

Aby użyć interfejs API zarządzania usługami, musisz [tworzenia konta platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"></a>Pojęcia
Zestaw Azure SDK for Python opakowuje [interfejs API zarządzania usługami][svc-mgmt-rest-api], która jest interfejs API REST. Wszystkie operacje interfejsu API są wykonywane za pośrednictwem protokołu SSL i wzajemnie uwierzytelnić przy użyciu certyfikatów X.509 v3. Usługa zarządzania jest możliwy z w ramach usługi działające na platformie Azure. Jest również dostępny bezpośrednio przez Internet z dowolnej aplikacji, która umożliwia wysyłanie żądania HTTPS oraz odbieranie odpowiedzi protokołu HTTPS.

## <a name="Installation"></a>Instalacji
Wszystkie funkcje opisane w tym artykule są dostępne w `azure-servicemanagement-legacy` pakiet, który można zainstalować przy użyciu narzędzia pip. Aby uzyskać więcej informacji na temat instalacji (na przykład jeśli jesteś nowym użytkownikiem Python), zobacz [zainstalować Python i zestawu Azure SDK](../python-how-to-install.md).

## <a name="Connect"></a>Połącz z zarządzania usługi
Aby połączyć się z punktem końcowym usługi zarządzania, należy identyfikator subskrypcji platformy Azure i prawidłowy certyfikat zarządzania. Możesz uzyskać identyfikator subskrypcji za pośrednictwem [portalu Azure][management-portal].

> [!NOTE]
> Teraz można używać certyfikatów utworzone za pomocą biblioteki OpenSSL uruchomionej w systemie Windows. Python 2.7.4 lub nowszy jest wymagany. Firma Microsoft zaleca użycie biblioteki OpenSSL zamiast PFX, ponieważ obsługa certyfikatów PFX jest mogą zostać usunięte w przyszłości.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certyfikaty zarządzania na systemem Windows lub Mac/Linux (OpenSSL)
Można użyć [OpenSSL](http://www.openssl.org/) można utworzyć certyfikat zarządzania. Należy utworzyć dwa certyfikaty, jeden dla serwera ( `.cer` plików) i jeden dla klienta ( `.pem` pliku). Aby utworzyć `.pem` plików, należy wykonać:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Aby utworzyć `.cer` certyfikatów, należy wykonać:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Aby uzyskać więcej informacji o certyfikatach Azure, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](cloud-services-certs-create.md). Pełny opis parametrów biblioteki OpenSSL, zobacz dokumentację w [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Po utworzeniu tych plików, Przekaż `.cer` plików na platformie Azure. W [portalu Azure][management-portal]na **ustawienia** wybierz opcję **przekazać**. Uwaga, w której zapisano `.pem` pliku.

Po uzyskaniu identyfikator subskrypcji należy utworzyć certyfikat i przekazać `.cer` plików na platformę Azure, nawiązać połączenia z punktem końcowym zarządzania platformy Azure. Połącz przez przekazanie identyfikator subskrypcji i ścieżkę do `.pem` pliku **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

W powyższym przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** klasa jest klasą podstawowy używany do zarządzania usługami Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certyfikaty zarządzania w systemie Windows (MakeCert)
Można utworzyć certyfikat zarządzania z podpisem własnym na tym komputerze przy użyciu `makecert.exe`. Otwórz **wiersz polecenia programu Visual Studio** jako **administratora** i należy użyć następującego polecenia, zastępując *AzureCertificate* o nazwie certyfikatu, którego chcesz użyć:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Polecenie tworzy `.cer` plików i instaluje je w **osobistych** magazynu certyfikatów. Aby uzyskać więcej informacji, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](cloud-services-certs-create.md).

Po utworzeniu certyfikatu należy przekazać `.cer` plików na platformie Azure. W [portalu Azure][management-portal]na **ustawienia** wybierz opcję **przekazać**.

Po uzyskaniu identyfikator subskrypcji należy utworzyć certyfikat i przekazać `.cer` plików na platformę Azure, nawiązać połączenia z punktem końcowym zarządzania platformy Azure. Połącz przez przekazanie identyfikator subskrypcji i lokalizacji certyfikatu w Twojej **osobistych** magazyn certyfikatów **ServiceManagementService** (ponownie, Zastąp *AzureCertificate* o nazwie certyfikatu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

W powyższym przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** klasa jest klasą podstawowy używany do zarządzania usługami Azure.

## <a name="ListAvailableLocations"></a>Listę dostępnych lokalizacji
Aby wyświetlić lokalizacje, które są dostępne dla usług hostingu, użyj **listy\_lokalizacje** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Podczas tworzenia usługi w chmurze lub Usługa magazynu, musisz podać prawidłową lokalizację. **Listy\_lokalizacje** metoda zawsze zwraca aktualną listę dostępnych lokalizacji. Opracowywania tego tekstu, są dostępne lokalizacje:

* Europa Zachodnia
* Europa Północna
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Środkowe stany USA
* Środkowo-północne stany USA
* Południowo-środkowe stany USA
* Zachodnie stany USA
* Wschodnie stany USA
* Japonia Wschodnia
* Japonia Zachodnia
* Brazylia Południowa
* Australia Wschodnia
* Australia Południowo-Wschodnia

## <a name="CreateCloudService"></a>Tworzenie usługi w chmurze
Podczas tworzenia aplikacji i uruchom go na platformie Azure, kod i konfiguracja łącznie są nazywane Azure [usługi w chmurze][cloud service]. (Były określane jako *usługi hostowanej* we wcześniejszych wersjach platformy Azure.) Można użyć **utworzyć\_hostowane\_usługi** metodę w celu utworzenia nowej usługi hostowanej. Utwórz usługę, podając nazwę usługi hostowanej (który musi być unikatowa na platformie Azure), etykiety (automatycznie zakodowane w formacie base64), opis oraz lokalizację.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Można wyświetlić listę wszystkich usług hostowanych dla Twojej subskrypcji z **listy\_hostowanej\_usług** metody.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Aby uzyskać informacje dotyczące konkretnej usługi hostowanej, przekaż nazwę hostowanej usługi do **uzyskać\_hostowane\_usługi\_właściwości** — metoda.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po utworzeniu usługi w chmurze, wdrażanie kodu do usługi z **utworzyć\_wdrożenia** metody.

## <a name="DeleteCloudService"></a>Usunąć usługi w chmurze
Można usunąć usługi w chmurze, przekazując nazwy usługi na **usunąć\_hostowane\_usługi** metody.

    sms.delete_hosted_service('myhostedservice')

Przed usunięciem usługi należy najpierw usunąć wszystkie wdrożenia dla usługi. Aby uzyskać więcej informacji, zobacz [usunąć wdrożenia](#DeleteDeployment).

## <a name="DeleteDeployment"></a>Usunąć wdrożenia
Aby usunąć wdrożenia, użyj **usunąć\_wdrożenia** metody. Poniższy przykład pokazuje, jak usunąć wdrożenie o nazwie `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"></a>Tworzenie usługi magazynu
A [usługi magazynu](../storage/common/storage-create-storage-account.md) zapewnia dostęp do usługi Azure [obiekty BLOB](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabel](../cosmos-db/table-storage-how-to-use-python.md), i [kolejek](../storage/queues/storage-python-how-to-use-queue-storage.md). Aby utworzyć usługę magazynu, należy nazwę usługi (od 3 do 24 małych liter i unikatowa na platformie Azure). Należy również opis, etykiety (maksymalnie 100 znaków, automatycznie zakodowane w formacie base64) i lokalizację. Poniższy przykład przedstawia sposób tworzenia usługi magazynu, określając lokalizację:

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

W powyższym przykładzie stan **utworzyć\_magazynu\_konta** operacji mogą być pobierane przez przekazanie wynik zwracany przez **utworzyć\_magazynu\_ konto** do **uzyskać\_operacji\_stan** metody. 

Można wyświetlić listę kont magazynu oraz ich właściwości z **listy\_magazynu\_kont** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"></a>Usuwanie usługi magazynu
Aby usunąć usługę magazynu, należy przekazać nazwę usługi magazynu do **usunąć\_magazynu\_konta** metody. Usunięcie usługi magazynu powoduje usunięcie wszystkich danych przechowywanych w usłudze (obiekty BLOB, tabel i kolejek).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"></a>Listę dostępnych systemów operacyjnych
Aby wyświetlić listę systemów operacyjnych, które są dostępne dla usług hostingu, użyj **listy\_operacyjnego\_systemów** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternatywnie można użyć **listy\_operacyjnego\_systemu\_rodzin** metodę, która grupuje rodziny systemów operacyjnych.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"></a>Utworzyć obraz systemu operacyjnego
Aby dodać obraz systemu operacyjnego do repozytorium obrazów, użyj **dodać\_os\_obrazu** metody.

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

Aby wyświetlić listę dostępnych obrazów systemu operacyjnego, należy użyć **listy\_os\_obrazów** metody. Zawiera wszystkie obrazy platformy i obrazów użytkowników.

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

## <a name="DeleteVMImage"></a>Usuń obraz systemu operacyjnego
Aby usunąć obrazu użytkownika, użyj **usunąć\_systemu operacyjnego\_obrazu** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"></a>Utwórz maszynę wirtualną
Aby utworzyć maszynę wirtualną, należy najpierw utworzyć [usługi w chmurze](#CreateCloudService). Następnie utwórz wdrożenie maszyny wirtualnej za pomocą **utworzyć\_wirtualnego\_maszyny\_wdrożenia** — metoda.

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

## <a name="DeleteVM"></a>Usuń maszynę wirtualną
Aby usunąć maszynę wirtualną, należy najpierw usunąć wdrożenia przy użyciu **usunąć\_wdrożenia** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Usługi w chmurze można następnie można usunąć za pomocą **usunąć\_hostowanej\_usługi** metody.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Utwórz maszynę wirtualną z obrazu przechwyconych maszyny wirtualnej
Aby przechwycić obraz maszyny Wirtualnej, należy najpierw wywołać **przechwytywania\_wirtualna\_obrazu** metody.

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

Aby pomyślnie przechwycony obraz, użyj **listy\_wirtualna\_obrazów** interfejsu API. Upewnij się, że obraz jest wyświetlana w wynikach.

    images = sms.list_vm_images()

Aby na koniec utwórz maszynę wirtualną za pomocą przechwyconego obrazu, należy użyć **utworzyć\_wirtualnego\_maszyny\_wdrożenia** jak wcześniej, ale tym razem przekazywanie vm_image_name zamiast tego.

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

Aby dowiedzieć się więcej o sposobie przechwytywania maszyny wirtualnej systemu Linux w klasycznym modelu wdrażania, zobacz [Przechwytywanie maszyny wirtualnej systemu Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Aby dowiedzieć się więcej o sposobie przechwytywania maszynę wirtualną w klasycznym modelu wdrażania, zobacz [Przechwytywanie maszyny wirtualnej systemu Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"></a>Następne kroki
Teraz, kiedy znasz już podstawy usługi zarządzania, należy uzyskać dostęp [kompletne API dokumentacji dla zestawu Azure SDK Python](http://azure-sdk-for-python.readthedocs.org/) i wykonać złożone zadania łatwe do zarządzania aplikacją Python.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](/develop/python/).

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
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/services/cloud-services/
