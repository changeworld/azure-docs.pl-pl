---
title: Co to jest usługa Azure Event Hubs? | Microsoft Docs
description: Informacje na temat usługi Azure Event Hubs, usługi strumieniowego przesyłania danych big data, umożliwiającej pozyskiwanie milionów zdarzeń na sekundę.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: overview
ms.custom: mvc
ms.date: 08/01/2018
ms.author: shvija
ms.openlocfilehash: 8437b1c10facc28c5fd71b70dd7acf01b7d39e8e
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42024048"
---
# <a name="what-is-azure-event-hubs"></a>Co to jest usługa Azure Event Hubs?

Azure Event Hubs to platforma do pozyskiwania i strumieniowego przesyłania danych, która umożliwia odbieranie i przetwarzanie milionów zdarzeń na sekundę. Usługa Event Hubs pozwala przetwarzać i przechowywać zdarzenia, dane lub dane telemetryczne generowane przez rozproszone oprogramowanie i urządzenia. Dane wysłane do centrum zdarzeń mogą zostać przekształcone i zmagazynowane przy użyciu dowolnego dostawcy analityki czasu rzeczywistego lub adapterów przetwarzania wsadowego/magazynowania. 

Typowe scenariusze użycia usługi Event Hubs są następujące:

- Wykrywanie anomalii (oszustwa/wartości odstające)
- Rejestrowanie aplikacji
- Potoki analiz, na przykład w analizie strumienia kliknięć
- Tworzenie dynamicznych pulpitów nawigacyjnych
- Archiwizowanie danych
- Przetwarzanie transakcji
- Przetwarzanie dane telemetrycznych o użytkownikach
- Strumieniowe przesyłanie danych telemetrycznych z urządzenia 

## <a name="why-use-event-hubs"></a>Dlaczego warto korzystać z usługi Event Hubs?

Dane mają wartość tylko wtedy, gdy można łatwo przetwarzać dane z różnych źródeł i formułować na ich podstawie aktualne wnioski. Usługa Event Hubs to rozproszona platforma do przetwarzania strumieniowego z małymi opóźnieniami, którą można bezproblemowo integrować z usługami danych i analiz na platformie Azure i poza nią w celu utworzenia pełnego potoku danych big data.

Usługa Event Hubs stanowi „drzwi wejściowe” dla potoku zdarzeń i jest często nazywana *systemem zbierania zdarzeń* w architekturach rozwiązań. System zbierania zdarzeń to składnik lub usługa, która znajduje się między wydawcami zdarzeń, a ich odbiorcami. Jego rolą jest oddzielenie procesu tworzenia strumienia zdarzeń od użycia tych zdarzeń. Usługa Event Hubs udostępnia ujednoliconą platformę przesyłania strumieniowego z buforem przechowywana czasowego, co umożliwia uniezależnienie wydawców i odbiorców zdarzeń. 

W poniższych sekcjach opisano najważniejsze funkcje usługi Azure Event Hubs: 

## <a name="fully-managed-paas"></a>W pełni zarządzana usługa PaaS 

Event Hubs to usługa zarządzana, wymagająca niewielkich nakładów na konfigurację i zarządzanie, dzięki czemu możesz skoncentrować się na swoich rozwiązaniach biznesowych. [Usługa Event Hubs dla ekosystemów platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) zapewnia środowisko platformy Kafka jako usługę bez konieczności konfigurowania i uruchamiania klastrów ani zarządzania nimi.

## <a name="support-for-real-time-and-batch-processing"></a>Obsługa przetwarzania wsadowego w czasie rzeczywistym

Możesz pozyskiwać, buforować, przechowywać i przetwarzać strumień danych w czasie rzeczywistym, aby uzyskiwać praktyczne informacje. W usłudze Event Hubs jest używany [partycjonowany model odbiorców](event-hubs-features.md#partitions), umożliwiający wielu aplikacjom jednoczesne przetwarzanie strumienia danych, podczas gdy Ty kontrolujesz prędkość przetwarzania.

Funkcja [Capture](event-hubs-capture-overview.md) umożliwia przechwytywanie danych niemal w czasie rzeczywistym do usługi [Azure Blob Storage](https://azure.microsoft.com/services/storage/blobs/) lub [Azure Data Lake Store](https://azure.microsoft.com/services/data-lake-store/)  na potrzeby przechowywania długoterminowego lub przetwarzania mikropartii. Jest to możliwe w ramach tego samego strumienia, który jest używany do analizy w czasie rzeczywistym. Funkcję Capture można szybko skonfigurować, używanie jej nie wiąże się z żadnymi kosztami administracyjnymi, a ponadto jest ona automatycznie skalowana za pomocą  [jednostek przepływności](event-hubs-features.md#throughput-units) usługi Event Hubs. Funkcja Capture w usłudze Event Hubs pozwala Ci skoncentrować się na przetwarzaniu, anie przechwytywaniu danych.

Usługę Azure Event Hubs można również zintegrować z usługą [Azure Functions](/azure/azure-functions/) w celu uzyskania architektury bezserwerowej.

## <a name="scalable"></a>Skalowalność 

Korzystając z usługi Event Hubs, możesz zacząć od strumieni danych liczonych w megabajtach i rozwijać je do rozmiaru gigabajtów, a nawet terabajtów. Funkcja [automatycznego rozszerzania](event-hubs-auto-inflate.md) jest jedną z wielu dostępnych opcji skalowania liczby jednostek przepływności odpowiednio do potrzeb użycia. 

## <a name="rich-ecosystem"></a>Zaawansowany ekosystem

Usługa [Event Hubs dla ekosystemów platformy Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md) umożliwia klientom i aplikacjom platformy [Apache Kafka (w wersji 1.0 lub nowszej)](https://kafka.apache.org/) komunikowanie się z usługą Event Hubs bez konieczności zarządzania klastrami.
 
Rozbudowany ekosystem dostępny za pomocą różnych [języków (.NET, Java, Python, Go, Node.js)](https://github.com/Azure/azure-event-hubs) umożliwia łatwe rozpoczęcie przetwarzania strumieni danych w usłudze Event Hubs. Wszystkie obsługiwane języki klienta zapewniają integrację niskiego poziomu.

## <a name="key-architecture-components"></a>Główne składniki architektury

Usługa Event Hubs umożliwia obsługę strumienia komunikatów, ale jej właściwości różnią się od tradycyjnych metod przesyłania komunikatów w przedsiębiorstwie. Możliwości usługi Event Hubs są zbudowane wokół scenariuszy wysokiej przepływności i przetwarzania zdarzeń. Usługa Event Hubs zawiera następujące [kluczowe składniki](event-hubs-features.md):

- **Producenci zdarzeń**: wszelkie jednostki, które wysyłają dane do centrum zdarzeń. Wydawcy zdarzeń mogą publikować zdarzenia przy użyciu protokołu HTTPS, protokołu AMQP 1.0 lub platformy Apache Kafka (w wersji 1.0 lub nowszej).
- **Partycje**: każdy użytkownik odczytuje tylko określony podzestaw, czyli partycję, strumienia komunikatów.
- **Grupy odbiorców**: widok (stan, pozycja lub przesunięcie) całego centrum zdarzeń. Dzięki grupom odbiorców wiele aplikacji odbiorczych może mieć osobny widok strumienia zdarzeń i niezależnie odczytywać strumień we własnym tempie i przy użyciu własnego przesunięcia.
- **Jednostki przepływności**: zakupione wcześniej jednostki pojemności, które określają przepływność usługi Event Hubs.
- **Odbiorcy zdarzeń**: wszelkie jednostki, które odczytują dane zdarzenia z centrum zdarzeń. Wszyscy odbiorcy usługi Event Hubs nawiązują połączenie za pomocą sesji protokołu AMQP 1.0, w ramach której zdarzenia są dostarczane, gdy tylko staną się dostępne.

Na poniższym rysunku przedstawiono architekturę przetwarzania strumienia usługi Event Hubs:

![Event Hubs](./media/event-hubs-about/event_hubs_architecture.png)


## <a name="next-steps"></a>Następne kroki

Aby rozpocząć korzystanie z usługi Event Hubs, zobacz następujące artykuły:

* [Ingest into Event Hubs (Pozyskiwanie danych do usługi Event Hubs)](event-hubs-quickstart-portal.md)
* [Event Hubs features overview (Omówienie funkcji usługi Event Hubs)](event-hubs-features.md)
* [Często zadawane pytania](event-hubs-faq.md)


