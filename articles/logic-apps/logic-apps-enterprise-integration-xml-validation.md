---
title: Sprawdzanie poprawności kodu XML dla integracji z systemem B2B Enterprise — Azure Logic Apps
description: Sprawdzanie poprawności kodu XML przy użyciu schematów w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: 7813dcb375ff4a123b1314f8f9db453b1f0b187e
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680246"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Sprawdzanie poprawności kodu XML dla integracji B2B Enterprise w Azure Logic Apps z Pakiet integracyjny dla przedsiębiorstw

Często w scenariuszach B2B partnerzy handlowi w umowie muszą upewnić się, że wymieniane komunikaty są prawidłowe przed rozpoczęciem przetwarzania danych. Można sprawdzać poprawność dokumentów względem wstępnie zdefiniowanego schematu przy użyciu akcji walidacji XML, która jest dostępna w Pakiet integracyjny dla przedsiębiorstw.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Pusta lub istniejąca aplikacja logiki, w której ma zostać użyta akcja walidacji kodu XML. Jeśli dopiero zaczynasz tworzyć aplikacje logiki, zapoznaj [się z tematem Azure Logic Apps](../logic-apps/logic-apps-overview.md) i [Szybki Start: Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure jest połączone z aplikacją logiki, w której planujesz użyć akcji walidacji XML, i zawiera schemat, którego chcesz użyć do walidacji zawartości XML. Zarówno aplikacja logiki, jak i konto integracji muszą istnieć w tej samej lokalizacji lub regionie platformy Azure.

## <a name="add-xml-validation-action"></a>Dodaj akcję walidacji kodu XML

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

1. Jeśli masz pustą aplikację logiki, w Projektancie aplikacji logiki w polu wyszukiwania wprowadź `HTTP request` jako filtr, a następnie wybierz wyzwalacz **po odebraniu żądania HTTP** . W przeciwnym razie przejdź do następnego kroku.

1. W ostatnim kroku w przepływie pracy wybierz pozycję **nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy nad strzałkę, która łączy te kroki, aby pojawił się znak plusa ( **+** ). Wybierz znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **wbudowane**. W polu wyszukiwania wprowadź `xml validation` jako filtr. Z listy Akcje wybierz pozycję **Walidacja kodu XML**.

   ![Znajdź i wybierz akcję "Walidacja XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Aby określić zawartość XML, którą chcesz zweryfikować, kliknij wewnątrz pola Content ( **zawartość** ), aby wyświetlić listę zawartości dynamicznej.

   ![Otwórz listę zawartości dynamicznej](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Lista zawartości dynamicznej zawiera tokeny właściwości, które reprezentują dane wyjściowe z poprzednich kroków w przepływie pracy. Jeśli na liście nie ma oczekiwanej właściwości, sprawdź wyzwalacz lub nagłówek akcji, czy możesz wybrać opcję **Zobacz więcej**.

1. Z listy zawartość dynamiczna wybierz właściwość, która ma zawartość, którą chcesz zweryfikować.

   Ten przykład wybiera **treść** danych wyjściowych z wyzwalacza.

   ![Wybierz zawartość do zweryfikowania](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Aby określić schemat, który ma być używany do walidacji, Otwórz listę **Nazwa schematu** i wybierz schemat sprawdzania poprawności, który został dodany do połączonego konta integracji.

   ![Wybierz schemat do użycia na potrzeby walidacji](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Zapisz aplikację logiki.

   Teraz można skonfigurować weryfikację. W aplikacji Real World możesz chcieć przechowywać zweryfikowane dane w aplikacji biznesowych, takich jak SalesForce. Aby wysłać zweryfikowane dane wyjściowe do usługi Salesforce, Dodaj akcję.

1. Aby przetestować akcję walidacji, można wysłać żądanie wyzwalające przepływ pracy aplikacji logiki.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)