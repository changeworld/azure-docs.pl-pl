---
title: Znak liczby - API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak interfejs API tekstu usługi Translator liczby znaków.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226438"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak interfejs API tekstu usługi Translator liczby znaków

Interfejs API tekstu usługi Translator liczy każdy znak danych wejściowych. Znaki w sensie Unicode, a nie w bajtach. Surogaty Unicode liczba jako dwa znaki. Biały znak i znaczników są liczone jak znaki. Długość odpowiedzi nie ma znaczenia.

Wywołania metod wykrywania i BreakSentence nie są wliczane do użycia znaku. Jednak oczekujemy, że wywołania do metod wykrywania i BreakSentence znajdują się w rozsądnej proporcji do korzystania z innych funkcji, które są uwzględniane. Firma Microsoft zastrzega sobie prawo do Rozpocznij zliczanie wykrywania i BreakSentence.

Aby uzyskać więcej informacji dotyczących liczby znaków [często zadawane pytania dotyczące programu Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
