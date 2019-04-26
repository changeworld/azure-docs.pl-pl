---
title: Użyj interfejsu API zarządzania usługami (Python) — Przewodnik po funkcjach usługi
description: Dowiedz się, jak programowo wykonywać typowe zadania zarządzania usługą za pomocą języka Python.
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
ms.openlocfilehash: 573c6d3ded8fea58e0c9ba1afa7da2d8dd0fce91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525524"
---
# <a name="use-service-management-from-python"></a>Użyj usługi zarządzania za pomocą języka Python
Ten przewodnik pokazuje, jak programowo wykonywać typowe zadania zarządzania usługą za pomocą języka Python. **ServiceManagementService** klasy w [zestawu Azure SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python) obsługuje dostęp programistyczny do większości funkcji związanych z zarządzaniem usługi, które są dostępne w [platformy Azure Portal][management-portal]. Ta funkcja umożliwia tworzenie, aktualizowanie i usuwanie usług w chmurze, wdrożeń, usług zarządzania danymi i maszyn wirtualnych. Ta funkcja może być przydatne w przypadku tworzenia aplikacji, które wymagają dostęp programistyczny do zarządzania usługami.

## <a name="WhatIs"> </a>Co to jest zarządzanie usługą?
Interfejs API zarządzania usługami platformy Azure zapewnia dostęp programistyczny do większości funkcjonalności zarządzania usługami dostępnej za pośrednictwem [witryny Azure portal][management-portal]. Zestaw Azure SDK dla języka Python można użyć do zarządzania usługami w chmurze i kont magazynu.

Aby korzystać z interfejsu API zarządzania usługami, musisz [utworzyć konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Pojęcia
Zestaw Azure SDK dla języka Python opakowuje [interfejsu API zarządzania usługami][svc-mgmt-rest-api], czyli interfejs API REST. Wszystkie operacje interfejsu API są realizowane poprzez protokół SSL i wzajemnie uwierzytelniana za pomocą certyfikatów X.509 v3. Składnik Usługa zarządzania jest możliwy z w ramach usługi działające na platformie Azure. On również możliwy bezpośrednio przez Internet z dowolnej aplikacji, który może wysyłać żądanie protokołu HTTPS i odbierać odpowiedź protokołu HTTPS.

## <a name="Installation"> </a>Instalacja
Wszystkie funkcje opisane w tym artykule są dostępne w `azure-servicemanagement-legacy` pakiet, który można zainstalować przy użyciu narzędzia pip. Aby uzyskać więcej informacji na temat instalacji (na przykład, jeśli jesteś nowym użytkownikiem Python), zobacz [zainstalowania języka Python i zestawu Azure SDK](../python-how-to-install.md).

## <a name="Connect"> </a>Łączenie do zarządzania usługami
Aby połączyć się z punktem końcowym zarządzania usługi, należy swój identyfikator subskrypcji platformy Azure i prawidłowy certyfikat zarządzania. Możesz uzyskać identyfikator subskrypcji za pośrednictwem [witryny Azure portal][management-portal].

> [!NOTE]
> Teraz można używać certyfikaty utworzone za pomocą biblioteki OpenSSL, gdy w systemie Windows. Python 2.7.4 lub nowszy jest wymagany. Zalecamy użycie biblioteki OpenSSL a nie .pfx, ponieważ obsługa certyfikatów PFX jest prawdopodobnie w przyszłości usunięte.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Zarządzanie certyfikatami na Windows/Mac/Linux (OpenSSL)
Możesz użyć [OpenSSL](https://www.openssl.org/) do utworzenia certyfikatu zarządzania. Należy utworzyć dwa certyfikaty, jeden dla serwera ( `.cer` pliku) i jeden dla klienta ( `.pem` pliku). Aby utworzyć `.pem` plików, należy wykonać:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Aby utworzyć `.cer` certyfikatów, należy wykonać:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Aby uzyskać więcej informacji o certyfikatach platformy Azure, zobacz [Omówienie certyfikatów usług Azure Cloud Services](cloud-services-certs-create.md). Aby uzyskać pełny opis parametrów biblioteki OpenSSL, zobacz dokumentację w [ https://www.openssl.org/docs/apps/openssl.html ](https://www.openssl.org/docs/apps/openssl.html).

Po utworzeniu tych plików, należy przekazać `.cer` plików na platformę Azure. W [witryny Azure portal][management-portal]na **ustawienia** zaznacz **przekazywanie**. Uwaga, w której zapisano `.pem` pliku.

Po uzyskaniu identyfikator subskrypcji, należy utworzyć certyfikat i przekaż `.cer` plików na platformę Azure, nawiązać połączenie z punktem końcowym zarządzania platformy Azure. Łączenie, przekazując Identyfikatora subskrypcji oraz ścieżkę do `.pem` plik **ServiceManagementService**.

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

W powyższym przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** klasa jest klasą podstawowy używany do zarządzania usługami platformy Azure.

### <a name="management-certificates-on-windows-makecert"></a>Zarządzanie certyfikatami na Windows (narzędzie MakeCert)
Można utworzyć certyfikat zarządzania z podpisem własnym na komputerze przy użyciu `makecert.exe`. Otwórz **wiersz polecenia programu Visual Studio** jako **administratora** i należy użyć następującego polecenia, zastępując *AzureCertificate* o nazwie certyfikatu, którego chcesz użyć:

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

Polecenie tworzy `.cer` plików i instaluje je w **osobistych** magazynu certyfikatów. Aby uzyskać więcej informacji, zobacz [Omówienie certyfikatów usług Azure Cloud Services](cloud-services-certs-create.md).

Po utworzeniu certyfikatu należy przekazać `.cer` plików na platformę Azure. W [witryny Azure portal][management-portal]na **ustawienia** zaznacz **przekazywanie**.

Po uzyskaniu identyfikator subskrypcji, należy utworzyć certyfikat i przekaż `.cer` plików na platformę Azure, nawiązać połączenie z punktem końcowym zarządzania platformy Azure. Łączenie, przekazując identyfikator subskrypcji i lokalizacji certyfikatu w Twojej **osobistych** magazyn certyfikatów **ServiceManagementService** (symbolu *AzureCertificate* o nazwie certyfikatu).

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

W powyższym przykładzie `sms` jest **ServiceManagementService** obiektu. **ServiceManagementService** klasa jest klasą podstawowy używany do zarządzania usługami platformy Azure.

## <a name="ListAvailableLocations"> </a>Listę dostępnych lokalizacji
Aby wyświetlić listę lokalizacji, które są dostępne dla usług hostingu, użyj **listy\_lokalizacje** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Podczas tworzenia usługi w chmurze lub Usługa magazynu, musisz podać prawidłową lokalizację. **Listy\_lokalizacje** metoda zawsze zwraca aktualną listę dostępnych lokalizacji. W trakcie tworzenia tej dokumentacji dostępne lokalizacje to:

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

## <a name="CreateCloudService"> </a>Utwórz usługę w chmurze
Podczas tworzenia aplikacji i uruchom go na platformie Azure, kod i konfiguracja łącznie są nazywane platformy Azure [usługi w chmurze][cloud service]. (Była nazywana *usługi hostowanej* we wcześniejszych wersjach platformy Azure.) Możesz użyć **tworzenie\_hostowane\_usługi** metodę w celu utworzenia nowej usługi hostowanej. Utwórz usługę, podając nazwę usługi hostowanej (który musi być unikatowa na platformie Azure), etykietę (automatycznie kodowany w formacie base64), opis i lokalizacji.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Możesz wyświetlić listę wszystkich usług hostowanych dla subskrypcji przy użyciu **listy\_hostowane\_usług** metody.

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Aby uzyskać informacje dotyczące określonej usługi hostowanej, należy przekazać nazwę usługi hostowanej na **uzyskać\_hostowane\_usługi\_właściwości** metody.

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Po utworzeniu usługi w chmurze, należy wdrożyć kod do usługi z **tworzenie\_wdrożenia** metody.

## <a name="DeleteCloudService"> </a>Usuwanie usługi w chmurze
Można usunąć usługi w chmurze przez przekazanie nazwy usługi na **Usuń\_hostowane\_usługi** metody.

    sms.delete_hosted_service('myhostedservice')

Aby można było usunąć usługę, należy najpierw usunąć wszystkich wdrożeń usługi. Aby uzyskać więcej informacji, zobacz [usunąć wdrożenie](#DeleteDeployment).

## <a name="DeleteDeployment"> </a>Usuwanie wdrożenia
Aby usunąć wdrożenie, użyj **Usuń\_wdrożenia** metody. Poniższy przykład pokazuje, jak usunąć wdrożenie o nazwie `v1`:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Tworzenie usługi storage
A [usługi storage](../storage/common/storage-create-storage-account.md) zapewnia dostęp do usługi Azure [obiektów blob](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabel](../cosmos-db/table-storage-how-to-use-python.md), i [kolejek](../storage/queues/storage-python-how-to-use-queue-storage.md). Aby utworzyć usługę storage, należy nazwę usługi (od 3 do 24 małych liter i unikatowy w obrębie platformy Azure). Należy również opis etykiety (maksymalnie 100 znaków, automatycznie kodowane w formacie base64) i lokalizacji. Poniższy przykład pokazuje, jak utworzyć usługę magazynu, określając lokalizację:

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

W powyższym przykładzie stan **tworzenie\_magazynu\_konta** operacji mogą być pobierane przez przekazanie wynik zwracany przez **tworzenie\_magazynu\_ konto** do **uzyskać\_operacji\_stan** metody. 

Możesz wyświetlić listę swoich kont magazynu i ich właściwości, za pomocą **listy\_magazynu\_kont** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Usuwanie usługi storage
Aby usunąć usługę storage, przekazać nazwę usługi magazynu do **Usuń\_magazynu\_konta** metody. Usunięcie usługi magazynu powoduje usunięcie wszystkich danych przechowywanych w usłudze (obiekty BLOB, tabele i kolejki).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Listę dostępnych systemów operacyjnych
Aby wyświetlić listę systemów operacyjnych, które są dostępne dla usług hostingu, należy użyć **listy\_operacyjnego\_systemów** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Alternatywnie, można użyć **listy\_operacyjnego\_systemu\_rodzin** metody, która grupuje systemów operacyjnych według rodziny.

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Tworzenie obrazu systemu operacyjnego
Aby dodać obraz systemu operacyjnego do repozytorium obrazów, użyj **Dodaj\_os\_obraz** metody.

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

Aby wyświetlić listę obrazów systemu operacyjnego, które są dostępne, należy użyć **listy\_os\_obrazów** metody. Zawiera wszystkie obrazy platformy i obrazów użytkowników.

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

## <a name="DeleteVMImage"> </a>Usuwanie obrazu systemu operacyjnego
Aby usunąć obrazu użytkownika, użyj **Usuń\_os\_obrazu** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Utwórz maszynę wirtualną
Aby utworzyć maszynę wirtualną, należy najpierw utworzyć [usługi w chmurze](#CreateCloudService). Następnie utwórz wdrożenie maszyny wirtualnej przy użyciu **tworzenie\_wirtualnego\_maszyny\_wdrożenia** metody.

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

## <a name="DeleteVM"> </a>Usuń maszynę wirtualną
Aby usunąć maszynę wirtualną, należy najpierw usunąć wdrożenie za pomocą **Usuń\_wdrożenia** metody.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

Następnie można usunąć usługi w chmurze przy użyciu **Usuń\_hostowane\_usługi** metody.

    sms.delete_hosted_service(service_name='myvm')

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Utwórz maszynę wirtualną z obrazu maszyny wirtualnej przechwycone
Aby przechwycić obraz maszyny Wirtualnej, należy najpierw wywołać **przechwytywania\_maszyny wirtualnej\_obraz** metody.

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

Aby pomyślnie przechwycono obrazu, użyj **listy\_maszyny wirtualnej\_obrazów** interfejsu API. Upewnij się, że obraz jest wyświetlany w wynikach.

    images = sms.list_vm_images()

Aby na koniec utwórz maszynę wirtualną przy użyciu przechwyconego obrazu, należy użyć **tworzenie\_wirtualnego\_maszyny\_wdrożenia** metodzie jak wcześniej, ale tym razem przekazać vm_image_name zamiast tego.

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

Aby dowiedzieć się więcej o tym, jak przechwycić maszynę wirtualną systemu Linux w klasycznym modelu wdrażania, zobacz [przechwycić maszynę wirtualną z systemem Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Aby dowiedzieć się więcej o tym, jak przechwycić maszynę wirtualną Windows, w klasycznym modelu wdrażania, zobacz [przechwycić maszynę wirtualną Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="What's Next"> </a>Następne kroki
Teraz, kiedy znasz już podstawy usługi zarządzania, możesz uzyskać dostęp [dokumentację API kompletny zestaw Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) i wykonywać zadania złożone, łatwe do zarządzania aplikacją języka Python.

Więcej informacji możesz znaleźć w [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/).

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
