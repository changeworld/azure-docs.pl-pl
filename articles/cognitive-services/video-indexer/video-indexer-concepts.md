---
title: Azure pojęcia indeksatora wideo | Dokumentacja firmy Microsoft
description: W tym temacie opisano niektóre pojęcia dotyczące usługi indeksatora wideo.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 04/17/2017
ms.author: juliako
ms.openlocfilehash: 01d92a6b55d2fb2c09cee333f482d79d2cdf763c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349000"
---
# <a name="video-indexer-concepts"></a>Wideo pojęcia indeksatora
 
W tym temacie opisano niektóre pojęcia dotyczące usługi indeksatora wideo.
    
## <a name="summarized-insights"></a>Podsumowanie insights

Zagregowany widok danych zawiera szczegółowe informacje podsumowujące: kroje, słowa kluczowe, opinie. Na przykład zamiast przez każdy z tysiącami przedziałów czasu i sprawdzanie, które kroje są w nim, wszystkie kroje zawiera podsumowanie szczegółowych danych, i dla każdego z nich, przedziałów czasu, w którym występuje i % czasu jest wyświetlany.

## <a name="topicskeywords"></a>Tematy/słowa kluczowe

Tematy/słowa kluczowe znajdują się na liście kluczy wyrażeń, które wyodrębnia indeksatora wideo w tekście. Na przykład wideo Scott Guthrie może zawierać następujące tematy/słów kluczowych: zabezpieczeń, Azure, Microsoft Cloud, przychodu.

## <a name="sentiments"></a>opinie

Kiedy indeksatora wideo analizuje zapisy, wykrywa również opinie. Na przykład "jest bardzo atrakcyjnych zdarzenia" jest dodatnią wskaźniki nastrojów klientów.

## <a name="time-range-vs-adjusted-time-range"></a>zakres czasu, a zakres czasu skorygowaną

Przedział czasu w oryginalnym wideo to TimeRange. AdjustedTimeRange jest zakres czasu względem bieżącej listy odtwarzania. Ponieważ można utworzyć listę odtwarzania z różnych wierszy różnych plików wideo, można podjąć wideo 1 godziny i korzystać tylko 1 wiersz z niego, na przykład 10:00 – 10:15. W takim przypadku należy listy odtwarzania o 1 wiersz, gdy przedział czasu jest 15-10:10:00, ale adjustedTimeRange to 00:00-00:15.
 
## <a name="blocks"></a>Bloki

Bloki są przeznaczone do ułatwiają przejść przez dane. Na przykład bloku może można podzielić na podstawie podczas zmiany głośniki lub istnieje długa wstrzymania.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się, jak rozpocząć pracę, zobacz [jak zarejestrować się i przekaż pierwszy wideo](video-indexer-get-started.md).

## <a name="see-also"></a>Zobacz także

[Film poglądowy dotyczący indeksatora](video-indexer-overview.md)

