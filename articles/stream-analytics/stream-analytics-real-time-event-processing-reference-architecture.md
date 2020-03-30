---
title: Przetwarzanie zdarzeń w czasie rzeczywistym przy użyciu usługi Azure Stream Analytics
description: W tym artykule opisano architekturę referencyjną, aby osiągnąć przetwarzanie i analizę zdarzeń w czasie rzeczywistym przy użyciu usługi Azure Stream Analytics.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: d219b3fcb27b23527c0a651bc8e842a9e036bfc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75431481"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektura referencyjna: przetwarzanie zdarzeń w czasie rzeczywistym za pomocą usługi Microsoft Azure Stream Analytics
Architektura referencyjna do przetwarzania zdarzeń w czasie rzeczywistym za pomocą usługi Azure Stream Analytics ma na celu zapewnienie ogólnego planu wdrażania platformy w czasie rzeczywistym jako rozwiązania do przetwarzania strumienia usługi (PaaS) za pomocą platformy Microsoft Azure.

## <a name="summary"></a>Podsumowanie
Tradycyjnie rozwiązania analityczne były oparte na możliwościach, takich jak ETL (wyodrębnianie, przekształcanie, ładowanie) i magazynowanie danych, gdzie dane są przechowywane przed analizą. Zmieniające się wymagania, w tym szybciej przychodzące dane, wypychają ten istniejący model do granic możliwości. Możliwość analizowania danych w ruchomych strumieniach przed magazynowaniem jest jednym z rozwiązań i chociaż nie jest to nowa funkcja, podejście to nie zostało szeroko przyjęte we wszystkich branżach branżowych. 

Platforma Microsoft Azure udostępnia obszerny katalog technologii analitycznych, które są w stanie obsługiwać szereg różnych scenariuszy i wymagań dotyczących rozwiązań. Wybranie usług platformy Azure do wdrożenia dla kompleksowego rozwiązania może być wyzwaniem, biorąc pod uwagę zakres ofert. Ten dokument został zaprojektowany w celu opisania możliwości i współpracy różnych usług platformy Azure, które obsługują rozwiązanie do przesyłania strumieniowego zdarzeń. Wyjaśniono również niektóre scenariusze, w których klienci mogą korzystać z tego typu podejścia.

## <a name="contents"></a>Spis treści
* Podsumowanie dla kierownictwa
* Wprowadzenie do analizy w czasie rzeczywistym
* Propozycja wartości danych w czasie rzeczywistym na platformie Azure
* Typowe scenariusze analizy w czasie rzeczywistym
* Architektura i komponenty
  * Źródła danych
  * Warstwa integracji danych
  * Warstwa analizy w czasie rzeczywistym
  * Warstwa przechowywania danych
  * Warstwa prezentacji / zużycia
* Podsumowanie

**Autor:** Charles Feddersen, architekt rozwiązań, Centrum Doskonałości danych Insights, Microsoft Corporation

**Opublikowano:** Styczeń 2015 r.

**Wersja:** 1.0

**Pobierz:** [Przetwarzanie zdarzeń w czasie rzeczywistym za pomocą usługi Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, wypróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Następne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

