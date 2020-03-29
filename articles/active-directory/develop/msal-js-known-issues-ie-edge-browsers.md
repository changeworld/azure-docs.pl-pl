---
title: Problemy z programem Internet Explorer & Microsoft Edge (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o problemach podczas korzystania z biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) w przeglądarkach Internet Explorer i Microsoft Edge.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5ae2dee68ec0da8e8a00d4f01583461462bc196c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696099"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Znane problemy w przeglądarkach Internet Explorer i Microsoft Edge (MSAL.js)

## <a name="issues-due-to-security-zones"></a>Problemy ze względu na strefy bezpieczeństwa
Mieliśmy wiele raportów o problemach z uwierzytelnianiem w IE i Microsoft Edge (od czasu aktualizacji *wersji przeglądarki Microsoft Edge do wersji 40.15063.0.0).* Śledzimy je i poinformowaliśmy zespół Microsoft Edge. Podczas gdy program Microsoft Edge działa nad rozwiązaniem, oto opis często występujących problemów i możliwych rozwiązań, które można zaimplementować.

### <a name="cause"></a>Przyczyna
Przyczyna większości tych problemów jest następująca. Magazyn sesji i magazyn lokalny są podzielone na partycje według stref zabezpieczeń w przeglądarce Microsoft Edge. W tej konkretnej wersji programu Microsoft Edge, gdy aplikacja jest przekierowywane przez strefy, magazyn sesji i magazyn lokalny są czyszczone. W szczególności magazyn sesji jest czyszczony w regularnej nawigacji przeglądarki, a zarówno sesji i magazynu lokalnego są czyszczone w trybie InPrivate przeglądarki. Plik MSAL.js zapisuje określony stan w magazynie sesji i polega na sprawdzaniu tego stanu podczas przepływów uwierzytelniania. Gdy magazyn sesji jest wyczyszczone, ten stan jest tracona i w związku z tym powoduje przerwane doświadczenia.

### <a name="issues"></a>Problemy

- **Nieskończone pętle przekierowania i ponowne ładowanie strony podczas uwierzytelniania**. Gdy użytkownicy logują się do aplikacji w programie Microsoft Edge, są przekierowywani z powrotem ze strony logowania usługi AAD i tkwią w nieskończonej pętli przekierowania, co powoduje powtarzające się ponowne ładowanie strony. Zwykle towarzyszy temu błąd `invalid_state` w magazynie sesji.

- **Nieskończone pozyskiwanie pętli tokenów i błędu AADSTS50058**. Gdy aplikacja uruchomiona w programie Microsoft Edge próbuje uzyskać token dla zasobu, aplikacja może utknąć w nieskończonej pętli wywołania tokenu nabycia wraz z następującym błędem z usługi AAD w śledzenia sieci:

    `Error :login_required; Error description:AADSTS50058: A silent sign-in request was sent but no user is signed in. The cookies used to represent the user's session were not sent in the request to Azure AD. This can happen if the user is using Internet Explorer or Edge, and the web app sending the silent sign-in request is in different IE security zone than the Azure AD endpoint (login.microsoftonline.com)`

- **Okno podręczne nie zamyka się lub jest zablokowane podczas korzystania z logowania za pośrednictwem okna podręcznego do uwierzytelniania**. Podczas uwierzytelniania przez okno podręczne w przeglądarce Microsoft Edge lub IE(InPrivate), po wprowadzeniu poświadczeń i zalogowaniu się, jeśli wiele domen w strefach zabezpieczeń są zaangażowane w nawigacji, okno wyskakujące nie zamyka, ponieważ MSAL.js traci dojście do w oknie podręcznym.  

### <a name="update-fix-available-in-msaljs-023"></a>Aktualizacja: Poprawka dostępna w msal.js 0.2.3
W pliku [MSAL.js 0.2.3](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)zostały wydane poprawki dotyczące problemów z pętlą przekierowania uwierzytelniania. Włącz flagę `storeAuthStateInCookie` w konfiguracji MSAL.js, aby skorzystać z tej poprawki. Domyślnie ta flaga jest ustawiona na false.

Gdy `storeAuthStateInCookie` flaga jest włączona, msal.js użyje plików cookie przeglądarki do przechowywania stanu żądania wymaganego do sprawdzania poprawności przepływów eru.

> [!NOTE]
> Ta poprawka nie jest jeszcze dostępna dla otoki msal-kątowe i msal-angularjs. Ta poprawka nie rozwiązuje problemu z oknami podręcznymi.

Użyj poniższych rozwiązań.

#### <a name="other-workarounds"></a>Inne obejścia
Przed przyjęciem tych obejść należy sprawdzić, czy problem występuje tylko w określonej wersji przeglądarki Microsoft Edge i działa w innych przeglądarkach.  
1. W pierwszym kroku, aby obejść te problemy, upewnij się, że domena aplikacji i wszystkie inne witryny zaangażowane w przekierowania przepływu uwierzytelniania są dodawane jako zaufane witryny w ustawieniach zabezpieczeń przeglądarki, tak aby należały do tej samej strefy zabezpieczeń.
Aby to zrobić, wykonaj następujące kroki:
    - Otwórz **program Internet Explorer** i kliknij **ustawienia** (ikona koła zębatego) w prawym górnym rogu
    - Wybieranie **opcji internetowych**
    - Wybierz kartę **Zabezpieczenia**
    - W obszarze opcji **Zaufane witryny** kliknij przycisk **Witryny** i dodaj adresy URL w oknie dialogowym, które zostanie otwarte.

2. Jak wspomniano wcześniej, ponieważ tylko magazyn sesji jest czyszczony podczas regularnej nawigacji, można skonfigurować msal.js zamiast tego używać magazynu lokalnego. Można to ustawić `cacheLocation` jako parametr konfiguracji podczas inicjowania MSAL.

Uwaga: nie rozwiąże to problemu podczas przeglądania InPrivate, ponieważ zarówno sesja, jak i magazyn lokalny są wyczyszczone.

## <a name="issues-due-to-popup-blockers"></a>Problemy z powodu blokowania wyskakujących okienka

Zdążają przypadki, gdy okna podręczne są blokowane w programie IE lub Microsoft Edge, na przykład gdy podczas uwierzytelniania wieloskładnikowego występuje drugie wyskakujące okienko. Otrzymasz alert w przeglądarce, aby umożliwić wyskakujące okienko raz lub zawsze. Jeśli zdecydujesz się zezwolić, przeglądarka automatycznie otworzy okno `null` podręczne i zwróci dla niego uchwyt. W rezultacie biblioteka nie ma dojścia do okna i nie ma sposobu, aby zamknąć okno podręczne. Ten sam problem nie występuje w Chrome, gdy monituje o zezwolenie na wyskakujące okienka, ponieważ nie otwiera automatycznie okna podręcznego.

Aby **obejść ten problem,** deweloperzy będą musieli zezwolić na wyskakujące okienka w programach IE i Microsoft Edge, zanim zaczną używać aplikacji, aby uniknąć tego problemu.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [używaniu pliku MSAL.js w programie Internet Explorer](msal-js-use-ie-browser.md).
