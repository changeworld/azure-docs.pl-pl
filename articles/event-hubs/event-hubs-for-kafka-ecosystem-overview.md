---
title: Korzystanie z centrum zdarzeń z aplikacji Apache Kafka — Usługi Azure Event Hubs | Dokumenty firmy Microsoft
description: Ten artykuł zawiera informacje na temat obsługi platformy Apache Platformy Kafka przez usługi Azure Event Hubs.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: seodec18
ms.date: 02/12/2020
ms.author: shvija
ms.openlocfilehash: 6dcbf0ad0f6678d892c5c02446cac09b4325384c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283654"
---
# <a name="use-azure-event-hubs-from-apache-kafka-applications"></a>Korzystanie z usługi Azure Event Hubs z aplikacji Apache Kafka
Usługa Event Hubs udostępnia punkt końcowy platformy Kafka, który może być używany przez istniejące aplikacje oparte na platformie Kafka jako alternatywa dla uruchamiania własnego klastra platformy Kafka. Usługa Event Hubs obsługuje [protokół Apache Kafka 1.0 lub nowszych](https://kafka.apache.org/documentation/)oraz współpracuje z istniejącymi aplikacjami platformy Kafka, w tym z programem MirrorMaker.  

## <a name="what-does-event-hubs-for-kafka-provide"></a>Co zapewniają centra zdarzeń dla platformy Kafka?

Usługa Event Hubs for Kafka zapewnia głowicę protokołu na stronie usługi Azure Event Hubs, która jest zgodna z pakietem binarnym w wersji 1.0 i nowszej dla zarówno do czytania, jak i pisania do tematów platformy Kafka. Można rozpocząć korzystanie z punktu końcowego platformy Kafka z aplikacji bez zmiany kodu, ale minimalna zmiana konfiguracji. Możesz zaktualizować parametry połączenia w konfiguracjach, aby wskazać punkt końcowy platformy Kafka udostępniane przez centrum zdarzeń zamiast wskazywać klaster platformy Kafka. Następnie można uruchomić przesyłanie strumieniowe zdarzeń z aplikacji, które używają protokołu Platformy Kafka do centrum zdarzeń. Ta integracja obsługuje również struktury, takie jak [Kafka Connect](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect), który jest obecnie w wersji zapoznawczej. 

Koncepcyjnie Kafka i Event Hubs są prawie identyczne: są one zarówno podzielonych na partycje dzienniki utworzone do przesyłania strumieniowego danych. W poniższej tabeli przedstawiono pojęcia dotyczące platformy Kafka i Centrum zdarzeń.

### <a name="kafka-and-event-hub-conceptual-mapping"></a>Mapowanie koncepcyjne platformy Kafka i Centrum zdarzeń

| Koncepcja Kafka | Koncepcja centrów zdarzeń|
| --- | --- |
| Klaster | Przestrzeń nazw |
| Temat | Centrum zdarzeń |
| Partycja | Partycja|
| Grupa konsumentów | Grupa konsumentów |
| Przesunięcie | Przesunięcie|

### <a name="key-differences-between-kafka-and-event-hubs"></a>Kluczowe różnice między platformą Kafka a centrami zdarzeń

[Apache Kafka](https://kafka.apache.org/) to oprogramowanie, które można uruchamiać w dowolnym miejscu, usługi Event Hubs to usługa w chmurze podobna do usługi Azure Blob Storage. Nie ma serwerów ani sieci do zarządzania i nie ma brokerów do skonfigurowania. Należy utworzyć obszar nazw, który jest nazwą FQDN, w której znajdują się tematy, a następnie utworzyć centra zdarzeń lub tematy w tej przestrzeni nazw. Aby uzyskać więcej informacji na temat centrów zdarzeń i obszarów nazw, zobacz [Funkcje centrów zdarzeń](event-hubs-features.md#namespace). Jako usługa w chmurze usługi Event Hubs używa jednego stabilnego wirtualnego adresu IP jako punktu końcowego, więc klienci nie muszą wiedzieć o brokerów lub maszyn w klastrze. 

Skala w centrach zdarzeń jest kontrolowana przez liczbę jednostek przepływności, które kupujesz, przy czym każda jednostka przepływności uprawnia do 1 MB na sekundę lub 1000 zdarzeń na sekundę transferu ruchu przychodzącego. Domyślnie Centra zdarzeń skaluje jednostki przepływności po osiągnięciu limitu za pomocą funkcji [Auto-inflate;](event-hubs-auto-inflate.md) ta funkcja działa również z funkcją Centrum zdarzeń dla platformy Kafka. 

### <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie
Za każdym razem, gdy publikujesz lub zużywasz zdarzenia z centrów zdarzeń dla platformy Kafka, klient próbuje uzyskać dostęp do zasobów centrum zdarzeń. Chcesz mieć pewność, że zasoby są dostępne przy użyciu autoryzowanej jednostki. Korzystając z protokołu Apache Kafka z klientami, można ustawić konfigurację uwierzytelniania i szyfrowania przy użyciu mechanizmów SASL. Podczas korzystania z Centrum zdarzeń dla platformy Kafka wymaga szyfrowania TLS (jak wszystkie dane przesyłane z Usługi Event Hubs jest szyfrowanie TLS). Można to zrobić, określając opcję SASL_SSL w pliku konfiguracyjnym. 

Usługa Azure Event Hubs udostępnia wiele opcji autoryzacji dostępu do bezpiecznych zasobów. 

- OAuth
- Sygnatura dostępu współdzielonego (SAS)

#### <a name="oauth"></a>OAuth
Usługa Event Hubs integruje się z usługą Azure Active Directory (Azure AD), która udostępnia serwer autoryzacji scentralizowany zgodny ze standardem **OAuth** 2.0. Za pomocą usługi Azure AD można użyć kontroli dostępu opartej na rolach (RBAC) do udzielania uprawnień szczegółowej do tożsamości klienta. Tej funkcji można użyć z klientami platformy Kafka, określając **SASL_SSL** dla protokołu i **OAUTHBEARER** dla mechanizmu. Aby uzyskać szczegółowe informacje na temat ról i poziomów RBAC dostępu do zakresu, zobacz [Autoryzowanie dostępu za pomocą usługi Azure AD](authorize-access-azure-active-directory.md).

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=OAUTHBEARER
sasl.jaas.config=org.apache.kafka.common.security.oauthbearer.OAuthBearerLoginModule required;
sasl.login.callback.handler.class=CustomAuthenticateCallbackHandler;
```

#### <a name="shared-access-signature-sas"></a>Sygnatura dostępu współdzielonego
Centra zdarzeń udostępnia również **sygnatury dostępu współdzielonego (SAS)** dla delegowanego dostępu do centrów zdarzeń dla zasobów platformy Kafka. Autoryzowanie dostępu przy użyciu mechanizmu opartego na tokenach OAuth 2.0 zapewnia doskonałe bezpieczeństwo i łatwość użycia za pośrednictwem sygnatury dostępu Współdzielonego. Wbudowane role można również wyeliminować potrzebę autoryzacji opartej na acl, która musi być obsługiwana i zarządzane przez użytkownika. Tej funkcji można użyć z klientami platformy Kafka, określając **SASL_SSL** dla protokołu i **PLAIN** dla mechanizmu. 

```xml
bootstrap.servers=NAMESPACENAME.servicebus.windows.net:9093
security.protocol=SASL_SSL
sasl.mechanism=PLAIN
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

#### <a name="samples"></a>Samples 
Aby uzyskać **samouczek** z instrukcjami krok po kroku, aby utworzyć centrum zdarzeń i uzyskać do niego dostęp przy użyciu sas lub OAuth, zobacz [Szybki start: Przesyłanie strumieniowe danych z centrum zdarzeń przy użyciu protokołu Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md).

Aby uzyskać więcej **przykładów,** które pokazują, jak używać OAuth z Centrum zdarzeń dla platformy Kafka, zobacz [przykłady w usłudze GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth).

## <a name="other-event-hubs-features-available-for-kafka"></a>Inne funkcje centrów zdarzeń dostępne dla platformy Kafka

Usługa Event Hubs for Kafka umożliwia pisanie za pomocą jednego protokołu i odczytywanie z innym, dzięki czemu obecni producenci platformy Kafka mogą kontynuować publikowanie za pośrednictwem platformy Kafka i dodawać czytniki za pomocą centrów zdarzeń, takich jak usługa Azure Stream Analytics lub usługi Azure Functions. Ponadto funkcje Centrów zdarzeń, takie jak [Przechwytywanie](event-hubs-capture-overview.md) i [odzyskiwanie po awarii geograficznej,](event-hubs-geo-dr.md) również współpracują z centrami zdarzeń dla platformy Kafka.

## <a name="features-that-are-not-yet-supported"></a>Funkcje, które nie są jeszcze obsługiwane 

Oto lista funkcji platformy Kafka, które nie są jeszcze obsługiwane:

*   Producent Idempotentny
*   Transakcja
*   Kompresja
*   Retencja oparta na rozmiarze
*   Zagęszczanie kłód
*   Dodawanie partycji do istniejącego tematu
*   Obsługa interfejsu API protokołu HTTP Platformy Kafka
*   Strumienie kafka

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera wprowadzenie do centrów zdarzeń dla platformy Kafka. Aby dowiedzieć się więcej, zobacz następujące łącza:

- [Jak utworzyć centrum zdarzeń](event-hubs-create.md)
- [Przesyłanie strumieniowe do usługi Event Hubs z aplikacji platformy Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md)
- [Dublowanie brokera platformy Kafka w centrum zdarzeń](event-hubs-kafka-mirror-maker-tutorial.md)
- [Łączenie platformy Apache Spark z centrum zdarzeń](event-hubs-kafka-spark-tutorial.md)
- [Łączenie platformy Apache Flink z centrum zdarzeń](event-hubs-kafka-flink-tutorial.md)
- [Integracja platformy Kafka Connect z centrum zdarzeń](event-hubs-kafka-connect-tutorial.md)
- [Łączenie strumieni Akka z centrum zdarzeń](event-hubs-kafka-akka-streams-tutorial.md)
- [Eksplorowanie przykładów w witrynie GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)


