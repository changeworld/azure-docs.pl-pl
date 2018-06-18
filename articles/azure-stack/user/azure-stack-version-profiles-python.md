---
title: Przy użyciu profilów wersji interfejsu API języka Python w stosie Azure | Dokumentacja firmy Microsoft
description: Informacje o używaniu języka Python w stosie Azure profile w wersji interfejsu API.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: d17ba9ed4548a986d6846d934aee197609ec80ca
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34806840"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Profile w wersji interfejsu API za pomocą języka Python w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

## <a name="python-and-api-version-profiles"></a>Profile w wersji języka Python i interfejsu API

Zestaw SDK Python obsługuje profile w wersji interfejsu API pod kątem platformy inną chmurę, na przykład stosu Azure i globalnych Azure. Można użyć interfejsu API profile tworzenia rozwiązań chmur hybrydowych. Zestaw SDK Python obsługuje następujące profile interfejsu API:

1. **latest**  
    Profil dotyczy najnowsze wersje interfejsu API dla wszystkich dostawców usługi na platformie Azure.
2.  **2017-03-09-profilu**  
    **2017-03-09-profilu**  
    Profil jest przeznaczony dla wersji interfejsu API dostawców zasobów obsługiwane przez stos Azure.

    Aby uzyskać więcej informacji o profilach interfejsu API i stosu Azure, zobacz [profile wersji Zarządzanie interfejsu API w stosie Azure](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

1.  Zainstaluj usługę Git z [oficjalna witryna](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Aby uzyskać instrukcje dotyczące instalowania zestawu SDK Python, zobacz [Azure dla deweloperów języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do użycia później. Aby uzyskać instrukcje utworzyć subskrypcję, zobacz [tworzyć subskrypcje ofert w stosie Azure](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Tworzenie nazwy głównej usługi, a następnie zapisz jego identyfikator i klucz tajny. Aby uzyskać instrukcje tworzenia nazwy głównej usługi dla stosu Azure, zobacz [dostęp aplikacji do stosu Azure](../azure-stack-create-service-principals.md). 
5.  Upewnij się, że nazwy głównej usługi ma rolę współautora/właściciela na subskrypcję. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do nazwy głównej usługi, zobacz [dostęp aplikacji do stosu Azure](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć zestawu Azure SDK Python stosu Azure, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych. 

| Wartość | Zmienne środowiskowe | Opis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Identyfikator dzierżawy | AZURE_TENANT_ID | Wartość stosu Azure [Identyfikatorem dzierżawy](../azure-stack-identity-overview.md). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisane podczas nazwy głównej usługi został utworzony w poprzedniej sekcji tego dokumentu. |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](../azure-stack-plan-offer-quota-overview.md#subscriptions) jest dostęp do oferty Azure stosu. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji głównej usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy Menedżera zasobów | ARM_ENDPOINT | Zobacz [punktu końcowego Menedżera zasobów Azure stosu](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Python próbki dla stosu Azure 

Poniższe przykłady kodu służy do wykonywania typowych zadań zarządzania dla maszyn wirtualnych w stosie Azure.

Przykłady kodu Pokaż na:

- Tworzenie maszyn wirtualnych:
    - Utwórz maszynę wirtualną z systemem Linux
    - Tworzenie maszyny wirtualnej systemu Windows
- Aktualizacja maszyny wirtualnej:
    - Rozwiń węzeł dysku
    - Tag maszyny wirtualnej
    - Dołącz dysków z danymi
    - Odłączanie dysków danych
- Działanie maszyny wirtualnej:
    - Uruchom maszynę wirtualną
    - Zatrzymaj maszynę wirtualną
    - Uruchom ponownie maszynę wirtualną
- Lista maszyn wirtualnych
- Usuń maszynę wirtualną

Aby przejrzeć kod do wykonania tych operacji, zapoznaj się **run_example()** funkcji w skrypcie Python **Hybrid/unmanaged-disks/example.py** w repozytorium GitHub [ wirtualny maszyny python — zarządzanie](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Każdej operacji jest jednoznacznie oznaczony komentarz i funkcji drukowania.
Przykłady niekoniecznie w kolejności przedstawionej w powyższej listy.


## <a name="run-the-python-sample"></a>Uruchamianie przykładowych Python

1.  Jeśli nie masz jeszcze, [zainstalować Python](https://www.python.org/downloads/).

    W tym przykładzie (i zestawu SDK) jest zgodna z Python 2.7 3.4, 3.5 i 3,6.

2.  Ogólne zalecenia dotyczące programowania Python jest używane środowisko wirtualne. 
    Aby uzyskać więcej informacji zobacz https://docs.python.org/3/tutorial/venv.html
    
    Zainstaluj i zainicjuj środowisko wirtualne z modułem "venv" Python 3 (należy zainstalować [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Klonowanie repozytorium.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Zainstaluj zależności przy użyciu narzędzia pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Utwórz [nazwy głównej usługi](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) do pracy z stosu Azure. Upewnij się, że ma Twojej nazwy głównej usługi [rolę współautora/właściciela](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) w ramach subskrypcji.

6.  Ustaw następujące zmienne i wyeksportować te zmienne środowiskowe w bieżącym powłoki. 

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  In order to run this sample, Ubuntu 16.04-LTS and WindowsServer 2012-R2-Datacenter images must be present in Azure Stack market place. These can be either [downloaded from Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-download-azure-marketplace-item) or [added to Platform Image Repository](https://docs.microsoft.com/azure/azure-stack/azure-stack-add-vm-image).

8. Run the sample.

    ```
    python unmanaged-disks\example.py
    ```

## Notes

You may be tempted to try to retrieve a VM's OS disk by using
`virtual_machine.storage_profile.os_disk`.
In some cases, this may do what you want,
but be aware that it gives you an `OSDisk` object.
In order to update the OS Disk's size, as `example.py` does,
you need not an `OSDisk` object but a `Disk` object.
`example.py` gets the `Disk` object with the following:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## Next steps

- [Azure Python Development Center](https://azure.microsoft.com/develop/python/)
- [Azure Virtual Machines documentation](https://azure.microsoft.com/services/virtual-machines/)
- [Learning Path for Virtual Machines](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- If you don't have a Microsoft Azure subscription, you can get a FREE trial account [here](http://go.microsoft.com/fwlink/?LinkId=330212).