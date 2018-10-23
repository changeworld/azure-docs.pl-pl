---
title: Znak liczby - API tekstu usługi Translator
titlesuffix: Azure Cognitive Services
description: Jak interfejs API tekstu usługi Translator liczby znaków.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649198"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Jak interfejs API tekstu usługi Translator liczby znaków

Interfejs API tekstu usługi Translator liczy każdy znak danych wejściowych. Znaki w sensie Unicode, a nie w bajtach. Surogaty Unicode liczba jako dwa znaki. Biały znak i znaczników są liczone jak znaki. Długość odpowiedzi nie ma znaczenia.

Wywołania metod wykrywania i BreakSentence nie są wliczane do użycia znaku. Jednak oczekujemy, że wywołania do metod wykrywania i BreakSentence znajdują się w rozsądnej proporcji do korzystania z innych funkcji, które są uwzględniane. Firma Microsoft zastrzega sobie prawo do Rozpocznij zliczanie wykrywania i BreakSentence.

Aby uzyskać więcej informacji dotyczących liczby znaków [często zadawane pytania dotyczące programu Microsoft Translator](https://www.microsoft.com/en-us/translator/faq.aspx).
