---
title: Znane problemy w przeglądarkach (Biblioteka Microsoft Authentication Library dla języka JavaScript) | Azure
description: Dowiedz się więcej o wiedzieć problemy, korzystając z Biblioteka Microsoft Authentication Library dla języka JavaScript (MSAL.js) za pomocą przeglądarki Internet Explorer i Microsoft Edge.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57ed956ec50c8bac26720a27894c07353928336
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65873903"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-with-msaljs"></a>Znane problemy w przeglądarkach Internet Explorer i Microsoft Edge z MSAL.js

## <a name="issues-due-to-security-zones"></a>Problemy ze względu na strefy zabezpieczeń
Mieliśmy wiele raportów dla problemów z uwierzytelnianiem w przeglądarce IE i Microsoft Edge (ponieważ aktualizacja *wersję przeglądarki Microsoft Edge do 40.15063.0.0*). Firma Microsoft są one śledzenia i powiadomiły zespołu Microsoft Edge. Gdy Microsoft Edge pracuje nad rozwiązaniem tego problemu, w tym miejscu znajduje się opis często występujących problemów i możliwe rozwiązania, które można zaimplementować.

### <a name="cause"></a>Przyczyna
Przyczyną dla większości z tych problemów jest następujący. Przechowywanie sesji i lokalny magazyn są partycjonowane na podstawie strefy zabezpieczeń w przeglądarce Microsoft Edge. W tej konkretnej wersji programu Microsoft Edge gdy aplikacja jest przekierowywane w poszczególnych strefach, sesja magazynu i Magazyn lokalny zostaną wyczyszczone. Przechowywanie sesji nie jest zaznaczona w nawigacji regularne przeglądarki i sesji i lokalny magazyn są usuwane w trybie InPrivate przeglądarki. MSAL.js zapisuje pewnego stanu w magazynie sesji i opiera się na sprawdzanie ten stan podczas przepływów uwierzytelniania. Po wyczyszczeniu pamięci masowej sesji ten stan zostanie utracony i dlatego wyniki w uszkodzona środowiska.

### <a name="issues"></a>Problemy

- **Podczas uwierzytelniania ponownie ładuje pętli nieskończonej przekierowania i strony**. Użytkownicy logują się do aplikacji w programie Microsoft Edge, nastąpi przekierowanie z powrotem ze strony logowania usługi AAD i są zablokowane w pętli nieskończonej przekierowania skutkuje przeładowania powtarzanych strony. Jest to zazwyczaj towarzyszy `invalid_state` błąd w magazynie sesji.

- **Nieskończone uzyskania tokenu pętli i błąd AADSTS50058**. Gdy aplikację działającą w programie Microsoft Edge próbuje uzyskać token dla zasobu, aplikacja może zostać zablokowane w pętli nieskończonej pobierania tokenu wywołania wraz z powodu następującego błędu z usługi AAD w ślad sieci:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Nie zamyka okna podręcznego, lub jest zablokowany podczas uwierzytelniania przy użyciu nazwy logowania za pomocą okna podręcznego**. Podczas uwierzytelniania za pomocą okna podręcznego w programie Microsoft Edge lub IE(InPrivate), po wprowadzeniu poświadczeń i logowania, jeśli obejmuje wiele domen w strefach zabezpieczeń w nawigacji, a w oknie podręcznym nie zamknąć, ponieważ MSAL.js traci dojścia do Okno podręczne.  

    Oto łącza do tych problemów z narzędzie do śledzenia problemów Microsoft Edge:  
    - [Błąd 13861050](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861050/)
    - [Błąd 13861663](https://developer.microsoft.com/en-us/microsoft-edge/platform/issues/13861663/)

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizacja: Poprawka jest dostępna w MSAL.js 0.2.3
Poprawki dla problemów pętli przekierowania uwierzytelniania zostały zwolnione w [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases). Włącz flagę `storeAuthStateInCookie` w pliku konfiguracyjnym MSAL.js, aby móc korzystać z tej poprawki. Domyślnie ta flaga jest ustawiona na wartość false.

Gdy `storeAuthStateInCookie` flaga jest włączona, MSAL.js użyje przeglądarki pliki cookie do przechowywania stanu żądania wymaganych do weryfikacji przepływu uwierzytelniania.

> [!NOTE]
> Ta poprawka nie jest jeszcze dostępne dla otoki biblioteki msal angular i Biblioteka msal angularjs. Ta poprawka nie rozwiążą problemu za pomocą okna podręcznego systemu windows.

Użyj poniższych obejść.

#### <a name="other-workarounds"></a>Inne rozwiązania
Należy koniecznie sprawdzić, czy problem występuje tylko w określonej wersji przeglądarki Microsoft Edge i działa w innych przeglądarkach przed wdrożeniem tego rodzaju obejścia.  
1. Pierwszym krokiem do rozwiązać te problemy, upewnij się, że domena aplikacji, oraz innych lokacji objętych przekierowuje przepływu uwierzytelniania są dodawane jako zaufanych witryn w ustawieniach zabezpieczeń w przeglądarce, dlatego, że należą one do tej samej strefie zabezpieczeń.
Aby to zrobić, wykonaj następujące kroki:
    - Otwórz **programu Internet Explorer** i kliknij pozycję **ustawienia** (ikonę koła zębatego) w prawym górnym rogu
    - Wybierz **Opcje internetowe**
    - Wybierz **zabezpieczeń** kartę
    - W obszarze **Zaufane witryny** opcji, kliknij pozycję **witryn** przycisku i Dodaj adresy URL w oknie dialogowym, która zostanie otwarta.

2. Jak wspomniano wcześniej, ponieważ tylko sesji magazynu zostanie wyczyszczona podczas regularnego nawigacji, można skonfigurować MSAL.js zamiast tego użyć magazynu lokalnego. To może być ustawiona jako `cacheLocation` parametru konfiguracji podczas inicjowania biblioteki MSAL.

Należy zauważyć, że to nie rozwiąże problem w przypadku przeglądania InPrivate, ponieważ Magazyn lokalny i sesji zostaną wyczyszczone.

## <a name="issues-due-to-popup-blockers"></a>Problemy z powodu blokady wyskakujących okienek

Istnieją przypadki, gdy wyskakujące okienka są zablokowane w programu Internet Explorer lub Microsoft Edge, na przykład gdy drugie okno podręczne występuje podczas uwierzytelniania wieloskładnikowego. W przeglądarce, aby umożliwić wyskakującego okienka raz lub nigdy nie zostanie wyświetlony alert. Jeśli chcesz zezwolić na przeglądarce zostanie otwarte okno podręczne, automatycznie i zwraca `null` obsługi dla niego. W wyniku biblioteki nie ma obsługi do okna, a nie istnieje sposób, aby zamknąć okno podręczne. Po wyświetleniu monitu, aby zezwolić na wyskakujące okienka, ponieważ nie automatycznie otworzyć okno podręczne, ten sam problem nie jest realizowane w przeglądarce Chrome.

Jako **obejście**, deweloperzy będą musieli Zezwalaj na wyskakujące okienka w przeglądarce IE i Microsoft Edge, przed rozpoczęciem przy użyciu swoich aplikacji, aby uniknąć tego problemu.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [przy użyciu MSAL.js w programie Internet Explorer](msal-js-use-ie-browser.md).
