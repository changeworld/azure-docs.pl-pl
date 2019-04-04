---
title: Za pomocą profilami wersji interfejsu API przy użyciu programu PowerShell w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z profilami wersji interfejsu API przy użyciu programu PowerShell w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 6bad40b840d6bd511ad0526c47e8a43f692a5cc2
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483581"
---
# <a name="use-api-version-profiles-for-powershell-in-azure-stack"></a>Użyj profilami wersji interfejsu API środowiska PowerShell w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Profilami wersji interfejsu API umożliwiają zarządzanie wersją różnice między platformą Azure i usługi Azure Stack. Profilu wersji interfejsu API to zbiór modułów AzureRM PowerShell w określonych wersjach interfejsu API. Każda platforma w chmurze ma zestaw obsługiwanych profilami wersji interfejsu API. Na przykład usługi Azure Stack obsługuje wersji określonego profilu Legitymacja, takie jak **2018-03-01-hybrydowego**, a platforma Azure obsługuje **najnowsze** profilu wersji interfejsu API. Po zainstalowaniu profilu, są zainstalowane moduły AzureRM PowerShell, które odpowiadają określony profil.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Instalowanie modułu programu PowerShell trzeba używać profilami wersji interfejsu API

**AzureRM.Bootstrapper** moduł, który jest dostępny za pośrednictwem galerii programu PowerShell zawiera polecenia cmdlet programu PowerShell, które są wymagane do pracy z profilami wersji interfejsu API. Użyj następującego polecenia cmdlet, aby zainstalować **AzureRM.Bootstrapper** modułu:

```powershell
Install-Module -Name AzureRm.BootStrapper
```

## <a name="azure-stack-version-and-profile-versions"></a>Wersja usługi Azure Stack i wersji profilu

W poniższej tabeli wymieniono wymagana wersja profilu interfejsu API i moniker modułu programu PowerShell administratora z umożliwiający najnowsze wersje usługi Azure Stack. Jeśli używasz w tym artykule przy użyciu wersji przed 1808 aktualizacji wersji profilu i moniker poprawnej wartości.

| Nr wersji | Profilu wersji interfejsu API | PS monikera modułu administracyjnego. |
| --- | --- | --- |
| 1811 lub nowszy | 2018-03-01-hybrid | 1.6.0 |
| 1808 lub nowszy | 2018-03-01-hybrid | 1.5.0 |
| 1804 lub nowszy | 2017-03-09-profile | 1.4.0 |
| Wersje wcześniejsze niż 1804 | 2017-03-09-profile | 1.2.11 |

> [!NOTE]  
> Aby uaktualnić 1.2.11 wersji, zobacz [Przewodnik po migracji](https://aka.ms/azpsh130migration).

## <a name="install-a-profile"></a>Instalowanie profilu

Użyj **Install-AzureRmProfile** polecenia cmdlet z **2018-03-01-hybrydowego** profilu wersji interfejsu API, aby zainstalować moduły AzureRM wymagane przez usługę Azure Stack. Moduły operatora usługi Azure Stack nie są zainstalowane za pomocą tego profilu wersji interfejsu API. Powinny być instalowane osobno jak określono w kroku 3 [Instalowanie programu PowerShell dla usługi Azure Stack](../azure-stack-powershell-install.md) artykułu.

```powershell
Install-AzureRMProfile -Profile 2018-03-01-hybrid
```

## <a name="install-and-import-modules-in-a-profile"></a>Zainstaluj i zaimportuj moduły w profilu

Użyj **Użyj-AzureRmProfile** polecenia cmdlet, aby zainstalować i zaimportować moduły, które są skojarzone z profilem wersji interfejsu API. W sesji programu PowerShell, można zaimportować tylko jeden profil wersji interfejsu API. Aby zaimportować innego profilu wersji interfejsu API, należy otworzyć nową sesję programu PowerShell. **Użyj-AzureRMProfile** polecenia cmdlet wykonuje następujące zadania:

1. Sprawdza, czy modułów programu PowerShell, skojarzone z określonego profilu wersji interfejsu API są zainstalowane w bieżącym zakresie.  
2. Pobiera i instaluje moduły, jeśli nie są już zainstalowane.
3. Importuje moduł do bieżącej sesji programu PowerShell.

```powershell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Scope CurrentUser -Force
```

Aby zainstalować i importowanie wybranych modułów AzureRM z profilu wersji interfejsu API, należy uruchomić **Użyj-AzureRMProfile** polecenia cmdlet z **modułu** parametru:

```powershell
# Installs and imports the compute, storage and network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Pobieranie zainstalowanych profilów

Użyj **Get-AzureRmProfile** polecenia cmdlet, aby uzyskać listę dostępnych profilami wersji interfejsu API:

```powershell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```

## <a name="update-profiles"></a>Profile Update

Użyj **Update-AzureRmProfile** polecenia cmdlet, aby zaktualizować moduły w profilu wersji interfejsu API do najnowszej wersji modułów, które są dostępne w galerii programu PowerShell. Zalecane jest, że należy zawsze uruchamiaj **Update-AzureRmProfile** polecenia cmdlet w nowej sesji programu PowerShell w celu uniknięcia konfliktów podczas importowania modułów. **Update-AzureRmProfile** uruchamia następujące zadania:

1. Sprawdza, czy jeśli najnowsze wersje modułów zainstalowanych w danym profilu wersji interfejsu API dla bieżącego zakresu.  
2. Wyświetlany jest monit o zainstalowanie, jeśli nie są już zainstalowane.  
3. Instaluje i importuje modułów zaktualizowany do bieżącej sesji programu PowerShell.  

```powershell
Update-AzureRmProfile -Profile 2018-03-01-hybrid
```

<!-- To remove the previously installed versions of the modules before updating to the latest available version, use the Update-AzureRmProfile cmdlet along with the **-RemovePreviousVersions** parameter:

```powershell 
Update-AzureRmProfile -Profile 2018-03-01-hybrid -RemovePreviousVersions
``` -->

To polecenie cmdlet jest uruchamiane następujące zadania:  

1. Sprawdza, czy jeśli najnowsze wersje modułów zainstalowanych w danym profilu wersji interfejsu API dla bieżącego zakresu.  
2. Usuwa starsze wersje modułów z bieżącego profilu wersji interfejsu API, a w bieżącej sesji programu PowerShell.  
3. wyświetli monit o zainstalowanie najnowszej wersji.  
4. Instaluje i importuje modułów zaktualizowany do bieżącej sesji programu PowerShell.  

## <a name="uninstall-profiles"></a>Odinstaluj profilów

Użyj **Uninstall-AzureRmProfile** polecenia cmdlet można odinstalować określonego profilu wersji interfejsu API.

```powershell
Uninstall-AzureRmProfile -Profile  2018-03-01-hybrid
```

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)  
