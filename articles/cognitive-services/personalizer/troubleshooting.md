---
title: Rozwiązywanie problemów — Personalizacja
titleSuffix: Azure Cognitive Services
description: Informacje dotyczące rozwiązywania problemów dotyczących narzędzia Personalizacja można znaleźć w tym artykule.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 7f7a6a08b86d21287c644f6a851d465d97f32e74
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663680"
---
# <a name="personalizer-troubleshooting"></a>Rozwiązywanie problemów z narzędziem Personalizacja

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące narzędzia do rozwiązywania problemów dotyczących narzędzia Personalizacja.

## <a name="learning-loop"></a>Pętla szkoleniowa

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Pętla szkoleniowa nie wydaje się uczyć. Jak to naprawić?

Pętla szkoleniowa wymaga kilku tysięcy wywołań, zanim funkcja Range wywoła priorytet. 

Jeśli nie wiesz, jak działa pętla szkoleniowa, uruchom [ocenę w trybie offline](concepts-offline-evaluation.md)i Zastosuj poprawione zasady uczenia. 

## <a name="next-steps"></a>Kolejne kroki

[Konfigurowanie częstotliwości aktualizacji modelu](how-to-settings.md#model-update-frequency)