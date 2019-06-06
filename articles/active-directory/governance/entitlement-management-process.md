---
title: Żądanie procesu i powiadomień e-mail w usłudze Azure AD uprawnienie management (wersja zapoznawcza) — usługi Azure Active Directory
description: Więcej informacji na temat procesu żądania dostępu do pakietu i powiadomienia e-mail są wysyłane w usłudze Azure Active Directory Zarządzanie uprawnieniami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: rolyon
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: aede5e315141251026867f7028ebf989d44da4d5
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473064"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Żądanie procesu i powiadomień e-mail w zarządzanie uprawnieniami w usłudze Azure AD (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie uprawnieniami w usłudze Azure Active Directory (Azure AD) jest obecnie w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gdy użytkownik przesyła żądanie do dostępu do pakietów, proces jest uruchomiony do dostarczenia tego żądania. Zarządzanie uprawnieniami w usłudze Azure AD wysyła również powiadomienia e-mail do osób zatwierdzających i dostępu komputerom zgłaszającym żądania podczas zdarzenia klawiszy zachodzą podczas procesu.

W tym artykule opisano proces żądania oraz powiadomienia e-mail, które są wysyłane.

## <a name="request-process"></a>Przetwarzanie żądania

Użytkownik, który wymaga dostępu do pakietu dostępu można przesłać żądanie dostępu. W zależności od konfiguracji zasad żądania może wymagać zatwierdzenia. Gdy żądanie zostanie zatwierdzone, rozpocznie się proces przydzielać użytkownikom dostęp do każdego zasobu w pakiecie dostępu. Na poniższym diagramie przedstawiono przegląd procesu i różne stany.

![Diagram procesu zatwierdzania](./media/entitlement-management-process/request-process.png)

| Stan | Opis |
| --- | --- |
| Przesłano | Użytkownik przesyła żądanie. |
| Oczekuje na zatwierdzenie | Jeśli zasady dostępu do pakietu wymaga zatwierdzenia, żądanie przenosi do oczekujących na zatwierdzenie. |
| Wygaśnięcie | Jeśli żadne osoby zatwierdzające zatwierdzić żądanie przed upływem limitu czasu żądania zatwierdzenia, żądanie wygaśnie. Aby ponowić próbę, użytkownik będzie musiał ponownie prześlij żądanie. |
| Odmowa dostępu | Osoba zatwierdzająca nie zezwala na żądanie. |
| Approved (Zatwierdzono) | Osoba zatwierdzająca je zatwierdzi żądanie. |
| Dostarczanie | Użytkownik ma **nie** został przypisany dostęp do wszystkich zasobów w pakiecie dostępu. Jeśli jest to użytkownik zewnętrzny, użytkownik jeszcze nie uzyskać dostępu do katalogu zasobów i zaakceptować monit o uprawnienia. |
| Dostarczono | Użytkownikowi przypisano dostęp do wszystkich zasobów w pakiecie dostępu. |
| Dostęp do rozszerzonych | Jeśli rozszerzenia są dozwolone w zasadach, użytkownik rozszerzone przypisania. |
| Dostępu wygasł | Użytkownik ma dostęp do pakietu dostępu wygasł. Aby uzyskać dostęp, użytkownik będzie musiał przesłać żądanie. |

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli jesteś osobą zatwierdzającą, są wysyłane powiadomienia e-mail, gdy musisz najpierw zatwierdzić żądanie dostępu i gdy żądanie dostępu zostało ukończone. Jeśli obiekt żądający, są wysyłane powiadomienia e-mail, które wskazują stan swojego żądania. Na poniższym diagramie przedstawiono gdy te powiadomienia e-mail są wysyłane.

![Uprawnienia zarządzania e-mail procesu](./media/entitlement-management-process/email-notifications.png)

Poniższa tabela zawiera szczegółowe informacje o każdej z tych powiadomień e-mail.

| # | Temat wiadomości e-mail | Podczas wysyłania | Wysłane do |
| --- | --- | --- | --- |
| 1 | Wymagana akcja: Przeglądanie żądania dostępu z *[obiektu żądającego]* do *[package dostępu]* przez *[date]* | Gdy obiekt żądający przesyła żądanie dostępu do pakietu | Wszystkie osoby zatwierdzające |
| 2 | Wymagana akcja: Przeglądanie żądania dostępu z *[obiektu żądającego]* do *[package dostępu]* przez *[date]* | Limit czasu żądania X dni przed zatwierdzeniem | Wszystkie osoby zatwierdzające |
| 3 | Powiadomienia o stanie: *[obiektu żądającego]* na żądanie dostępu do *[package dostępu]* utracił ważność | Gdy osób zatwierdzających nie Zatwierdź lub Odrzuć żądanie dostępu w ramach czas trwania żądania | Obiekt żądający |
| 4 | Powiadomienia o stanie: *[obiektu żądającego]* żądanie dostępu do *[package dostępu]* zostało ukończone | Podczas pierwszej osoby zatwierdzającej zatwierdza lub odrzuca żądanie dostępu | Wszystkie osoby zatwierdzające |
| 5 | Odmowa dostępu do *[package dostępu]* | Gdy obiekt żądający odmówiono dostępu do pakietu dostępu | Obiekt żądający |
| 6 | Masz teraz dostęp do *[package dostępu]*  | Gdy obiekt żądający udzielony dostęp do każdego zasobu w pakiecie dostępu | Obiekt żądający |
| 7 | Dostęp do *[package dostępu]* wygaśnie po upływie X dni | X dni przed żądającego dostępu do pakietu dostępu wygasa | Obiekt żądający |
| 8 | Dostęp do *[package dostępu]* utracił ważność | Kiedy wygaśnie żądającego dostępu do pakietów programu access | Obiekt żądający |

### <a name="access-request-emails"></a>Żądania dostępu w wiadomości e-mail

Gdy obiekt żądający wyśle żądanie dostępu do pakietu dostępu, który jest skonfigurowany do żądania zatwierdzenia, wszystkie osoby zatwierdzające skonfigurowane w zasadach otrzymywać powiadomienia e-mail szczegółowe informacje o żądaniu. Szczegółowe informacje obejmują nazwę podmiotu żądającego, organizacji, dostęp datę początkową i końcową, jeśli podano uzasadnienie biznesowe, gdy żądanie zostało przesłane i w przypadku, gdy żądanie wygaśnie. Wiadomość e-mail zawiera link, w których osoby zatwierdzające mogli zatwierdzać lub odrzucać żądania dostępu. Poniżej przedstawiono przykładowe powiadomienie e-mail, który jest wysyłany do osoby zatwierdzającej, gdy obiekt żądający przesyła żądanie dostępu.

![Wiadomość e-mail z żądaniem dostępu do przeglądu](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Wiadomości e-mail zatwierdzonych lub zabronionych

Obiekty żądające są powiadamiani, gdy żądanie dostępu jest zatwierdzone i dostępne dla lub w przypadku, gdy ich żądanie dostępu zostało odrzucone. Gdy osoba zatwierdzająca otrzyma żądanie dostępu przesłane przez obiekt żądający, oni mogli zatwierdzać lub odrzucać żądania dostępu. Konieczne będzie dodawanie uzasadnienie biznesowe dla ich decyzję osoby zatwierdzającej.

Po zatwierdzeniu żądania dostępu, zarządzanie uprawnieniami rozpoczyna się proces udzielania dostępu do obiektu żądającego do każdego z zasobów w pakiecie dostępu. Po osoby zgłaszającej żądanie ma zostać przyznany dostęp do wszystkich zasobów w pakiecie dostępu, wiadomość e-mail z powiadomieniem są wysyłane do osoby zgłaszającej żądanie, czy ich żądanie dostępu została zatwierdzona i że klienci mają teraz dostęp do pakietu dostępu. Poniżej przedstawiono przykładowe e-mail z powiadomieniem jest wysyłany do obiektu żądającego, gdy uzyska dostęp do pakietu dostępu.

Gdy żądanie dostępu zostało odrzucone, wiadomość e-mail z powiadomieniem są wysyłane do osoby zgłaszającej żądanie. Poniżej przedstawiono przykładowe e-mail z powiadomieniem jest wysyłany do obiektu żądającego, gdy ich żądanie dostępu zostało odrzucone.

### <a name="expired-access-request-emails"></a>Ważność wiadomości e-mail żądania dostępu

Obiekty żądające są powiadamiani, gdy żądanie dostępu wygasł. Gdy obiekt żądający przesyła żądanie dostępu, żądanie ma czas trwania żądania po upływie którego wygaśnie. Jeśli nie istnieją żadne osoby zatwierdzające, którzy przedstawia Zatwierdź/niezezwalania decyzji, żądania w dalszym ciągu pozostają w stanie oczekuje na zatwierdzenie. Gdy żądanie osiąga ten czas trwania wygasania skonfigurowany, żądanie wygaśnie i może nie jest już przydzielane lub nie przez osoby zatwierdzające. W tym przypadku żądanie przechodzi do stanu wygaśnięcia. Wygasłe żądanie nie jest już zostać zatwierdzone lub odrzucone. Wiadomość e-mail z powiadomieniem są wysyłane do osoby zgłaszającej żądanie, który wygasł ich żądania dostępu i należy ponownie przesłać żądanie dostępu. Poniżej przedstawiono przykładowe powiadomienie e-mail wysyłanym do obiektu żądającego, gdy żądanie dostępu wygasł.

![Ważność wiadomości e-mail z żądaniem dostępu](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Kolejne kroki

- [Żądanie dostępu do pakietów programu access](entitlement-management-request-access.md)
- [Zatwierdź lub Odrzuć żądania dostępu](entitlement-management-request-approve.md)
