---
title: Sprawdzanie poprawności xml dla integracji przedsiębiorstwa B2B
description: Sprawdzanie poprawności kodu XML przy użyciu schematów w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792161"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Sprawdzanie poprawności xml dla integracji przedsiębiorstwa B2B w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa

Często w scenariuszach B2B partnerzy handlowi w umowie muszą upewnić się, że wiadomości, które wymieniają, są ważne przed rozpoczęciem przetwarzania danych. Można sprawdzić poprawność dokumentów względem wstępnie zdefiniowanego schematu przy użyciu akcji sprawdzania poprawności XML, która jest dostępna w pakiecie integracji przedsiębiorstwa.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji, [zarejestruj się, aby korzystać z bezpłatnego konta platformy Azure.](https://azure.microsoft.com/free/)

* Pusta lub istniejąca aplikacja logiki, w której chcesz użyć akcji sprawdzania poprawności XML. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest Usługa Azure Logic Apps](../logic-apps/logic-apps-overview.md) i szybki [start: Utwórz pierwszą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) skojarzone z subskrypcją platformy Azure jest połączone z aplikacją logiki, w której zamierzasz użyć akcji sprawdzania poprawności XML i zawiera schemat, którego chcesz użyć do sprawdzania poprawności zawartości XML. Zarówno aplikacja logiki i konta integracji musi istnieć w tej samej lokalizacji lub regionu platformy Azure.

## <a name="add-xml-validation-action"></a>Dodawanie akcji sprawdzania poprawności XML

1. W [witrynie Azure portal](https://portal.azure.com)otwórz aplikację logiki w projektancie aplikacji logiki.

1. Jeśli masz pustą aplikację logiki, w Logic App Designer `HTTP request` w polu wyszukiwania wprowadź jako filtr i wybierz **wyzwalacz Po odebraniu żądania HTTP.** W przeciwnym razie przejdź do następnego kroku.

1. W obszarze ostatniego kroku w przepływie pracy wybierz pozycję **Nowy krok**.

   Aby dodać akcję między istniejącymi krokami, przesuń wskaźnik myszy na**+** strzałkę łączącą te kroki, tak aby pojawi się znak plus ( ). Zaznacz ten znak plus, a następnie wybierz pozycję **Dodaj akcję**.

1. W obszarze **Wybierz akcję**wybierz pozycję **Wbudowane**. W polu wyszukiwania `xml validation` wprowadź jako filtr. Z listy akcji wybierz pozycję **Sprawdzanie poprawności XML**.

   ![Znajdowanie i wybieranie akcji "Sprawdzanie poprawności XML"](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Aby określić zawartość XML, którą chcesz sprawdzić, kliknij wewnątrz pola **Zawartość,** aby wyświetlić dynamiczną listę zawartości.

   ![Otwórz listę zawartości dynamicznej](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Lista zawartości dynamicznej zawiera tokeny właściwości, które reprezentują dane wyjściowe z poprzednich kroków w przepływie pracy. Jeśli na liście nie jest wyświetlona oczekiwana właściwość, sprawdź nagłówek wyzwalacza lub akcji, czy możesz wybrać **opcję Zobacz więcej**.

1. Z dynamicznej listy zawartości wybierz właściwość zawierającą zawartość, którą chcesz zweryfikować.

   W tym przykładzie wybiera dane wyjściowe **treści** z wyzwalacza.

   ![Wybierz zawartość do sprawdzenia poprawności](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Aby określić schemat, który ma być używany do sprawdzania poprawności, otwórz listę **Nazwa schematu** i wybierz schemat sprawdzania poprawności dodany do połączonego konta integracji.

   ![Wybierz schemat do użycia do sprawdzania poprawności](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Zapisz aplikację logiki.

   Teraz skończysz konfigurować sprawdzanie poprawności. W aplikacji w świecie rzeczywistym możesz chcieć przechowywać zweryfikowane dane w aplikacji biznesowej (LOB), takiej jak SalesForce. Aby wysłać zweryfikowane dane wyjściowe do salesforce, dodaj akcję.

1. Aby przetestować akcję sprawdzania poprawności, można wysłać żądanie wyzwolenia przepływu pracy aplikacji logiki.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [pakiecie integracji dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md)