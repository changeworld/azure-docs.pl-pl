---
title: Zarządzanie metadanymi artefaktów konta integracji
description: Dodawanie lub pobieranie metadanych artefaktów z kont integracji w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 01/17/2019
ms.openlocfilehash: bc119f1ce8efb821781dabfb9dd259cc5c8d9c23
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792471"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Zarządzanie metadanymi artefaktów na kontach integracji za pomocą usługi Azure Logic Apps i pakietu Enterprise Integration Pack

Można zdefiniować niestandardowe metadane artefaktów na kontach integracji i uzyskać te metadane w czasie wykonywania aplikacji logiki do użycia. Na przykład można podać metadane artefaktów, takich jak partnerzy, umowy, schematy i mapy — wszystkie metadane magazynu przy użyciu par klucza wartości. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawowe [konto integracji,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) które ma artefakty, w których chcesz dodać metadane, na przykład: 

  * [Partner](logic-apps-enterprise-integration-partners.md)
  * [Umowy](logic-apps-enterprise-integration-agreements.md)
  * [Schemat](logic-apps-enterprise-integration-schemas.md)
  * [Mapę](logic-apps-enterprise-integration-maps.md)

* Aplikacja logiki, która jest połączona z kontem integracji i metadanymi artefaktów, których chcesz użyć. Jeśli aplikacja logiki nie jest jeszcze połączona, dowiedz się, [jak połączyć aplikacje logiki z kontami integracji.](logic-apps-enterprise-integration-create-integration-account.md#link-account) 

  Jeśli nie masz jeszcze aplikacji logiki, dowiedz się, [jak tworzyć aplikacje logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Dodaj wyzwalacz i akcje, których chcesz użyć do zarządzania metadanymi artefaktów. Możesz też wypróbować elementy, dodaj wyzwalacz, taki jak **Żądanie** lub **HTTP,** do aplikacji logiki.

## <a name="add-metadata-to-artifacts"></a>Dodawanie metadanych do artefaktów

1. Zaloguj się do <a href="https://portal.azure.com" target="_blank">witryny Azure Portal</a> przy użyciu poświadczeń konta Azure. Znajdź i otwórz swoje konto integracyjne.

1. Wybierz artefakt, w którym chcesz dodać metadane, a następnie wybierz pozycję **Edytuj**. Wprowadź szczegóły metadanych dla tego artefaktu, na przykład:

   ![Wprowadzanie metadanych](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **OK**.

1. Aby wyświetlić te metadane w definicji notacji obiektu JavaScript (JSON) dla konta integracji, wybierz pozycję **Edytuj jako JSON,** aby otworzyć edytor JSON: 

   ![JSON dla metadanych partnera](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>Pobierz metadane artefaktów

1. W witrynie Azure portal otwórz aplikację logiki, która jest połączona z kontem integracji, które chcesz. 

1. Jeśli dodajesz krok do uzyskania metadanych w obszarze wyzwalacza lub ostatniej akcji w przepływie pracy, wybierz pozycję **Nowy krok** > **Dodaj akcję.** 

1. W polu wyszukiwania wpisz "konto integracji". W polu wyszukiwania wybierz pozycję **Wszystko**. Z listy akcji wybierz tę akcję: **Wyszukiwanie artefaktów konta integracji — konto integracji**

   ![Wybierz opcję "Wyszukiwanie artefaktów konta integracji"](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. Podaj te informacje dla artefaktu, który chcesz znaleźć:

   | Właściwość | Wymagany | Wartość | Opis | 
   |----------|---------|-------|-------------| 
   | **Typ artefaktu** | Tak | **Schemat**, **Mapa**, **Partner**, **Umowa**, lub typ niestandardowy | Typ artefaktu, który chcesz | 
   | **Nazwa artefaktu** | Tak | <*nazwa artefaktu*> | Nazwa artefaktu, który chcesz | 
   ||| 

   Załóżmy na przykład, że chcesz uzyskać metadane artefaktu partnera handlowego:

   ![Wybierz typ artefaktu i wprowadź nazwę artefaktu](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. Dodaj akcję, która ma być obsługiwana do obsługi tych metadanych, na przykład:

   1. W obszarze Akcja **wyszukiwania artefaktów konta integracji** wybierz pozycję **Następny krok**i wybierz pozycję **Dodaj akcję**. 

   1. W polu wyszukiwania wpisz "http". W polu wyszukiwania wybierz pozycję **Wbudowane**i wybierz tę akcję: **HTTP - HTTP**

      ![Dodaj akcję HTTP](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. Podaj informacje dotyczące metadanych artefaktów, którymi chcesz zarządzać. 

      Załóżmy na przykład, `routingUrl` że chcesz uzyskać metadane, które są dodawane wcześniej w tym temacie. Oto wartości właściwości, które można określić: 

      | Właściwość | Wymagany | Wartość | Opis | 
      |----------|----------|-------|-------------| 
      | **Metoda** | Tak | <*operacja do uruchomienia*> | Operacja HTTP do uruchomienia na artefakt. Na przykład ta akcja HTTP używa metody **GET.** | 
      | **Identyfikator URI** | Tak | <*lokalizacja metadanych*> | Aby uzyskać `routingUrl` dostęp do wartości metadanych z pobranego artefaktu, można użyć wyrażenia, na przykład: <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **Nagłówki** | Nie | <*wartości nagłówka*> | Wszystkie wyjścia nagłówka z wyzwalacza, który chcesz przekazać do akcji HTTP. Na przykład, aby przekazać w `headers` wartości właściwości wyzwalacza: można użyć wyrażenia, na przykład: <p>`@triggeroutputs()['headers']` | 
      | **Treść** | Nie | <*zawartość ciała*> | Wszelkie inne treści, które mają być `body` przekazywane przez właściwość akcji HTTP. W tym przykładzie `properties` przekazuje wartości artefaktu do akcji HTTP: <p>1. Kliknij wewnątrz **Body** właściwości, więc pojawi się lista zawartości dynamicznej. Jeśli nie są wyświetlane żadne właściwości, wybierz pozycję **Zobacz więcej**. <br>2. Z dynamicznej listy zawartości w obszarze **Wyszukiwanie artefaktów konta integracji**wybierz pozycję **Właściwości**. | 
      |||| 

      Przykład:

      ![Określanie wartości i wyrażeń dla akcji HTTP](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. Aby sprawdzić informacje podane dla akcji HTTP, wyświetl definicję JSON aplikacji logiki. Na pasku narzędzi Projektanta aplikacji logiki wybierz **pozycję Widok kodu,** aby na przykład wyświetlić definicję JSON aplikacji:

      ![Definicja JSON aplikacji logiki](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      Po przełączeniu z powrotem do projektanta aplikacji logiki wszystkie użyte wyrażenia są teraz rozpoznawane, na przykład:

      ![Rozwiązane wyrażenia w projektancie aplikacji logiki](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o umowach](logic-apps-enterprise-integration-agreements.md)
