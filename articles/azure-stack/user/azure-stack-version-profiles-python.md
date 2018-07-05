---
title: Za pomocą profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack.
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
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "34806840"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Profile wersji języka Python i interfejsu API

Zestaw SDK języka Python obsługuje profilami wersji interfejsu API pod kątem platform inną chmurę, takich jak Azure Stack i globalnej platformy Azure. Można używać profilów interfejsu API w tworzeniu rozwiązań dla chmury hybrydowej. Zestaw SDK języka Python obsługuje następujące profile interfejsu API:

1. **latest**  
    Profil jest przeznaczony dla najnowszej wersji interfejsu API dla wszystkich dostawców usług na platformie Azure.
2.  **2017-03-09-profile**  
    **2017-03-09-profile**  
    Profil jest przeznaczony dla wersji interfejsu API dostawcy zasobów, obsługiwane przez usługę Azure Stack.

    Aby uzyskać więcej informacji o profilach interfejsu API i usługi Azure Stack, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md).

## <a name="install-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

1.  Zainstaluj oprogramowanie Git z [oficjalna witryna](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2.  Aby uzyskać instrukcje dotyczące instalowania zestawu SDK języka Python, zobacz [platformy Azure dla deweloperów języka Python](https://docs.microsoft.com/python/azure/python-sdk-azure-install?view=azure-python).
3.  Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji ma być używany w dalszej części. Aby uzyskać instrukcje utworzyć subskrypcję, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack](../azure-stack-subscribe-plan-provision-vm.md). 
4.  Tworzenie jednostki usługi, a następnie zapisz jego identyfikator i klucz tajny. Aby uzyskać instrukcje, aby utworzyć jednostkę usługi dla usługi Azure Stack, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md). 
5.  Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać zestawu Azure SDK dla języka Python z usługą Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych. 

| Wartość | Zmienne środowiskowe | Opis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Identyfikator dzierżawy | AZURE_TENANT_ID | Zalety usługi Azure Stack [identyfikator dzierżawy](../azure-stack-identity-overview.md). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o nazwę główną usługi został utworzony w poprzedniej sekcji niniejszego dokumentu. |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](../azure-stack-plan-offer-quota-overview.md#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji nazwy głównej usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT | Zobacz [punktu końcowego Menedżera zasobów usługi Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |


## <a name="python-samples-for-azure-stack"></a>Przykłady w języku Python dla usługi Azure Stack 

Poniższe przykłady kodu można użyć do wykonywania typowych zadań zarządzania dla maszyn wirtualnych w usługi Azure Stack.

Przykłady kodu pokazują, aby:

- Tworzenie maszyn wirtualnych:
    - Utwórz maszynę wirtualną z systemem Linux
    - Tworzenie maszyny wirtualnej systemu Windows
- Zaktualizuj maszynę wirtualną:
    - Rozwiń dysk
    - Tagowanie maszyny wirtualnej
    - Dołączanie dysków danych
    - Odłączanie dysków danych
- Działanie maszyny wirtualnej:
    - Uruchom maszynę wirtualną
    - Zatrzymaj maszynę wirtualną
    - Uruchom ponownie maszynę wirtualną
- Lista maszyn wirtualnych
- Usuń maszynę wirtualną

Aby przejrzeć kod do wykonywania tych operacji, zapoznaj się z **run_example()** funkcji w skrypcie języka Python **Hybrid/unmanaged-disks/example.py** w repozytorium GitHub [ wirtualne maszyn python-w zarządzaniu](https://github.com/viananth/virtual-machines-python-manage/tree/8643ed4bec62aae6fdb81518f68d835452872f88).

Każda operacja jest wyraźnie oznaczony komentarz i funkcję drukowania.
Przykłady niekoniecznie w kolejności przedstawionej w powyższej listy.


## <a name="run-the-python-sample"></a>Uruchom aplikację przykładową języka Python

1.  Jeśli nie masz jeszcze, [zainstaluj język Python](https://www.python.org/downloads/).

    W tym przykładzie (i zestawu SDK) jest zgodna z języka Python 2.7, 3.4, 3.5 i 3.6.

2.  Ogólne zalecenia dotyczące programowania w języku Python jest środowisko wirtualne. 
    Aby uzyskać więcej informacji zobacz https://docs.python.org/3/tutorial/venv.html
    
    Zainstaluj i zainicjuj środowisko wirtualne z modułem "venv" na środowisku Python 3 (należy zainstalować [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

    ````bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ````

3.  Sklonuj repozytorium.

    ````bash
    git clone https://github.com/Azure-Samples/virtual-machines-python-manage.git
    ````

4.  Instalowanie zależności przy użyciu narzędzia pip.

    ````bash
    cd virtual-machines-python-manage\Hybrid
    pip install -r requirements.txt
    ````

5.  Tworzenie [nazwy głównej usługi](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals) do pracy z usługą Azure Stack. Upewnij się, Twoja jednostka usługi ma [roli właściciel/Współautor](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) w ramach Twojej subskrypcji.

6.  Ustaw następujące zmienne i wyeksportować te zmienne środowiskowe w bieżącej powłoce. 

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