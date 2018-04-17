---
title: Rozwiązywanie problemów powłoki chmury Azure | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z powłoki w chmurze Azure
services: azure
documentationcenter: ''
author: maertendMSFT
manager: angelc
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/22/2018
ms.author: damaerte
ms.openlocfilehash: 7ab344f77ef88ffdc2ff1976d97b0b9aa86aa3fc
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshooting--limitations-of-azure-cloud-shell"></a>Rozwiązywanie problemów z & ograniczenia Azure Cloud powłoki

Następujące znane rozwiązania problemów w powłoce chmury Azure:

## <a name="general-troubleshooting"></a>Rozwiązywanie ogólnych

### <a name="early-timeouts-in-firefox"></a>Wczesne limitów czasu w programie FireFox
- **Szczegóły**: powłoki chmury wykorzystuje Otwórz websocket do przekazania wejścia/wyjścia do przeglądarki. FireFox ma wstępnie zdefiniowane zasady, które można zamknąć protokół websocket przedwcześnie powoduje wczesne limitów czasu w chmurze powłoki.
- **Rozdzielczość**: Otwórz przeglądarkę FireFox i przejdź do "o: config" w polu adresu URL. Wyszukaj "network.websocket.timeout.ping.request" i zmień wartość z zakresu od 0 do 10.

### <a name="storage-dialog---error-403-requestdisallowedbypolicy"></a>Okno dialogowe magazynu — błąd: 403 RequestDisallowedByPolicy
- **Szczegóły**: podczas tworzenia konta magazynu za pośrednictwem chmury powłoki, to nie powiodło się z powodu zasad Azure wprowadzane przez administratora. Zawiera komunikat o błędzie: `The resource action 'Microsoft.Storage/storageAccounts/write' is disallowed by one or more policies.`
- **Rozdzielczość**: Skontaktuj się z administratorem usługi Azure, aby usunąć lub zaktualizować zasady Azure zezwalających na utworzenie magazynu.

### <a name="storage-dialog---error-400-disallowedoperation"></a>Okno dialogowe magazynu — błąd: 400 DisallowedOperation
 - **Szczegóły**: podczas korzystania z subskrypcji usługi Azure Active Directory, nie można utworzyć magazynu.
 - **Rozdzielczość**: Korzystanie z subskrypcji platformy Azure umożliwia tworzenie zasobów magazynu. Subskrypcje platformy Azure AD nie będą mogli tworzyć zasobów platformy Azure.

### <a name="terminal-output---error-failed-to-connect-terminal-websocket-cannot-be-established-press-enter-to-reconnect"></a>Terminal output — błąd: nie można nawiązać połączenia terminal: obiektu websocket nie powiodło się. Naciśnij klawisz `Enter` połączyć się ponownie.
 - **Szczegóły**: powłoki chmury wymaga możliwość ustanowienia połączenia obiektu websocket infrastruktury chmury powłoki.
 - **Rozdzielczość**: Sprawdź skonfigurowano ustawienia sieci, aby umożliwić wysyłanie żądań https i żądania protokołu websocket do domen na *. console.azure.com.

## <a name="bash-troubleshooting"></a>Bash, rozwiązywanie problemów

### <a name="cannot-run-az-login"></a>Nie można uruchomić az logowania

- **Szczegóły**: systemem `az login` nie będzie działać, ponieważ zostali już uwierzytelnieni w ramach konta używane do logowania się do portalu powłoki chmury lub Azure.
- **Rozdzielczość**: Korzystanie z konta, używany do podpisywania lub wyloguj się i ponownego uwierzytelnienia przy użyciu danego konta platformy Azure.

### <a name="cannot-run-the-docker-daemon"></a>Nie można uruchomić demona docker

- **Szczegóły**: powłoki chmury korzysta z kontenera do hostowania środowiska powłoki, w związku z tym systemem demona jest niedozwolone.
- **Rozdzielczość**: Korzystanie z [docker maszyny](https://docs.docker.com/machine/overview/), który jest instalowany domyślnie, aby zarządzać kontenery docker z hostem zdalnym Docker.

## <a name="powershell-troubleshooting"></a>Rozwiązywanie problemów z programu PowerShell

### <a name="no-home-directory-persistence"></a>Trwałość katalogu No $Home

- **Szczegóły**: wszystkie dane tej aplikacji (takich jak: git, vim i inne) zapisuje `$Home` nie jest zachowywane między sesjami programu PowerShell.
- **Rozdzielczość**: W profilu programu PowerShell, należy utworzyć łącze symboliczne do folderu określonej aplikacji w `clouddrive` do $Home.

### <a name="ctrlc-doesnt-exit-out-of-a-cmdlet-prompt"></a>CTRL + C nie powodować wyjście z wiersza polecenia Cmdlet

- **Szczegóły**: podczas próby zamknąć wiersz polecenia Cmdlet `Ctrl+C` monit nie zakończyć.
- **Rozdzielczość**: aby wyjść z wiersza, naciśnij klawisz `Ctrl+C` następnie `Enter`.

### <a name="gui-applications-are-not-supported"></a>Graficzny interfejs użytkownika aplikacji nie są obsługiwane.

- **Szczegóły**: Jeśli użytkownik uruchamia aplikację graficznego interfejsu użytkownika, monit nie zwraca. Na przykład gdy użytkownik klonów prywatne repozytorium GitHub, który jest włączone uwierzytelnianie dwuskładnikowe, ukończenia uwierzytelniania dwuskładnikowego zostanie wyświetlone okno dialogowe.  
- **Rozdzielczość**: Zamknij i ponownie otwórz powłokę.

### <a name="get-help--online-does-not-open-the-help-page"></a>Get-Help - online nie powoduje otwarcia strony pomocy

- **Szczegóły**: Jeśli użytkownik wpisze `Get-Help Find-Module -online`, takich jak jedną zobaczy komunikat o błędzie: `Starting a browser to display online Help failed. No program or browser is associated to open the URI http://go.microsoft.com/fwlink/?LinkID=398574.`
- **Rozdzielczość**: Skopiuj adres url, a następnie otwórz go ponownie ręcznie w przeglądarce.

### <a name="troubleshooting-remote-management-of-azure-vms"></a>Rozwiązywanie problemów z zdalne zarządzanie maszynami wirtualnymi platformy Azure

- **Szczegóły**: z powodu domyślne ustawienia zapory systemu Windows dla usługi WinRM użytkownik może się pojawić następujący błąd: `Ensure the WinRM service is running. Remote Desktop into the VM for the first time and ensure it can be discovered.`
- **Rozdzielczość**: Upewnij się, że maszyna wirtualna jest uruchomiona. Można uruchomić `Get-AzureRmVM -Status` Aby sprawdzić stan maszyny Wirtualnej.  Następnie dodaj nową regułę zapory na Maszynie wirtualnej zdalnego umożliwia nawiązywanie połączeń usługi WinRM z dowolnej podsieci, na przykład

 ``` Powershell
 New-NetFirewallRule -Name 'WINRM-HTTP-In-TCP-PSCloudShell' -Group 'Windows Remote Management' -Enabled True -Protocol TCP -LocalPort 5985 -Direction Inbound -Action Allow -DisplayName 'Windows Remote Management - PSCloud (HTTP-In)' -Profile Public
 ```
 Można użyć [Azure niestandardowe rozszerzenie skryptu](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-customscript) w celu uniknięcia logowania do zdalnego maszyny Wirtualnej do dodawania nowej reguły zapory.
 Powyższy skrypt do pliku, można zapisać powiedz `addfirerule.ps1`i przekaż go do Twojego kontenera magazynu systemu Azure.
 Spróbuj wykonać następujące polecenie:

 ``` Powershell
 Get-AzureRmVM -Name MyVM1 -ResourceGroupName MyResourceGroup | Set-AzureRmVMCustomScriptExtension -VMName MyVM1 -FileUri https://mystorageaccount.blob.core.windows.net/mycontainer/addfirerule.ps1 -Run 'addfirerule.ps1' -Name myextension
 ```

### <a name="dir-caches-the-result-in-azure-drive"></a>`dir` buforuje wynik dysku platformy Azure

- **Szczegóły**: wynik `dir` są buforowane na dysku platformy Azure.
- **Rozdzielczość**: po utworzeniu lub usunięcie zasobu w widoku dysku platformy Azure Uruchom `dir -force` do aktualizacji.

## <a name="general-limitations"></a>Ogólne ograniczenia
Powłoka chmury Azure ma następujące znane ograniczenia:

### <a name="system-state-and-persistence"></a>Stan systemu i trwałości

Komputer udostępniający sesję powłoki chmury jest tymczasowy i zostanie odtworzony po sesja jest nieaktywny przez 20 minut. Chmura powłoki wymaga udziału plików na platformę Azure ma zostać zainstalowany. W związku z tym subskrypcja musi mieć możliwość skonfigurowany dostęp powłoka chmury zasobów magazynu. Inne zagadnienia dotyczące obejmują:

* Z magazynem zainstalowanym, tylko zmiany w `clouddrive` katalogu są zachowywane. W Bash Twoje `$Home` katalogu również jest trwały.
* Udziały plików platformy Azure może być instalowany tylko z poziomu programu [przypisane region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * W Bash, uruchom `env` można znaleźć w Twoim regionie Ustaw jako `ACC_LOCATION`.
* Usługa pliki Azure obsługuje tylko lokalnie nadmiarowego magazynu i kont magazynu geograficznie nadmiarowego.

### <a name="browser-support"></a>Obsługa przeglądarek

Powłoka chmura obsługuje najnowsze wersje Microsoft Edge, programu Microsoft Internet Explorer, Google Chrome, Mozilla Firefox i Apple Safari. Przeglądarka Safari w trybie prywatnym nie jest obsługiwane.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!include [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Dla danego użytkownika mogą być aktywne tylko jedno powłoki

Użytkownicy mogą uruchamiać tylko jeden typ powłoki w czasie, albo **Bash** lub **PowerShell**. Jednak może istnieć wiele wystąpień uruchomione w tym samym czasie Bash lub programu PowerShell. Wymiany między Bash lub środowiska PowerShell powoduje, że chmury powłoki, aby ponownie uruchomić, które mają zakończenie istniejącej sesji.

### <a name="usage-limits"></a>Limity użycia

Powłoka chmury jest przeznaczony dla przypadków użycia interaktywnego. W związku z tym wszystkie długotrwałe nieinterakcyjnym sesje zostaną zakończone bez ostrzeżenia.

## <a name="bash-limitations"></a>Ograniczenia bash

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia zostały ustawione jako normalnych użytkowników bez dostępu do operacji sudo. Każda instalacja poza Twojej `$Home` katalogu nie jest trwały.

### <a name="editing-bashrc"></a>Edytowanie .bashrc

Mają ostrożność w przypadku edycji .bashrc w ten sposób może spowodować nieoczekiwane błędy w chmurze powłoki.

## <a name="powershell-limitations"></a>Ograniczenia programu PowerShell

### <a name="slow-startup-time"></a>Czas uruchamiania powolne

PowerShell w powłoce chmury Azure (wersja zapoznawcza) może potrwać do 60 sekund zainicjować wersji zapoznawczej.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku podczas tworzenia dysku platformy Azure:

Za pomocą poleceń cmdlet programu PowerShell, użytkownicy nie można utworzyć plików w obszarze dysku platformy Azure. Podczas tworzenia nowych plików przy użyciu innych narzędzi, takich jak vim lub nano, pliki są domyślnie zapisywane do folderu C:\Users. 

### <a name="gui-applications-are-not-supported"></a>Graficzny interfejs użytkownika aplikacji nie są obsługiwane.

Jeśli użytkownik uruchamia polecenia, które mogą utworzyć okno dialogowe systemu Windows, takich jak `Connect-AzureAD` lub `Login-AzureRMAccount`, takich jak jedną zobaczy komunikat o błędzie: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

## <a name="gdpr-compliance-for-cloud-shell"></a>Zgodność GDPR powłoki chmury

Powłoka chmury Azure poważnie danych osobistych, dane zebrane i przechowywane przez usługę Azure Cloud powłoki służą do zapewniania wartości domyślne dla środowiska, takie jak niedawno używanych powłoki, preferowany rozmiar czcionki, typ Preferowana czcionka i udziału plików szczegóły który kopii clouddrive. Powinien zostać eksportu lub usunąć te dane, firma Microsoft uwzględniła następujące instrukcje.

### <a name="export"></a>Eksportowanie
W celu **wyeksportować** preferowanych ustawień użytkownika powłoki chmury zapisuje dla Ciebie, takich jak powłoki, rozmiar czcionki i typ czcionki, uruchom następujące polecenia.

1. Uruchamianie Bash w powłoce chmury
2. Uruchom następujące polecenia:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token" -s | jq
```

### <a name="delete"></a>Usuwanie
W celu **usunąć** preferowanych ustawień użytkownika powłoki chmury zapisuje dla Ciebie, takich jak powłoki, rozmiar czcionki i typ czcionki, uruchom następujące polecenia. Przy następnym uruchomieniu powłoki chmury użytkownik jest proszony o dołączeniu udział plików ponownie. 

Rzeczywiste pliki Azure udziału nie zostaną usunięte, jeśli usuniesz ustawień użytkownika przejdź do usługi pliki Azure do ukończenia tego działania.

1. Uruchamianie Bash w powłoce chmury
2. Uruchom następujące polecenia:
```
user@Azure:~$ token="Bearer $(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s | jq -r ".access_token")"
user@Azure:~$ curl -X DELETE https://management.azure.com/providers/Microsoft.Portal/usersettings/cloudconsole?api-version=2017-12-01-preview -H Authorization:"$token"
```
