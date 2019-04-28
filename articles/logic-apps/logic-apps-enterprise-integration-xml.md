---
title: Wiadomości XML dla integracji dla przedsiębiorstw B2B — Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przetwarzanie, sprawdzanie poprawności, przekształcania i wzbogacania komunikatów XML dla rozwiązań B2B w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: a75ac9773072423c13eef85ecad29c632c13d024
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996581"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Wiadomości XML i pliki proste w usłudze Azure Logic Apps z pakietem integracyjnym dla przedsiębiorstw

Przy użyciu aplikacji logiki, masz możliwość przetwarzania komunikatów XML, które wysyłania i odbierania. Ta funkcja jest uwzględniona w pakiet integracyjny dla przedsiębiorstw. Dla tych użytkowników, za pomocą programu BizTalk Server w tle pakiet integracyjny dla przedsiębiorstw zapewnia możliwości podobne do przekształcania i sprawdzania poprawności komunikatów, pracować z plików prostych i nawet za pomocą XPath wzbogacanie lub prowadzenie określonych właściwości komunikatu. 

Dla tych użytkowników, którzy są nowe w tym obszarze te funkcje Rozwiń, jak przetwarzać komunikaty w ramach przepływu pracy. Na przykład jeśli jesteś w scenariuszu business-to-business i pracować z określonych schematów XML, możesz użyć pakietu integracyjnego dla przedsiębiorstw, aby zwiększyć jak Twoja firma przetwarzający te komunikaty. 

Zawiera pakiet integracyjny dla przedsiębiorstw: 

* [Sprawdzanie poprawności kodu XML](logic-apps-enterprise-integration-xml-validation.md "więcej informacji na temat sprawdzanie poprawności kodu XML komunikat") — sprawdzanie poprawności przychodzących lub wychodzących wiadomości XML ze schematem określonym.
* [Przekształceń danych XML](../logic-apps/logic-apps-enterprise-integration-transform.md "więcej informacji na temat przekształcenia wiadomości XML oraz maps") — konwersji lub dostosować komunikat XML na podstawie wymagań dotyczących lub wymagania partnera.
* [Płaskie pliku kodowania i dekodowania pliku prostego](logic-apps-enterprise-integration-flatfile.md "więcej informacji na temat kodowania/dekodowanie pliku prostego") — kodowania i dekodowania pliku prostego. Na przykład SAP akceptuje i wysyła IDOC pliki w formacie pliku prostego. Integracja z wieloma platformami utworzyć wiadomości XML, łącznie z aplikacji logiki. Tak można utworzyć aplikację logiki, która używa kodera pliku prostego do "konwersji" pliki XML do plików prostych. 
* [Wyrażenie XPath](https://msdn.microsoft.com/library/mt643789.aspx) — wzbogacanie komunikat i prowadzenie określonych właściwości wiadomości. Wyodrębnione właściwości służy następnie do rozsyłania wiadomości do miejsca docelowego lub pośredniczący punktu końcowego.

## <a name="try-it-out"></a>Testowanie
[Wdrażanie aplikacji logiki w pełni funkcjonalne](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (przykład GitHub) przy użyciu funkcji XML w usłudze Azure Logic Apps.

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej na temat pakietu integracyjnego dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "więcej informacji na temat pakietu integracyjnego dla przedsiębiorstw")
