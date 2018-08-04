---
title: Rozwiązywanie problemów z usługi Azure Cloud Shell z | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi Azure Cloud Shell
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
ms.openlocfilehash: 51d9b8e4299cfa3bc850c7405c27233d678f98b6
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494404"
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Rozwiązywanie problemów z & ograniczenia dotyczące usługi Azure Cloud Shell

Następujące rozwiązania znanych problemów w usłudze Azure Cloud Shell:

## <a name="general-troubleshooting"></a>Rozwiązywanie ogólnych

### <a name="early-timeouts-in-firefox"></a>Wczesne przekroczeń limitu czasu w przeglądarce FireFox

- **Szczegóły**: usługi Cloud Shell korzysta z otwartych websocket, przekazać dane wejściowe i wyjściowe do przeglądarki. FireFox ma wstępnie zdefiniowanych zasad, które można zamknąć websocket przedwcześnie powoduje wczesne przekroczeń limitu czasu w usłudze Cloud Shell.
- **Rozpoznawanie**: Otwórz przeglądarkę FireFox i przejdź do "o: config" w polu adres URL. Wyszukaj "network.websocket.timeout.ping.request" i zmień wartość z zakresu od 0 do 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Okno dialogowe magazynu — błąd: 403 RequestDisallowedByPolicy

- **Szczegóły**: podczas tworzenia konta magazynu w usłudze Cloud Shell jest niepowodzeniem z powodu usługa Azure policy, umieszczone przez administratora. Komunikat o błędzie będzie obejmować: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozpoznawanie**: Skontaktuj się z administratorem platformy Azure, aby usunąć lub zaktualizować zasady platformy Azure, odmawianie tworzenia magazynu.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Okno dialogowe magazynu — błąd: 400 DisallowedOperation

- **Szczegóły**: korzystając z subskrypcji usługi Azure Active Directory, nie można utworzyć magazynu.
- **Rozpoznawanie**: Korzystanie z subskrypcji platformy Azure umożliwia tworzenie zasobów magazynu. Subskrypcje usługi Azure AD nie są możliwe do tworzenia zasobów platformy Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal dane wyjściowe — błąd: nie można połączyć z terminalu: nie może zostać ustanowione protokołu websocket. Naciśnij klawisz `Enter` celu ponownego nawiązania połączenia.
- **Szczegóły**: usługi Cloud Shell wymaga możliwości nawiązania połączenia protokołu websocket w infrastrukturze Cloud Shell.
- **Rozpoznawanie**: Sprawdź zostały skonfigurowane ustawienia sieciowe umożliwiające wysyłanie żądań protokołu https i żądań protokołu websocket z domenami w *. console.azure.com.

### <a name="set-your-cloud-shell-connection-to-support-using-tls-12"></a>Ustaw połączenie usługi Cloud Shell umożliwiającą używanie protokołu TLS 1.2
 - **Szczegóły**: Definiowanie wersji protokołu TLS dla połączenia do usługi Cloud Shell, należy ustawić określone ustawienia przeglądarki.
 - **Rozpoznawanie**: Przejdź do ustawień zabezpieczeń przeglądarki, a następnie zaznacz pole wyboru "Użyj TLS 1.2".

## <a name="bash-troubleshooting"></a>Rozwiązywanie problemów z programu bash

### <a name="cannot-run-the-docker-daemon"></a>Nie można uruchomić demona platformy docker

- **Szczegóły**: usługi Cloud Shell korzysta z kontenera hostowania środowiska powłoki, w wyniku uruchamianie demona jest niedozwolone.
- **Rozpoznawanie**: Korzystanie z [maszyny platformy docker](https://docs.docker.com/machine/overview/), która jest instalowana domyślnie do zarządzania kontenerami platformy docker ze zdalnym hoście platformy Docker.

## <a name="powershell-troubleshooting"></a>Rozwiązywanie problemów z programu PowerShell

### <a name="gui-applications-are-not-supported"></a>Aplikacje graficznego interfejsu użytkownika nie są obsługiwane.

- **Szczegóły**: Jeśli użytkownik uruchamia aplikację graficznego interfejsu użytkownika, monit nie zwraca. Na przykład gdy jeden sklonować prywatne repozytorium GitHub, które ma włączone uwierzytelnianie dwuskładnikowe, ukończenia uwierzytelniania dwuskładnikowego zostanie wyświetlone okno dialogowe.
- **Rozpoznawanie**: Zamknij i otwórz ponownie powłoki.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Rozwiązywanie problemów z zdalnego zarządzania maszynami wirtualnymi platformy Azure

- **Szczegóły**: ze względu na domyślne ustawienia zapory Windows w przypadku usługi WinRM, użytkownik może zostać wyświetlony następujący błąd: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozpoznawanie**: Uruchom `Enable-AzureRmVMPSRemoting` włączyć wszystkie aspekty komunikacji zdalnej programu PowerShell na komputerze docelowym.

### <a name="dir-does-not-update-the-result-in-azure-drive"></a>`dir` nie wpływa na wynik na dysku platformy Azure

- **Szczegóły**: Domyślnie, aby zoptymalizować środowisko użytkownika, wyniki `dir` jest buforowana na dysku platformy Azure.
- **Rozpoznawanie**: po utworzenia, aktualizacji lub usunięcia zasobu platformy Azure Uruchom `dir -force` niezaktualizowanie na dysku platformy Azure.

## <a name="general-limitations"></a>Ogólne ograniczenia

Usługa Azure Cloud Shell ma następujące znane ograniczenia:

### <a name="system-state-and-persistence"></a>Stan systemu i stan trwały

Komputer który udostępnia sesję usługi Cloud Shell jest tymczasowy i zostanie odtworzony po sesji jest nieaktywny przez 20 minut. Usługa cloud Shell wymaga udziału plików platformy Azure ma zostać zainstalowany. W rezultacie Twoja subskrypcja musi umożliwiać konfigurowania zasobów magazynu w celu dostępu do usługi Cloud Shell. Inne zagadnienia to:

- Usługa storage zainstalowany tylko modyfikacji w ramach `clouddrive` katalogu są zachowywane. W powłoce Bash Twoje `$Home` katalogu również utrwalone.
- Udziały plików platformy Azure można instalować tylko z poziomu usługi [przypisane region](persisting-shell-storage.md#mount-a-new-clouddrive).
  - W powłoce Bash, uruchom `env` można znaleźć w danym regionie, Ustaw jako `ACC_LOCATION`.
- Usługa pliki systemu Azure obsługuje tylko lokalnie nadmiarowym i kontami usługi storage geograficznie nadmiarowy.

### <a name="browser-support"></a>Obsługa przeglądarek

Usługa cloud Shell obsługuje najnowsze wersje następujących przeglądarek:

- Microsoft Edge
- Microsoft Internet Explorer
- Google Chrome
- Mozilla Firefox
- Apple Safari
  - Safari w trybie prywatnym nie jest obsługiwane.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Dla danego użytkownika może być aktywne tylko jedno powłoki

Użytkownicy mogą uruchamiać tylko jeden rodzaj powłoki w czasie, albo **Bash** lub **PowerShell**. Jednakże może mieć wiele wystąpień powłoki Bash lub programu PowerShell działających w tym samym czasie. Trwa zamienianie między powłoki Bash lub programu PowerShell powoduje, że usługa Cloud Shell, aby ponownie uruchomić, które kończy się istniejącej sesji.

### <a name="usage-limits"></a>Limity użycia

Usługa cloud Shell jest przeznaczony dla przypadków użycia interaktywne. W rezultacie wszystkie sesje nieinterakcyjnych długotrwałych zostaną zakończone bez ostrzeżenia.

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako zwykli użytkownicy bez dostępu "sudo". Żadnej instalacji spoza Twojej `$Home` katalogu nie jest trwały.

## <a name="bash-limitations"></a>Ograniczenia funkcji bash

### <a name="editing-bashrc"></a>Edytowanie .bashrc

Uwaga należy podjąć, gdy Edycja .bashrc w ten sposób może spowodować nieoczekiwane błędy w usłudze Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia dotyczące programu PowerShell

### <a name="preview-version-of-azuread-module"></a>Wersję zapoznawczą modułu Azure AD

Obecnie `AzureAD.Standard.Preview`, wersję zapoznawczą modułu .NET Standard na podstawie, jest dostępna. Ten moduł dostarcza taką samą funkcjonalność jak `AzureAD`.

### <a name="sqlserver-module-functionality"></a>`SqlServer` Moduł funkcji

`SqlServer` Modułu uwzględnione w usłudze Cloud Shell zapewnia tylko w wersji wstępnej obsługę programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku po utworzeniu z dysku platformy Azure

Za pomocą poleceń cmdlet programu PowerShell, użytkownicy nie mogą tworzyć pliki w obszarze dysk platformy Azure. Podczas tworzenia nowych plików przy użyciu innych narzędzi, takich jak vim lub nano, pliki są zapisywane w `$HOME` domyślnie.

### <a name="commands-that-create-gui-pop-ups-are-not-supported"></a>Polecenia, utworzyć wyskakujące okienka graficznego interfejsu użytkownika, które nie są obsługiwane.

Jeśli użytkownik uruchamia polecenia, które mogą utworzyć okno dialogowe Windows, takich jak `Connect-AzureAD` lub `Connect-AzureRmAccount`, takich jak jedną zobaczy komunikat o błędzie: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="tab-completion-can-throw-psreadline-exception"></a>Uzupełnianie po naciśnięciu tabulatora może zgłosić wyjątek PSReadline

Jeśli ustawiono trybu edycji PSReadline użytkownika Emacs, użytkownik próbuje wyświetlić wszystkie możliwości za pośrednictwem uzupełniania po naciśnięciu tabulatora i wielkość okna jest zbyt mała, aby wyświetlić wszystkie możliwości, PSReadline zgłosi nieobsługiwany wyjątek.

### <a name="large-gap-after-displaying-progress-bar"></a>Duży odstęp po wyświetleniu paska postępu

Jeśli polecenie lub akcji użytkownika jest wyświetlany pasek takie karty postępu ukończenia podczas w `Azure:` dysków jest możliwe, że kursor nie jest prawidłowo i przerwa pojawia się, gdzie poprzednio pasek postępu.

### <a name="random-characters-appear-inline"></a>Wyświetlane losowo wybranych znaków w tekście

Kody sekwencji pozycja kursora, na przykład `5;13R`, może być wyświetlany w danych wejściowych użytkownika. Można ręcznie usunąć znaki.

## <a name="personal-data-in-cloud-shell"></a>Dane osobowe w usłudze Cloud Shell

Usługa Azure Cloud Shell poważnie traktuje informacje osobiste, dane przechwytywane i przechowywane w usłudze Azure Cloud Shell służą do zapewniania ustawień domyślnych do własnych potrzeb, takich jak niedawno używanych powłoki, preferowany rozmiar czcionki, typ czcionki preferowanych i pliku szczegóły udziału Chmura, ponownie dysk. Powinien zostać wyeksportować lub usunąć te dane, użyj poniższych instrukcji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="export"></a>Eksportowanie
W celu **wyeksportować** ustawienia użytkownika usługi Cloud Shell zapisuje dla Ciebie, takie jak preferowana powłoki, rozmiar czcionki i typ czcionki, uruchom następujące polecenia.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia powłoki Bash lub programu PowerShell:

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

### <a name="delete"></a>Usuwanie
W celu **Usuń** ustawień użytkownika w usłudze Cloud Shell zapisuje dla Ciebie, takie jak preferowana powłoki, rozmiar czcionki i typ czcionki, uruchom następujące polecenia. Przy następnym uruchomieniu usługi Cloud Shell użytkownik jest proszony o dołączyć udziału plików ponownie. 

>[!Note]
> Jeśli usuniesz ustawień użytkownika, nie można usunąć rzeczywistego udziału plików platformy Azure. Przejdź do usługi Azure Files, aby ukończyć tę akcję.

1. [![](https://shell.azure.com/images/launchcloudshell.png "Uruchom usługę Azure Cloud Shell")](https://shell.azure.com)
2. Uruchom następujące polecenia powłoki Bash lub programu PowerShell:

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
