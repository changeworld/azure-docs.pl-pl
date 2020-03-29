---
title: Ograniczenia usługi Azure Cloud Shell | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 092dccab82326bb9983f11ff64fe50aee7b1084d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951483"
---
# <a name="limitations-of-azure-cloud-shell"></a>Ograniczenia powłoki chmury platformy Azure

Usługa Azure Cloud Shell ma następujące znane ograniczenia:

## <a name="general-limitations"></a>Ogólne ograniczenia

### <a name="system-state-and-persistence"></a>Stan systemu i trwałość

Komputer, który udostępnia sesję Cloud Shell jest tymczasowy i jest odtwarzany po sesji jest nieaktywny przez 20 minut. Usługa Cloud Shell wymaga zainstalowanego udziału plików platformy Azure. W rezultacie subskrypcja musi być w stanie skonfigurować zasoby magazynu, aby uzyskać dostęp do usługi Cloud Shell. Inne zagadnienia obejmują:

* W przypadku zainstalowanego magazynu `$Home` utrwalone są tylko modyfikacje w katalogu.
* Udziały plików platformy Azure można montować tylko z [poziomu przypisanego regionu](persisting-shell-storage.md#mount-a-new-clouddrive).
  * W Bash, `env` uruchom, aby `ACC_LOCATION`znaleźć swój region ustawiony jako .

### <a name="browser-support"></a>Obsługa przeglądarki

Usługa Cloud Shell obsługuje najnowsze wersje programów Microsoft Edge, Microsoft Internet Explorer, Google Chrome, Mozilla Firefox i Apple Safari. Safari w trybie prywatnym nie jest obsługiwane.

### <a name="copy-and-paste"></a>Kopiowanie i wklejanie

[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

### <a name="for-a-given-user-only-one-shell-can-be-active"></a>Dla danego użytkownika tylko jedna powłoka może być aktywna

Użytkownicy mogą uruchamiać tylko jeden typ powłoki naraz, **bash** lub **PowerShell**. Jednak może mieć wiele wystąpień Bash lub PowerShell uruchomiony w tym czasie. Zamiana między Bash lub PowerShell za pomocą menu powoduje ponowne uruchomienie usługi Cloud Shell, która kończy istniejące sesje. Alternatywnie, można uruchomić bash wewnątrz programu `bash`PowerShell wpisując , i można `pwsh`uruchomić powershell wewnątrz bash wpisując .

### <a name="usage-limits"></a>Limity użycia

Usługa Cloud Shell jest przeznaczona do zastosowań interaktywnych. W rezultacie wszystkie długotrwałe sesje nieinterakcyjne są kończyne bez ostrzeżenia.

## <a name="bash-limitations"></a>Ograniczenia bash

### <a name="user-permissions"></a>Uprawnienia użytkowników

Uprawnienia są ustawiane jako stali użytkownicy bez dostępu sudo. Żadna instalacja poza katalogem `$Home` nie jest utrwalona.

### <a name="editing-bashrc-or-profile"></a>Edycja .bashrc lub $PROFILE

Należy zachować ostrożność podczas edytowania pliku $PROFILE programu .bashrc lub programu PowerShell, co może spowodować nieoczekiwane błędy w usłudze Cloud Shell.

## <a name="powershell-limitations"></a>Ograniczenia programu PowerShell

### <a name="azuread-module-name"></a>`AzureAD`nazwa modułu

Nazwa `AzureAD` modułu jest `AzureAD.Standard.Preview`obecnie , moduł zapewnia taką samą funkcjonalność.

### <a name="sqlserver-module-functionality"></a>`SqlServer`funkcjonalność modułu

Moduł `SqlServer` zawarty w usłudze Cloud Shell ma tylko wstępną obsługę programu PowerShell Core. W szczególności `Invoke-SqlCmd` nie jest jeszcze dostępna.

### <a name="default-file-location-when-created-from-azure-drive"></a>Domyślna lokalizacja pliku podczas tworzenia z dysku platformy Azure:

Za pomocą poleceń cmdlet programu PowerShell użytkownicy nie mogą tworzyć plików na dysku Azure: Drive. Gdy użytkownicy tworzą nowe pliki przy użyciu innych narzędzi, takich `$HOME` jak vim lub nano, pliki są domyślnie zapisywane. 

### <a name="gui-applications-are-not-supported"></a>Aplikacje gui nie są obsługiwane

Jeśli użytkownik uruchomi polecenie, które utworzy okno dialogowe systemu Windows, zostanie wyświetlony komunikat o błędzie, taki jak: `Unable to load DLL 'IEFRAME.dll': The specified module could not be found. (Exception from HRESULT: 0x8007007E)`.

### <a name="large-gap-after-displaying-progress-bar"></a>Duża przerwa po wyświetleniu paska postępu

Jeśli użytkownik wykonuje akcję, która wyświetla pasek postępu, na przykład `Azure:` kartę ukończenia podczas pracy na dysku, to jest możliwe, że kursor nie jest ustawiony poprawnie i pojawi się przerwa, gdzie pasek postępu był wcześniej.

## <a name="next-steps"></a>Następne kroki

[Rozwiązywanie problemów z powłoką chmury](troubleshooting.md) <br>
[Przewodnik Szybki start po powłoce Bash](quickstart.md) <br>
[Przewodnik Szybki start po programie PowerShell](quickstart-powershell.md)
