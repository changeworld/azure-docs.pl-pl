---
title: Zarządzanie metadanymi artefaktu konta integracji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie lub uzyskiwanie metadanych artefaktów konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60846205"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Zarządzanie metadanymi artefaktu w ramach kont integracji z usługi Azure Logic Apps i pakiet integracyjny dla przedsiębiorstw

Można zdefiniować niestandardowych metadanych dla artefaktów w ramach kont integracji i Uzyskaj tych metadanych podczas wykonywania aplikacji logiki do użycia. Na przykład możesz podać metadanych artefaktów, takich jak partnerów, umów, schematów i map - wszystkie metadane magazynu przy użyciu pary klucz wartość. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawowy [konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) zawierający artefakty, które chcesz dodać metadane, na przykład: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Umowy](logic-apps-enterprise-integration-agreements.md)
  * [Schemat](logic-apps-enterprise-integration-schemas.md)
  * [Mapy](logic-apps-enterprise-integration-maps.md)

* Aplikację logiki, która jest połączona z metadanych konta i artefaktu integracji chcesz użyć. Dowiedz się, jeśli Twoja aplikacja logiki nie jest już połączone, [sposobu łączenia aplikacji logiki z konta integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

  Dowiedz się, jeśli nie masz jeszcze aplikacji logiki, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Dodawanie wyzwalacza i akcje, które chcesz użyć do zarządzania metadane artefaktów. Lub, aby po prostu spróbować testowaliśmy różne rzeczy, Dodaj wyzwalacz, taki jak **żądania** lub **HTTP** do aplikacji logiki.

## <a name="add-metadata-to-artifacts"></a>Dodawanie metadanych artefaktów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure. Znajdź i Otwórz swoje konto integracji.

1. Wybierz artefaktów, które chcesz dodać metadanych, a następnie wybierz polecenie **Edytuj**. Wprowadź szczegóły metadanych dla tego artefaktu, na przykład:

   ![Wprowadź metadane](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

1. Aby wyświetlić te metadane w definicji JavaScript Object Notation (JSON) dla konta integracji, wybierz **Edytuj jako dane JSON** tak, aby zostanie otwarty w edytorze kodu JSON: 

   ![Kod JSON dla metadanych partnera](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Pobierz metadane artefaktów

1. W witrynie Azure portal Otwórz aplikację logiki, która jest połączony z kontem integracji, które chcesz. 

1. W Projektancie aplikacji logiki w przypadku dodawania kroku do pobierania metadanych ostatnią akcję lub wyzwalacz w przepływie pracy, wybierz **nowy krok** > **Dodaj akcję**. 

1. W polu wyszukiwania wprowadź "konto integracji". W polu wyszukiwania wybierz pozycję **Wszystko**. Z listy akcji wybierz następującą akcję: **Wyszukiwanie artefaktu konta integracji — konta integracji**

   ![Wybierz pozycję "Wyszukiwanie artefaktu konta integracji"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Podaj te informacje w artefakcie, że ma zostać znaleziona:

   | Właściwość | Wymagany | Value | Opis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Yes | **Schemat**, **mapy**, **partnera**, **umowy**, lub typu niestandardowego | Typ artefaktu, który ma | 
   | **Nazwa artefaktu** | Yes | <*Nazwa artefaktu*> | Nazwa artefaktu, który ma | 
   ||| 

   Na przykład załóżmy, że chcesz pobrać metadanych dla handlowym artefaktu partnerów:

   ![Wybierz typ artefaktu, a następnie wprowadź nazwę artefaktu](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Dodaj akcję, którą do obsługi tych metadanych, na przykład:

   1. W obszarze **wyszukiwanie artefaktu konta integracji** akcji, wybierz **następny krok**i wybierz **Dodaj akcję**. 

   1. W polu wyszukiwania wprowadź "http". W polu wyszukiwania, wybierz **elementy wbudowane**i wybierz następującą akcję: **PROTOKÓŁ HTTP - HTTP**

      ![Dodawanie akcji HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Podaj informacje dotyczące metadane artefaktów, którymi chcesz zarządzać. 

      Załóżmy, że chcesz pobrać `routingUrl` metadanych, który jest dodawany we wcześniejszej części tego tematu. Poniżej przedstawiono wartości właściwości, których można określić: 

      | Właściwość | Wymagany | Value | Opis | 
      |----------|----------|-------|-------------| 
      | **— Metoda** | Yes | <*Operacja do uruchomienia*> | Operacja HTTP do uruchamiania w artefakcie. Na przykład, ta akcja HTTP używa **UZYSKAĆ** metody. | 
      | **IDENTYFIKATOR URI** | Tak | <*metadata-location*> | Aby uzyskać dostęp do `routingUrl` metadanych z wartości artefaktu możesz pobrać, można użyć wyrażenia, na przykład: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Nagłówki** | Nie | <*header-values*> | Dowolny nagłówek danych wyjściowych z wyzwalacza, który chcesz przekazać do akcji HTTP. Na przykład, aby przekazać wyzwalacza `headers` wartość właściwości: Możesz na przykład użyć wyrażenia: <p>`@triggeroutputs()['headers']` | 
      | **Treść** | Nie | <*body-content*> | Inną zawartość chcesz przekazać za pomocą akcji HTTP `body` właściwości. Ten przykład przekazuje artefaktu `properties` wartości akcji HTTP: <p>1. Kliknij wewnątrz **treści** właściwości, aby wyświetlić listę zawartości dynamicznej. Nie właściwości są wyświetlane, wybierz opcję **Zobacz więcej**. <br>2. Z listy zawartości dynamicznej w obszarze **wyszukiwanie artefaktu konta integracji**, wybierz opcję **właściwości**. | 
      |||| 

      Na przykład:

      ![Określ wartości i wyrażeń dla akcji HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Aby sprawdzić informacje podane dla akcji HTTP, wyświetlanie definicji JSON aplikacji logiki. Na pasku narzędzi Projektanta aplikacji logiki wybierz **widok kodu** więc definicji JSON aplikacji pojawia się na przykład:

      ![Definicji JSON aplikacji logiki](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po przełączeniu do projektanta aplikacji logiki, wszystkie wyrażenia, którego użyłeś pojawiają się rozwiązania, na przykład:

      ![Rozpoznać wyrażenia w Projektancie aplikacji logiki](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o umowach dotyczących](logic-apps-enterprise-integration-agreements.md)
