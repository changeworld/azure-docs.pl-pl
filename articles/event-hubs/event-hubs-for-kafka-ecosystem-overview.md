---
title: Użyj Centrum zdarzeń z aplikacji platformy Apache Kafka — usługa Azure Event Hubs | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera informacje na temat obsługi platformy Apache Kafka w usłudze Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: fc81226e754178ad0edfff96a494dd7522662261
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395439"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Za pomocą usługi Azure Event Hubs z poziomu aplikacji platformy Apache Kafka
Usługa Event Hubs zapewnia punktu końcowego platformy Kafka, który może być używany przez istniejącą platformy Kafka na podstawie aplikacji zamiast uruchamiania klastra Kafka. Event Hubs obsługuje [protokół Apache Kafka 1,0 i nowsze](https://kafka.apache.org/documentation/)i współpracuje z istniejącymi aplikacjami Kafka, w tym narzędzia MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co oferuje usługi Event Hubs dla platformy Kafka

Usługa Event Hubs dla platformy Kafka funkcji zawiera head protokołu, na podstawie usługi Azure Event Hubs jest binarny zgodny z platformą Kafka w wersji 1.0 i nowszym w przypadku zarówno odczytywanie z oraz zapisywanie do tematów platformy Kafka. Punkt końcowy platformy Kafka z poziomu aplikacji może korzystać z żadne zmiany kodu, ale zmiana minimalnej konfiguracji. Należy zaktualizować parametry połączenia w konfiguracji w celu wskazania punktu końcowego platformy Kafka, udostępnianych przez Centrum zdarzeń zamiast wskazujący klastra Kafka. Następnie należy uruchomić, przesyłanie strumieniowe zdarzeń z poziomu aplikacji, które używają protokołu Kafka do usługi Event Hubs. Ta integracja obsługuje również platformy, takie jak [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), które są obecnie dostępne w wersji zapoznawczej. 

Kafka koncepcyjnie i Event Hubs są niemal identyczne: są to dzienniki partycjonowane dla danych przesyłanych strumieniowo. Poniższa tabela zawiera mapowanie pojęcia między platformy Kafka i centrów zdarzeń.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Usługi Kafka i Centrum zdarzeń koncepcyjne mapowanie

| Koncepcja platformy Kafka | Koncepcja centrów zdarzeń|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Centrum zdarzeń |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Podstawowe różnice między Kafka i usługi Event Hubs

Gdy [Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które można uruchomić w dowolnym miejscu, Event Hubs jest usługą w chmurze podobną do BLOB Storage platformy Azure. Brak serwerów lub sieci do zarządzania ani nie brokerów, aby skonfigurować. Tworzenie przestrzeni nazw, czyli nazwę FQDN, w którym będzie funkcjonować tematów, a następnie utworzyć usługi Event Hubs lub tematów w ramach tej przestrzeni nazw. Aby uzyskać więcej informacji na temat Event Hubs i przestrzeni nazw, zobacz [Event Hubs Features](event-hubs-features.md#namespace). Jako usługa w chmurze Event Hubs używa jednego stabilnego wirtualnego adresu IP jako punktu końcowego, dlatego klienci nie muszą znać brokerów ani maszyn w klastrze. 

Skalowanie w usłudze Event Hubs jest kontrolowana przez liczbę jednostek przepływności można kupić, każda jednostka przepływności uprawniających należy do 1 MB na sekundę lub 1000 zdarzeń na sekundę transferu danych przychodzących. Domyślnie Event Hubs skaluje jednostki przepływności po osiągnięciu limitu przez funkcję [autostartu](event-hubs-auto-inflate.md) . Ta funkcja działa również z funkcją Event Hubs for Kafka. 

### <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia z Event Hubs Kafka, klient próbuje uzyskać dostęp do zasobów Event Hubs. Chcesz się upewnić, że zasoby są dostępne za pomocą autoryzowanej jednostki. W przypadku korzystania z protokołu Apache Kafka z klientami można ustawić konfigurację uwierzytelniania i szyfrowania przy użyciu mechanizmów SASL. W przypadku korzystania z Event Hubs dla Kafka wymagane jest szyfrowanie TLS (ponieważ wszystkie dane przesyłane za pomocą Event Hubs są szyfrowane przy użyciu protokołu TLS). Można to zrobić, określając opcję SASL_SSL w pliku konfiguracji. 

Usługa Azure Event Hubs udostępnia wiele opcji autoryzacji dostępu do bezpiecznych zasobów. 

- OAuth
- Sygnatura dostępu współdzielonego (SAS)

#### <a name="oauth"></a>OAuth
Event Hubs integruje się z usługą Azure Active Directory (Azure AD), która zapewnia scentralizowany serwer autoryzacji zgodny z protokołem **OAuth** 2,0. W usłudze Azure AD można używać kontroli dostępu opartej na rolach (RBAC) do udzielania szczegółowych uprawnień do tożsamości klientów. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** protokołu i **OAUTHBEARER** dla mechanizmu. Aby uzyskać szczegółowe informacje o rolach i poziomach kontroli dostępu, zobacz [autoryzowanie dostępu za pomocą usługi Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego (SAS)
Event Hubs zapewnia także **sygnatury dostępu współdzielonego (SAS)** dla delegowanego dostępu do Event Hubs dla zasobów Kafka. Autoryzowanie dostępu przy użyciu mechanizmu uwierzytelniania OAuth 2,0 zapewnia doskonałe zabezpieczenia i łatwość użycia w porównaniu z SAS. Wbudowane role mogą również wyeliminować potrzebę autoryzacji opartej na listach ACL, która musi być utrzymywana i zarządzana przez użytkownika. Tej funkcji można używać z klientami Kafka przez określenie **SASL_SSL** dla protokołu i **zwykłego** dla mechanizmu. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Przykłady 
**Samouczek** z instrukcjami krok po kroku, aby utworzyć centrum zdarzeń z włączonym Kafka i uzyskać do niego dostęp przy użyciu SAS lub OAuth, zobacz [Szybki Start: przesyłanie strumieniowe danych za pomocą Event Hubs przy użyciu protokołu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Aby uzyskać więcej **przykładów** , które pokazują, jak używać protokołu OAuth z Event Hubs for Kafka, zobacz [przykłady w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Inne funkcje usługi Event Hubs jest dostępna dla platformy Kafka

Centra zdarzeń dla funkcji platformy Kafka umożliwia jeden protokół do pisania i odczytać za pomocą innego, tak, aby kontynuować publikowanie za pomocą platformy Kafka w Twojej bieżącej producentów platformy Kafka i dodawać czytelników przy użyciu usługi Event Hubs, takich jak Azure Stream Analytics lub Azure Functions. Ponadto funkcje Event Hubs, takie jak [przechwytywanie](event-hubs-capture-overview.md) i replikacja [geograficzna](event-hubs-geo-dr.md) , działają również z funkcją Event Hubs for Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkcje, które nie są jeszcze obsługiwane 

Poniżej przedstawiono listę funkcji platformy Kafka, które nie są jeszcze obsługiwane:

*   Producent Idempotentne
*   Transakcji
*   Kompresja
*   Przechowywania na podstawie rozmiaru
*   Kompaktowanie dziennika
*   Dodawanie partycji istniejącemu tematowi
*   Obsługa protokołu HTTP platformy Kafka interfejsu API
*   Kafka Streams

## <a name="next-steps"></a>Następne kroki

W tym artykule podano zapoznać się z wprowadzeniem do usługi Event Hubs dla platformy Kafka. Aby dowiedzieć się więcej, zobacz następujące linki:

- [Jak utworzyć centra Event Hubs z obsługą platformy Kafka](event-hubs-create-kafka-enabled.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-flink-tutorial.md)
- [Integrowanie narzędzia Kafka Connect z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-connect-tutorial.md)
- [Łączenie biblioteki Akka Streams z centrum zdarzeń z obsługą platformy Kafka](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


