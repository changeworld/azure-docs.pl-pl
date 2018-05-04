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
ms.date: 04/30/2018
ms.author: mabrigg
ms.reviewer: sijuman
<!-- dev: viananth -->
ms.openlocfilehash: a4fe62ba8c0732745326831b977e8975e1210436
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2018
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Profile w wersji interfejsu API za pomocą języka Python w stosie Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

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

5.  Utwórz [nazwy głównej usługi](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals) do pracy z stosu Azure. Upewnij się, że ma Twojej nazwy głównej usługi [rolę współautora/właściciela](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#assign-role-to-service-principal) w ramach subskrypcji.

6.  Ustaw następujące zmienne i wyeksportować te zmienne środowiskowe w bieżącym powłoki. 

`Note: provide an explanation of where these variables come from?`

    ````bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    ```

7.  Należy pamiętać, że aby można było uruchomić ten przykład, Ubuntu 16.04-LTS i Windows Server 2012 — R2-Datacenter obrazów musi znajdować się w platformę handlową platformy Azure stosu. Mogą to być albo [pobierane z usługi Azure](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-download-azure-marketplace-item) lub [dodawane do repozytorium obrazów platformy](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-add-vm-image).


8. Uruchom przykład.

    ```
    python unmanaged-disks\example.py
    ```

## <a name="notes"></a>Uwagi

Być może wydawać się próba pobrania dysku systemu operacyjnego maszyny Wirtualnej za pomocą `virtual_machine.storage_profile.os_disk`.
W niektórych przypadkach może to wykonywać co ma, ale należy pamiętać, że daje `OSDisk` obiektu.
Aby można było zaktualizować rozmiar dysku systemu operacyjnego jako `example.py` należy nie `OSDisk` obiektu, ale `Disk` obiektu.
`example.py` pobiera `Disk` obiektu z następujących czynności:

```python
os_disk_name = virtual_machine.storage_profile.os_disk.name
os_disk = compute_client.disks.get(GROUP_NAME, os_disk_name)
```

## <a name="next-steps"></a>Kolejne kroki

- [Centrum programowania Python platformy Azure](https://azure.microsoft.com/develop/python/)
- [Dokumentację maszyn wirtualnych platformy Azure](https://azure.microsoft.com/services/virtual-machines/)
- [Ścieżka szkoleniowa dotycząca maszyny wirtualne](https://azure.microsoft.com/documentation/learning-paths/virtual-machines/)
- Jeśli nie masz subskrypcji Microsoft Azure możesz ją uzyskać bezpłatne konto próbne [tutaj](http://go.microsoft.com/fwlink/?LinkId=330212).
