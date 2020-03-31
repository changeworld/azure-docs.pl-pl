---
title: Rozwiązywanie problemów z powłoką w chmurze platformy Azure | Dokumenty firmy Microsoft
description: Rozwiązywanie problemów z powłoką chmury platformy Azure
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 0b1b22095c77344ed71762d3d51b12f19d9f1811
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458056"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Rozwiązywanie problemów & ograniczeniami usługi Azure Cloud Shell

Znane rozwiązania dotyczące rozwiązywania problemów w usłudze Azure Cloud Shell obejmują:

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-troubleshooting"></a>Ogólne rozwiązywanie problemów

### <a name="error-running-azuread-cmdlets-in-powershell"></a>Błąd podczas uruchamiania poleceń cmdlet azuread w programie PowerShell

- **Szczegóły:** Po uruchomieniu poleceń cmdlet `Get-AzureADUser` AzureAD, takich jak `You must call the Connect-AzureAD cmdlet before calling any other cmdlets`w usłudze Cloud Shell, może zostać wyświetlony błąd: . 
- **Rozdzielczość**: `Connect-AzureAD` Uruchom polecenie cmdlet. Wcześniej usługa Cloud Shell uruchomiła tę polecenie cmdlet automatycznie podczas uruchamiania programu PowerShell. Aby przyspieszyć czas rozpoczęcia, polecenie cmdlet nie jest już uruchamiane automatycznie. Można przywrócić poprzednie zachowanie, dodając `Connect-AzureAD` do pliku $PROFILE w programie PowerShell.

### <a name="early-timeouts-in-firefox"></a>Wczesne limity czasu w FireFox

- **Szczegóły:** Cloud Shell wykorzystuje otwartą sieć websocket do przekazywania danych wejściowych / wyjściowych do przeglądarki. FireFox ma wstępnie ustawione zasady, które mogą zamknąć websocket przedwcześnie powodując wczesne limity czasu w Cloud Shell.
- **Rozdzielczość:** Otwórz FireFox i przejdź do "about:config" w polu URL. Wyszukaj hasło "network.websocket.timeout.ping.request" i zmień wartość z 0 na 10.

### <a name="disabling-cloud-shell-in-a-locked-down-network-environment"></a>Wyłączanie powłoki chmury w zablokowanym środowisku sieciowym

- **Szczegóły**: Administratorzy mogą chcieć wyłączyć dostęp do usługi Cloud Shell dla swoich użytkowników. Usługa Cloud Shell korzysta `ux.console.azure.com` z dostępu do domeny, której można odmówić, zatrzymując dostęp do punktów wejścia usługi Cloud Shell, w tym portal.azure.com, shell.azure.com, rozszerzenie konta azure kodu programu Visual Studio i docs.microsoft.com. W chmurze rządu USA punktem wejścia jest `ux.console.azure.us`; nie ma odpowiednich shell.azure.us.
- **Rozdzielczość:** Ograniczanie `ux.console.azure.com` `ux.console.azure.us` dostępu do ustawień sieciowych lub za pośrednictwem ustawień sieciowych do środowiska. Ikona powłoki w chmurze nadal będzie istnieć w witrynie Azure portal, ale nie pomyślnie połączy się z usługą.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Okno dialogowe magazynu — błąd: 403 RequestDisallowedByPolicy

- **Szczegóły:** Podczas tworzenia konta magazynu za pośrednictwem usługi Cloud Shell nie powiedzie się z powodu zasad platformy Azure umieszczonych przez administratora. Komunikat o błędzie będzie zawierać:`The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozwiązanie:** Skontaktuj się z administratorem platformy Azure, aby usunąć lub zaktualizować zasady platformy Azure odmawiające tworzenia magazynu.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Okno dialogowe magazynu — błąd: 400 niedozwolonych operacji

- **Szczegóły:** Podczas korzystania z subskrypcji usługi Azure Active Directory nie można utworzyć magazynu.
- **Rozwiązanie:** Użyj subskrypcji platformy Azure zdolnej do tworzenia zasobów magazynu. Subskrypcje usługi Azure AD nie mogą tworzyć zasobów platformy Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Wyjście terminala - Błąd: Nie można podłączyć terminala: nie można ustanowić websocket. Naciśnij, `Enter` aby ponownie się połączyć.
- **Szczegóły:** Usługa Cloud Shell wymaga możliwości nawiązania połączenia websocket z infrastrukturą Cloud Shell.
- **Rozwiązanie**: Sprawdź, czy skonfigurowano ustawienia sieciowe, aby włączyć wysyłanie żądań https i żądań websocket do domen na *.console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ustawianie połączenia cloud shell do obsługi przy użyciu protokołu TLS 1.2
 - **Szczegóły**: Aby zdefiniować wersję protokołu TLS dla połączenia z powłoką w chmurze, należy ustawić ustawienia specyficzne dla przeglądarki.
 - **Rozwiązanie**: Przejdź do ustawień zabezpieczeń przeglądarki i zaznacz pole wyboru obok opcji "Użyj protokołu TLS 1.2".

## <a name="bash-troubleshooting"></a>Rozwiązywanie problemów z bashem

### <a name="cannot-run-the-docker-daemon"></a>Nie można uruchomić demona docker

- **Szczegóły:** Usługa Cloud Shell wykorzystuje kontener do hosta środowiska powłoki, w wyniku czego uruchomienie demona jest niedozwolone.
- **Rozdzielczość:** Korzystaj z [komputera docker,](https://docs.docker.com/machine/overview/)który jest instalowany domyślnie, do zarządzania kontenerami docker ze zdalnego hosta platformy Docker.

## <a name="powershell-troubleshooting"></a>Rozwiązywanie problemów z programem PowerShell

### <a name="gui-applications-are-not-supported"></a>Aplikacje gui nie są obsługiwane

- **Szczegóły:** Jeśli użytkownik uruchomi aplikację gui, monit nie zwraca. Na przykład, gdy jeden klon prywatne repozytorium GitHub, który ma włączone uwierzytelnianie dwuskładnikowe, zostanie wyświetlone okno dialogowe do ukończenia uwierzytelniania dwuskładnikowego.
- **Rozdzielczość:** Zamknij i ponownie otwórz powłokę.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Rozwiązywanie problemów ze zdalnym zarządzaniem maszyn wirtualnych platformy Azure
> [!NOTE]
> Maszyny wirtualne platformy Azure muszą mieć publiczny adres IP.

- **Szczegóły**: Ze względu na domyślne ustawienia Zapory systemu Windows dla usługi WinRM użytkownik może wystąpić z następującym błędem:`Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozwiązanie:** `Enable-AzVMPSRemoting` Uruchom, aby włączyć wszystkie aspekty komunikacji zdalnej programu PowerShell na komputerze docelowym.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir`nie aktualizuje wyniku na dysku platformy Azure

- **Szczegóły:** Domyślnie, aby zoptymalizować środowisko użytkownika, wyniki `dir` są buforowane na dysku platformy Azure.
- **Rozwiązanie:** Po utworzeniu, zaktualizowaniu lub `dir -force` usunięciu zasobu platformy Azure uruchom, aby zaktualizować wyniki na dysku platformy Azure.

## <a name="general-limitations"></a>Ogólne ograniczenia

Usługa Azure Cloud Shell ma następujące znane ograniczenia:

### <a name="quota-limitations"></a>Ograniczenia przydziału

Usługa Azure Cloud Shell ma limit 20 równoczesnych użytkowników na dzierżawę na region. Jeśli spróbujesz otworzyć więcej jednoczesnych sesji niż limit, zostanie wyświetlony błąd "Dzierżawa użytkownika ponad przydział" błąd. Jeśli masz uzasadnioną potrzebę otwarcia większej liczby sesji niż ta (na przykład w przypadku sesji szkoleniowych), skontaktuj się z pomocą techniczną przed przewidywanym użyciem, aby poprosić o zwiększenie przydziału.

Usługa Cloud Shell jest dostępna jako bezpłatna usługa i jest przeznaczona do konfigurowania środowiska platformy Azure, a nie jako platforma obliczeniowa ogólnego przeznaczenia. Nadmierne użycie automatyczne może być uznane za naruszenie Warunków korzystania z usługi platformy Azure i może prowadzić do zablokowania dostępu do usługi Cloud Shell.

### <a name="system-state-and-persistence"></a>Stan systemu i trwałość

Komputer, który udostępnia sesję Cloud Shell jest tymczasowy i jest odtwarzany po sesji jest nieaktywny przez 20 minut. Usługa Cloud Shell wymaga zainstalowanego udziału plików platformy Azure. W rezultacie subskrypcja musi być w stanie skonfigurować zasoby magazynu, aby uzyskać dostęp do usługi Cloud Shell. Inne zagadnienia obejmują:

- W przypadku zainstalowanego magazynu `clouddrive` utrwalone są tylko modyfikacje w katalogu. W Bash `$HOME` katalog jest również utrwalony.
- Udziały plików platformy Azure można montować tylko z [poziomu przypisanego regionu](persisting-shell-storage.md#mount-a-new-clouddrive).
  - W Bash, `env` uruchom, aby `ACC_LOCATION`znaleźć swój region ustawiony jako .
- Usługa Azure Files obsługuje tylko lokalnie nadmiarowe konta magazynu i magazynu geograficznie nadmiarowego.

### <a name="browser-support"></a>Obsługa przeglądarki

Usługa Cloud Shell obsługuje najnowsze wersje następujących przeglądarek:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari w trybie prywatnym nie jest obsługiwane.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="usage-limits"></a>Limity użycia

Usługa Cloud Shell jest przeznaczona do zastosowań interaktywnych. W rezultacie wszystkie długotrwałe sesje nieinterakcyjne są kończyne bez ostrzeżenia.

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako stali użytkownicy bez dostępu sudo. Żadna instalacja poza katalogem `$Home` nie jest utrwalona.

## <a name="bash-limitations"></a>Ograniczenia bash

### <a name="editing-bashrc"></a>Edycja .bashrc

Podczas edycji programu .bashrc może to spowodować nieoczekiwane błędy w usłudze Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia programu PowerShell

### <a name="preview-version-of-azuread-module"></a>Wersja zapoznawcza modułu AzureAD

Obecnie `AzureAD.Standard.Preview`dostępna jest wersja zapoznawcza modułu opartego na standardzie .NET. Ten moduł zapewnia taką `AzureAD`samą funkcjonalność jak .

### <a name="sqlserver-module-functionality"></a>`SqlServer`funkcjonalność modułu

Moduł `SqlServer` zawarty w usłudze Cloud Shell ma tylko wstępną obsługę programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku podczas tworzenia z dysku platformy Azure

Za pomocą poleceń cmdlet programu PowerShell użytkownicy nie mogą tworzyć plików na dysku platformy Azure. Gdy użytkownicy tworzą nowe pliki przy użyciu innych narzędzi, takich `$HOME` jak vim lub nano, pliki są domyślnie zapisywane.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Ukończenie karty może zgłosić wyjątek PSReadline

Jeśli psreadline użytkownika EditMode jest ustawiona na Emacs, użytkownik próbuje wyświetlić wszystkie możliwości za pomocą uzupełniania karty, a rozmiar okna jest zbyt mały, aby wyświetlić wszystkie możliwości, PSReadline zrzuci nieobsługiwał wyjątek.

### <a name="large-gap-after-displaying-progress-bar"></a>Duża przerwa po wyświetleniu paska postępu

Jeśli polecenie lub akcja użytkownika wyświetla pasek postępu, taki `Azure:` zakładka kończy się na dysku, możliwe jest, że kursor nie jest ustawiony poprawnie i pojawi się przerwa, w której pasek postępu był wcześniej.

### <a name="random-characters-appear-inline"></a>Losowe znaki pojawiają się w linii

Kody sekwencji pozycjonowania kursora, na przykład, `5;13R`mogą pojawić się w danych wejściowych użytkownika. Znaki można usunąć ręcznie.

## <a name="personal-data-in-cloud-shell"></a>Dane osobowe w usłudze Cloud Shell

Usługa Azure Cloud Shell traktuje twoje dane osobowe poważnie, dane przechwycone i przechowywane przez usługę Azure Cloud Shell są używane do zapewnienia ustawień domyślnych dla środowiska, takich jak ostatnio używana powłoka, preferowany rozmiar czcionki, preferowany typ czcionki i szczegóły udziału plików że z powrotem dysk w chmurze. Jeśli chcesz wyeksportować lub usunąć te dane, skorzystaj z poniższych instrukcji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Eksportowanie
Aby **wyeksportować** ustawienia użytkownika, zapisy usługi Cloud Shell, takie jak preferowana powłoka, rozmiar czcionki i typ czcionki, uruchom następujące polecenia.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia w programie Bash lub PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
  ```

Program PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  ((Invoke-WebRequest -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}).Content | ConvertFrom-Json).properties | Format-List
```

### <a name="delete"></a>Usuń
Aby **usunąć** ustawienia użytkownika, zapisy usługi Cloud Shell, takie jak preferowana powłoka, rozmiar czcionki i typ czcionki, uruchom następujące polecenia. Przy następnym uruchomieniu usługi Cloud Shell zostaniesz poproszony o ponowne udostępnienie pliku. 

>[!Note]
> Jeśli usuniesz ustawienia użytkownika, rzeczywisty udział usługi Azure Files nie zostanie usunięty. Przejdź do usługi Azure Files, aby ukończyć tę akcję.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Launch Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia w programie Bash lub PowerShell:

Bash:

  ```
  token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
  curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
  ```

Program PowerShell:

  ```powershell
  $token= ((Invoke-WebRequest -Uri "$env:MSI_ENDPOINT`?resource=https://management.core.windows.net/" -Headers @{Metadata='true'}).content |  ConvertFrom-Json).access_token
  Invoke-WebRequest -Method Delete -Uri https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -Headers @{Authorization = "Bearer $token"}
  ```
## <a name="azure-government-limitations"></a>Ograniczenia platformy Azure dla instytucji rządowych
Usługa Azure Cloud Shell w usłudze Azure dla instytucji rządowych jest dostępna tylko za pośrednictwem witryny Azure portal.

>[!Note]
> Łączenie się z GCC-High lub Government DoD Clouds for Exchange Online nie jest obecnie obsługiwane.
