---
title: Włączanie funkcji Podłączanie pulpitu zdalnego dla roli w usługach Azure Cloud Services przy użyciu programu PowerShell
description: Konfigurowanie aplikacji usługi w chmurze platformy azure przy użyciu programu PowerShell, aby zezwolić na połączenia pulpitu zdalnego
services: cloud-services
documentationcenter: ''
author: jpconnock
manager: timlt
editor: ''
ms.assetid: bf2f70a4-20dc-4302-a91a-38cd7a2baa62
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 43ccc8e53c30219630ad10ee66a4db38656818e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60525398"
---
# <a name="enable-remote-desktop-connection-for-a-role-in-azure-cloud-services-using-powershell"></a>Włączanie funkcji Podłączanie pulpitu zdalnego dla roli w usługach Azure Cloud Services przy użyciu programu PowerShell

> [!div class="op_single_selector"]
> * [Azure Portal](cloud-services-role-enable-remote-desktop-new-portal.md)
> * [Program PowerShell](cloud-services-role-enable-remote-desktop-powershell.md)
> * [Program Visual Studio](cloud-services-role-enable-remote-desktop-visual-studio.md)

Pulpit zdalny zapewnia dostęp do pulpitu roli działających na platformie Azure. Podłączanie pulpitu zdalnego umożliwia rozwiązywanie problemów i diagnozowanie problemów z aplikacją, gdy jest on uruchomiony.

W tym artykule opisano, jak można włączyć pulpitu zdalnego na role usługi w chmurze przy użyciu programu PowerShell. Zobacz [jak zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/overview) wymagań wstępnych wymagane na potrzeby tego artykułu. Rozszerzenie usług pulpitu zdalnego korzysta z programu PowerShell, dzięki czemu można włączyć pulpitu zdalnego, po wdrożeniu aplikacji.

## <a name="configure-remote-desktop-from-powershell"></a>Konfigurowanie pulpitu zdalnego za pomocą programu PowerShell
[AzureServiceRemoteDesktopExtension zestaw](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) polecenie cmdlet pozwala na włączenie pulpitu zdalnego dla określonych ról lub wszystkie role wdrażania usługi w chmurze. Polecenie cmdlet pozwala określić nazwę użytkownika i hasło dla użytkownika pulpitu zdalnego za pośrednictwem *poświadczeń* parametr, który akceptuje obiekt PSCredential.

Jeśli używasz programu PowerShell interaktywnie, łatwo można ustawić obiektu PSCredential, wywołując [Get-Credentials](https://technet.microsoft.com/library/hh849815.aspx) polecenia cmdlet.

```powershell
$remoteusercredentials = Get-Credential
```

To polecenie wyświetla okno dialogowe, dzięki czemu możesz wprowadzić nazwę użytkownika i hasło użytkownika zdalnego w bezpieczny sposób.

Ponieważ Środowisko PowerShell pomaga w scenariuszach automatyzacji, możesz również skonfigurować **PSCredential** obiekt w taki sposób, który nie wymaga interakcji z użytkownikiem. Najpierw musisz skonfigurować bezpieczne hasło. Zaczynać się określenie hasła w postaci zwykłego tekstu przekonwertuj go na bezpieczny ciąg, w którym używana jest [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx). Następnie należy przekonwertować to bezpieczny ciąg do usługi za pomocą zaszyfrowanego ciągu standardowego [ConvertFrom-SecureString](https://technet.microsoft.com/library/hh849814.aspx). Teraz można zapisać tego zaszyfrowanego ciągu standardowego pliku za pomocą [zestaw zawartości](https://technet.microsoft.com/library/ee176959.aspx).

Można także utworzyć plik bezpieczne hasło, dzięki czemu nie trzeba wpisać hasło każdym razem, gdy. Ponadto plik bezpieczne hasło jest lepsze niż zwykły plik tekstowy. Następujące polecenie programu PowerShell umożliwia utworzenie pliku bezpieczne hasło:

```powershell
ConvertTo-SecureString -String "Password123" -AsPlainText -Force | ConvertFrom-SecureString | Set-Content "password.txt"
```

> [!IMPORTANT]
> Przy ustawianiu hasła, upewnij się, że spełniasz [wymagania co do złożoności](https://technet.microsoft.com/library/cc786468.aspx).

Aby utworzyć obiekt poświadczeń z pliku bezpieczne hasło, należy przeczytać zawartość pliku i przekonwertować z powrotem na bezpieczny ciąg, w którym używana jest [ConvertTo-SecureString](https://technet.microsoft.com/library/hh849818.aspx).

[AzureServiceRemoteDesktopExtension zestaw](/powershell/module/servicemanagement/azure/set-azureserviceremotedesktopextension?view=azuresmps-3.7.0) akceptuje także polecenia cmdlet *wygaśnięcia* parametr, który określa **daty/godziny** w którym wygaśnięciem ważności konta użytkownika. Na przykład można ustawić konto wygaśnie za kilka dni od bieżącej daty i godziny.

W tym przykładzie programu PowerShell pokazuje, jak ustawić rozszerzenie usług pulpitu zdalnego w usłudze w chmurze:

```powershell
$servicename = "cloudservice"
$username = "RemoteDesktopUser"
$securepassword = Get-Content -Path "password.txt" | ConvertTo-SecureString
$expiry = $(Get-Date).AddDays(1)
$credential = New-Object System.Management.Automation.PSCredential $username,$securepassword
Set-AzureServiceRemoteDesktopExtension -ServiceName $servicename -Credential $credential -Expiration $expiry
```
Można również opcjonalnie określić miejsce wdrożenia i role, które chcesz włączyć pulpitu zdalnego na. Jeśli nie określono parametrów, polecenie cmdlet włącza Pulpit zdalny dla wszystkich ról w **produkcji** miejsce wdrożenia.

Rozszerzenie usług pulpitu zdalnego jest powiązane z wdrożeniem. Jeśli tworzysz nowe wdrożenie usługi, należy włączyć pulpitu zdalnego w tym wdrożeniu. Jeśli chcesz zawsze włączonym pulpitem zdalnym, następnie należy rozważyć integrowanie skryptów środowiska PowerShell wdrażanie przepływu pracy.

## <a name="remote-desktop-into-a-role-instance"></a>Za pomocą pulpitu zdalnego wystąpienia roli

[Get-AzureRemoteDesktopFile](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) polecenie cmdlet jest używany do zdalnego pulpitu w wystąpieniu określonych ról usługi w chmurze. Możesz użyć *LocalPath* lokalnie plik parametru, aby pobrać protokołu RDP. Możesz też *Uruchom* parametru, aby bezpośrednio Uruchom okno dialogowe Podłączanie pulpitu zdalnego, aby dostęp do wystąpienia roli usługi w chmurze.

```powershell
Get-AzureRemoteDesktopFile -ServiceName $servicename -Name "WorkerRole1_IN_0" -Launch
```

## <a name="check-if-remote-desktop-extension-is-enabled-on-a-service"></a>Sprawdź, czy rozszerzenie usług pulpitu zdalnego jest włączony w usłudze

[Get AzureServiceRemoteDesktopExtension](/powershell/module/servicemanagement/azure/get-azureremotedesktopfile?view=azuresmps-3.7.0) polecenie cmdlet wyświetla, Pulpit zdalny jest włączone lub wyłączone w ramach wdrożenia usługi. Polecenie cmdlet zwraca nazwę użytkownika dla użytkownika pulpitu zdalnego i role, które włączono rozszerzenie usług pulpitu zdalnego. Domyślnie taka sytuacja wystąpi dla miejsca wdrożenia, a użytkownik może użyć miejsca przejściowego.

```powershell
Get-AzureServiceRemoteDesktopExtension -ServiceName $servicename
```

## <a name="remove-remote-desktop-extension-from-a-service"></a>Usuń rozszerzenie usług pulpitu zdalnego z poziomu usługi

Jeśli została już włączona rozszerzenia pulpitu zdalnego we wdrożeniu, a następnie konieczne zaktualizowanie ustawień pulpitu zdalnego, należy najpierw usunąć rozszerzenie. I włącz ją ponownie z nowymi ustawieniami. Na przykład, jeśli chcesz ustawić nowe hasło dla konta użytkownika zdalnego lub konto wygasło. W ten sposób jest wymagany na istniejące wdrożenia, które mają włączeniu rozszerzenia usług pulpitu zdalnego. W przypadku nowych wdrożeń można po prostu zastosować rozszerzenia bezpośrednio.

Aby usunąć rozszerzenie usług pulpitu zdalnego z wdrożeniem, można użyć [AzureServiceRemoteDesktopExtension Usuń](/powershell/module/servicemanagement/azure/remove-azureserviceremotedesktopextension?view=azuresmps-3.7.0) polecenia cmdlet. Można również opcjonalnie określić miejsce wdrożenia i ról, z którego chcesz usunąć rozszerzenie usług pulpitu zdalnego.

```powershell
Remove-AzureServiceRemoteDesktopExtension -ServiceName $servicename -UninstallConfiguration
```

> [!NOTE]
> Aby całkowicie usunąć konfigurację rozszerzenia, należy wywołać *Usuń* polecenia cmdlet z **UninstallConfiguration** parametru.
>
> **UninstallConfiguration** parametru odinstalowuje żadnej konfiguracji rozszerzenia, która jest stosowana do usługi. Każdej konfiguracji rozszerzenia jest skojarzona z konfiguracją usługi. Wywoływanie *Usuń* polecenia cmdlet bez **UninstallConfiguration** powoduje usunięcie <mark>wdrożenia</mark> z konfiguracji rozszerzenia, więc powodując rzeczywiste usunięcie rozszerzenie. Jednak konfiguracja rozszerzenia pozostaje skojarzona z usługą.

## <a name="additional-resources"></a>Dodatkowe zasoby

[Jak skonfigurować usługi w chmurze](cloud-services-how-to-configure-portal.md)