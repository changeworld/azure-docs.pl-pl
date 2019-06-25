---
title: Rozwiązywanie problemów
titleSuffix: Azure Cognitive Services
description: Rozwiązywanie problemów z pytania dotyczące Personalizer znajdują się w tym artykule.
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: 5136bd295c12c4439a894b4dcf0b868d32ce43ca
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313144"
---
# <a name="personalizer-troubleshooting"></a>Rozwiązywanie problemów z personalizer

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozwiązywania problemów dotyczące Personalizer.

## <a name="learning-loop"></a>Learning pętli

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>Aby dowiedzieć się, prawdopodobnie nie pętli uczenia. Jak rozwiązać ten problem?

Pętli learning wymaga kilku tysięcy wywołań za wynagrodzeniem przed rangi wywołania priorytety skutecznie. 

Jeśli wiesz, jak obecnie zachowuje się pętlę metodyki uczenia, uruchom [oceny w trybie offline](concepts-offline-evaluation.md)i Zastosuj zasady poprawiony uczenia. 

## <a name="next-steps"></a>Kolejne kroki

[Skonfiguruj częstotliwość aktualizacji modelu](how-to-settings.md#model-update-frequency)