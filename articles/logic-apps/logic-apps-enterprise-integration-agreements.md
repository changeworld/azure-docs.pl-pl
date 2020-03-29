---
title: Umowy z partnerami handlowymi
description: Tworzenie umów między partnerami handlowymi i zarządzanie nimi przy użyciu aplikacji Azure Logic Apps i pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 521a0ef4053be55e6c7322da5af26ccfc6c844e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790741"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Tworzenie umów handlowych partnerów i zarządzanie nimi w usłudze Azure Logic Apps

*Umowa* [z partnerem](../logic-apps/logic-apps-enterprise-integration-partners.md) 
handlowym pomaga organizacjom i firmom bezproblemowo komunikować się ze sobą, definiując konkretny protokół branżowy do użycia podczas wymiany komunikatów między firmami (B2B). Umowy przynoszą wspólne korzyści, na przykład:

* Umożliwiaj organizacjom wymianę informacji przy użyciu dobrze znanego formatu.
* Zwiększ wydajność podczas przeprowadzania transakcji B2B.
* Są łatwe do tworzenia, zarządzania i używania do tworzenia rozwiązań integracyjnych dla przedsiębiorstw.

W tym artykule pokazano, jak utworzyć umowę AS2, EDIFACT lub X12, której można użyć podczas tworzenia rozwiązań integracyjnych dla przedsiębiorstw dla scenariuszy B2B przy użyciu [pakietu Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md) i aplikacji Azure Logic [Apps.](../logic-apps/logic-apps-overview.md) Po utworzeniu umowy można następnie użyć łączników AS2, EDIFACT lub X12 do wymiany komunikatów B2B.

Aby utworzyć umowy dotyczące wymiany wiadomości RosettaNet, zobacz [Komunikaty Exchange RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, [zarejestruj się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji musi być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerów handlowych](../logic-apps/logic-apps-enterprise-integration-partners.md) utworzonych już na koncie integracyjnym. Umowa wymaga zarówno partnera przyjmującego, jak i partnera gościa. Obaj partnerzy muszą używać tego samego kwalifikatora "tożsamości biznesowej" jako umowy, którą chcesz utworzyć, takiej jak AS2, X12 lub EDIFACT.

* Opcjonalnie: aplikacja logiki, w której chcesz użyć umowy i wyzwalacz, który uruchamia przepływ pracy aplikacji logiki. Aby po prostu utworzyć konto integracji i artefakty B2B, nie potrzebujesz aplikacji logiki. Jednak zanim aplikacja logiki można używać artefaktów B2B na koncie integracji, należy połączyć konto integracji z aplikacji logiki. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Tworzenie umów

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
W głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**. W polu wyszukiwania wpisz "integracja" jako filtr. Z wyników wybierz ten zasób: **Konta integracji**

   ![Znajdź swoje konto integracyjne](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. W obszarze **Konta integracji**wybierz konto integracji, na którym chcesz utworzyć umowę.

   ![Wybierz konto integracji, na którym ma być utworzona umowa](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. W okienku po prawej stronie w obszarze **Komponenty**wybierz **kafelek Umowy.**

   ![Wybierz "Umowy"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. W obszarze **Umowy**wybierz pozycję **Dodaj**. W okienku **Dodawanie** podaj informacje o umowie, na przykład:

   ![Wybierz "Dodaj"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*nazwa umowy*> | Nazwa umowy |
   | **Typ umowy** | Tak | **AS2**, **X12**lub **EDIFACT** | Typ protokołu dla umowy. Podczas tworzenia pliku umowy zawartość w tym pliku musi być zgodna z typem umowy. | |  
   | **Partner gospodarza** | Tak | <*nazwa hosta-partner*> | Partner-host reprezentuje organizację określającą umowę |
   | **Tożsamość hosta** | Tak | <*identyfikator hosta-partnera*> | Identyfikator partnera przyjmującego |
   | **Partner gości** | Tak | <*nazwisko gościa-partnera*> | Partner-gość reprezentuje organizację, która prowadzi interesy z partnerem-gospodarzem |
   | **Tożsamość gościa** | Tak | <*identyfikator gościa-partnera*> | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różna | Różna | Te właściwości określają, jak partner hosta odbiera wszystkie przychodzące wiadomości od partnera gościa w umowie. Aby uzyskać więcej informacji, zobacz odpowiedni typ umowy: <p>- [Ustawienia komunikatów AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatów EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Ustawienia komunikatów X12](logic-apps-enterprise-integration-x12.md) |
   | **Ustawienia wysyłania** | Różna | Różna | Te właściwości określają, jak partner hosta wysyła wszystkie wiadomości wychodzące do partnera gościa w umowie. Aby uzyskać więcej informacji, zobacz odpowiedni typ umowy: <p>- [Ustawienia komunikatów AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatów EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [Ustawienia komunikatów X12](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Po zakończeniu tworzenia umowy na stronie **Dodaj** wybierz pozycję **OK**i wróć do konta integracyjnego.

   Lista **Umów zawiera** teraz nową umowę.

## <a name="edit-agreements"></a>Edytowanie umów

1. W [witrynie Azure portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**.

1. W polu wyszukiwania wpisz "integracja" jako filtr. Z wyników wybierz ten zasób: **Konta integracji**

1. W obszarze **Konta integracji**wybierz konto integracji, które ma umowę, którą chcesz edytować.

1. W okienku po prawej stronie w obszarze **Komponenty**wybierz **kafelek Umowy.**

1. W obszarze **Umowy**wybierz umowę i wybierz pozycję **Edytuj**.

1. Wykonuj zmiany, a następnie zapisuj.

## <a name="delete-agreements"></a>Usuwanie umów

1. W [witrynie Azure portal](https://portal.azure.com)w głównym menu platformy Azure wybierz pozycję **Wszystkie usługi**.

1. W polu wyszukiwania wpisz "integracja" jako filtr. Z wyników wybierz ten zasób: **Konta integracji**

1. W obszarze **Konta integracji**wybierz konto integracji, które ma umowę, którą chcesz usunąć.

1. W okienku po prawej stronie w obszarze **Komponenty**wybierz **kafelek Umowy.**

1. W obszarze **Umowy**wybierz umowę i wybierz pozycję **Usuń**.

1. Potwierdź, że chcesz usunąć wybraną umowę.

## <a name="next-steps"></a>Następne kroki

* [Wymienianie komunikatów AS2](logic-apps-enterprise-integration-as2.md)
* [Wymienianie komunikatów EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Wymienianie komunikatów X12](logic-apps-enterprise-integration-x12.md)
