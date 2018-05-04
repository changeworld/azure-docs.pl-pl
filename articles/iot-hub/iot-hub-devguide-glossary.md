---
title: Usługa Azure IoT Hub słownik terminów | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — słownik terminów wspólnej odnoszących się do Centrum IoT Azure.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 16ef29ea-a185-48c3-ba13-329325dc6716
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 828489510cea16ebba1944c0e6d1fc88f9375fc7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="glossary-of-iot-hub-terms"></a>Słownik terminów Centrum IoT
W tym artykule wymieniono niektóre typowe terminy używane w artykułach Centrum IoT.

## <a name="advanced-message-queueing-protocol"></a>Protokół usługi kolejkowania komunikatów zaawansowane
[Zaawansowane protokołu usługi kolejkowania komunikatów (AMQP)](https://www.amqp.org/) jest jeden z komunikatów protokołów, które [Centrum IoT](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokoły obsługi komunikatów, które obsługuje Centrum IoT, zobacz [wysyłania i odbierania wiadomości z Centrum IoT](iot-hub-devguide-messaging.md).

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
[Interfejsu wiersza polecenia Azure](../cli-install-nodejs.md) to narzędzie do tworzenia i zarządzania zasobami na platformie Microsoft Azure i platform, open source, opartych na powłoki poleceń. Ta wersja interfejsu wiersza polecenia jest implementowane przy użyciu środowiska Node.js.

## <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
[Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) to narzędzie do tworzenia i zarządzania zasobami na platformie Microsoft Azure i platform, open source, opartych na powłoki poleceń. Ta wersja zapoznawcza interfejsu wiersza polecenia jest implementowane za pomocą języka Python.


## <a name="azure-iot-device-sdks"></a>Urządzenia IoT Azure SDK
Brak _urządzenia zestawów SDK_ dostępne dla wielu języków, które umożliwiają tworzenie [aplikacji dla urządzeń](#device-app) który interakcję z Centrum IoT. Samouczki Centrum IoT pokazują, jak używać tych zestawy SDK urządzenia. Kod źródłowy i uzyskać więcej informacji o urządzeniu zestawów SDK można znaleźć w tej witrynie GitHub [repozytorium](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Zestawy Azure IoT usługi SDK
Brak _usługi SDK_ dostępne dla wielu języków, które umożliwiają tworzenie [zaplecza aplikacji](#back-end-app) który interakcję z Centrum IoT. Samouczki Centrum IoT pokazują, jak używać tych zestawów SDK usługi. Kod źródłowy i uzyskać więcej informacji na temat zestawów SDK usługi można znaleźć w tej witrynie GitHub [repozytorium](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-portal"></a>Azure Portal
[Portalu Microsoft Azure](https://portal.azure.com) to centralne miejsce, w którym można udostępnić i zarządzania zasobami platformy Azure. Organizuje ona jego zawartości przy użyciu _bloków_.

## <a name="azure-powershell"></a>Azure PowerShell
[Program Azure PowerShell](/powershell/azure/overview) to zbiór poleceń cmdlet służy do zarządzania na platformie Azure za pomocą środowiska Windows PowerShell. Tworzenie, testowanie i wdrażanie przy użyciu poleceń cmdlet i zarządzanie rozwiązań i usług świadczonych za pośrednictwem platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) umożliwia pracę z zasobami w rozwiązaniu jako grupa. Można wdrożyć, zaktualizować lub usunięcie zasobów dla rozwiązania w jednej, skoordynowanej operacji.

## <a name="azure-service-bus"></a>Azure Service Bus
[Usługa Service Bus](../service-bus/index.md) zapewnia włączoną obsługę chmury komunikacji z obsługą wiadomości przedsiębiorstwa i obsługiwanych przez przekaźnik komunikacji, który pomaga połączyć rozwiązań lokalnych z chmurą. Niektóre samouczki Centrum IoT korzystają usługi Service Bus [kolejek](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Usługa Azure Storage](../storage/common/storage-introduction.md) jest rozwiązanie magazynu w chmurze. Obejmuje on usługi magazynu obiektów Blob, używanego do przechowywania danych bez struktury. Niektóre samouczki Centrum IoT używać magazynu obiektów blob.

## <a name="back-end-app"></a>Zaplecza aplikacji
W kontekście [Centrum IoT](#iot-hub), aplikacji wewnętrznych to aplikacja, która łączy punkty końcowe Centrum IoT połączonej usługi. Na przykład aplikacji zaplecza może pobrać [urządzenia do chmury](#device-to-cloud)wiadomości lub zarządzać [rejestru tożsamości](#identity-registry). Zazwyczaj uruchamiania aplikacji zaplecza w chmurze, ale w wielu samouczków aplikacji zaplecza są konsoli aplikacje działające na komputerze deweloperskim lokalnego.

## <a name="built-in-endpoints"></a>Wbudowane punkty końcowe
Centrum IoT, co obejmuje wbudowane [punktu końcowego](iot-hub-devguide-endpoints.md) czyli zdarzenia koncentratora zgodny. Można użyć dowolnego mechanizm, który współpracuje z usługą Event Hubs można odczytać urządzenia do chmury wiadomości z tego punktu końcowego.

## <a name="cloud-gateway"></a>Brama chmury
Brama chmury umożliwia łączność w przypadku urządzeń, które nie może połączyć się bezpośrednio do [Centrum IoT](#iot-hub). Brama chmury jest hostowana w chmurze contrast do [bramy pola](#field-gateway) systemem lokalnym na urządzeniach. Typowy przypadek użycia bramy chmury jest wdrożenie translacji protokołów dla urządzeń.

## <a name="cloud-to-device"></a>Chmura-urządzenie
Odnosi się do wiadomości wysyłane z Centrum IoT do podłączonego urządzenia. Często komunikaty te są poleceniami, które poinstruować urządzenia, aby podejmować działanie. Aby uzyskać więcej informacji, zobacz [wysyłania i odbierania wiadomości z Centrum IoT](iot-hub-devguide-messaging.md).

## <a name="connection-string"></a>Parametry połączenia
Umożliwia parametry połączenia w kodzie aplikacji Hermetyzowanie informacje wymagane do połączenia z punktem końcowym. Ciąg połączenia zwykle obejmują adres punktu końcowego i informacji o zabezpieczeniach, ale ciąg połączenia formatów różnią się w usługach. Istnieją dwa typy parametrów połączenia skojarzony z usługą Centrum IoT:
- *Parametry połączenia urządzenia* włączyć łączyć się urządzenia uwzględniającym punkty końcowe Centrum IoT urządzenia.
- *Parametry połączenia Centrum IoT* Włącz aplikacji zaplecza do nawiązania połączenia usługi połączonej punkty końcowe Centrum IoT.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe
Można tworzyć niestandardowe [punkty końcowe](iot-hub-devguide-endpoints.md) w Centrum IoT w celu dostarczania komunikatów przez [reguły routingu](#routing-rules). Niestandardowe punkty końcowe połączyć się bezpośrednio do Centrum zdarzeń, kolejki usługi Service Bus lub tematu usługi Service Bus.

## <a name="custom-gateway"></a>Bram
Brama umożliwia łączność w przypadku urządzeń, które nie może połączyć się bezpośrednio do [Centrum IoT](#iot-hub). Można użyć [Azure IoT krawędzi](#azure-iot-edge) do tworzenia niestandardowych bram, które implementuje niestandardowej logiki do obsługi wiadomości, konwersje protokołu niestandardowego i innego typu przetwarzania na krawędzi.

## <a name="data-point-message"></a>Komunikat punktu danych
Komunikat punktu danych jest [urządzenia do chmury](#device-to-cloud) komunikat, który zawiera [telemetrii](#telemetry) dane, takie jak szybkości knie lub temperatury.

## <a name="desired-configuration"></a>Wymaganą konfiguracją
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), desired configuration odwołuje się do pełny zestaw właściwości oraz metadanych w dwie urządzenia, które mają być synchronizowane z urządzeniem.

## <a name="desired-properties"></a>Żądane właściwości
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), żądana właściwości to podsekcji dwie urządzenia, który jest używany z [zgłosił właściwości](#reported-properties) do synchronizowania konfiguracji urządzenia lub warunku. Żądane właściwości można ustawić tylko [zaplecza aplikacji](#back-end-app) i są przestrzegane przez [aplikacji urządzenia](#device-app).

## <a name="device-to-cloud"></a>Urządzenie-chmura
Odwołuje się do komunikatów wysyłanych z podłączonego urządzenia do [Centrum IoT](#iot-hub). Te komunikaty mogą być [punktu danych](#data-point-message) lub [interakcyjne](#interactive-message) wiadomości. Aby uzyskać więcej informacji, zobacz [wysyłania i odbierania wiadomości z Centrum IoT](iot-hub-devguide-messaging.md).

## <a name="device"></a>Urządzenie
W kontekście IoT urządzenie jest zwykle na małą skalę, autonomicznym urządzeniem może zbierać dane lub inne urządzenia. Na przykład urządzenie może być środowiska monitorowania urządzenia, lub do kontrolera nadchodzić i wentylatorów systemów w cieplarnianych. [Katalogu urządzenia](https://catalog.azureiotsuite.com/) zawiera listę urządzeń sprzętowych certyfikowane do pracy z [Centrum IoT](#iot-hub).

## <a name="device-app"></a>Urządzenia aplikacji
Aplikacja urządzenia jest uruchamiana na Twojej [urządzenia](#device) i obsługuje komunikację z Twojej [Centrum IoT](#iot-hub). Zwykle, użyj jednej z [urządzenia Azure IoT SDK](#azure-iot-device-sdks) podczas wdrożenia aplikacji urządzenia. W wielu samouczków IoT używasz [symulowane urządzenie](#simulated-device) dla wygody.

## <a name="device-condition"></a>Stan urządzenia
Odwołuje się do informacji o stanie urządzenia, takich jak metoda łączności obecnie w użyciu, zgodnie z raportem [aplikacji urządzenia](#device-app). [Aplikacje urządzenia](#device-app) zgłaszać także ich możliwości. Można wysyłać zapytania dotyczące stanu i możliwości odpowiednie informacje przy użyciu twins urządzenia.

## <a name="device-data"></a>Dane urządzenia
Dane urządzenia odwołuje się do danych na urządzeniu przechowywanych w Centrum IoT [rejestru tożsamości](#identity-registry). Istnieje możliwość importowania i eksportowania tych danych.

## <a name="device-explorer"></a>Eksplorator urządzenia
[Explorer urządzenia](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) to narzędzie jest uruchamiana w systemie Windows, który umożliwia zarządzanie urządzeniami w [rejestru tożsamości](#identity-registry). Narzędzie można również wysyłania i odbierania wiadomości na urządzeniach.

## <a name="device-identities-rest-api"></a>Interfejs API REST tożsamości urządzeń
[Interfejsu API REST tożsamości urządzenia](https://docs.microsoft.com/rest/api/iothub/iothubresource) umożliwia zarządzanie urządzeniami zarejestrowane w [rejestru tożsamości](#identity-registry) przy użyciu interfejsu API REST. Zazwyczaj należy użyć jednej z wyższego poziomu [usługi SDK](#azure-iot-service-sdks) jak pokazano w samouczkach Centrum IoT.

## <a name="device-identity"></a>Tożsamość urządzenia
Tożsamość tego urządzenia jest unikatowy identyfikator przypisany do wszystkich urządzeń zarejestrowanych w [rejestru tożsamości](#identity-registry).

## <a name="module-identity"></a>Moduł tożsamości
Tożsamość modułu jest unikatowy identyfikator przypisany do każdego modułu, który należy do urządzenia. Moduł tożsamości jest zarejestrowane w [rejestru tożsamości](#identity-registry).

## <a name="device-management"></a>Zarządzanie urządzeniami
Zarządzanie urządzeniami obejmuje pełny cykl związane z zarządzaniem urządzeniami w rozwiązania IoT, w tym planowania, inicjowania obsługi administracyjnej, konfigurowania, monitorowania i wycofywania.

## <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami
[Centrum IoT](#iot-hub) umożliwia typowe wzorce zarządzania urządzenie tym ponowny rozruch, wykonywanie ustawień fabrycznych i wykonywanie aktualizacji oprogramowania układowego na urządzeniach.

## <a name="device-messaging-rest-api"></a>Interfejs API REST komunikatów urządzenia
Można użyć [interfejsu API REST wiadomości urządzenia](https://docs.microsoft.com/rest/api/iothub/httpruntime) z urządzenia do wysyłania wiadomości urządzenia do chmury do Centrum IoT i odbierania [chmury do urządzenia](#cloud-to-device) komunikaty z Centrum IoT. Zazwyczaj należy użyć jednej z wyższego poziomu [urządzenia zestawów SDK](#azure-iot-device-sdks) jak pokazano w samouczkach Centrum IoT.

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń
Inicjowanie obsługi administracyjnej urządzeń jest proces dodawania początkowej [danych urządzenia](#device-data) sklepów w rozwiązaniu. Aby włączyć nowe urządzenia w celu nawiązania połączenia z koncentratorem, należy dodać identyfikator urządzenia i klucze do Centrum IoT [rejestru tożsamości](#identity-registry). W ramach procesu inicjowania obsługi administracyjnej może być konieczne Inicjuj dane specyficzne dla urządzenia w innych magazynach rozwiązania.

## <a name="device-twin"></a>Bliźniak urządzenia
A [dwie urządzenia](iot-hub-devguide-device-twins.md) jest dokumentem JSON, która przechowuje informacje o stanie urządzenia, takie jak metadanych, konfiguracji i warunki. [Centrum IoT](#iot-hub) będzie się powtarzał dwie urządzenia, dla wszystkich urządzeń, których udostępnianie w Centrum IoT. Twins urządzenia umożliwia synchronizowanie [warunki urządzenia](#device-condition) i konfiguracje między urządzeniem i rozwiązanie zaplecza. Umożliwia wysyłanie zapytań twins urządzenia, aby znaleźć określone urządzenia i zbadać stanu długotrwałej operacji.

## <a name="module-twin"></a>Dwie modułu
Podobnie jak dwie urządzenia, dwie modułu jest dokument JSON, która przechowuje informacje o stanie modułu, takich jak metadanych, konfiguracji i warunki. Centrum IoT utrzymuje dwie moduł, dla każdej tożsamości modułu dostarczanym w ramach tożsamości urządzenia w Centrum IoT. Moduł twins umożliwiają synchronizowanie modułu warunków i konfiguracje między modułem i zaplecza rozwiązania. Umożliwia wysyłanie zapytań twins modułu, aby znaleźć określone moduły i zbadać stanu długotrwałej operacji.

## <a name="twin-queries"></a>Dwie zapytań
[Urządzenia i modułu zapytania dwie](iot-hub-devguide-query-language.md) używać języka kwerend Centrum IoT przypominającego SQL można pobrać informacji z urządzenia twins lub twins modułu. Można użyć tego samego języka zapytań Centrum IoT można pobrać informacji o [zadania](#job) uruchomiona w Centrum IoT.

## <a name="device-twin-rest-api"></a>Interfejs API REST dwie urządzenia
Można użyć [interfejsu API REST dwie urządzenia](https://docs.microsoft.com/rest/api/iothub/devicetwinapi) z rozwiązania serwer wewnętrzny, aby zarządzać twins Twojego urządzenia. Interfejs API umożliwia pobierania i aktualizowania [dwie urządzenia](#device-twin) właściwości i wywoływać [bezpośrednie metody](#direct-method). Zazwyczaj należy użyć jednej z wyższego poziomu [usługi SDK](#azure-iot-service-sdks) jak pokazano w samouczkach Centrum IoT.

## <a name="twin-synchronization"></a>Dwie synchronizacji
Dwie używa synchronizacji [żądanego właściwości](#desired-properties) w twins urządzenia lub twins modułu, aby skonfigurować moduły lub urządzeń i pobrać [zgłosił właściwości](#reported-properties) z nich do przechowywania w dwie.

## <a name="direct-method"></a>Metoda bezpośrednia
A [metoda bezpośrednia](iot-hub-devguide-direct-methods.md) sposób można wywołać metodę można wykonać na urządzeniu przez wywołanie interfejsu API w Centrum IoT.

## <a name="endpoint"></a>Endpoint
Centrum IoT udostępnia wiele [punkty końcowe](iot-hub-devguide-endpoints.md) umożliwiających aplikacji nawiązać połączenia z Centrum IoT. Istnieją punkty końcowe skierowane do urządzenia, które włączyć urządzeń do wykonywania operacji, takich jak wysyłanie [urządzenia do chmury](#device-to-cloud) wiadomości i odbieranie [chmury do urządzenia](#cloud-to-device) wiadomości. Brak punktów końcowych zarządzania połączonej usługi, które umożliwiają [zaplecza aplikacji](#back-end-app) do wykonywania operacji, takich jak [tożsamości urządzenia](#device-identity) i zarządzanie dwie urządzenia. Brak połączonej usługi [wbudowane punkty końcowe](#built-in-endpoints) do odczytywania wiadomości urządzenia do chmury. Można utworzyć [niestandardowe punkty końcowe](#custom-endpoints) do odbierania wiadomości urządzenia do chmury przez [reguły routingu](#routing-rules).

## <a name="event-hubs-service"></a>Usługa centra zdarzeń
[Centra zdarzeń](../event-hubs/event-hubs-what-is-event-hubs.md) jest wysoce skalowalna Usługa transferu danych, który może obsługiwać miliony zdarzeń na sekundę. Usługa umożliwia przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Dla porównania w usłudze IoT Hub, zobacz [porównania Azure IoT Hub i usługi Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Punkt końcowy zgodny z centrum zdarzeń usługi Event Hubs
Aby odczytać [urządzenia do chmury](#device-to-cloud) wiadomości wysyłane do Centrum IoT, można połączyć z punktem końcowym na Centrum i zastosować dowolną metodę zgodnego Centrum zdarzeń można odczytać tych wiadomości. Metody zgodnych z Centrum zdarzeń obejmują użycie [zestawów SDK centra zdarzeń](../event-hubs/event-hubs-programming-guide.md) i [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Pole bramy
Brama pola umożliwia łączność w przypadku urządzeń, które nie może połączyć się bezpośrednio do [Centrum IoT](#iot-hub) są zwykle wdrażane lokalnie z urządzenia. Aby uzyskać więcej informacji, zobacz [co to jest Centrum IoT Azure?](iot-hub-what-is-iot-hub.md)

## <a name="free-account"></a>Bezpłatne konto
Można utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) ukończenie samouczki Centrum IoT i wypróbować usługę Centrum IoT (i innych usług Azure).

## <a name="gateway"></a>Brama
Brama umożliwia łączność w przypadku urządzeń, które nie może połączyć się bezpośrednio do [Centrum IoT](#iot-hub). Zobacz też [pola bramy](#field-gateway), [chmury bramy](#cloud-gateway), i [bram](#custom-gateway).

## <a name="identity-registry"></a>Tożsamość rejestru
[Rejestru tożsamości](iot-hub-devguide-identity-registry.md) wbudowany składnik Centrum IoT, która przechowuje informacje o poszczególnych urządzeniach może nawiązać połączenia z Centrum IoT.

## <a name="interactive-message"></a>Interakcyjne wiadomości
Interakcyjne komunikat [chmury do urządzenia](#cloud-to-device) komunikat, który wyzwala natychmiastowych akcji w zaplecza rozwiązania. Na przykład urządzenie może wysyłać alarmu o niepowodzeniu, które powinny być automatycznie zalogowany do systemu CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Usługa IoT Hub
Centrum IoT to w pełni zarządzana usługa platformy Azure, co umożliwia komunikację dwukierunkową i niezawodności między milionów urządzeń, a zaplecze rozwiązania. Aby uzyskać więcej informacji, zobacz [co to jest Centrum IoT Azure?](iot-hub-what-is-iot-hub.md) Przy użyciu programu [subskrypcji platformy Azure](#subscription), możesz utworzyć centra IoT do obsługi sieci IoT wiadomości obciążeń.

## <a name="iot-hub-metrics"></a>Metryki Centrum IoT
[Centrum IoT metryki](iot-hub-metrics.md) udostępnia dane o stanie centra IoT w Twojej [subskrypcji platformy Azure](#subscription). Centrum IoT metryk umożliwiają oceny ogólnej kondycji usługi i dołączone do niego urządzenia. Metryki Centrum IoT ułatwiają Zobacz, co dzieje się z Centrum IoT i badania głównej przyczyny problemów bez konieczności skontaktuj się z pomocą techniczną platformy Azure.

## <a name="iot-hub-query-language"></a>Język zapytań Centrum IoT
[Język zapytań Centrum IoT](iot-hub-devguide-query-language.md) jest języka przypominającego SQL, który umożliwia utworzenie zapytania z [zadania](#job) i twins urządzenia.

## <a name="iot-hub-resource-provider-rest-api"></a>Dostawca zasobów Centrum IoT interfejsu API REST
Można użyć [interfejsu API REST dostawcy zasobów Centrum IoT](https://docs.microsoft.com/rest/api/iothub/resourceprovider/iot-hub-resource-provider-rest) do zarządzania centra IoT w Twojej [subskrypcji platformy Azure](#subscription) wykonywania operacji, takich jak tworzenie, aktualizowanie i Usuwanie koncentratorów.

## <a name="iot-suite"></a>Pakiet IoT
Pakiet Azure IoT pakiety ze sobą wiele usług platformy Azure z wstępnie skonfigurowanych rozwiązań. Te wstępnie skonfigurowanych rozwiązań umożliwiają szybkie rozpoczęcie pracy z implementacjami na trasie o typowych scenariuszach IoT. Aby uzyskać więcej informacji, zobacz [co to jest pakiet IoT Azure?](../iot-suite/iot-suite-overview.md)

## <a name="the-iot-extension-for-azure-cli-20"></a>Rozszerzenie IoT Azure CLI 2.0
[Rozszerzenie IoT Azure CLI 2.0](https://github.com/Azure/azure-iot-cli-extension) to narzędzie i platform, wiersza polecenia. Narzędzie pozwala na zarządzanie urządzeniami w [rejestru tożsamości](#identity-registry), wysyłania i odbierania wiadomości oraz pliki z urządzeń i monitorować działania Centrum IoT.

## <a name="job"></a>Zadanie
Można użyć z zaplecza rozwiązania [zadania](iot-hub-devguide-jobs.md) na planowanie i śledzenie działań na zbiór urządzeń zarejestrowanych w usłudze Centrum IoT. Działania obejmują aktualizowanie urządzenia dwie [żądanego właściwości](#desired-properties), aktualizowania dwie urządzenia [tagi](#tags)i wywoływanie [bezpośrednie metody](#direct-method). [Centrum IoT](#iot-hub) używa również zadania, aby [importowanie do i eksportowanie](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [rejestru tożsamości](#identity-registry).

## <a name="jobs-rest-api"></a>Zadania interfejsu API REST
[Interfejsu API REST zadania](https://docs.microsoft.com/rest/api/iothub/jobapi) umożliwia zarządzanie [zadania](#job) uruchomiona w Centrum IoT.

## <a name="mqtt"></a>MQTT
[MQTT](http://mqtt.org/) jest jeden z komunikatów protokołów, które [Centrum IoT](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokoły obsługi komunikatów, które obsługuje Centrum IoT, zobacz [wysyłania i odbierania wiadomości z Centrum IoT](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorowanie operacji
Centrum IoT [operacje monitorowania](iot-hub-operations-monitoring.md) umożliwia monitorowanie stanu operacji w Centrum IoT w czasie rzeczywistym. [Centrum IoT](#iot-hub) śledzi zdarzenia przez różne kategorie działań. Można włączyć do wysyłania zdarzeń z co najmniej jednej kategorii do punktu końcowego Centrum IoT w celu przetwarzania. Możesz monitorować dane błędy lub konfigurowanie bardziej złożonych przetwarzania na podstawie wzorców danych.

## <a name="physical-device"></a>Urządzenie fizyczne
Urządzenie fizyczne jest prawdziwe urządzeniami, takimi jak Pi malina, w którym łączy się z Centrum IoT. Dla wygody, użyj wielu samouczki Centrum IoT [symulowane urządzeń](#simulated-device) umożliwiają uruchamianie przykładów na komputerze lokalnym.

## <a name="primary-and-secondary-keys"></a>Klucze podstawowe i pomocnicze
Po podłączeniu urządzenia uwzględniającym lub połączonej usługi punktu końcowego w Centrum IoT z [ciąg połączenia](#connection-string) zawiera klucz, aby przyznać dostęp. Po dodaniu urządzenia do [rejestru tożsamości](#identity-registry) lub Dodaj [udostępnionych zasad dostępu](#shared-access-policy) do Centrum, Usługa generuje klucza podstawowego i pomocniczego. Mając dwa klucze umożliwia przerzucane z jednego klucza do innego, po zaktualizowaniu klucza bez utraty dostępu do Centrum IoT.

## <a name="protocol-gateway"></a>Brama protokołu
Brama protokołu jest zwykle wdrażana w chmurze i zapewnia protokół tłumaczeń urządzeń nawiązujących połączenie z [Centrum IoT](#iot-hub). Aby uzyskać więcej informacji, zobacz [co to jest Centrum IoT Azure?](iot-hub-what-is-iot-hub.md)

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności
Istnieją różne [przydziały](iot-hub-devguide-quotas-throttling.md) przeznaczonych do używania [Centrum IoT](#iot-hub), wiele przydziały różnić w zależności od warstwy Centrum IoT. [Centrum IoT](#iot-hub) ma również zastosowanie [ogranicza](iot-hub-devguide-quotas-throttling.md) korzystanie z usługi w czasie wykonywania.

## <a name="reported-configuration"></a>Zgłoszony konfiguracji
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), zgłosił konfiguracji odwołuje się do pełny zestaw właściwości oraz metadanych w dwie urządzenia, które powinny być zgłaszane do zaplecza rozwiązania.

## <a name="reported-properties"></a>Zgłoszony właściwości
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), zgłosiła właściwości jest podciągiem dwie urządzenia używane z [żądanego właściwości](#desired-properties) do synchronizowania konfiguracji urządzenia lub warunku. Zgłoszony właściwości można ustawić tylko [aplikacji urządzenia](#device-app) i może odczytywać i sprawdzać za [zaplecza aplikacji](#back-end-app).

## <a name="resource-group"></a>Grupa zasobów
[Usługa Azure Resource Manager](#azure-resource-manager) korzysta z grup zasobów do grupowania powiązanych zasobów. Grupy zasobów służy do wykonywania operacji na wszystkie zasoby w grupie jednocześnie.

## <a name="retry-policy"></a>Zasady ponawiania
Używanie zasad ponawiania do obsługi [błędów przejściowych](https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx) przypadku połączenia z usługą w chmurze.

## <a name="routing-rules"></a>Reguły routingu
Możesz skonfigurować [reguły routingu](iot-hub-devguide-messages-read-custom.md) w Centrum IoT można przekierować wiadomości urządzenia do chmury [wbudowanym punktem końcowym](#built-in-endpoints) lub [niestandardowe punkty końcowe](#custom-endpoints) do przetworzenia przez użytkownika zaplecza rozwiązania.

## <a name="sasl-plain"></a>ZWYKŁY SASL
ZWYKŁY SASL jest protokołem który [AMQP](#advanced-message-queue-protocol) używa protokołu transferu tokenów zabezpieczających.

## <a name="shared-access-signature"></a>Sygnatury dostępu współdzielonego
Udostępniony sygnatur dostępu (SAS) są mechanizmu uwierzytelniania na podstawie bezpiecznego wartości skrótu SHA-256 lub identyfikatorów URI. Uwierzytelniania sygnatury dostępu Współdzielonego ma dwa składniki: _zasad dostępu współużytkowanego_ i _sygnatura dostępu współdzielonego_ (często nazywane token). Urządzenie korzysta SAS do uwierzytelniania za pomocą Centrum IoT. [Aplikacje zaplecza](#back-end-app) również użyć do uwierzytelniania za pomocą usługi połączonej punkty końcowe Centrum IoT sygnatury dostępu Współdzielonego. Zwykle obejmują tokenu sygnatury dostępu Współdzielonego w [ciąg połączenia](#connection-string) że aplikacja używa do ustanowienia połączenia z Centrum IoT.

## <a name="shared-access-policy"></a>Zasady dostępu współużytkowanego
Zasady dostępu współdzielonego definiuje uprawnienia przyznane dla każdego, kto ma prawidłową [klucz podstawowy lub pomocniczy](#primary-and-secondary-keys) skojarzone z tej zasady. Zasady dostępu współdzielonego i klucze można zarządzać w Centrum [portal](#azure-portal).

## <a name="simulated-device"></a>Symulowane urządzenie
Dla wygody wiele samouczki Centrum IoT Użyj symulowanego urządzenia, aby włączyć uruchomienia próbek na komputerze lokalnym. Z kolei [urządzenie fizyczne](#physical-device) jest prawdziwe urządzeniami, takimi jak Pi malina, w którym łączy się z Centrum IoT.

## <a name="solution"></a>Rozwiązanie
A _rozwiązania_ mogą odwoływać się do rozwiązania Visual Studio, który zawiera jeden lub więcej projektów. A _rozwiązania_ mogą również dotyczyć rozwiązania IoT, który zawiera elementy, takie jak urządzenia, [aplikacji dla urządzeń](#device-app), Centrum IoT, innymi usługami platformy Azure i [zaplecza aplikacji](#back-end-app).

## <a name="subscription"></a>Subskrypcja
Jeśli karta ma miejsce jest subskrypcja platformy Azure. Tworzenie każdego zasobów platformy Azure lub usługi Azure, którego używasz jest skojarzony z jedną subskrypcją. Wiele przydziały mają zastosowanie również na poziomie subskrypcji.

## <a name="system-properties"></a>Właściwości systemu
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), system właściwości tylko do odczytu i zawierają informacje na temat użycia urządzenia, takie jak ostatni stan połączenia i czasu działania.

## <a name="tags"></a>Tagi
W kontekście [dwie urządzenia](iot-hub-devguide-device-twins.md), tagi są metadane urządzenia przechowywane i pobierane przez zaplecza rozwiązania w formie dokumentu JSON. Tagi nie są widoczne dla aplikacji na urządzeniu.

## <a name="telemetry"></a>Telemetria
Urządzenia zbierania danych telemetrycznych, takich jak szybkości knie lub temperatury i użyj [punktu danych wiadomości](#data-point-messages) do wysyłania danych telemetrycznych do Centrum IoT.

## <a name="token-service"></a>Usługa tokenu
Usługi tokenu można użyć do zaimplementowania mechanizmu uwierzytelniania urządzeń. Używa Centrum IoT [udostępnionych zasad dostępu](#shared-access-policy) z **DeviceConnect** uprawnienia do tworzenia *zakres urządzeń* tokenów. Tokeny te włączyć w urządzeniu nawiązać połączenia z Centrum IoT. Urządzenie używa mechanizmu uwierzytelniania niestandardowego do uwierzytelniania za pomocą usługi tokenu. Jeśli urządzenie zostanie pomyślnie uwierzytelniony, usługi tokenu wystawia token sygnatury dostępu Współdzielonego dla urządzenia na potrzeby dostępu Centrum IoT.

## <a name="x509-client-certificate"></a>Certyfikat klienta X.509
Urządzenie można użyć certyfikatu X.509 do uwierzytelniania za pomocą [Centrum IoT](#iot-hub). Za pomocą certyfikatu X.509 jest to alternatywa dla użycia [tokenu sygnatury dostępu Współdzielonego](#shared-access-signature).
