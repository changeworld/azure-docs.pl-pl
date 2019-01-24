---
title: Za pomocą profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: 8049db848e34b0aa9bc23f08169a8c63f765791a
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/17/2019
ms.locfileid: "54389750"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Profilami wersji interfejsu API za pomocą języka Python w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

## <a name="python-and-api-version-profiles"></a>Profile wersji języka Python i interfejsu API

Zestaw SDK języka Python obsługuje profilami wersji interfejsu API pod kątem platform inną chmurę, takich jak Azure Stack i globalnej platformy Azure. Można używać profilów interfejsu API w tworzeniu rozwiązań dla chmury hybrydowej. Zestaw SDK języka Python obsługuje następujące profile interfejsu API:

- **latest**  
    Ten profil jest przeznaczony dla najnowszej wersji interfejsu API dla wszystkich dostawców usług na platformie Azure.
- **2018-03-01-hybrydowe**     
    Ten profil jest przeznaczony dla najnowszej wersji interfejsu API dla wszystkich dostawców zasobów platformy Azure Stack.
- **2017-03-09-profile**  
    Ten profil jest przeznaczony dla najbardziej zgodne wersje interfejsu API dostawcy zasobów, obsługiwane przez usługę Azure Stack.

   Aby uzyskać więcej informacji o profilach interfejsu API i usługi Azure Stack, zobacz [Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalowanie zestawu Azure Python SDK

1. Zainstaluj oprogramowanie Git z [oficjalna witryna](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Aby uzyskać instrukcje dotyczące instalowania zestawu SDK języka Python, zobacz [platformy Azure dla deweloperów języka Python](/python/azure/python-sdk-azure-install?view=azure-python).
3. Jeśli nie jest dostępny, Utwórz subskrypcję i Zapisz identyfikator subskrypcji do użycia w przyszłości. Aby uzyskać instrukcje dotyczące tworzenia subskrypcji, zobacz [Tworzenie subskrypcji ofert w usłudze Azure Stack](../azure-stack-subscribe-plan-provision-vm.md).
4. Tworzenie jednostki usługi, a następnie zapisz jego identyfikator i klucz tajny. Aby uzyskać instrukcje dotyczące sposobu tworzenia jednostki usługi dla usługi Azure Stack, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md).
5. Upewnij się, że jednostki usługi rola właściciel/Współautor subskrypcji. Aby uzyskać instrukcje dotyczące sposobu przypisywania roli do jednostki usługi, zobacz [zapewniają dostęp aplikacji do usługi Azure Stack](../azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było używać zestawu Azure Python SDK przy użyciu usługi Azure Stack, należy podać następujące wartości, a następnie ustaw wartości zmiennych środowiskowych. Zapoznaj się z instrukcjami pod tabelą systemu operacyjnego na temat ustawiania zmiennych środowiskowych.

| Wartość | Zmienne środowiskowe | Opis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| Identyfikator dzierżawy | AZURE_TENANT_ID | Zalety usługi Azure Stack [identyfikator dzierżawy](../azure-stack-identity-overview.md). |
| Identyfikator klienta | AZURE_CLIENT_ID | Usługa identyfikator podmiotu zabezpieczeń aplikacji zapisywał informacje o nazwę główną usługi został utworzony w poprzedniej sekcji tego artykułu. |
| Identyfikator subskrypcji | AZURE_SUBSCRIPTION_ID | [Identyfikator subskrypcji](../azure-stack-plan-offer-quota-overview.md#subscriptions) jest sposób uzyskiwania dostępu do oferty w usłudze Azure Stack. |
| Wpis tajny klienta | AZURE_CLIENT_SECRET | Klucz tajny aplikacji jednostki usługi zapisane podczas tworzenia nazwy głównej usługi. |
| Punkt końcowy usługi Resource Manager | ARM_ENDPOINT | Zobacz [punktu końcowego Menedżera zasobów usługi Azure Stack](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Lokalizacja zasobu | AZURE_RESOURCE_LOCATION | Lokalizacja zasobu środowiska Azure Stack.

## <a name="python-samples-for-azure-stack"></a>Przykłady w języku Python dla usługi Azure Stack

Przykłady kodu, dostępnych dla usługi Azure Stack przy użyciu zestawu SDK języka Python, należą:

- [Zarządzanie zasobami i grupami zasobów](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/).
- [Zarządzanie kontem magazynu](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/).
- [Zarządzanie maszynami wirtualnymi](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/).

## <a name="python-manage-virtual-machine-sample"></a>Python Zarządzanie przykładowej maszyny wirtualnej

Poniższy przykładowy kod umożliwia wykonywanie typowych zadań zarządzania dla maszyn wirtualnych w usługi Azure Stack. Przykładowy kod dowiesz się, aby:

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

Aby przejrzeć kod, który wykonuje te operacje, zobacz **run_example()** funkcji w skrypcie języka Python **example.py** w repozytorium GitHub [hybrydowego-obliczeń-Python-Zarządzanie-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Każda operacja jest wyraźnie oznaczony komentarz i funkcję drukowania. Przykłady niekoniecznie w kolejności przedstawionej na tej liście.

## <a name="run-the-python-sample"></a>Uruchom aplikację przykładową języka Python

1. Jeśli jeszcze tego nie zrobiono, [zainstaluj język Python](https://www.python.org/downloads/). W tym przykładzie (i zestawu SDK) jest zgodna z języka Python 2.7, 3.4, 3.5 i 3.6.

2. Ogólne zalecenia dotyczące programowania w języku Python jest środowisko wirtualne. Aby uzyskać więcej informacji, zobacz [dokumentace Pro Python](https://docs.python.org/3/tutorial/venv.html).

3. Zainstaluj i zainicjuj środowisko wirtualne z modułem "venv" na środowisku Python 3 (należy zainstalować [virtualenv](https://pypi.python.org/pypi/virtualenv) for Python 2.7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Sklonuj repozytorium:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Instalowanie zależności przy użyciu narzędzia pip:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Tworzenie [nazwy głównej usługi](../azure-stack-create-service-principals.md) do pracy z usługą Azure Stack. Upewnij się, Twoja jednostka usługi ma [roli właściciel/Współautor](../azure-stack-create-service-principals.md#assign-a-role) w ramach Twojej subskrypcji.

7. Ustaw następujące zmienne i wyeksportować te zmienne środowiskowe w bieżącej powłoce:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Aby można było uruchomić ten przykład, Ubuntu 16.04-LTS i obrazy WindowsServer-2012-R2-Datacenter musi być obecny w witrynie marketplace usługi Azure Stack. Mogą to być albo [pobrany z platformy Azure](../azure-stack-download-azure-marketplace-item.md), lub dodać do [repozytorium obrazów platformy](../azure-stack-add-vm-image.md).

9. Uruchom przykład:

    ```python
    python example.py
    ```


## <a name="next-steps"></a>Kolejne kroki

- [Centrum deweloperów języka Python platformy Azure](https://azure.microsoft.com/develop/python/)
- [Dokumentacja dotycząca maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Ścieżka szkoleniowa dla maszyn wirtualnych](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
- Jeśli nie masz subskrypcji Microsoft Azure, możesz uzyskać bezpłatne konto próbne [tutaj](https://go.microsoft.com/fwlink/?LinkId=330212).
