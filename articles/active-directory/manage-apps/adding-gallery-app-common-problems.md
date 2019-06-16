---
title: Problem podczas dodawania aplikacji galerii usługi Azure AD | Dokumentacja firmy Microsoft
description: Zrozumienie typowych twarzy osób problemy podczas dodawania aplikacji galerii usługi Azure AD i sposoby ich rozwiązywania
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784424"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Problem podczas dodawania aplikacji galerii usługi Azure AD

Ten artykuł zawiera omówienie typowych twarzy osób problemów podczas dodawania aplikacji galerii usługi Azure AD i co można zrobić, aby je rozwiązać.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Po kliknięciu przycisku "Dodaj" i zajęło dużo czasu na pojawiają się w mojej aplikacji

W pewnych okolicznościach może potrwać 1 – 2 minutach (i czasami dłużej) do pojawiają się po dodaniu go do katalogu aplikacji. Chociaż nie jest to normalne oczekiwanej wydajności widać, dodanie aplikacji jest w toku, klikając **powiadomienia** ikonę (dzwonek) w prawym górnym rogu [witryny Azure portal](https://portal.azure.com/) i wyszukiwanie dla **w toku** lub **Ukończono** powiadomień etykietą **dodawania aplikacji.**

Aplikacja nigdy nie zostanie dodany, czy w przypadku wystąpienia błędu podczas klikania **Dodaj** przycisk, zobaczysz **powiadomień** w **błąd** stanu. Jeśli chcesz więcej szczegółów o błędzie, aby dowiedzieć się więcej na temat lub udostępnienia innym specjalistą pomocy technicznej, widać więcej informacji na temat błędu, wykonując kroki opisane w [sposobu wyświetlania szczegółów powiadomienia portalu](#how-to-see-the-details-of-a-portal-notification) sekcji.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Po kliknięciu przycisku "Dodaj" i pojawił się w mojej aplikacji

Czasami ze względu na problemy przejściowe problemy z siecią lub usterkę, dodawania aplikacji nie powiedzie się. Można stwierdzić, dzieje się po kliknięciu **powiadomienia** ikonę (dzwonek) w prawym górnym rogu witryny Azure portal i wyświetlana jest ikona red (!) obok swojej **Dodawanie aplikacji** powiadomień. Oznacza to, że wystąpił błąd podczas tworzenia aplikacji.

Jeśli wystąpi błąd, podczas klikania **Dodaj** przycisk, zobaczysz **powiadomień** w **błąd** stanu. Jeśli chcesz więcej szczegółów o błędzie, aby dowiedzieć się więcej na temat lub udostępnienia innym specjalistą pomocy technicznej, widać więcej informacji na temat błędu, wykonując kroki opisane w [sposobu wyświetlania szczegółów powiadomienia portalu](#how-to-see-the-details-of-a-portal-notification) sekcji.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Nie wiem, jak skonfigurować aplikację, gdy został dodany

Jeśli potrzebujesz pomocy przy zapoznaniu się z aplikacjami, [listę samouczków dotyczących integracji aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) artykuł jest dobrym miejscem do rozpoczęcia.

Oprócz tego [biblioteki dokumentów aplikacji w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index) pomoże Ci dowiedzieć się więcej o logowanie jednokrotne z usługą Azure AD i jak działa.

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Jak wyświetlić szczegóły powiadomieniu portalu

Możesz zobaczyć szczegółowe informacje o wszystkich powiadomień portalu, wykonując poniższe kroki:

1.  Wybierz **powiadomienia** ikonę (dzwonek) w prawym górnym rogu witryny Azure portal

2.  Wybierz powiadomienia w **błąd** stanu (te z czerwonym (!) obok nich).

    >[!NOTE]
    >Nie można kliknąć powiadomienia w **pomyślnie** lub **w toku** stanu.
    >
    >

4.  Skorzystaj z informacji w ramach **szczegóły powiadomienia** Aby poznać więcej szczegółów o problemie.

5.  Jeśli nadal potrzebujesz pomocy, możesz również udostępniać tych informacji, inżynier pomocy technicznej lub grupę produktu, aby uzyskać pomoc dotyczącą danego problemu.

6.  Kliknij przycisk **kopiowania** **ikonę** po prawej stronie **błąd kopiowania** skopiuj wszystkie szczegóły powiadomienia na udostępnianie pomocy technicznej lub produktu inżynier grupy w polu tekstowym.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Jak uzyskać pomoc, wysyłając powiadomienia, szczegółowe informacje ze specjalistą pomocy technicznej

Bardzo ważne jest, że udostępniasz **poniższymi szczegółami** z inżynierem pomocy technicznej, jeśli potrzebujesz pomocy, dzięki czemu mogą one pomóc szybko. Możesz to zrobić w prosty sposób przez **zrobieniem zrzutu ekranu,** lub przez kliknięcie przycisku **ikona błędu kopiowania**, liczba znalezionych na prawo od **błąd kopiowania** pola tekstowego.

## <a name="notification-details-explained"></a>Szczegóły powiadomienia wyjaśniono

Zobacz następujące opisy, aby uzyskać więcej informacji na temat powiadomień.

### <a name="essential-notification-items"></a>Essential Notification Items

- **Tytuł** — opisowy tytuł powiadomienia

  * Przykład — **ustawienia serwera proxy aplikacji**

- **Opis** — opisu co nastąpiło w wyniku operacji

  -   Przykład — **wprowadzony wewnętrzny adres url jest już używana przez inną aplikację**

- **Identyfikator powiadomienia** — Unikatowy identyfikator powiadomienia

  -   Przykład — **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Identyfikator żądania klienta** — identyfikator określonego żądania przez przeglądarkę

  -   Przykład — **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Czas UTC sygnatury** — sygnatura czasowa, w którym wystąpił powiadomienia, w formacie UTC

  -   Przykład — **2017-03-23T19:50:43.7583681Z**

- **Wewnętrzny identyfikator transakcji** — wewnętrzny identyfikator, możemy użyć, aby wyszukać błąd w naszych systemach

  -   Przykład — **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **Nazwa UPN** — użytkownik, który wykonał operację

  -   Przykład — **tperkins\@f128.info**

- **Identyfikator dzierżawy** — Unikatowy identyfikator dzierżawy, który był użytkownika, który wykonał operację

  -   Przykład — **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Identyfikator obiektu użytkownika** — Unikatowy identyfikator użytkownika, który wykonał operację

  -   Przykład — **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Elementy szczegółowe powiadomienia

-   **Nazwa wyświetlana** — **(może być pusta)** bardziej szczegółowe nazwy wyświetlane dla błędu

    -   Przykład — **ustawienia serwera proxy aplikacji**

-   **Stan** — określonych stan powiadomienia

    -   Przykład — **nie powiodło się**

-   **Obiekt o identyfikatorze** — **(może być pusta)** identyfikator obiektu, dla której wykonano operację

    -   Example – **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Szczegóły** — szczegółowy opis co nastąpiło w wyniku operacji

    -   Przykład — **wewnętrzny adres url `https://bing.com/` jest nieprawidłowy, ponieważ jest już używana**

-   **Błąd kopiowania** — kliknij przycisk **ikonę kopiowania** po prawej stronie **błąd kopiowania** skopiuj wszystkie szczegóły powiadomienia na udostępnianie z grupą pomocy technicznej lub produktów w polu tekstowym 
-   inżynier ds

    -   Przykład ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

