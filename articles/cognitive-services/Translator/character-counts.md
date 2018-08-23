---
title: Liczba znaków interfejs API tekstu usługi Microsoft Translator | Dokumentacja firmy Microsoft
description: Jak interfejs API tekstu usługi Microsoft Translator liczby znaków.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/16/2018
ms.locfileid: "41987660"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Jak interfejs API tekstu usługi Microsoft Translator liczby znaków

Usługa Microsoft Translator liczy każdy znak danych wejściowych. Znaki w sensie Unicode, a nie w bajtach. Surogaty Unicode liczba jako dwa znaki. Biały znak i znaczników są liczone jak znaki. Długość odpowiedzi nie ma znaczenia.

Wywołania metod wykrywania i BreakSentence nie są wliczane do użycia znaku. Jednak oczekujemy, że wywołania do metod wykrywania i BreakSentence znajdują się w rozsądnej proporcji do korzystania z innych funkcji, które są uwzględniane. Firma Microsoft zastrzega sobie prawo do Rozpocznij zliczanie wykrywania i BreakSentence. 

Aby uzyskać więcej informacji dotyczących liczby znaków [często zadawane pytania dotyczące programu Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
