---
title: Zażądaj procesu i powiadomień e-mail w usłudze Azure AD uprawnień zarządzania — Azure Active Directory
description: Dowiedz się więcej o procesie żądania dla pakietu dostępu i powiadomieniach e-mail, które są wysyłane w Azure Active Directory zarządzania prawami.
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
ms.date: 10/30/2019
ms.author: ajburnle
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e34d2c69cc808552a3b0c604804f3cd2597b379b
ms.sourcegitcommit: fa5ce8924930f56bcac17f6c2a359c1a5b9660c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73199958"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Zażądaj procesu i powiadomień e-mail w usłudze Azure AD uprawnienia do zarządzania

Gdy użytkownik przesyła żądanie do pakietu dostępu, rozpocznie się dostarczenie tego żądania dostępu. Zarządzanie prawami w usłudze Azure AD wysyła powiadomienia e-mail do osób zatwierdzających i osoby żądające w przypadku wystąpienia kluczowych zdarzeń w trakcie procesu. W tym artykule opisano proces żądania i wysyłane powiadomienia e-mail.

## <a name="request-process"></a>Proces żądania

Użytkownik wymagający dostępu do pakietu dostępu może przesłać żądanie dostępu. W zależności od konfiguracji zasad żądanie może wymagać zatwierdzenia. Gdy żądanie zostanie zatwierdzone, proces zacznie przypisywać użytkownikowi dostęp do każdego zasobu w pakiecie dostępu. Na poniższym diagramie przedstawiono przegląd procesu i różne stany:

![Diagram procesu zatwierdzania](./media/entitlement-management-process/request-process.png)

| Stan | Opis |
| --- | --- |
| Złożona | Użytkownik przesyła żądanie. |
| Oczekiwanie na zatwierdzenie | Jeśli zasady dla pakietu dostępu wymagają zatwierdzenia, żądanie przechodzi do oczekującego zatwierdzenia. |
| Wygasłe | Jeśli żadne osoby zatwierdzające nie zatwierdzą żądania w ramach limitu czasu żądania zatwierdzenia, żądanie wygasa. Aby ponowić próbę, użytkownik będzie musiał ponownie przesłać żądanie. |
| Dozwolone | Osoba zatwierdzająca odrzuca żądanie. |
| Approved (Zatwierdzono) | Osoba zatwierdzająca zatwierdza żądanie. |
| Wykonania | Użytkownikowi **nie** przypisano dostępu do wszystkich zasobów w pakiecie dostępu. Jeśli jest to użytkownik zewnętrzny, użytkownik może jeszcze nie uzyskać dostępu do katalogu zasobów i zaakceptował monit o zgodę. |
| Dostarczono | Użytkownikowi przypisano dostęp do wszystkich zasobów w pakiecie dostępu. |
| Dostęp rozszerzony | Jeśli w zasadach są dozwolone rozszerzenia, Użytkownik rozszerzył przypisanie. |
| Dostęp wygasł | Dostęp użytkownika do pakietu dostępu wygasł. Aby uzyskać dostęp ponownie, użytkownik będzie musiał przesłać żądanie. |

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli jesteś osobą zatwierdzającą, wysyłasz powiadomienia pocztą e-mail, gdy musisz zatwierdzić żądanie dostępu i gdy żądanie dostępu zostało zakończone. Jeśli jesteś zleceniodawcą, wysyłane są powiadomienia e-mail, które wskazują stan Twojego żądania.

Na poniższych diagramach przedstawiono, kiedy te powiadomienia e-mail są wysyłane do osób zatwierdzających lub osoby żądającej. Odwołuje się do [tabeli powiadomień e-mail](entitlement-management-process.md#email-notifications-table) , aby znaleźć odpowiednią liczbę powiadomień e-mail wyświetlanych na diagramach.

### <a name="primary-approvers-and-alternate-approvers"></a>Główni osoby zatwierdzające i alternatywne osoby zatwierdzające
Na poniższym diagramie przedstawiono środowisko głównych osób zatwierdzających i alternatywne osoby zatwierdzające oraz powiadomienia e-mail otrzymywane podczas procesu żądania:

![Przepływ procesu zatwierdzania podstawowego i alternatywnego](./media/entitlement-management-process/primary-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Osoby żądające
Na poniższym diagramie przedstawiono środowisko żądających i powiadomienia e-mail otrzymywane podczas procesu żądania:

![Przepływ procesu żądającego](./media/entitlement-management-process/requestor-approval-and-expiration-request-flow.png)

### <a name="email-notifications-table"></a>Tabela powiadomień e-mail
Poniższa tabela zawiera więcej szczegółów na temat każdej z tych powiadomień e-mail. Aby zarządzać tymi wiadomościami e-mail, możesz użyć reguł. Na przykład w programie Outlook można utworzyć reguły przenoszenia wiadomości e-mail do folderu, jeśli temat zawiera wyrazy z tej tabeli:

| # | Temat wiadomości e-mail | Po wysłaniu | Wysłane do |
| --- | --- | --- | --- |
| 1 | Wymagana akcja: Zatwierdź lub Odrzuć przekazane żądanie przez *[Date]* | Ta wiadomość e-mail będzie wysyłana do osoby zatwierdzającej alternatywny etap 1 (po eskalacji żądania) w celu podjęcia odpowiednich działań. | Etap — 1 alternatywny podmiot zatwierdzający |
| 2 | Wymagana akcja: Zatwierdź lub Odmów żądania przez *[Date]* | Ta wiadomość e-mail zostanie wysłana do głównych osób zatwierdzających etap 1, jeśli eskalacja jest wyłączona, aby podjąć działania. | Główny zatwierdzający etap 1 |
| 3 | Przypomnienie: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[osoba żądająca]* | Ta wiadomość e-mail z przypomnieniem będzie wysyłana do głównych osób zatwierdzających etap 1, jeśli eskalacja jest wyłączona, aby podejmować działania, tylko gdy nie zostały jeszcze wykonane działania. | Główny zatwierdzający etap 1 |
| 4 | Zatwierdź lub Odrzuć żądanie przez *[Time]* w dniu *[Date]* | Ta wiadomość e-mail zostanie wysłana do głównych osób zatwierdzających etap 1 (jeśli eskalacja jest włączona) w celu podjęcia odpowiednich działań. | Główny zatwierdzający etap 1 |
| 5 | Przypomnienie o wymaganym akcji: Zatwierdź lub Odrzuć żądanie przez *[Date]* dla *[żądającego]* | Ta wiadomość e-mail z przypomnieniem będzie wysyłana do głównych osób zatwierdzających etap 1, jeśli eskalacja jest włączona, aby podejmować działania, tylko wtedy, gdy nie zostały jeszcze wykonane działania. | Główny zatwierdzający etap 1 |
| 6 | Żądanie wygasło dla elementu *[access_package]* | Ta wiadomość e-mail będzie wysyłana do głównych osób zatwierdzających i/lub zastępców osób zatwierdzających na jeden etap lub na wiele etapów, po upływie tego żądania. | Osoba zatwierdzająca podstawowego etapu 1, etap 1 alternatywnej osoby zatwierdzającej |
| 7 | Żądanie zatwierdzone dla *[żądającej]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do głównych osób zatwierdzających i/lub zastępców osób zatwierdzających, po ukończeniu żądania. | Osoba zatwierdzająca podstawowego etapu 1, etap 1 alternatywnej osoby zatwierdzającej |
| 8 | Żądanie zatwierdzone dla *[żądającej]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do głównych osób zatwierdzających i/lub zastępców osób zatwierdzających, które są jednoetapowe, tylko wtedy, gdy etap 1 zostanie zatwierdzony. | Osoba zatwierdzająca podstawowego etapu 1, etap 1 alternatywnej osoby zatwierdzającej |
| 9 | Żądanie odrzucone do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do osoby żądającej tylko wtedy, gdy jej żądanie zostanie odrzucone | Obiektu żądającego |
| 10 | Twoje żądanie wygasło dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do osoby żądającej na końcu etapu 1, jednego lub wieloetapowego żądania po wygaśnięciu żądania. | Obiektu żądającego |
| 18 | Masz teraz dostęp do programu *[access_package]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych, aby rozpocząć korzystanie z nich. | Obiektu żądającego |
| 19 | Zwiększ dostęp dla *[access_package]* do *[Date]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych przed wygaśnięciem jej dostępu. | Obiektu żądającego |
| 20 | Zakończono dostęp do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do użytkowników końcowych po wygaśnięciu dostępu. | Obiektu żądającego |

### <a name="access-request-emails"></a>Adresy e-mail żądania dostępu

Gdy obiekt żądający przesyła żądanie dostępu do pakietu dostępu skonfigurowanego do żądania zatwierdzenia, wszystkie osoby zatwierdzające dodani do zasad otrzymają powiadomienie e-mail z informacjami dotyczącymi żądania. Szczegóły obejmują imię i nazwisko osoby żądającej, organizacji, daty rozpoczęcia i zakończenia dostępu (o ile zostały podane), uzasadnienie biznesowe, czas przesyłania żądania oraz czas wygaśnięcia żądania.

Wiadomość e-mail zawiera osoby zatwierdzające łącza, które można kliknąć, aby przejść do elementu dostępu lub odrzucić żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby zatwierdzającej, gdy osoba żądająca prześle żądanie dostępu:

![Zatwierdź żądanie uzyskania dostępu do poczty e-mail pakietu](./media/entitlement-management-shared/approver-request-email.png)

Główne osoby zatwierdzające są również wysyłane pocztą e-mail z przypomnieniem dotyczącym podjęcia działań i podejmowania decyzji dotyczących żądania. Poniżej znajduje się Przykładowa wiadomość e-mail z osobami zatwierdzającymi podstawowych powiadomień, które będą powiadamiać o konieczności podjęcia działania:

![Adres e-mail żądania dostępu do przypomnienia](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approver-request-emails"></a>Alternatywne wiadomości e-mail z żądaniem zatwierdzenia

Jeśli przekazywanie do alternatywnych osób zatwierdzających jest włączone, zgodnie z zasadami przekazywania, jeśli żądanie jest nadal oczekujące, żądanie zostanie przesłane dalej. Zastępca osoby zatwierdzającej otrzyma wiadomość e-mail z powiadomieniem, aby zatwierdzić lub odrzucić żądanie. Oto przykładowa wiadomość e-mail z powiadomieniem, że osoby zatwierdzające alternatywne otrzymują:

![Alternatywny adres e-mail żądania osoby zatwierdzającej](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Zarówno podstawowe osoby zatwierdzające, jak i alternatywne osoby zatwierdzające mogą zatwierdzić lub odrzucić żądanie.

### <a name="approved-or-denied-emails"></a>Zatwierdzone lub odrzucone wiadomości e-mail

Osoby żądające są powiadamiane, gdy ich żądanie dostępu zostanie zatwierdzone i dostępne w celu uzyskania dostępu lub gdy żądanie dostępu zostanie odrzucone. Gdy osoba zatwierdzająca odbiera żądanie dostępu przesłane przez żądającego, może zatwierdzić lub odrzucić żądanie dostępu. Osoba zatwierdzająca musi dodać uzasadnienie biznesowe dla swojej decyzji. Oto przykładowa wiadomość e-mail wysyłana do głównych lub alternatywnych osób zatwierdzających po zatwierdzeniu żądania:

![Przejrzyj adres e-mail żądania dostępu](./media/entitlement-management-process/approver-request-email-approved.png)

Gdy żądanie dostępu zostanie zatwierdzone, a dostęp do nich jest inicjowany, do osoby żądającej jest wysyłana wiadomość e-mail z informacją o tym, że mają teraz dostęp do pakietu dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy zostanie udzielony dostęp do pakietu dostępu:

![Wygasły adres e-mail żądania dostępu](./media/entitlement-management-process/requestor-email-approved.png)

W przypadku odrzucenia żądania dostępu do osoby żądającej jest wysyłana wiadomość e-mail z powiadomieniem. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy żądanie dostępu zostanie odrzucone:

![Adres e-mail żądania żądania osoby żądającej](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="expired-access-request-emails"></a>Wygasłe wiadomości e-mail z żądaniem dostępu

Żądania dostępu mogły wygasnąć, jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania. 

Gdy żądanie osiągnie swoją skonfigurowaną datę wygaśnięcia i wygasa, nie może być już zatwierdzone ani odrzucone przez osoby zatwierdzające. Oto przykładowa wiadomość e-mail z powiadomieniem wysyłanym do wszystkich głównych i alternatywnych osób zatwierdzających:

 ![Osoby zatwierdzające utraciły wiadomość e-mail z żądaniem dostępu](./media/entitlement-management-process/approver-request-email-expired.png)

 Powiadomienie e-mail jest również wysyłane do osoby żądającej, powiadamiają o tym, że ich żądanie dostępu wygasło, i że muszą ponownie przesłać żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do osoby żądającej, gdy ich żądanie dostępu wygasło:

![Wiadomość e-mail z żądaniem żądania dostępu wygasła](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Następne kroki

- [Zażądaj dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odrzucanie żądań dostępu](entitlement-management-request-approve.md)
