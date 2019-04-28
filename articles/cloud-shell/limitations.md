---
title: Ograniczenia platformy Azure Cloud Shell | Dokumentacja firmy Microsoft
description: Omówienie ograniczeń usługi Azure Cloud Shell
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: damaerte
ms.openlocfilehash: 8fd88221818d28c227c33719c03e522e815a408b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097058"
---
# <a name="limitations-of-azure-cloud-shell"></a>Ograniczenia dotyczące usługi Azure Cloud Shell

Usługa Azure Cloud Shell ma następujące znane ograniczenia:

## <a name="general-limitations"></a>Ogólne ograniczenia

### <a name="system-state-and-persistence"></a>Stan systemu i stan trwały

Komputer który udostępnia sesję usługi Cloud Shell jest tymczasowy i zostanie odtworzony po sesji jest nieaktywny przez 20 minut. Usługa cloud Shell wymaga udziału plików platformy Azure ma zostać zainstalowany. W rezultacie Twoja subskrypcja musi umożliwiać konfigurowania zasobów magazynu w celu dostępu do usługi Cloud Shell. Inne zagadnienia to:

* Usługa storage zainstalowany tylko modyfikacji w ramach `$Home` katalogu są zachowywane.
* Udziały plików platformy Azure można instalować tylko z poziomu usługi [przypisane region](persisting-shell-storage.md#mount-a-new-clouddrive).
  * W powłoce Bash, uruchom `env` można znaleźć w danym regionie, Ustaw jako `ACC_LOCATION`.

### <a name="browser-support"></a>Obsługa przeglądarek

Usługi cloud Shell obsługuje najnowsze wersje programu Microsoft Edge, program Microsoft Internet Explorer, Google Chrome, Mozilla Firefox i Safari firmy Apple. Safari w trybie prywatnym nie jest obsługiwane.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Dla danego użytkownika może być aktywne tylko jedno powłoki

Użytkownicy mogą uruchamiać tylko jeden rodzaj powłoki w czasie, albo **Bash** lub **PowerShell**. Jednakże może mieć wiele wystąpień powłoki Bash lub programu PowerShell działających w tym samym czasie. Trwa zamienianie między powłoki Bash lub programu PowerShell za pomocą menu powoduje, że Cloud Shell, aby ponownie uruchomić, która kończy istniejącej sesji. Alternatywnie można uruchomić bash wewnątrz programu PowerShell, wpisując `bash`, i programu PowerShell można uruchomić wewnątrz powłoki bash, wpisując `pwsh`.

### <a name="usage-limits"></a>Limity użycia

Usługa cloud Shell jest przeznaczony dla przypadków użycia interaktywne. W rezultacie wszystkie sesje nieinterakcyjnych długotrwałych zostaną zakończone bez ostrzeżenia.

## <a name="bash-limitations"></a>Ograniczenia funkcji bash

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako zwykli użytkownicy bez dostępu "sudo". Żadnej instalacji spoza Twojej `$Home` katalogu nie jest trwały.

### <a name="editing-bashrc-or-profile"></a>Edytowanie .bashrc lub $PROFILE

Uwaga należy podjąć, gdy Edycja .bashrc lub programu PowerShell $PROFILE pliku, w ten sposób może spowodować nieoczekiwane błędy w usłudze Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia dotyczące programu PowerShell

### <a name="azuread-module-name"></a>`AzureAD` Nazwa modułu

`AzureAD` Nazwa modułu jest obecnie `AzureAD.Standard.Preview`, moduł zapewnia taką samą funkcjonalność.

### <a name="sqlserver-module-functionality"></a>`SqlServer` Moduł funkcji

`SqlServer` Modułu uwzględnione w usłudze Cloud Shell zapewnia tylko w wersji wstępnej obsługę programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku po utworzeniu z dysku platformy Azure:

Za pomocą poleceń cmdlet programu PowerShell, użytkownicy nie mogą tworzyć pliki w ramach platformy Azure: dysk. Podczas tworzenia nowych plików przy użyciu innych narzędzi, takich jak vim lub nano, pliki są zapisywane w `$HOME` domyślnie. 

### <a name="gui-applications-are-not-supported"></a>Aplikacje graficznego interfejsu użytkownika nie są obsługiwane.

Jeśli użytkownik uruchamia polecenia, które mogą utworzyć okno dialogowe Windows, jeden zobaczy następujący komunikat o błędzie takich jak: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Duży odstęp po wyświetleniu paska postępu

Jeśli użytkownik wykona akcję, która Wyświetla pasek takie karty postępu ukończenia podczas w `Azure:` dysków jest możliwe, że kursor nie jest prawidłowo i przerwa pojawia się, gdzie poprzednio pasek postępu.

## <a name="next-steps"></a>Kolejne kroki

[Rozwiązywanie problemów z usługi Cloud Shell](troubleshooting.md) <br>
[Przewodnik Szybki start po powłoce Bash](quickstart.md) <br>
[Przewodnik Szybki start po programie PowerShell](quickstart-powershell.md)
