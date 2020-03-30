---
title: Moduł programu PowerShell dla usług Azure Lab | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje o module programu PowerShell, który pomaga w zarządzaniu artefaktami w usługach Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2019
ms.author: spelluru
ms.openlocfilehash: cc82355ee43f9fee4f9c2e1bb1bcc0481e4dcea3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73609400"
---
# <a name="azlabservices-powershell-module-preview"></a>Moduł programu PowerShell Az.LabServices (wersja zapoznawcza)
Az.LabServices to moduł programu PowerShell, który upraszcza zarządzanie usługami Azure Lab. Zapewnia tworzenie, wykonywanie zapytań, aktualizowanie i usuwanie kont laboratoryjnych, laboratoriów, maszyn wirtualnych i obrazów. Aby uzyskać więcej informacji na temat tego modułu, zobacz [stronę główną Az.LabServices w serwisie GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).

> [!NOTE]
> Ten moduł znajduje się w **wersji zapoznawczej**. 

## <a name="example-command"></a>Przykładowe polecenie
Oto przykład użycia polecenia programu PowerShell, aby zatrzymać wszystkie uruchomione maszyny wirtualne we wszystkich laboratoriach.

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>Wprowadzenie
1. Zainstaluj [program Azure PowerShell,](https://docs.microsoft.com/powershell/azure/overview) jeśli nie istnieje na komputerze. 
2. Pobierz [Az.LabServices.psm1](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) na swój komputer.
3. Importuj moduł:

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. Uruchom następujące polecenie, aby wyświetlić listę wszystkich laboratoriów w ramach subskrypcji.

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>Następne kroki
Zobacz [stronę główną Az.LabServices na GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library).
