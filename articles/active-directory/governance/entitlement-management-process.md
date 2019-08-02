---
title: Zażądaj procesu i powiadomień e-mail w usłudze Azure AD uprawnień do zarządzania (wersja zapoznawcza) — Azure Active Directory
description: Dowiedz się więcej o procesie żądania dla pakietu dostępu i powiadomieniach e-mail, które są wysyłane w Azure Active Directory Zarządzanie prawami (wersja zapoznawcza).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb5f322d8bc974274f7f2da7811b124499224635
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2019
ms.locfileid: "68678143"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management-preview"></a>Zażądaj procesu i powiadomień e-mail w usłudze Azure AD uprawnienia do zarządzania (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzanie prawami w usłudze Azure Active Directory (Azure AD) jest obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Gdy użytkownik przesyła żądanie do pakietu dostępu, proces jest uruchamiany w celu dostarczenia tego żądania. Zarządzanie prawami w usłudze Azure AD wysyła również powiadomienia e-mail do osób zatwierdzających i osoby żądające w przypadku wystąpienia kluczowych zdarzeń w trakcie procesu.

W tym artykule opisano proces żądania oraz wysyłane powiadomienia e-mail.

## <a name="request-process"></a>Proces żądania

Użytkownik wymagający dostępu do pakietu dostępu może przesłać żądanie dostępu. W zależności od konfiguracji zasad żądanie może wymagać zatwierdzenia. Gdy żądanie zostanie zatwierdzone, proces zacznie przypisywać użytkownikowi dostęp do każdego zasobu w pakiecie dostępu. Na poniższym diagramie przedstawiono przegląd procesu i różne stany.

![Diagram procesu zatwierdzania](./media/entitlement-management-process/request-process.png)

| Stan | Opis |
| --- | --- |
| Przesłano | Użytkownik przesyła żądanie. |
| Oczekuje na zatwierdzenie | Jeśli zasady dla pakietu dostępu wymagają zatwierdzenia, żądanie przechodzi do oczekującego zatwierdzenia. |
| Wygaśnięcie | Jeśli żadne osoby zatwierdzające nie zatwierdzą żądania w ramach limitu czasu żądania zatwierdzenia, żądanie wygasa. Aby ponowić próbę, użytkownik będzie musiał ponownie przesłać żądanie. |
| Odmówiono | Osoba zatwierdzająca odrzuca żądanie. |
| Zatwierdzono | Osoba zatwierdzająca zatwierdza żądanie. |
| Dostarczanie | Użytkownikowi **nie** przypisano dostępu do wszystkich zasobów w pakiecie dostępu. Jeśli jest to użytkownik zewnętrzny, użytkownik może jeszcze nie uzyskać dostępu do katalogu zasobów i zaakceptował monit o zgodę. |
| Dostarczono | Użytkownikowi przypisano dostęp do wszystkich zasobów w pakiecie dostępu. |
| Dostęp rozszerzony | Jeśli w zasadach są dozwolone rozszerzenia, Użytkownik rozszerzył przypisanie. |
| Dostęp wygasł | Dostęp użytkownika do pakietu dostępu wygasł. Aby uzyskać dostęp ponownie, użytkownik będzie musiał przesłać żądanie. |

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli jesteś osobą zatwierdzającą, wysyłasz powiadomienia pocztą e-mail, gdy musisz zatwierdzić żądanie dostępu i gdy żądanie dostępu zostało zakończone. Jeśli jesteś zleceniodawcą, wysyłane są powiadomienia e-mail, które wskazują stan Twojego żądania. Na poniższym diagramie przedstawiono, kiedy wysyłane są te powiadomienia e-mail.

![Proces poczty e-mail do zarządzania prawami](./media/entitlement-management-process/email-notifications.png)

Poniższa tabela zawiera więcej szczegółów na temat każdej z tych powiadomień e-mail.

| # | Temat wiadomości e-mail | Po wysłaniu | Wysłane do |
| --- | --- | --- | --- |
| 1 | Wymagana akcja: Przeglądanie żądania dostępu od *[żądającego]* do *[pakiet dostępu]* przez *[Date]* | Gdy obiekt żądający wyśle żądanie dotyczące pakietu dostępu | Wszystkie osoby zatwierdzające |
| 2 | Wymagana akcja: Przeglądanie żądania dostępu od *[żądającego]* do *[pakiet dostępu]* przez *[Date]* | X dni przed upływem limitu czasu żądania zatwierdzenia | Wszystkie osoby zatwierdzające |
| 3 | Powiadomienie o stanie: upłynął czas żądania dostępu [ *żądającego]* do *[pakietu dostępu]* | Kiedy osoby zatwierdzające nie zatwierdzają lub nie odrzucają żądania dostępu w czasie trwania żądania | Żądający |
| 4 | Powiadomienie o stanie: zakończono żądanie dostępu *[żądającego]* do *[pakietu dostępu]* | Kiedy pierwsza osoba zatwierdzająca zatwierdza lub odrzuca żądanie dostępu | Wszystkie osoby zatwierdzające |
| 5 | Odmówiono dostępu do *[pakiet dostępu]* | Gdy obiekt żądający odrzucił dostęp do pakietu dostępu | Żądający |
| 6 | Masz teraz dostęp do programu *[dostęp do pakietu]*  | Gdy obiekt żądający uzyska dostęp do każdego zasobu w pakiecie dostępu | Żądający |
| 7 | Dostęp do *[pakietu dostępu]* wygaśnie w ciągu X dni | X dni przed uzyskaniem dostępu do pakietu dostępu przez żądającego | Żądający |
| 8 | Twój dostęp do *[pakietu dostępu]* wygasł | Gdy wygasa dostęp do pakietu dostępu przez żądającego | Żądający |

### <a name="access-request-emails"></a>Adresy e-mail żądania dostępu

Gdy obiekt żądający przesyła żądanie dostępu do pakietu dostępu, który jest skonfigurowany do wymagania zatwierdzenia, wszyscy osoby zatwierdzające skonfigurowani w zasadach otrzymają powiadomienie e-mail ze szczegółami żądania. Szczegóły obejmują imię i nazwisko osoby żądającej, organizację, rozpoczęcie dostępu i datę zakończenia, jeśli zostały podane, uzasadnienie biznesowe, czas przesyłania żądania oraz czas wygaśnięcia żądania. Wiadomość e-mail zawiera link, w którym osoby zatwierdzające mogą zatwierdzać lub odrzucać żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby zatwierdzającej, gdy osoba żądająca prześle żądanie dostępu.

![Przejrzyj adres e-mail żądania dostępu](./media/entitlement-management-shared/email-approve-request.png)

### <a name="approved-or-denied-emails"></a>Zatwierdzone lub odrzucone wiadomości e-mail

Osoby żądające są powiadamiane, gdy ich żądanie dostępu zostanie zatwierdzone i dostępne w celu uzyskania dostępu lub gdy żądanie dostępu zostanie odrzucone. Gdy osoba zatwierdzająca odbiera żądanie dostępu przesłane przez żądającego, może zatwierdzić lub odrzucić żądanie dostępu. Osoba zatwierdzająca musi dodać uzasadnienie biznesowe dla swojej decyzji.

Gdy żądanie dostępu zostanie zatwierdzone, zarządzanie uprawnieniami uruchamia proces udzielenia żądającemu dostępu do każdego z zasobów w pakiecie dostępu. Gdy osoba żądająca uzyska dostęp do każdego zasobu w pakiecie dostępu, do osoby żądającej zostanie wysłane powiadomienie e-mail, że ich żądanie dostępu zostało zatwierdzone oraz że mają teraz dostęp do pakietu dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy zostanie udzielony dostęp do pakietu dostępu.

W przypadku odrzucenia żądania dostępu do osoby żądającej jest wysyłana wiadomość e-mail z powiadomieniem. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy żądanie dostępu zostanie odrzucone.

### <a name="expired-access-request-emails"></a>Wygasłe wiadomości e-mail z żądaniem dostępu

Osoby żądające są powiadamiane, gdy ich żądanie dostępu wygasło. Gdy obiekt żądający przesyła żądanie dostępu, żądanie ma czas trwania żądania, po upływie którego wygasa. Jeśli nie ma osób zatwierdzających, które przesyłają decyzję o zatwierdzeniu/odmowie, żądanie nadal pozostaje w stanie oczekiwania na zatwierdzenie. Gdy żądanie osiągnie skonfigurowany czas wygaśnięcia, żądanie wygasa i nie może być już zatwierdzane lub odrzucane przez osoby zatwierdzające. W takim przypadku żądanie przejdzie w stan wygaśnięcia. Wygasłego żądania nie można już zatwierdzać ani odrzucać. Powiadomienie e-mail jest wysyłane do osoby żądającej, że ich żądanie dostępu wygasło, i że muszą ponownie przesłać żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy ich żądanie dostępu wygasło.

![Wygasły adres e-mail żądania dostępu](./media/entitlement-management-process/email-expired-access-request.png)

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)
