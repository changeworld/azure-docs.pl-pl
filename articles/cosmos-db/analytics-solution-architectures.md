---
title: Rozwiązania wykorzystujące globalnie rozproszoną analizę w usłudze Azure Cosmos DB.
description: Dowiedz się więcej o rozwiązaniach, które można skonsytować przy użyciu globalnie rozproszonej analizy w usłudze Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.topic: conceptual
ms.service: cosmos-db
ms.date: 09/26/2019
ms.reviewer: sngun
ms.openlocfilehash: d0b5042c1401a95cc75f4c01ace843659b6babfe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73681716"
---
# <a name="solutions-using-globally-distributed-analytics-in-azure-cosmos-db"></a>Rozwiązania wykorzystujące globalnie rozproszoną analizę w usłudze Azure Cosmos DB

W tym artykule opisano rozwiązania, które można skonsmować przy użyciu globalnie rozproszonej analizy w usłudze Azure Cosmos DB.

## <a name="retail-and-consumer-goods"></a>Towary detaliczne i konsumpcyjne

Za pomocą platformy Spark w usłudze Azure Cosmos DB można dostarczać rekomendacje i oferty w czasie rzeczywistym. Możesz pomóc klientom odkryć potrzebne im produkty dzięki personalizacji w czasie rzeczywistym i rekomendacjom produktów.

* Można użyć wbudowanej obsługi uczenia maszynowego udostępnianej przez środowisko uruchomieniowe Apache Spark do generowania zaleceń w czasie rzeczywistym w katalogach produktów.

* Możesz wydobywać dane strumienia kliknięć, dane zakupu i dane klientów, aby zapewnić ukierunkowane zalecenia, które zapewniają wartość okresu istnienia.

* Korzystając z funkcji globalnej dystrybucji usługi Azure Cosmos DB, duże ilości danych produktów, które są rozłożone na regionach, mogą być analizowane w milisekundach.

* Możesz szybko uzyskać szczegółowe informacje dotyczące geograficznie rozproszonych użytkowników i danych. Możesz poprawić współczynnik konwersji promocji, reklamując odpowiednią reklamę właściwemu użytkownikowi we właściwym czasie.

* Możesz wykorzystać wbudowaną funkcję przesyłania strumieniowego Platformy Spark, aby wzbogacić dane na żywo, łącząc je ze statycznymi danymi klientów. W ten sposób możesz wyświetlać bardziej spersonalizowane i ukierunkowane reklamy w czasie rzeczywistym i w kontekście tego, co robią klienci.

Na poniższej ilustracji pokazano, jak pomoc techniczna usługi Azure Cosmos DB Spark jest używana do optymalizacji cen i promocji:

![Pomoc techniczna platformy Spark usługi Azure Cosmos DB w celu optymalizacji cen i promocji](./media/analytics-solution-architectures/optimize-pricing-and-promotions.png)


Na poniższej ilustracji pokazano, jak pomoc techniczna usługi Azure Cosmos DB Spark jest używana w silniku rekomendacji w czasie rzeczywistym:

![Pomoc techniczna usługi Azure Cosmos DB Spark w silniku rekomendacji w czasie rzeczywistym](./media/analytics-solution-architectures/real-time-recommendation-engine.png)

## <a name="manufacturing-and-iot"></a>Produkcja i IoT

Platforma analityczna azure Cosmos DB umożliwia analizę danych IoT w czasie rzeczywistym z milionów urządzeń na skalę globalną. Możesz tworzyć nowoczesne innowacje, takie jak przewidywanie wzorców pogodowych, analiza predykcyjna i optymalizacja zużycia energii.

* Korzystając z usługi Azure Cosmos DB, można wydobywać dane, takie jak metryki zasobów w czasie rzeczywistym i czynniki pogodowe, a następnie zastosować inteligentną analizę sieci, aby zoptymalizować wydajność podłączonych urządzeń w terenie. Inteligentna analiza sieci jest kluczem do kontrolowania kosztów operacyjnych, poprawy niezawodności sieci i dostarczania spersonalizowanych usług energetycznych dla konsumentów.

Na poniższej ilustracji pokazano, jak obsługa platformy Spark usługi Azure Cosmos DB jest używana do odczytywania metryk z urządzeń IoT i stosowania analizy inteligentnej sieci:

![Obsługa platformy Spark usługi Azure Cosmos DB do odczytu metryk z urządzeń IoT](./media/analytics-solution-architectures/read-metrics-from-iot-devices.png)

## <a name="predictive-maintenance"></a>Konserwacja zapobiegawcza

* Utrzymanie zasobów, takich jak sprężarki, które są używane w małych platformach wiertniczych do platform głębinowych jest złożonym przedsięwzięciem. Zasoby te znajdują się na całym świecie i generują petabajty danych. Korzystając z usługi Azure Cosmos DB, można utworzyć kompleksowy potok danych predykcyjnych, który używa strumieniowania platformy Spark do przetwarzania dużych ilości danych telemetrycznych czujników, przechowywania części zasobów i danych mapowań czujników.

* Można tworzyć i wdrażać modele uczenia maszynowego, aby przewidzieć błędy zasobów, zanim się pojawią, i wydać zlecenia pracy konserwacji przed wystąpieniem awarii.

Na poniższej ilustracji pokazano, jak pomoc techniczna platformy Spark usługi Azure Cosmos DB jest używana do tworzenia systemu konserwacji predykcyjnej:

![Pomoc techniczna platformy Spark usługi Azure Cosmos DB w celu zbudowania systemu konserwacji predykcyjnej](./media/analytics-solution-architectures/predictive-maintenance-system.png)

Na poniższej ilustracji pokazano, jak pomoc techniczna platformy Spark usługi Azure Cosmos DB jest używana do tworzenia systemu diagnostyki pojazdów w czasie rzeczywistym:

![Pomoc techniczna platformy Spark usługi Azure Cosmos DB w celu zbudowania systemu diagnostyki pojazdów w czasie rzeczywistym](./media/analytics-solution-architectures/real-time-vehicle-diagnostic-system.png)

## <a name="gaming"></a>Gry

* Dzięki wbudowanej obsłudze platformy Spark usługa Azure Cosmos DB umożliwia łatwe tworzenie, skalowanie i wdrażanie zaawansowanych modeli analizy i uczenia maszynowego w ciągu kilku minut, aby uzyskać najlepsze możliwe wrażenia w grach.

* Możesz analizować dane gracza, zakupów i zachowań, aby tworzyć odpowiednie spersonalizowane oferty, aby uzyskać wysokie współczynniki konwersji.

* Korzystając z uczenia maszynowego platformy Spark, można analizować i uzyskiwać szczegółowe informacje na temat danych telemetrycznych gier. Można zdiagnozować i zapobiec powolnym czasem ładowania i problemom w grze.

Na poniższej ilustracji pokazano, jak pomoc techniczna platformy Spark usługi Azure Cosmos DB jest używana w analizie gier:

![Pomoc techniczna platformy Azure Cosmos DB w zakresie analizy gier](./media/analytics-solution-architectures/gaming-analytics.png)

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o korzyściach płynących z usługi Azure Cosmos DB, zobacz [artykuł omówienie.](introduction.md)
* [Wprowadzenie do interfejsu API bazy danych usługi Azure Cosmos dla usługi MongoDB](mongodb-introduction.md)
* [Wprowadzenie do interfejsu API Cassandra usługi Azure Cosmos DB](cassandra-introduction.md)
* [Wprowadzenie do interfejsu API gremlin usługi Azure Cosmos DB](graph-introduction.md)
* [Wprowadzenie do interfejsu API tabeli usługi Azure Cosmos DB](table-introduction.md)
