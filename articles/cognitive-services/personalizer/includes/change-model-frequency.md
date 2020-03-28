---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122894"
---
## <a name="change-the-model-update-frequency"></a>Zmienianie częstotliwości aktualizacji modelu

W witrynie Azure portal w zasobie Personalizer na stronie **Konfiguracja** zmień **częstotliwość aktualizacji modelu** na 10 sekund. Ten krótki czas będzie szybko trenować usługę, dzięki czemu można zobaczyć, jak zmienia się najważniejsze działanie dla każdej iteracji.

![Zmienianie częstotliwości aktualizacji modelu](../media/settings/configure-model-update-frequency-settings.png)

Gdy pętla Personalizer jest po raz pierwszy skomunikowany, nie ma modelu, ponieważ nie było żadnych wywołań interfejsu API nagrody do szkolenia z. Wywołania rangi zwracają równe prawdopodobieństwa dla każdego przedmiotu. Aplikacja powinna nadal zawsze klasyfikować zawartość przy użyciu danych wyjściowych RewardActionId.