---
title: Zarządzanie metadanymi artefaktu konta integracji — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie lub pobrać metadane artefaktów z konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 02/23/2018
ms.openlocfilehash: 537014c2780fe94cfb35806759f8bcbd974c4c95
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128807"
---
# <a name="manage-artifact-metadata-from-integration-accounts-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zarządzanie metadanymi artefaktu z konta integracji w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Można zdefiniować niestandardowych metadanych dla artefaktów w konta integracji i pobrać tych metadanych podczas wykonywania aplikacji logiki. Na przykład można określić metadanych dla artefaktów, takich jak partnerów, umów, schematów i map - wszystkie metadane magazynu przy użyciu pary klucz wartość. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Dodaj metadane do artefaktów na konta integracji

1. W witrynie Azure portal utworzyć [konta integracji](logic-apps-enterprise-integration-create-integration-account.md).

2. Na przykład dodawanie artefaktu z kontem integracji, [partnera](logic-apps-enterprise-integration-partners.md), [umowy](logic-apps-enterprise-integration-agreements.md), lub [schematu](logic-apps-enterprise-integration-schemas.md).

3. Wybierz artefakt, wybierz pozycję **Edytuj**, a następnie wprowadź szczegóły metadanych.

   ![Wprowadź metadane](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Pobieranie metadanych z artefaktów dla usługi logic apps

1. W witrynie Azure portal utworzyć [aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Tworzenie [link z aplikacji logiki na koncie integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. W Projektancie aplikacji logiki Dodaj wyzwalacz, takich jak **żądania** lub **HTTP** do aplikacji logiki.

4. W obszarze wyzwalacza wybierz **nowy krok** > **Dodaj akcję**. Wyszukaj "konto integracji", dzięki czemu można znaleźć, a następnie wybierz tę akcję: **konto integracji — wyszukiwanie artefaktu konta integracji**

   ![Wybierz wyszukiwanie artefaktu konta integracji](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Wybierz **typ artefaktu** i podaj **nazwa artefaktu**. Na przykład:

   ![Wybierz typ artefaktu i określ nazwy artefaktu](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Przykład: Pobrać partnera metadanych

Załóżmy, że to partner ma metadanych z `routingUrl` szczegóły:

![Znajdź partnera z metadanych "routingURL"](media/logic-apps-enterprise-integration-metadata/image6.png)

1. W aplikacji logiki Dodaj wyzwalacz, **konto integracji — wyszukiwanie artefaktu konta integracji** akcję dla swojego partnera, a **HTTP** akcji, na przykład:

   ![Dodaj wyzwalacz, wyszukiwanie artefaktu i akcji HTTP do aplikacji logiki](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Aby pobrać identyfikator URI, na pasku narzędzi Projektanta aplikacji logiki, wybierz opcję **widok kodu** aplikacji logiki. Definicji aplikacji logiki powinien wyglądać następująco:

   ![Wyszukiwania odnośników](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej o umowach dotyczących](logic-apps-enterprise-integration-agreements.md)
