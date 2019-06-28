---
title: Tworzenie i zarządzanie nimi umowy z partnerami handlowymi — Azure Logic Apps
description: Tworzenie i zarządzanie umowami między partnerami handlowymi przy użyciu usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: 4bfee4ec442c9e7b0351b0fd0c6a2b8e163a2541
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67330303"
---
# <a name="create-and-manage-trading-partner-agreements-in-azure-logic-apps"></a>Tworzenie i zarządzanie nimi umowy z partnerami handlowymi w usłudze Azure Logic Apps

A [partner handlowy](../logic-apps/logic-apps-enterprise-integration-partners.md) 
*umowy* pozwala organizacji i przedsiębiorstw, definiując określone standardowym protokołem do użycia podczas wymiany bezproblemowo łączy ze sobą komunikaty Business-to-business (B2B). Umowy zawierają typowe korzyści, na przykład:

* Pozwalają organizacjom wymiany informacji przy użyciu znanego formatu.
* Zwiększ wydajność podczas przeprowadzania transakcji B2B.
* To ułatwia tworzenie i zarządzanie nimi na użytek tworzenie rozwiązań integracji przedsiębiorstwa.

W tym artykule przedstawiono sposób tworzenia AS2, EDIFACT lub X12 umowy, używanego podczas tworzenia rozwiązań integracji dla scenariuszy B2B przedsiębiorstwa przy użyciu [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md) i [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md). Po utworzeniu umowę, możesz następnie użyć AS2, EDIFACT lub X12 łączników na potrzeby wymiany komunikatów B2B.

Aby utworzyć umów wymiany komunikatów RosettaNet, zobacz [wiadomości programu Exchange RosettaNet](../logic-apps/logic-apps-enterprise-integration-rosettanet.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure [Załóż bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) do przechowywania umowy i innych artefaktów B2B. To konto integracji muszą być skojarzone z subskrypcją platformy Azure.

* Co najmniej dwóch [partnerami handlowymi](../logic-apps/logic-apps-enterprise-integration-partners.md) już utworzoną na koncie integracji. Umowa wymaga zarówno partner hosta, jak i partnera gościa. Obu partnerach musi używać tego samego kwalifikator "tożsamość firmy", co umowy, którą chcesz utworzyć, takie jak AS2, X 12 lub EDIFACT.

* Opcjonalnie: Aplikacja logiki, w której chcesz użyć umowy i wyzwalacza uruchamiającego przepływ pracy aplikacji logiki. Po prostu utworzenie konta integracji i artefaktów B2B, nie potrzebujesz aplikacji logiki. Jednak zanim aplikacja logiki można użyć artefaktów B2B na koncie integracji, należy połączyć swoje konto integracji aplikacji logiki. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Utwórz swoją pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-agreements"></a>Tworzenie umów

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
W głównym menu platformy Azure, wybierz **wszystkich usług**. W polu wyszukiwania wprowadź "integration" jako filtr. Z listy wyników wybierz tego zasobu: **Integracja kont**

   ![Znajdź swoje konto integracji](./media/logic-apps-enterprise-integration-agreements/find-integration-accounts.png)

1. W obszarze **kont integracji**, wybierz konto integracji, w którym chcesz utworzyć umowy.

   ![Wybierz miejsce utworzenia umowę konta integracji](./media/logic-apps-enterprise-integration-agreements/select-integration-account.png)

1. W okienku po prawej stronie w obszarze **składniki**, wybierz **umów** kafelka.

   ![Wybierz pozycję "Umowy"](./media/logic-apps-enterprise-integration-agreements/agreement-1.png)

1. W obszarze **umów**, wybierz **Dodaj**. W **Dodaj** okienku, podaj informacje o umowy, na przykład:

   ![Wybieranie pozycji "Dodaj"](./media/logic-apps-enterprise-integration-agreements/agreement-2.png)

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa** | Tak | <*agreement-name*> | Nazwa umowy |
   | **Typ umowy** | Tak | **AS2**, **X12**, lub **EDIFACT** | Typ protokołu dla umowy. Podczas tworzenia pliku umowy zawartości w tym pliku musi być zgodna z typem umowy. | |  
   | **Host Partner** | Tak | <*host-partner-name*> | Partner hosta reprezentuje organizację, która określa umowy |
   | **Tożsamość hosta** | Yes | <*host-partner-identifier*> | Identyfikator partnera hosta |
   | **Partner gościa** | Tak | <*guest-partner-name*> | Partner gościa reprezentuje organizację, która jest prowadzących działalność we współpracy z partnerem hosta |
   | **Tożsamość gościa** | Tak | <*guest-partner-identifier*> | Identyfikator partnera gościa |
   | **Ustawienia odbierania** | Różna | Różna | Te właściwości określają, jak partner hosta odbiera komunikaty przychodzące od partnera gościa w umowie. Aby uzyskać więcej informacji zobacz typ odpowiedniej umowy: <p>- [Ustawienia komunikatu AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatu EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [X12 komunikatu ustawienia](logic-apps-enterprise-integration-x12.md) |
   | **Ustawienia wysyłania** | Różna | Różna | Te właściwości określają, jak partner hosta wysyła wszystkie komunikaty wychodzące do partnera gościa w umowie. Aby uzyskać więcej informacji zobacz typ odpowiedniej umowy: <p>- [Ustawienia komunikatu AS2](../logic-apps/logic-apps-enterprise-integration-as2-message-settings.md) <br>- [Ustawienia komunikatu EDIFACT](logic-apps-enterprise-integration-edifact.md) <br>- [X12 komunikatu ustawienia](logic-apps-enterprise-integration-x12.md) |
   |||||

1. Po zakończeniu tworzenia umowy, na **Dodaj** wybierz **OK**i wrócić do swojego konta integracji.

   **Umów** lista zawiera teraz nowe umowy.

## <a name="edit-agreements"></a>Edytowanie umów

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **wszystkich usług**.

1. W polu wyszukiwania wprowadź "integration" jako filtr. Z listy wyników wybierz tego zasobu: **Integracja kont**

1. W obszarze **kont integracji**, wybrać konto integracji, które ma umowę, którą chcesz edytować.

1. W okienku po prawej stronie w obszarze **składniki**, wybierz **umów** kafelka.

1. W obszarze **umów**, wybierz umowy i wybierz **Edytuj**.

1. Upewnij, a następnie zapisz zmiany.

## <a name="delete-agreements"></a>Usuwanie umowy

1. W [witryny Azure portal](https://portal.azure.com), w menu głównym platformy Azure wybierz **wszystkich usług**.

1. W polu wyszukiwania wprowadź "integration" jako filtr. Z listy wyników wybierz tego zasobu: **Integracja kont**

1. W obszarze **kont integracji**, wybrać konto integracji, które ma umowę do usunięcia.

1. W okienku po prawej stronie w obszarze **składniki**, wybierz **umów** kafelka.

1. W obszarze **umów**, wybierz umowy i wybierz **Usuń**.

1. Upewnij się, że chcesz usunąć wybraną umowę.

## <a name="next-steps"></a>Kolejne kroki

* [Wymienianie komunikatów AS2](logic-apps-enterprise-integration-as2.md)
* [Wymienianie komunikatów EDIFACT](logic-apps-enterprise-integration-edifact.md)
* [Wymiana X12 komunikatów](logic-apps-enterprise-integration-x12.md)
