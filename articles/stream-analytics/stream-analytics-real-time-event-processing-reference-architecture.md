---
title: Przetwarzanie zdarzeń w czasie rzeczywistym za pomocą usługi Azure Stream Analytics przetwarzania zdarzeń
description: W tym artykule opisano architekturę odwołania do osiągnięcia analytics przy użyciu usługi Azure Stream Analytics i przetwarzania zdarzeń w czasie rzeczywistym.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/24/2017
ms.openlocfilehash: 8a5d426d67916e010c7fff048eebdc77b93c5c38
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2018
ms.locfileid: "30902586"
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Architektura odwołania: zdarzeń w czasie rzeczywistym przetwarzania za pomocą usługi Microsoft Azure Stream Analytics
Architektura referencyjna dla zdarzenia w czasie rzeczywistym przetwarzania za pomocą usługi Azure Stream Analytics mają na celu dostarczenie ogólnego planu wdrażania platformy w czasie rzeczywistym jako rozwiązanie przetwarzania strumienia usługa (PaaS) w systemie Microsoft Azure.

## <a name="summary"></a>Podsumowanie
Tradycyjnie rozwiązań analitycznych, została oparta na możliwości, takie jak ETL (extract, transform, load) i magazynowanie danych, których są przechowywane dane przed analizą. Zmieniających się wymagań, w tym więcej danych szybko nadchodzących, push istniejący model i ten limit. Zdolność do analizowania danych w ramach przenoszenie strumieni przed magazynu jest jedno rozwiązanie, a mimo że nie jest dostępna nowa funkcja, to rozwiązanie nie powszechnie przyjęto we wszystkich przypadkach branżowych. 

Microsoft Azure udostępnia szeroką gamę katalog analytics technologii, które mogą obsłużyć tablicy scenariuszy różnych rozwiązań i wymagań. Wybrać usługi systemu Azure, które do wdrożenia rozwiązania na trasie, może być wyzwaniem podana szerokość oferty. Ten dokument jest przeznaczony do opisano współdziałanie różnych usług Azure, które obsługują rozwiązanie strumienia zdarzeń. Objaśniono także niektóre scenariusze, w których klienci mogą korzystać z takiego podejścia.

## <a name="contents"></a>Zawartość
* Podsumowanie dla kierownictwa
* Wprowadzenie do analiz w czasie rzeczywistym
* Wartości oferty w czasie rzeczywistym danych na platformie Azure
* Typowe scenariusze dotyczące analiz w czasie rzeczywistym
* Architektura i składniki
  * Źródła danych
  * Integracja danych warstwy
  * Warstwa analiz w czasie rzeczywistym
  * Warstwa magazynu danych
  * Prezentacja / warstwy zużycie
* Podsumowanie

**Autor:** Centrum Insights danych Charlesa Feddersen, architektów rozwiązania doskonałości, firmy Microsoft Corporation

**Opublikowane:** stycznia 2015

**Poprawka:** 1.0

**Pobieranie:** [zdarzeń w czasie rzeczywistym przetwarzania za pomocą usługi Microsoft Azure Stream Analytics](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Uzyskiwanie pomocy
Aby uzyskać dodatkową pomoc, spróbuj [forum usługi Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Kolejne kroki
* [Wprowadzenie do usługi Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics (Rozpoczynanie pracy z usługą Azure Stream Analytics)](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs (Skalowanie zadań usługi Azure Stream Analytics)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Dokumentacja dotycząca języka zapytań usługi Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API Reference (Dokumentacja interfejsu API REST zarządzania usługą Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835031.aspx)

