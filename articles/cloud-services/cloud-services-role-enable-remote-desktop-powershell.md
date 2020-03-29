---
title: Włączanie pulpitu zdalnego dla roli za pomocą programu PowerShell
titleSuffix: Azure Cloud Services
description: Jak skonfigurować aplikację usługi w chmurze platformy Azure przy użyciu programu PowerShell w celu umożliwienia połączeń pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: e4e8dca6c5359e865e6a17fc47fe47802b0ee9e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75386123"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Włączanie połączenia pulpitu zdalnego dla roli w usługach w chmurze platformy Azure przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Portal Azure](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Powershell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu roli uruchomionej na platformie Azure. Za pomocą połączenia pulpitu zdalnego można rozwiązywać i diagnozować problemy z aplikacją, gdy jest uruchomiona.

W tym artykule opisano, jak włączyć pulpit zdalny w rolach usługi w chmurze przy użyciu programu PowerShell. Zobacz [Jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) dla wymagań wstępnych wymaganych dla tego artykułu. Program PowerShell korzysta z rozszerzenia pulpitu zdalnego, dzięki czemu można włączyć pulpit zdalny po wdrożeniu aplikacji.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurowanie pulpitu zdalnego z programu PowerShell
Polecenie cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) umożliwia włączenie pulpitu zdalnego na określonych rolach lub wszystkich rolach wdrożenia usługi w chmurze. Polecenie cmdlet umożliwia określenie nazwy użytkownika i hasła dla użytkownika pulpitu zdalnego za pomocą parametru *Poświadczenia,* który akceptuje obiekt PSCredential.

Jeśli używasz programu PowerShell interaktywnie, można łatwo ustawić PSCredential obiektu wywołując [get-credentials](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

```powershell
$remoteusercredentials = Get-Credential
```

To polecenie wyświetla okno dialogowe umożliwiające bezpieczne wprowadzenie nazwy użytkownika i hasła użytkownika zdalnego.

Ponieważ program PowerShell pomaga w scenariuszach automatyzacji, można również skonfigurować **PSCredential** obiektu w sposób, który nie wymaga interakcji z użytkownikiem. Najpierw musisz skonfigurować bezpieczne hasło. Zaczynasz od określenia hasła zwykłego tekstu konwertowania go na bezpieczny ciąg za pomocą [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Następnie musisz przekonwertować ten bezpieczny ciąg na zaszyfrowany standardowy ciąg za pomocą [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Teraz możesz zapisać ten zaszyfrowany standardowy ciąg w pliku przy użyciu [funkcji Set-Content](https://technet.microsoft.com/library/ee176959.aspx).

Można również utworzyć bezpieczny plik hasła, dzięki czemu nie trzeba wpisywać hasła za każdym razem. Ponadto bezpieczny plik hasła jest lepszy niż zwykły plik tekstowy. Użyj następującego programu PowerShell, aby utworzyć bezpieczny plik hasła:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Podczas ustawiania hasła upewnij się, że spełniasz [wymagania dotyczące złożoności](https://technet.microsoft.com/library/cc786468.aspx).

Aby utworzyć obiekt poświadczeń z bezpiecznego pliku hasła, należy odczytać zawartość pliku i przekonwertować go z powrotem na bezpieczny ciąg za pomocą [funkcji ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

Polecenie cmdlet [Set-AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) akceptuje również parametr *Expiration,* który określa **datetime,** w którym wygasa konto użytkownika. Można na przykład ustawić, że konto wygaśnie kilka dni od bieżącej daty i godziny.

W tym przykładzie programu PowerShell pokazano, jak ustawić rozszerzenie pulpitu zdalnego w usłudze w chmurze:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Można również opcjonalnie określić gniazdo wdrażania i role, na których chcesz włączyć pulpit zdalny. Jeśli te parametry nie są określone, polecenie cmdlet włącza pulpit zdalny na wszystkich rolach w gnieździe wdrożenia **produkcyjnego.**

Rozszerzenie Pulpit zdalny jest skojarzone z wdrożeniem. Jeśli utworzysz nowe wdrożenie dla usługi, musisz włączyć pulpit zdalny w tym wdrożeniu. Jeśli zawsze chcesz mieć włączony pulpit zdalny, należy rozważyć zintegrowanie skryptów programu PowerShell z przepływem pracy wdrażania.

## <a name="remote-desktop-into-a-role-instance"></a>Pulpit zdalny w wystąpieniu roli

Polecenie cmdlet [Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) służy do pulpitu zdalnego w określonym wystąpieniu roli usługi w chmurze. Za pomocą parametru *LocalPath* można pobrać plik RDP lokalnie. Możesz też użyć parametru *Uruchom,* aby bezpośrednio uruchomić okno dialogowe Podłączanie pulpitu zdalnego, aby uzyskać dostęp do wystąpienia roli usługi w chmurze.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Sprawdzanie, czy rozszerzenie pulpitu zdalnego jest włączone w usłudze

Polecenie cmdlet [Get-AzureRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) wyświetla, że pulpit zdalny jest włączony lub wyłączony we wdrożeniu usługi. Polecenie cmdlet zwraca nazwę użytkownika pulpitu zdalnego i role, dla których jest włączone rozszerzenie pulpitu zdalnego. Domyślnie dzieje się tak na gnieździe wdrażania i zamiast tego można użyć miejsca przejściowego.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Usuwanie rozszerzenia pulpitu zdalnego z usługi

Jeśli rozszerzenie pulpitu zdalnego zostało już włączone podczas wdrożenia i konieczne jest zaktualizowanie ustawień pulpitu zdalnego, najpierw usuń rozszerzenie. I włącz go ponownie z nowymi ustawieniami. Na przykład, jeśli chcesz ustawić nowe hasło dla zdalnego konta użytkownika lub konto wygasło. W tym celu jest to wymagane w przypadku istniejących wdrożeń z włączonym rozszerzeniem pulpitu zdalnego. W przypadku nowych wdrożeń można po prostu zastosować rozszerzenie bezpośrednio.

Aby usunąć rozszerzenie pulpitu zdalnego z wdrożenia, można użyć polecenia cmdlet [Remove-AzureServiceRemoteDesktopExtension.](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) Można również opcjonalnie określić gniazdo wdrażania i rolę, z której chcesz usunąć rozszerzenie pulpitu zdalnego.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Aby całkowicie usunąć konfigurację rozszerzenia, należy wywołać polecenie cmdlet *usuń* z parametrem **UninstallConfiguration.**
>
> **Parametr UninstallConfiguration** odinstalowuje dowolną konfigurację rozszerzenia, która jest stosowana do usługi. Każda konfiguracja rozszerzenia jest skojarzona z konfiguracją usługi. Wywołanie polecenia cmdlet *usuwania* bez **uninstallconfiguration** dezinstaluje <mark>wdrożenie</mark> z konfiguracji rozszerzenia, skutecznie usuwając rozszerzenie. Jednak konfiguracja rozszerzenia pozostaje skojarzona z usługą.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)


