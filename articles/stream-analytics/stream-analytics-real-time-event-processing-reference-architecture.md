---
title: Przetwarzanie zdarzeń w czasie rzeczywistym za pomocą przetwarzanie zdarzeń usługi Azure Stream Analytics
description: W tym artykule opisano architekturę referencyjną, aby osiągnąć przetwarzania zdarzeń w czasie rzeczywistym i analizy przy użyciu usługi Azure Stream Analytics.
services: stream-analytics
author: jseb225
ms.author: jeanb
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: ab1ca89427f332151120420c3c087902584706f4
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621778"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektura referencyjna: W czasie rzeczywistym do przetwarzania zdarzeń, za pomocą usługi Microsoft Azure Stream Analytics
Architektura referencyjna dla zdarzeń w czasie rzeczywistym przetwarzania za pomocą usługi Azure Stream Analytics mają na celu dostarczenie ogólny plan wdrażania w czasie rzeczywistym platforma jako usługa (PaaS) rozwiązanie przetwarzanie strumienia z platformą Microsoft Azure.

## <a name="summary"></a>Podsumowanie
Tradycyjne rozwiązania do analizy, została oparta na możliwości, takie jak ETL (extract, transform, load) i magazynowanie danych, której dane są przechowywane przed analizy. Zmiany wymagań, w tym większej ilości danych szybko nadchodzących, przesyłanej ten istniejący model do limitu. Możliwość analizowania danych w ramach przenoszenia strumienie przed magazynu to jedno rozwiązanie, a mimo że nie jest dostępna nowa funkcja, to podejście nie ma powszechnie zaakceptowany we wszystkich przypadkach branży. 

Microsoft Azure oferuje rozbudowane katalogu technologii analitycznych, które obsługują szereg scenariuszy różnych rozwiązań i wymagania. Wybierając którymi usługami platformy Azure, aby wdrożyć rozwiązania end-to-end może być trudne, biorąc pod uwagę szerokie oferty. Ten dokument jest przeznaczony do opisywania, możliwości i współdziałanie różnych usług platformy Azure, które obsługują to rozwiązanie do przesyłania strumieniowego wydarzeń. Również objaśniono niektóre scenariusze, w których klienci mogą korzystać z tego typu podejście.

## <a name="contents"></a>Zawartość
* Podsumowanie dla kierownictwa
* Wprowadzenie do analizy w czasie rzeczywistym
* Korzyści z danych w czasie rzeczywistym na platformie Azure
* Typowe scenariusze dotyczące analizy w czasie rzeczywistym
* Architektura i składniki
  * Źródła danych
  * Warstwa integracji danych
  * Warstwa analizy w czasie rzeczywistym
  * Warstwa magazynu danych
  * Prezentacja / użycie warstw
* Wniosek

**Autor:** Centrum Insights danych Charles Feddersen, architekt rozwiązań, doskonałą funkcjonalność, Microsoft Corporation

**Data publikacji:** Styczeń 2015

**Poprawki:** 1.0

**Pobieranie:** [W czasie rzeczywistym do przetwarzania zdarzeń, za pomocą usługi Microsoft Azure Stream Analytics](https://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dalszą pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

