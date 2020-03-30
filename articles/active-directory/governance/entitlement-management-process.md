---
title: Żądania powiadomień & procesu — zarządzanie uprawnieniami usługi Azure AD
description: Dowiedz się więcej o procesie żądania pakietu dostępu i o wysłaniu powiadomień e-mail w zarządzaniu uprawnieniami usługi Azure Active Directory.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: mamtakumar
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mamkumar
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4ff270977449bb80f97073342dc0c726a3f2316
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128522"
---
# <a name="request-process-and-email-notifications-in-azure-ad-entitlement-management"></a>Żądania powiadomień procesowych i e-mail w zarządzaniu uprawnieniami usługi Azure AD

Gdy użytkownik przesyła żądanie do pakietu dostępu, proces rozpoczyna się dostarczanie tego żądania dostępu. Zarządzanie uprawnieniami usługi Azure AD wysyła powiadomienia e-mail do osób zatwierdzających i żądających, gdy wystąpią kluczowe zdarzenia podczas procesu. W tym artykule opisano proces żądania i powiadomienia e-mail, które są wysyłane.

## <a name="request-process"></a>Proces żądania

Użytkownik, który potrzebuje dostępu do pakietu dostępu, może przesłać żądanie dostępu. W zależności od konfiguracji zasad żądanie może wymagać zatwierdzenia. Po zatwierdzeniu żądania rozpoczyna się proces przypisywania dostępu użytkownika do każdego zasobu w pakiecie dostępu. Na poniższym diagramie przedstawiono przegląd procesu i różnych stanów:

![Diagram procesu zatwierdzania](./media/entitlement-management-process/request-process.png)

| Stan | Opis |
| --- | --- |
| Przesłano | Użytkownik przesyła żądanie. |
| Oczekujące na zatwierdzenie | Jeśli zasady dla pakietu dostępu wymaga zatwierdzenia, żądanie przechodzi do oczekującego zatwierdzenia. |
| Wygasłe | Jeśli żadna osoba zatwierdzająca nie zatwierdzi żądania w przecenie czasu żądania zatwierdzenia, żądanie wygasa. Aby spróbować ponownie, użytkownik będzie musiał ponownie przesłać swoje żądanie. |
| Odmowa | Osoba zatwierdzająca odrzuca żądanie. |
| Approved (Zatwierdzono) | Osoba zatwierdzająca zatwierdza żądanie. |
| Dostarczanie | Użytkownikowi **nie** przypisano dostępu do wszystkich zasobów w pakiecie dostępu. Jeśli jest to użytkownik zewnętrzny, użytkownik może nie mieć jeszcze dostępu do katalogu zasobów. Mogą również nie zaakceptować monitu o wyrażenie zgody. |
| Dostarczono | Użytkownikowi przypisano dostęp do wszystkich zasobów w pakiecie dostępu. |
| Rozszerzony dostęp | Jeśli rozszerzenia są dozwolone w zasadach, użytkownik rozszerzył przypisanie. |
| Program Access wygasł | Użytkownik ma dostęp do pakietu dostępu wygasł. Aby uzyskać dostęp ponownie, użytkownik będzie musiał przesłać żądanie. |

## <a name="email-notifications"></a>Powiadomienia e-mail

Jeśli jesteś osoba zatwierdzająca, wysyłasz powiadomienia e-mail, gdy musisz zatwierdzić żądanie dostępu. Otrzymasz również powiadomienia po zakończeniu żądania dostępu. Wysyłane są również powiadomienia e-mail, które wskazują stan twojego żądania, jeśli jesteś z żądaniem.

Na poniższych diagramach pokazano, gdy te powiadomienia e-mail są wysyłane do osób zatwierdzających lub żądających. Odwołaj się do [tabeli powiadomień e-mail,](entitlement-management-process.md#email-notifications-table) aby znaleźć odpowiedni numer powiadomień e-mail wyświetlanych na diagramach.

### <a name="first-approvers-and-alternate-approvers"></a>Osoby zatwierdzające pierwsze i osoby zatwierdzające
Na poniższym diagramie przedstawiono środowisko osób zatwierdzających i osób zatwierdzających naprzemienne oraz powiadomienia e-mail, które otrzymują podczas procesu żądania:

![Przepływ procesu pierwszego i alternatywnego procesu osób zatwierdzających](./media/entitlement-management-process/first-approvers-and-alternate-with-escalation-flow.png)

### <a name="requestors"></a>Osoby żądające
Na poniższym diagramie przedstawiono środowisko żądań i powiadomienia e-mail, które otrzymują podczas procesu żądania:

![Przepływ procesu żądania lub](./media/entitlement-management-process/requestor-approval-request-flow.png)

### <a name="2-stage-approval"></a>Homologacja dwustopniowa
Na poniższym diagramie przedstawiono środowisko osób zatwierdzających etap 1 i etap 2 oraz powiadomienia e-mail, które otrzymują podczas procesu żądania:

![2-stopniowy przepływ procesu zatwierdzania](./media/entitlement-management-process/2stage-approval-with-request-timeout-flow.png)

### <a name="email-notifications-table"></a>Tabela powiadomień e-mail
Poniższa tabela zawiera więcej szczegółów na temat każdego z tych powiadomień e-mail. Aby zarządzać tymi wiadomościami e-mail, możesz użyć reguł. Na przykład w programie Outlook można utworzyć reguły przenoszenia wiadomości e-mail do folderu, jeśli temat zawiera słowa z tej tabeli:

| # | Temat wiadomości e-mail | Po wysłaniu | Wysłane do |
| --- | --- | --- | --- |
| 1 | Wymagane działanie: Zatwierdzanie lub odrzucanie żądania przesyłania dalej przez *[data]* | Ta wiadomość e-mail zostanie wysłana do alternatywnych osób zatwierdzających stage-1 (po eskalacji żądania) w celu podjęcia działań. | Zastępcy osoby zatwierdzające etap 1 |
| 2 | Wymagane działanie: Zatwierdzanie lub odrzucanie żądania przez *[data]* | Ten e-mail zostanie wysłany do pierwszej osoby zatwierdzającej, jeśli eskalacja jest wyłączona, aby podjąć działania. | Pierwsza osoba zatwierdzająca |
| 3 | Przypomnienie: Zatwierdź lub odmów żądania przez *[data]* dla *[requestor]* | Ten e-mail z przypomnieniem zostanie wysłany do pierwszej osoby zatwierdzającej, jeśli eskalacja jest wyłączona. Wiadomość e-mail prosi ich o podjęcie działań, jeśli jeszcze tego nie zrobili. | Pierwsza osoba zatwierdzająca |
| 4 | Zatwierdzanie lub odrzucanie żądania przez *[time]* w dniu *[data]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej (jeśli eskalacja jest włączona) w celu podjęcia działań. | Pierwsza osoba zatwierdzająca |
| 5 | Przypomnienie wymagane do działania: Zatwierdź lub odmów żądania do *[daty]* dla *[requestor]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do pierwszej osoby zatwierdzającej, jeśli eskalacja jest włączona. Wiadomość e-mail prosi ich o podjęcie działań, jeśli jeszcze tego nie zrobili. | Pierwsza osoba zatwierdzająca |
| 6 | Wniosek wygasł dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i zastępców zatwierdzających etap 1 po wygaśnięciu żądania. | Pierwsza osoba zatwierdzająca, alternatywne osoby zatwierdzające etap 1 |
| 7 | Wniosek zatwierdzony dla *[requestor]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i zastępców zatwierdzających etap 1 po zakończeniu żądania. | Pierwsza osoba zatwierdzająca, alternatywne osoby zatwierdzające etap 1 |
| 8 | Wniosek zatwierdzony dla *[requestor]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do pierwszej osoby zatwierdzającej i zastępców zatwierdzających etap 1 żądania dwuetapowego po zatwierdzeniu żądania etapu 1. | Pierwsza osoba zatwierdzająca, alternatywne osoby zatwierdzające etap 1 |
| 9 | Odmowa *[access_package]* | Ten e-mail zostanie wysłany do zwołyczajnika, gdy jego żądanie zostanie odrzucone | Requestor |
| 10 | Twoja prośba wygasła w *dniu [access_package]* | Ten e-mail zostanie wysłany do osoby ubiegaczej się o zamówienie po zakończeniu pojedynczego lub dwuetapowego żądania. Wiadomość e-mail powiadamia żądającego o wygaśnięciu żądania. | Requestor |
| 11 | Wymagane działanie: Zatwierdzanie lub odrzucanie żądania przez *[data]* | Ten e-mail zostanie wysłany do drugiej osoby zatwierdzającej, jeśli eskalacja jest wyłączona, aby podjąć działania. | Druga osoba zatwierdzająca |
| 12 | Przypomnienie wymagane do działania: Zatwierdź lub odmów żądania do *[data]* | Ten e-mail z przypomnieniem zostanie wysłany do drugiej osoby zatwierdzającej, jeśli eskalacja jest wyłączona. Powiadomienie prosi ich o podjęcie działań, jeśli jeszcze tego nie zrobili. | Druga osoba zatwierdzająca |
| 13 | Wymagane działanie: Zatwierdzanie lub odrzucanie żądania przez *[data]* dla *[requestor]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest włączona, w celu podjęcia działań. | Druga osoba zatwierdzająca |
| 14 | Przypomnienie wymagane do działania: Zatwierdź lub odmów żądania do *[daty]* dla *[requestor]* | Ta wiadomość e-mail z przypomnieniem zostanie wysłana do drugiej osoby zatwierdzającej, jeśli eskalacja jest włączona. Powiadomienie prosi ich o podjęcie działań, jeśli jeszcze tego nie zrobili. | Druga osoba zatwierdzająca |
| 15 | Wymagane działanie: Zatwierdzanie lub odrzucanie żądania przesyłania dalej przez *[data]* | Ta wiadomość e-mail zostanie wysłana do alternatywnych osób zatwierdzających etap 2, jeśli eskalacja jest włączona, aby podjąć działania. | Zastępcy osoby zatwierdzające etap 2 |
| 16 | Wniosek zatwierdzony dla *[requestor]* do *[access_package]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej i zastępców zatwierdzających etap 2 po zatwierdzeniu żądania. | Druga osoba zatwierdzająca, alternatywne osoby zatwierdzające etap 2 |
| 17 | Wniosek wygasł dla *[access_package]* | Ta wiadomość e-mail zostanie wysłana do drugiej osoby zatwierdzającej lub alternatywnych osób zatwierdzających po wygaśnięciu żądania. | Druga osoba zatwierdzająca, zastępcy zatwierdzający etap 2 |
| 18 | Masz teraz dostęp do *[access_package]* | Ten e-mail zostanie wysłany do użytkowników końcowych, aby rozpocząć korzystanie z ich dostępu. | Requestor |
| 19 | Rozszerzenie dostępu dla *[access_package]* o *[date]* | Ten e-mail zostanie wysłany do użytkowników końcowych przed wygaśnięciem ich dostępu. | Requestor |
| 20 | Dostęp został zakończony dla *[access_package]* | Ten e-mail zostanie wysłany do użytkowników końcowych po wygaśnięciu ich dostępu. | Requestor |

### <a name="access-request-emails"></a>Dostęp do wiadomości e-mail z prośbami o dostęp

Gdy osoba żądająca przesyła żądanie dostępu dla pakietu dostępu skonfigurowanego tak, aby wymagała zatwierdzenia, wszystkie osoby zatwierdzające dodane do zasad otrzymają powiadomienie e-mail ze szczegółami żądania. Szczegóły w wiadomości e-mail obejmują: organizację nazwy żądacza i uzasadnienie biznesowe; oraz żądaną datę rozpoczęcia i zakończenia dostępu (jeśli jest to podane). Szczegóły będą również zawierać, kiedy wniosek został złożony i kiedy wniosek wygaśnie.

Wiadomość e-mail zawiera link, który osoby zatwierdzające można kliknąć, aby przejść do mojego dostępu, aby zatwierdzić lub odrzucić żądanie dostępu. Oto przykładowe powiadomienie e-mail wysyłane do pierwszej osoby zatwierdzającej lub drugiej osoby zatwierdzającej (jeśli jest włączone zatwierdzanie dwuetapowe) w celu wypełnienia żądania dostępu:

![Zatwierdzanie żądania dostępu do wiadomości e-mail pakietu](./media/entitlement-management-shared/approver-request-email.png)

Osoby zatwierdzające mogą również otrzymywać wiadomość e-mail z przypomnieniem. Wiadomość e-mail prosi osobę zatwierdzaącą o podjęcie decyzji w sprawie żądania. Oto przykładowe powiadomienie e-mail, które osoba zatwierdzająca otrzymuje, aby przypomnieć im o działaniu:

![Wiadomość e-mail z prośbą o dostęp do przypomnienia](./media/entitlement-management-process/approver-access-request-reminder-email.png)

### <a name="alternate-approvers-request-emails"></a>Alternatywne osoby zatwierdzające żądają wiadomości e-mail

Jeśli ustawienie alternatywnych osób zatwierdzających jest włączone, a żądanie jest nadal oczekujące, zostanie przekazane dalej. Osoby zatwierdzające inne osoby otrzymają wiadomość e-mail z prośbą o zatwierdzenie lub odrzucenie żądania. Alternatywne osoby zatwierdzające można włączyć w etapie 1 i etapie 2. Oto przykładowy e-mail powiadomienia, które otrzymują alternatywni osoby zatwierdzające:

![Alternatywne osoby zatwierdzające żądają wiadomości e-mail](./media/entitlement-management-process/alternate-approver-email-fwd-request.png)

Zarówno osoba zatwierdzająca, jak i osoby zatwierdzające mogą zatwierdzać lub odrzucać żądanie.

### <a name="approved-or-denied-emails"></a>Zatwierdzone lub odrzucone wiadomości e-mail

 Gdy osoba zatwierdzająca otrzyma żądanie dostępu przesłane przez żądacza, może zatwierdzić lub odrzucić żądanie dostępu. Osoba zatwierdzająca musi dodać uzasadnienie biznesowe swojej decyzji. Oto przykładowy adres e-mail wysłany do osób zatwierdzających i osób zatwierdzających po zatwierdzeniu żądania:

![Zatwierdzone żądanie dostępu do wiadomości e-mail pakietu](./media/entitlement-management-process/approver-request-email-approved.png)

Po zatwierdzeniu żądania dostępu i ich dostęp jest aprowizowana, powiadomienie e-mail jest wysyłane do żądacza, że mają teraz dostęp do pakietu dostępu. Oto przykładowe powiadomienie e-mail, które jest wysyłane do żądacza, gdy zostanie im przyznany dostęp do pakietu dostępu:

![Zatwierdzona wiadomość e-mail z żądaniem dostępu lub żądaniem dostępu](./media/entitlement-management-process/requestor-email-approved.png)

Gdy żądanie dostępu zostanie odrzucone, powiadomienie e-mail jest wysyłane do żądacza. Oto przykładowe powiadomienie e-mail wysyłane do żądacza po odrzuceniu żądania dostępu:

![Odmowa żądania wiadomości e-mail](./media/entitlement-management-process/requestor-email-denied.png)

### <a name="2-stage-approval-access-request-emails"></a>2-stopniowe wiadomości e-mail z prośbą o dostęp do zatwierdzenia

Jeśli dwuetapowe zatwierdzanie jest włączone, co najmniej dwie osoby zatwierdzające muszą zatwierdzić żądanie, po jednym z każdego etapu, zanim żądający będzie mógł uzyskać dostęp.

Podczas etapu 1 pierwsza osoba zatwierdzająca otrzyma wiadomość e-mail z prośbą o dostęp i podejmie decyzję. Jeśli zatwierdzą żądanie, wszystkie osoby zatwierdzające pierwsze i osoby zatwierdzające w etapie 1 (jeśli eskalacja jest włączona) otrzymają powiadomienie, że etap 1 jest zakończony. Oto przykładowy adres e-mail powiadomienia wysyłanego po zakończeniu etapu 1:

![2-stopniowa wiadomość e-mail z prośbą o dostęp](./media/entitlement-management-process/approver-request-email-2stage.png)

Po pierwszym lub alternatywnym zatwierdzeniu żądania w etapie 1 rozpoczyna się etap 2. Podczas etapu 2 druga osoba zatwierdzająca otrzyma wiadomość e-mail z powiadomieniem o żądaniu dostępu. Po drugiej osoby zatwierdzającej lub alternatywnych osób zatwierdzających w etapie 2 (jeśli eskalacja jest włączona) zdecydować o zatwierdzeniu lub odrzuceniu żądania, wiadomości e-mail powiadomień są wysyłane do pierwszej i drugiej osoby zatwierdzającej i wszystkich alternatywnych osób zatwierdzających w etapie 1 i etapie 2, a także do żądających.

### <a name="expired-access-request-emails"></a>Wygasłe wiadomości e-mail z prośbą o dostęp

Żądania dostępu mogą wygasnąć, jeśli żadna osoba zatwierdzająca nie zatwierdziła lub nie odrzuciła żądania. 

Gdy żądanie osiągnie skonfigurowaną datę wygaśnięcia i wygaśnie, nie może już zostać zatwierdzone lub odrzucone przez osoby zatwierdzające. Oto przykładowy e-mail z powiadomieniem wysłanym do wszystkich pierwszych, drugich (jeśli włączone jest zatwierdzanie dwuetapowe) i alternatywnych osób zatwierdzających:

![Wiadomość e-mail z wygasłym żądaniem dostępu osoby zatwierdzającej](./media/entitlement-management-process/approver-request-email-expired.png)

Powiadomienie e-mail jest również wysyłane do żądającego, informując go, że jego żądanie dostępu wygasło i że muszą ponownie przesłać żądanie dostępu. Na poniższym diagramie przedstawiono środowisko żądacza i powiadomienia e-mail, które otrzymują, gdy żądają rozszerzenia dostępu:

![Żądanie lub rozszerzenie przepływu procesu dostępu](./media/entitlement-management-process/requestor-expiration-request-flow.png) 

Oto przykładowe powiadomienie e-mail wysyłane do żądacza po wygaśnięciu żądania dostępu:

![Wiadomość e-mail z żądaniem lub wygasłym żądaniem dostępu](./media/entitlement-management-process/requestor-email-request-expired.png)

## <a name="next-steps"></a>Następne kroki

- [Żądanie dostępu do pakietu dostępu](entitlement-management-request-access.md)
- [Zatwierdzanie lub odmawianie żądań dostępu](entitlement-management-request-approve.md)
