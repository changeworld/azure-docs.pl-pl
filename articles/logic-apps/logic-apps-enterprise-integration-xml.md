---
title: Wiadomości XML i pliki płaskie
description: Przetwarzaj, weryfikuj i przekształcaj wiadomości XML w usłudze Azure Logic Apps za pomocą pakietu Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792155"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Komunikaty XML i pliki płaskie w usłudze Azure Logic Apps z pakietem integracji przedsiębiorstwa

W [usłudze Azure Logic Apps](logic-apps-overview.md)można przetwarzać wiadomości XML wysyłane i odbierane przy użyciu pakietu Enterprise Integration Pack. Jeśli używasz programu BizTalk Server, pakiet integracji przedsiębiorstwa zapewnia podobne możliwości przekształcania i sprawdzania poprawności wiadomości, pracy z plikami płaskimi, a nawet używania programu XPath do wzbogacania lub wyodrębniania określonych właściwości z wiadomości. Jeśli jesteś nowy w tym miejscu, te funkcje rozwinąć sposób przetwarzania wiadomości w przepływie pracy aplikacji logiki. Na przykład jeśli masz scenariusz business-to-business (B2B) i pracować z określonymi schematami XML, możesz użyć pakietu integracji przedsiębiorstwa, aby poprawić sposób przetwarzania tych komunikatów przez firmę.

Na przykład pakiet integracji przedsiębiorstw zawiera następujące funkcje:

* [Sprawdzanie poprawności XML:](logic-apps-enterprise-integration-xml-validation.md)Sprawdzanie poprawności przychodzącego lub wychodzącego komunikatu XML względem określonego schematu.

* [Transformacja XML:](logic-apps-enterprise-integration-transform.md)konwertuj lub dostosuj komunikat XML na podstawie wymagań lub wymagań partnera za pomocą map.

* [Płaskie kodowanie plików i dekodowanie plików płaskich:](logic-apps-enterprise-integration-flatfile.md)Kodowanie lub dekodowanie pliku płaskiego.

  Na przykład SAP akceptuje i wysyła pliki IDOC w formacie pliku płaskiego. Wiele platform integracji tworzy komunikaty XML, w tym aplikacje logiki. Można więc utworzyć aplikację logiki, która używa kodera plików płaskich do "konwersji" plików XML na pliki płaskie.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Wzbogać wiadomość i wyodrębnić określone właściwości z wiadomości. Następnie można użyć wyodrębnione właściwości do kierowania wiadomości do miejsca docelowego lub pośredniego punktu końcowego.

## <a name="sample"></a>Sample

[Wdrażanie w pełni operacyjnej aplikacji logiki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (przykład GitHub) przy użyciu funkcji XML w usłudze Azure Logic Apps.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [pakiecie integracji dla przedsiębiorstw](logic-apps-enterprise-integration-overview.md)
