---
title: Microsoft Translator tekst interfejsu API znak liczby | Dokumentacja firmy Microsoft
description: Jak interfejsu API usługi Microsoft Translator tekst liczby znaków.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347673"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Jak interfejsu API usługi Microsoft Translator tekst liczby znaków

Microsoft Translator zlicza każdy znak wejściowy. Znaki w tym sensie Unicode nie bajtów. Unicode surogatów liczby jako dwa znaki. Biały znak i znaczniki są liczone jak znaki. Długość odpowiedzi nie ma znaczenia.

Wywołania metod wykrywania i BreakSentence nie są uwzględniane w użycie znaków. Jednak oczekujemy, że wywołań do metod wykrywania i BreakSentence są uzasadnione proporcjonalnie do używania innych funkcji, które są uwzględniane. Firma Microsoft zastrzega sobie prawo do rozpocząć zliczanie Wykryj i BreakSentence. 

Tłumaczenie automatyczne wykrywanie oferty w przypadku pominięcia od parametru języka. 

Więcej informacji na temat liczby znaków znajduje się w [Translator Microsoft — często zadawane pytania](https://www.microsoft.com/en-us/translator/faq.aspx).
