---
title: Praca z wiadomości XML w przepływach pracy - Azure Logic Apps | Dokumentacja firmy Microsoft
description: Przetworzyć, sprawdzanie poprawności, transformacji i wzbogacić wiadomości XML w aplikacji logiki i business-scenariuszy przy użyciu pakiet integracyjny dla przedsiębiorstw
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: jeconnoc
editor: ''
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 85bdaff5cb88bbadaed778458b66c0cce7bce9de
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298978"
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Sprawdzanie poprawności i Przekształcanie XML, kodowania i dekodowania plików prostych i wzbogacić funkcji komunikatów w aplikacjach logiki

Za pomocą aplikacji logiki, możesz mieć możliwość przetwarzania komunikatów XML, które wysyłania i odbierania. Ta funkcja jest uwzględniona w pakiet integracyjny dla przedsiębiorstw. Dla tych użytkowników z tła BizTalk Server pakiet integracyjny dla przedsiębiorstw zapewnia podobne możliwości transformacji i sprawdzania poprawności komunikatów, Praca z plików prostych i nawet umożliwia XPath wzbogacić lub wyodrębniania właściwości specyficzne dla wiadomości. 

Dla tych użytkowników, którzy są nowe w tym miejscu te funkcje rozwiń sposób przetwarzania komunikatów w ramach przepływu pracy. Na przykład jeśli są w scenariuszu business-to-business i pracować z określonych schematów XML, a następnie pakiet integracyjny dla przedsiębiorstw można użyć w celu zwiększenia jak firmy przetwarza tych wiadomości. 

Zawiera pakiet integracyjny dla przedsiębiorstw: 

* [Sprawdzanie poprawności kodu XML](logic-apps-enterprise-integration-xml-validation.md "Dowiedz się więcej na temat sprawdzanie poprawności kodu XML wiadomości") -zweryfikować komunikatu przychodzącego lub wychodzącego XML względem określonego schematu.
* [Przekształcanie XML](../logic-apps/logic-apps-enterprise-integration-transform.md "więcej informacji na temat przekształcenia wiadomości XML i map") — przekonwertować lub dostosować komunikat XML na podstawie wymagań lub wymagania partnera.
* [Płaskie pliku kodowania i dekodowania pliku prostego](logic-apps-enterprise-integration-flatfile.md "informacje o pliku prostego kodowania/dekodowania") — kodowania i dekodowania plik prosty. Na przykład SAP akceptuje i wysyła IDOC pliki w formacie pliku prostego. Wiele platform integracji utworzyć wiadomości XML, łącznie z Logic Apps. Tak można utworzyć aplikację logiki, która używa kodera pliku prostego "konwersji" plików XML do plików prostych. 
* [Wyrażenie XPath](https://msdn.microsoft.com/library/mt643789.aspx) — wzbogacić komunikat i wyodrębniania właściwości specyficzne dla wiadomości. Następnie można wyodrębnionego właściwości kierowania wiadomości do miejsca docelowego lub pośredniczące punktu końcowego.

## <a name="try-it-out"></a>Wypróbuj
[Wdrażanie aplikacji logiki w pełni funkcjonalna ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (przykład GitHub) przy użyciu funkcji XML w aplikacjach logiki platformy Azure.

## <a name="learn-more"></a>Dowiedz się więcej
[Dowiedz się więcej o pakiet integracyjny dla przedsiębiorstw](../logic-apps/logic-apps-enterprise-integration-overview.md "Dowiedz się więcej na temat pakiet integracyjny dla przedsiębiorstw")
