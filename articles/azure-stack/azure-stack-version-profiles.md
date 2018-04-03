---
title: "Przy użyciu profilów wersji interfejsu API w stosie Azure | Dokumentacja firmy Microsoft"
description: "Więcej informacji na temat profilów wersji interfejsu API Azure stosu."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: EBAEA4D2-098B-4B5A-A197-2CEA631A1882
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 68f4250c2a2a6bed1a1e21dc444e93cc87b6f59b
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2017
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Zarządzanie profilami wersji interfejsu API Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Profile w wersji interfejsu API umożliwiają zarządzanie różnice wersji platformy Azure i stosu Azure. Profil wersji interfejsu API to zbiór modułów programu AzureRM PowerShell z określonych wersji interfejsu API. Każdej platformy w chmurze ma zestaw obsługiwanych profile w wersji interfejsu API. Na przykład stosu Azure obsługuje wersji określonego profilu z takich jak **2017-03-09-profilu**, i obsługuje Azure **najnowsze** profilu wersji interfejsu API. Po zainstalowaniu profilu są zainstalowane moduły programu AzureRM PowerShell, które odpowiadają określonego profilu.

## <a name="install-the-powershell-module-required-to-use-api-version-profiles"></a>Zainstaluj moduł programu PowerShell wymaganych do używania profilów wersji interfejsu API

**AzureRM.Bootstrapper** moduł, który jest dostępny za pośrednictwem galerii programu PowerShell zawiera polecenia cmdlet programu PowerShell, które są wymagane do pracy z profilami wersji interfejsu API. Aby zainstalować moduł AzureRM.Bootstrapper, należy użyć następującego polecenia cmdlet:

```PowerShell
Install-Module -Name AzureRm.BootStrapper
```
Moduł AzureRM.Bootstrapper jest w wersji zapoznawczej; Szczegóły i funkcje mogą ulec zmianie. Aby pobrać i zainstalować najnowszą wersję tego modułu z galerii programu PowerShell, uruchom następujące polecenie cmdlet:

```PowerShell
Update-Module -Name "AzureRm.BootStrapper"
```

## <a name="install-a-profile"></a>Instalowanie profilu

Użyj **AzureRmProfile instalacji** polecenia cmdlet z **2017-03-09-profilu** profilu wersji interfejsu API, aby zainstalować moduły AzureRM wymagane przez stos Azure. Należy pamiętać, że moduły operator Azure stosu nie są zainstalowane z tym profilem wersji interfejsu API i powinny być instalowane osobno jak określono w kroku 3 [Zainstaluj program PowerShell Azure stosu](azure-stack-powershell-install.md) artykułu.

```PowerShell 
Install-AzureRMProfile -Profile 2017-03-09-profile
```
## <a name="install-and-import-modules-in-a-profile"></a>Zainstaluj i zaimportuj moduły w profilu

Użyj **AzureRmProfile użyj** polecenia cmdlet, aby zainstalować i zaimportować moduły, które są skojarzone z profilem wersji interfejsu API. W sesji programu PowerShell można importować tylko jeden profil wersji interfejsu API. Aby zaimportować innego profilu wersji interfejsu API, musi Otwórz nową sesję programu PowerShell. Polecenia cmdlet AzureRMProfile Użyj uruchamia następujące zadania:  
1. Sprawdza, czy moduły programu PowerShell skojarzonych z profilem określona wersja interfejsu API są zainstalowane w bieżącym zakresie.  
2. Pobiera i instaluje modułów, jeśli nie są już zainstalowane.   
3. Importuje moduł do bieżącej sesji programu PowerShell. 

```PowerShell
# Installs and imports the specified API version profile into the current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser

# Installs and imports the specified API version profile into the current PowerShell session without any prompts
Use-AzureRmProfile -Profile 2017-03-09-profile -Scope CurrentUser -Force
```

Aby zainstalować i importowanie wybranych modułów AzureRM z profilu wersji interfejsu API, uruchom polecenie cmdlet Użyj AzureRMProfile z **modułu** parametru:

```PowerShell
# Installs and imports the compute, Storage and Network modules from the specified API version profile into your current PowerShell session.
Use-AzureRmProfile -Profile 2017-03-09-profile -Module AzureRM.Compute, AzureRM.Storage, AzureRM.Network
```

## <a name="get-the-installed-profiles"></a>Uzyskać zainstalowanych profile

Użyj **Get-AzureRmProfile** polecenia cmdlet, aby uzyskać listę dostępnych profilów wersji interfejsu API: 

```PowerShell
# lists all API version profiles provided by the AzureRM.BootStrapper module.
Get-AzureRmProfile -ListAvailable 

# lists the API version profiles which are installed on your machine
Get-AzureRmProfile
```
## <a name="update-profiles"></a>Zaktualizować profile

Użyj **AzureRmProfile aktualizacji** polecenia cmdlet do aktualizacji do najnowszej wersji modułów, które są dostępne w PSGallery moduły w profilu wersji interfejsu API. Zaleca się są zawsze uruchamiane **AzureRmProfile aktualizacji** polecenia cmdlet w nowej sesji programu PowerShell w celu uniknięcia konfliktów z importowaniem modułów. Polecenie cmdlet Update-AzureRmProfile uruchamia następujące zadania:

1. Sprawdza, czy najnowsze wersje moduły są zainstalowane w danym profilu wersji interfejsu API dla bieżącego zakresu.  
2. Wyświetlany jest monit o zainstalowanie, jeśli nie są już zainstalowane.  
3. Instaluje i importuje zaktualizowane moduły do bieżącej sesji programu PowerShell.  

```PowerShell
Update-AzureRmProfile -Profile 2017-03-09-profile
```

Aby usunąć zainstalowanych wcześniej wersji moduły przed zaktualizowaniem do najnowszej dostępnej wersji, użyj polecenia cmdlet AzureRmProfile aktualizacji wraz z **- RemovePreviousVersions** parametru:

```PowerShell 
Update-AzureRmProfile -Profile 2017-03-09-profile -RemovePreviousVersions
```

To polecenie cmdlet jest uruchamiane następujące zadania:  

1. Sprawdza, czy najnowsze wersje moduły są zainstalowane w danym profilu wersji interfejsu API dla bieżącego zakresu.  
2. Usuwa starsze wersje modułów z bieżącym profilu wersji interfejsu API i w bieżącej sesji programu PowerShell.  
4. wyświetlany jest monit o zainstalowanie najnowszej wersji.  
5. Instaluje i importuje zaktualizowane moduły do bieżącej sesji programu PowerShell.  
 
## <a name="uninstall-profiles"></a>Odinstaluj profilów

Użyj **AzureRmProfile Odinstaluj** polecenia cmdlet można odinstalować określonego profilu wersji interfejsu API.

```PowerShell 
Uninstall-AzureRmProfile -Profile 2017-03-09-profile
```

## <a name="next-steps"></a>Następne kroki
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](user/azure-stack-powershell-configure-user.md)  
