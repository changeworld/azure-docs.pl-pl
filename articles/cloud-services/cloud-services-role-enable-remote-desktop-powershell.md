---
title: Włącz połączenia pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu PowerShell
description: Konfigurowanie aplikacji usługi chmury azure przy użyciu programu PowerShell, aby umożliwić połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: thraka
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: adegeo
ms.openlocfilehash: 84fe7ba418399562b6e36ed009c5e6e47fbe24da
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2018
ms.locfileid: "29874144"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Włącz połączenia pulpitu zdalnego dla roli w usług Azure Cloud Services przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny umożliwia dostęp do pulpitu z poziomu roli uruchomionej w systemie Azure. Podłączanie pulpitu zdalnego umożliwia rozwiązywanie problemów i diagnozowanie problemów z aplikacją, gdy jest on uruchomiony.

W tym artykule opisano, jak można włączyć pulpitu zdalnego na role usługi w chmurze przy użyciu programu PowerShell. Zobacz [jak instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview) wymagań wstępnych wymagane dla tego artykułu. Rozszerzenie usług pulpitu zdalnego korzysta z programu PowerShell, można włączyć pulpitu zdalnego, po wdrożeniu aplikacji.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurowanie pulpitu zdalnego z programu PowerShell

[AzureServiceRemoteDesktopExtension zestaw](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) polecenie cmdlet pozwala na włączenie pulpitu zdalnego dla określonych ról lub wszystkich ról wdrożenia usługi chmury. Polecenie cmdlet pozwala określić nazwę użytkownika i hasło dla użytkownika pulpitu zdalnego za pośrednictwem *poświadczeń* parametr, który akceptuje obiektu PSCredential.

Jeśli używasz programu PowerShell interaktywnego, łatwo można ustawić obiektu PSCredential przez wywołanie metody [poświadczenia Get](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

```
$remoteusercredentials = Get-Credential
```

To polecenie wyświetla okno dialogowe umożliwia wprowadzenie nazwy użytkownika i hasła dla użytkownika zdalnego w bezpieczny sposób.

Ponieważ PowerShell pomaga w scenariuszach automatyzacji, można również skonfigurować **PSCredential** obiektu w taki sposób, który nie wymaga interakcji z użytkownikiem. Najpierw należy skonfigurować bezpieczne hasło. Rozpoczynać się od określenia hasła w postaci zwykłego tekstu przekonwertować go na bezpieczny ciąg przy użyciu [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Obok należy konwertować za pomocą zaszyfrowanego ciągu standardowego to bezpieczny ciąg [ConvertFrom SecureString](https://technet.microsoft.com/library/hh849814.aspx). Teraz możesz zapisać ten zaszyfrowany ciąg standardowy w pliku za pomocą [zestawu zawartości](https://technet.microsoft.com/library/ee176959.aspx).

Można również utworzyć plik bezpieczne hasło, dzięki czemu nie trzeba wpisywać hasło zawsze. Ponadto plik bezpieczne hasło jest lepszym rozwiązaniem niż zwykły plik tekstowy. Można utworzyć pliku bezpieczne hasło, należy użyć następującego środowiska PowerShell:

```
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Przy ustawianiu hasła, upewnij się, że spełniają [wymagania dotyczące złożoności](https://technet.microsoft.com/library/cc786468.aspx).

Do tworzenia obiektu poświadczenia z pliku bezpieczne hasło, należy odczytać zawartości pliku i przekonwertować je ponownie na bezpieczny ciąg przy użyciu [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[AzureServiceRemoteDesktopExtension zestaw](/powershell/module/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) polecenia cmdlet również akceptuje *wygaśnięcia* parametr, który określa **DateTime** , w którym wygaśnięciem konta użytkownika. Można na przykład ustawić konto wygasa za kilka dni od bieżącej daty i godziny.

W tym przykładzie programu PowerShell pokazuje, jak ustawić rozszerzenie usług pulpitu zdalnego dla usługi w chmurze:

```
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Opcjonalnie można określić miejsce wdrożenia i role, które chcesz włączyć pulpitu zdalnego na. Jeśli nie podano tych parametrów, polecenie cmdlet umożliwia pulpitu zdalnego na wszystkich ról w **produkcji** miejsca wdrożenia.

Rozszerzenie usług pulpitu zdalnego jest powiązane z wdrożeniem. W przypadku utworzenia nowego wdrożenia usługi, należy włączyć pulpitu zdalnego w tym wdrożeniu. Jeśli chcesz mieć włączoną usługą Pulpit zdalny, następnie należy rozważyć włączenie skryptów programu PowerShell do przepływu pracy wdrażania.

## <a name="remote-desktop-into-a-role-instance"></a>Pulpit zdalny do wystąpienia roli

[Get-AzureRemoteDesktopFile](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) polecenia cmdlet jest używane do zdalnego pulpitu do konkretnej roli wystąpienie usługi w chmurze. Można użyć *LocalPath* parametr, aby pobrać protokołu RDP plików lokalnie. Lub użyć *uruchamianie* parametr, aby bezpośrednio uruchomić okno dialogowe Podłączanie pulpitu zdalnego, dostęp wystąpienia roli usługi chmury.

```
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Sprawdź, czy rozszerzenie Pulpit zdalny jest włączony w usłudze

[Get AzureServiceRemoteDesktopExtension](/powershell/module/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) polecenie cmdlet wyświetla który pulpitu zdalnego jest włączone lub wyłączone w ramach wdrożenia usługi. Polecenie cmdlet zwraca nazwę użytkownika dla użytkownika pulpitu zdalnego i role, które włączono rozszerzenie usług pulpitu zdalnego. Domyślnie dzieje się tak na miejsce wdrożenia i użytkownik może użyć miejsca przemieszczania.

```
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Usuń rozszerzenie Pulpit zdalny z usługą

Jeśli została już włączona rozszerzenia pulpitu zdalnego w ramach wdrożenia i należy zaktualizować ustawienia pulpitu zdalnego, najpierw usuń rozszerzenie. I włącz ją ponownie przy użyciu nowych ustawień. Jeśli na przykład aby ustawić nowe hasło dla konta użytkownika zdalnego lub wygasłe konto. W ten sposób jest wymagany na istniejące wdrożenia, które mają rozszerzenie usług pulpitu zdalnego włączone. W przypadku nowych wdrożeń można po prostu zastosować rozszerzenia bezpośrednio.

Aby usunąć rozszerzenia usług pulpitu zdalnego z wdrożenia, można użyć [AzureServiceRemoteDesktopExtension Usuń](/powershell/module/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) polecenia cmdlet. Opcjonalnie można określić miejsce wdrożenia i roli, z którego chcesz usunąć rozszerzenie usług pulpitu zdalnego.

```
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Aby całkowicie usunąć Konfiguracja rozszerzenia, należy wywołać *Usuń* polecenia cmdlet z **UninstallConfiguration** parametru.
>
> **UninstallConfiguration** parametru odinstalowuje żadnej konfiguracji rozszerzenia, która jest stosowana do usługi. Konfiguracja każdego rozszerzenia jest skojarzona z konfiguracją usługi. Wywoływanie *Usuń* polecenia cmdlet bez **UninstallConfiguration** Usuwa skojarzenia <mark>wdrożenia</mark> konfiguracji rozszerzenia, co powoduje wykluczenie rozszerzenia. Jednak konfiguracja rozszerzenia pozostaje skojarzony z usługą.

## <a name="additional-resources"></a>Zasoby dodatkowe

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)
