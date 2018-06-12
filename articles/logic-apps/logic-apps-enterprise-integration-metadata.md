---
title: Zarządzanie integracji konta artefaktu metadanych - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Dodawanie lub pobrać artefaktu metadanych z konta integracji dla usługi Azure Logic Apps
author: padmavc
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/23/2018
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e7ef6aef9bc1062ae0f76adfbaf086961fcaa94
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298369"
---
# <a name="manage-artifact-metadata-in-integration-accounts-for-logic-apps"></a>Zarządzanie artefaktu metadanych w konta integracji dla usługi logic apps

Można zdefiniować niestandardowych metadanych dla artefaktów na kontach integracji i pobrać metadane w czasie wykonywania dla aplikacji logiki. Na przykład można określić metadanych dla artefaktów, takich jak partnerów, umów, schematów i map — wszystkie metadane magazynu przy użyciu pary klucz wartość. 

## <a name="add-metadata-to-artifacts-in-integration-accounts"></a>Dodaj metadane do artefaktów na kontach integracji

1. W portalu Azure, Utwórz [konta integracji](logic-apps-enterprise-integration-create-integration-account.md).

2. Na przykład dodać artefaktów do konta integracji, [partnera](logic-apps-enterprise-integration-partners.md), [umowy](logic-apps-enterprise-integration-agreements.md), lub [schematu](logic-apps-enterprise-integration-schemas.md).

3. Wybierz artefakt, wybierz **Edytuj**, a następnie wprowadź szczegóły metadanych.

   ![Wprowadź metadanych](media/logic-apps-enterprise-integration-metadata/image1.png)

## <a name="retrieve-metadata-from-artifacts-for-logic-apps"></a>Pobieranie metadanych z artefaktów dla usługi logic apps

1. W portalu Azure, Utwórz [aplikacji logiki](quickstart-create-first-logic-app-workflow.md).

2. Utwórz [link z aplikacji logiki do swojego konta integracji](logic-apps-enterprise-integration-create-integration-account.md#link-account). 

3. W Projektancie aplikacji logiki, dodać wyzwalacza, takich jak **żądania** lub **HTTP** do aplikacji logiki.

4. W obszarze wyzwalacza, wybierz **nowy krok** > **Dodaj akcję**. Wyszukaj "integracji konto", można znaleźć, a następnie wybierz tę akcję: **konta integracji — wyszukiwanie artefaktu konta integracji**

   ![Wybierz wyszukiwanie artefaktu konta integracji](media/logic-apps-enterprise-integration-metadata/image2.png)

5. Wybierz **typu artefaktu** i podaj **nazwa artefaktu**. Na przykład:

   ![Wybierz typ artefaktu i określ nazwę artefaktów](media/logic-apps-enterprise-integration-metadata/image3.png)

## <a name="example-retrieve-partner-metadata"></a>Przykład: Pobrać partnera metadanych

Załóżmy, że ten partner ma metadanych z `routingUrl` szczegóły:

![Znajdź partnera z metadanych "routingURL"](media/logic-apps-enterprise-integration-metadata/image6.png)

1. W aplikacji logiki, Dodaj Twój wyzwalacz **konta integracji — wyszukiwanie artefaktu konta integracji** akcji dla swojego partnera i **HTTP** akcji, na przykład:

   ![Dodaj wyzwalacza, artefaktu wyszukiwania i akcji HTTP do aplikacji logiki](media/logic-apps-enterprise-integration-metadata/image4.png)

2. Aby pobrać identyfikator URI, na pasku narzędzi Projektanta aplikacji logiki, wybierz **widoku kodu** aplikacji logiki. Definicję aplikacji logiki powinna wyglądać następująco:

   ![Wyszukiwanie wyszukiwania](media/logic-apps-enterprise-integration-metadata/image5.png)

## <a name="next-steps"></a>Kolejne kroki

* [Dowiedz się więcej na temat umów](logic-apps-enterprise-integration-agreements.md)
