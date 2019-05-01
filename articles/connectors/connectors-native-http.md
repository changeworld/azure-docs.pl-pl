---
title: Łączenie do dowolnego punktu końcowego HTTP w usłudze Azure Logic Apps | Dokumentacja firmy Microsoft
description: Automatyzowanie zadań i przepływów pracy, które komunikują się z dowolnego punktu końcowego HTTP przy użyciu usługi Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e11c6b4d-65a5-4d2d-8e13-38150db09c0b
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 22b21512c78a06f2639ca9339f3b7a20c7f5bfa3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713810"
---
# <a name="call-http-or-https-endpoints-with-azure-logic-apps"></a>Wywoływanie punktów końcowych HTTP lub HTTPS w usłudze Azure Logic Apps

Korzystając z usługi Azure Logic Apps i łącznik protokołu HTTP (Hypertext Transfer) możesz zautomatyzować przepływy pracy, które komunikują się z dowolnego punktu końcowego HTTP lub HTTPS, tworząc aplikacje logiki. Na przykład można monitorować punkt końcowy usługi dla witryny sieci Web. W tym punkcie końcowym, takich jak witryny sieci Web zostanie wyłączona, w przypadku wystąpienia zdarzenia zdarzenie wyzwala przepływ pracy aplikacji logiki i uruchamia określone działania.

Można wyzwalacza HTTP jako pierwszy krok w swojej przepływ pracy sprawdzania lub *sondowania* punktu końcowego zgodnie z ustalonym harmonogramem. W przypadku każdego wyboru wyzwalacz wysyła wywołania lub *żądania* do punktu końcowego. Odpowiedzi punktu końcowego określa, czy wykonywania przepływu pracy aplikacji logiki. Wyzwalacz jest przekazywane wraz z dowolnej zawartości z odpowiedzi na działania w aplikacji logiki. 

Akcja HTTP można użyć innych krokiem w przepływie pracy wywoływania punktu końcowego, jeśli chcesz. Odpowiedzi punktu końcowego określa sposób uruchamiania pozostałych akcji Twój przepływ pracy. 

Na podstawie możliwości docelowy punkt końcowy ten łącznik obsługuje zabezpieczeń TLS (Transport Layer) w wersjach 1.0, 1.1 i 1.2. Usługa Logic Apps negocjuje z punktem końcowym za pośrednictwem za pomocą najwyższej obsługiwana wersja to możliwe. Tak na przykład, jeśli punkt końcowy obsługuje 1.2, łącznik używa 1.2 najpierw. W przeciwnym razie łącznik używa dalej najwyższy obsługiwanej wersji.

Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Adres URL dla docelowego punktu końcowego, który chcesz wybrać 

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, z którym ma zostać wywołana docelowy punkt końcowy, można uruchomić z wyzwalaczem HTTP [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji HTTP, należy uruchomić aplikację logiki z wyzwalaczem.

## <a name="add-http-trigger"></a>Dodawanie wyzwalacza HTTP

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoje pustej aplikacji logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W polu wyszukiwania wprowadź "http" jako filtr. W obszarze listy wyzwalaczy wybierz **HTTP** wyzwalacza. 

   ![Wybieranie wyzwalacza HTTP](./media/connectors-native-http/select-http-trigger.png)

1. Podaj [parametry i wartości wyzwalacza HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger) mają zostać uwzględnione w wywołaniu docelowy punkt końcowy. Skonfiguruj cykl dla jak często ma wyzwalacz, aby sprawdzić docelowy punkt końcowy.

   ![Wprowadź parametry wyzwalacza HTTP](./media/connectors-native-http/http-trigger-parameters.png)

   Aby uzyskać więcej informacji na temat wyzwalacza HTTP, parametry i wartości, zobacz [odwołania do typów wyzwalaczy i akcji](../logic-apps/logic-apps-workflow-actions-triggers.md##http-trigger).

1. Kontynuuj tworzenie przepływu pracy aplikacji logiki, za pomocą akcji, które są uruchamiane po aktywowaniu wyzwalacza.

## <a name="add-http-action"></a>Dodawanie akcji HTTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W ostatnim kroku, której chcesz dodać akcji HTTP, wybierz **nowy krok**. 

   W tym przykładzie aplikacja logiki zaczyna się od wyzwalacza HTTP w pierwszym kroku.

1. W polu wyszukiwania wprowadź "http" jako filtr. W obszarze listy akcji wybierz **HTTP** akcji.

   ![Wybieranie akcji HTTP](./media/connectors-native-http/select-http-action.png)

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus (**+**) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Podaj [parametry i wartości akcji HTTP](../logic-apps/logic-apps-workflow-actions-triggers.md##http-action) mają zostać uwzględnione w wywołaniu docelowy punkt końcowy. 

   ![Wprowadź parametry akcji HTTP](./media/connectors-native-http/http-action-parameters.png)

1. Gdy wszystko będzie gotowe, upewnij się, że zapisywanie aplikacji logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**. 

## <a name="authentication"></a>Authentication

Aby ustawić uwierzytelniania, wybierz **Pokaż opcje zaawansowane** wewnątrz akcję lub wyzwalacz. Aby uzyskać więcej informacji na temat typów uwierzytelniania HTTP wyzwalaczy i akcji, zobacz [odwołania do typów wyzwalaczy i akcji](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
