---
title: Usługa Azure IoT Hub słownik terminów związanych z | Dokumentacja firmy Microsoft
description: Przewodnik dewelopera — ze słownikiem pojęć wspólnych odnoszących się do usługi Azure IoT Hub.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.openlocfilehash: 6f89e27b06179c33857d581c0c6e3fc78c683d48
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "62119813"
---
# <a name="glossary-of-iot-hub-terms"></a>Słownik terminów usługi IoT Hub
W tym artykule wymieniono niektóre typowe terminy używane w artykułach usługi IoT Hub.

## <a name="advanced-message-queueing-protocol"></a>Protokół Kolejkowanie komunikatów zaawansowane
[Zaawansowane komunikat Kolejkowanie Protocol (AMQP)](https://www.amqp.org/) jest jedną z wiadomości, protokoły [usługi IoT Hub](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów, obsługiwanych przez usługę IoT Hub, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="automatic-device-management"></a>Zarządzanie urządzeniami automatyczne
Automatyczne zarządzanie urządzeniami w usłudze Azure IoT Hub automatyzuje wiele powtarzających się i złożonych zadań zarządzania flot duże urządzenia względem całej ich cykle życia. Za pomocą automatycznego zarządzania urządzeniami można docelowe zbiór urządzeń, na podstawie ich właściwości, zdefiniuj wymaganą konfiguracją i umożliwić usługi IoT Hub aktualizowania urządzenia, które pochodzą one do zakresu.  Składa się z [konfiguracji urządzeń automatycznego](iot-hub-auto-device-config.md) i [automatycznego wdrożenia usługi IoT Edge](../iot-edge/how-to-deploy-monitor.md).

## <a name="automatic-device-configuration"></a>Konfiguracja urządzenia automatycznego
Można użyć w zapleczu rozwiązania [konfiguracji urządzeń automatycznego](iot-hub-auto-device-config.md) można przypisać żądanych właściwości z zestawem [bliźniaczych reprezentacji urządzeń](#device-twin) i stan raportu przy użyciu metryk systemu i metryki niestandardowe. 

## <a name="azure-classic-cli"></a>Klasyczny interfejs wiersza polecenia platformy Azure
[Klasycznego wiersza polecenia platformy Azure](../cli-install-nodejs.md) to narzędzie dla wielu platform, typu open source, opartych na powłoce poleceń do tworzenia i zarządzania zasobami na platformie Microsoft Azure. Ta wersja interfejsu wiersza polecenia powinny służyć do tylko w przypadku wdrożeń klasycznych.

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
[Wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2) to narzędzie dla wielu platform, typu open source, opartych na powłoce poleceń do tworzenia i zarządzania zasobami na platformie Microsoft Azure.


## <a name="azure-iot-device-sdks"></a>Zestawy SDK platformy Azure w zakresie urządzeń IoT
Istnieją _zestawy SDK urządzeń_ dostępne dla wielu języków, które umożliwiają tworzenie [aplikacji dla urządzeń](#device-app) , które prowadzić interakcję z usługą IoT hub. Samouczki usługi IoT Hub dowiesz się, jak używać tych zestawów SDK urządzeń. Kod źródłowy i dodatkowych informacji na temat zestawów SDK urządzeń można znaleźć w tej usłudze GitHub [repozytorium](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-service-sdks"></a>Zestawy SDK usługi Azure IoT
Istnieją _usługi SDK_ dostępne dla wielu języków, które umożliwiają tworzenie [aplikacji zaplecza](#back-end-app) , które prowadzić interakcję z usługą IoT hub. Samouczki usługi IoT Hub dowiesz się, jak używać tych zestawów SDK usługi. Kod źródłowy i dodatkowych informacji na temat zestawów SDK usługi można znaleźć w tej usłudze GitHub [repozytorium](https://github.com/Azure/azure-iot-sdks).

## <a name="azure-iot-tools"></a>Narzędzia usługi Azure IoT
[Narzędzia IoT Azure](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) jest rozszerzeniem programu Visual Studio Code dla wielu platform, typu open source, które pomaga zarządzać usługi Azure IoT Hub i urządzeniami w programie VS Code. Za pomocą narzędzia IoT Azure deweloperów IoT można projektu IoT w programie VS Code z łatwością twórz.

## <a name="azure-portal"></a>Azure Portal
[Portalu Microsoft Azure](https://portal.azure.com) to centralne miejsce, w którym można aprowizować i zarządzania zasobami platformy Azure. Organizuje ona jego zawartości za pomocą _bloków_.

## <a name="azure-powershell"></a>Azure PowerShell
[Program Azure PowerShell](/powershell/azure/overview) to zbiór poleceń cmdlet, można użyć do zarządzania platformy Azure przy użyciu programu Windows PowerShell. Korzystanie z poleceń cmdlet do tworzenia, testowania, wdrażania i zarządzania rozwiązania i usługi dostarczane za pośrednictwem platformy Azure.

## <a name="azure-resource-manager"></a>Azure Resource Manager
[Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy. Można wdrożyć, zaktualizować lub usunąć zasoby dla danego rozwiązania w jednej, skoordynowanej operacji.

## <a name="azure-service-bus"></a>Azure Service Bus
[Usługa Service Bus](../service-bus/index.md) zapewnia komunikacji w chmurze z obsługą wiadomości przedsiębiorstwa i obsługiwanych przez przekaźnik komunikacji, który pomoże Ci połączyć rozwiązania lokalne z chmurą. Samouczki usługi IoT Hub należy używać usługi Service Bus [kolejek](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="azure-storage"></a>Azure Storage
[Usługa Azure Storage](../storage/common/storage-introduction.md) to rozwiązanie magazynu w chmurze. Obejmuje usługi magazynu obiektów Blob, używanego do przechowywania danych obiektów bez struktury. Samouczki usługi IoT Hub używać magazynu obiektów blob.

## <a name="back-end-app"></a>Aplikacja wewnętrzna
W kontekście [usługi IoT Hub](#iot-hub), aplikacji zaplecza, to aplikacja, która łączy się z jednego z punktów końcowych usługi skierowaną w Centrum IoT hub. Na przykład, może pobrać aplikacji zaplecza [urządzenia do chmury](#device-to-cloud) wiadomości lub zarządzać [rejestr tożsamości](#identity-registry). Zazwyczaj aplikacji zaplecza działa w chmurze, ale w wielu samouczków aplikacji zaplecza są aplikacji konsoli uruchomionych na lokalnej maszynie do programowania.

## <a name="built-in-endpoints"></a>Wbudowane punkty końcowe
Każde wystąpienie usługi IoT hub zawiera wbudowaną [punktu końcowego](iot-hub-devguide-endpoints.md) czyli zgodnego z Centrum zdarzeń. Możesz użyć dowolnego mechanizmu, który współpracuje z usługą Event Hubs do odczytywania komunikatów z urządzenia do chmury z tego punktu końcowego.

## <a name="cloud-gateway"></a>Brama chmury
Brama chmury umożliwia łączność urządzeń, które nie może połączyć się bezpośrednio do [usługi IoT Hub](#iot-hub). Brama chmury jest hostowana w chmurze, w przeciwieństwie do [bramy w terenie](#field-gateway) , które jest uruchamiane lokalnych do urządzeń. Typowym przypadkiem użycia dla bramy chmury jest do zaimplementowania translację protokołu dla urządzeń.

## <a name="cloud-to-device"></a>Chmura urządzenie
Odnosi się do komunikatów wysyłanych z usługi IoT hub do podłączonego urządzenia. Często te komunikaty są poleceń, które poinstruowanie urządzenia w celu wykonania akcji. Aby uzyskać więcej informacji, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="configuration"></a>Konfigurowanie
W kontekście [konfiguracji urządzenia automatycznego](iot-hub-auto-device-config.md), konfiguracji w ramach usługi IoT Hub definiują pożądaną konfigurację dla bliźniaczych reprezentacji urządzeń i oferuje zestaw metryk, aby raport status i postęp.

## <a name="connection-string"></a>Parametry połączenia
Możesz używać parametrów połączenia w kodzie aplikacji do hermetyzacji informacje wymagane do połączenia z punktem końcowym. Parametry połączenia są zazwyczaj zawiera adres punktu końcowego i informacji o zabezpieczeniach, ale parametry połączenia, które formaty różnią się w usługach. Istnieją dwa typy parametrów połączenia związane z usługą IoT Hub:
- *Parametry połączenia urządzenia* Włącz urządzeniom na łączenie się z punktami końcowymi przeznaczonych dla urządzenia w Centrum IoT hub.
- *Parametry połączenia Centrum IoT* umożliwić aplikacji zaplecza, aby nawiązać połączenie z punktami końcowymi przeznaczonych dla usługi w Centrum IoT hub.

## <a name="custom-endpoints"></a>Niestandardowe punkty końcowe
Możesz utworzyć niestandardowe [punktów końcowych](iot-hub-devguide-endpoints.md) w Centrum IoT hub w celu dostarczania komunikatów wysłanych przez [reguły routingu](#routing-rules). Niestandardowe punkty końcowe łączyć się bezpośrednio z Centrum zdarzeń, kolejki usługi Service Bus lub tematu usługi Service Bus.

## <a name="custom-gateway"></a>Bram
Brama zapewnia łączność urządzeń, które nie może połączyć się bezpośrednio do [usługi IoT Hub](#iot-hub). Usługa Azure IoT Edge umożliwia tworzenie niestandardowych bram, które implementują logikę niestandardową do obsługi komunikatów, konwersje niestandardowego protokołu i inne procesy przetwarzania na urządzeniach brzegowych.

## <a name="data-point-message"></a>Punkt danych komunikatu
Komunikat punktu danych jest [urządzenia do chmury](#device-to-cloud) wiadomość, która zawiera [telemetrii](#telemetry) dane, takie jak prędkość wiatru lub temperatury.

## <a name="desired-configuration"></a>Wymaganą konfiguracją
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), desired configuration odwołuje się do pełnego zestawu właściwości i metadanych w bliźniaczej reprezentacji urządzenia, które mają być synchronizowane z urządzeniem.

## <a name="desired-properties"></a>Żądane właściwości
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), żądane właściwości jest podciągiem bliźniaczej reprezentacji urządzenia, który jest używany z [zgłaszanych właściwości](#reported-properties) zsynchronizować konfiguracji urządzenia lub warunek. Żądane właściwości można ustawić tylko przy [aplikacji zaplecza](#back-end-app) i są przestrzegane [aplikacji urządzenia](#device-app).

## <a name="device-to-cloud"></a>Urządzenia do chmury
Odwołuje się do komunikatów wysyłanych z urządzenia podłączone do [usługi IoT Hub](#iot-hub). Te komunikaty mogą być [punktu danych](#data-point-message) lub [interaktywne](#interactive-message) wiadomości. Aby uzyskać więcej informacji, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="device"></a>Urządzenie
W kontekście IoT urządzenie jest zazwyczaj na małą skalę, urządzenie obliczeniowe autonomicznych, które może zbierać dane lub kontrolować innych urządzeń. Na przykład urządzenie może być urządzenie monitorowania środowiska lub kontrolera dla systemów nią i wentylatorów w cieplarnianych. [— Wykaz urządzeń](https://catalog.azureiotsolutions.com/) zawiera listę urządzeń sprzętowych certyfikowana do pracy z [usługi IoT Hub](#iot-hub).

## <a name="device-app"></a>Aplikacja urządzenia
Aplikacja urządzenie działa w swojej [urządzenia](#device) i obsługuje komunikację z Twojej [usługi IoT hub](#iot-hub). Zwykle, użyj jednej z [zestawy SDK urządzeń Azure IoT](#azure-iot-device-sdks) podczas implementacji aplikacji urządzenia. W wielu samouczki IoT [symulowane urządzenie](#simulated-device) dla wygody.

## <a name="device-condition"></a>Stan urządzenia
Odwołuje się do informacji o stanie urządzenia, takie jak metoda łączności, obecnie w użyciu, zgłoszonej przez [aplikacji urządzenia](#device-app). [Aplikacje urządzenia](#device-app) zgłaszać także ich możliwości. Można tworzyć zapytania warunku i możliwości informacji za pomocą bliźniaczych reprezentacji urządzeń.

## <a name="device-data"></a>Dane urządzenia
Dane urządzenia, który odwołuje się do danych na urządzenie, przechowywane w usłudze IoT Hub [rejestr tożsamości](#identity-registry). Istnieje możliwość importowania i eksportowania tych danych.

## <a name="device-explorer"></a>Device Explorer
[Eksplorator urządzeń](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) to narzędzie, które jest uruchamiane na Windows i umożliwia zarządzanie urządzeniami w [rejestr tożsamości](#identity-registry). Narzędzie umożliwia również wysyłanie oraz odbieranie komunikatów do urządzeń.

## <a name="device-identity"></a>Tożsamość urządzenia
Tożsamość urządzenia jest unikatowy identyfikator przypisany do wszystkich urządzeń zarejestrowanych w [rejestr tożsamości](#identity-registry).

## <a name="device-management"></a>Zarządzanie urządzeniami
Zarządzanie urządzeniami obejmuje pełny cykl życia, związane z zarządzaniem urządzeń w rozwiązaniu IoT, w tym planowanie, inicjowania obsługi, konfigurowania, monitorowania i wycofywania.

## <a name="device-management-patterns"></a>Wzorce zarządzania urządzeniami
[Usługa IoT hub](#iot-hub) umożliwia typowych wzorców zarządzania urządzeniami, w tym ponowny rozruch, wykonywanie resetuje fabryki i wykonywanie aktualizacji oprogramowania układowego na urządzeniach.

## <a name="device-rest-api"></a>Urządzenia interfejsu API REST
Możesz użyć [interfejs API REST urządzenia](https://docs.microsoft.com/rest/api/iothub/device) z urządzenia, aby wysyłać komunikaty urządzenie chmura do usługi IoT hub i odbieranie [chmury do urządzenia](#cloud-to-device) komunikaty z Centrum IoT hub. Zwykle, należy użyć jednej z wyższego poziomu [zestawy SDK urządzeń](#azure-iot-device-sdks) pokazany w ramach samouczków usługi IoT Hub.

## <a name="device-provisioning"></a>Inicjowanie obsługi administracyjnej urządzeń
Inicjowanie obsługi administracyjnej urządzeń jest proces dodawania początkowej [danych urządzenia](#device-data) sklepów w rozwiązaniu. Aby włączyć nowe urządzenie nawiązać połączenie z Centrum, należy dodać identyfikator urządzenia i klucze do usługi IoT Hub [rejestr tożsamości](#identity-registry). Jako część procesu inicjowania obsługi administracyjnej może być konieczne zainicjowanie danych specyficznych dla urządzenia w innych magazynach rozwiązania.

## <a name="device-twin"></a>Bliźniak urządzenia
A [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md) to dokument JSON, która przechowuje informacje o stanie urządzenia, takie jak metadane, konfiguracje i warunki. [Usługa IoT Hub](#iot-hub) będzie nadal występował w bliźniaczej reprezentacji urządzenia dla każdego urządzenia, aprowizować w usłudze IoT hub. Bliźniacze reprezentacje urządzeń umożliwia synchronizowanie [warunki urządzenia](#device-condition) i konfiguracje wiadomości pomiędzy urządzeniem a rozwiązania zaplecza. Można tworzyć zapytania bliźniacze reprezentacje urządzeń, aby znaleźć określone urządzenia i wykonać zapytanie o stan długotrwałych operacji.

## <a name="direct-method"></a>Metoda bezpośrednia
A [metoda bezpośrednia](iot-hub-devguide-direct-methods.md) sposób do wyzwolenia metodę można wykonać na urządzeniu za pomocą wywołania interfejsu API w usłudze IoT hub.

## <a name="endpoint"></a>Endpoint
Usługa IoT hub udostępnia wiele [punktów końcowych](iot-hub-devguide-endpoints.md) , dzięki którym aplikacje mogą połączyć się z Centrum IoT. Brak punktów końcowych przeznaczonych dla urządzeń, umożliwiające urządzeń wykonywać operacje takie jak wysyłanie [urządzenia do chmury](#device-to-cloud) wiadomości i odbieranie [chmury do urządzenia](#cloud-to-device) wiadomości. Brak punktów końcowych zarządzania przeznaczonych dla usługi, które umożliwiają [aplikacji zaplecza](#back-end-app) do wykonywania operacji takich jak [tożsamości urządzenia](#device-identity) i zarządzanie bliźniaczej reprezentacji urządzenia. Brak dostępnego z usługi [wbudowanych punktach końcowych](#built-in-endpoints) do odczytywania komunikatów z urządzenia do chmury. Możesz utworzyć [niestandardowe punkty końcowe](#custom-endpoints) do odbierania komunikatów z urządzenia do chmury przez [reguły routingu](#routing-rules).

## <a name="event-hubs-service"></a>Usługa centra zdarzeń
[Usługa Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) jest wysoce skalowalna Usługa transferu danych przychodzących, która może pozyskiwać miliony zdarzeń na sekundę. Usługa pozwala na przetwarzanie i analizowanie olbrzymich ilości danych wytworzonych przez podłączone urządzenia i aplikacje. Porównanie z usługą IoT Hub, zobacz [porównanie usługi Azure IoT Hub i Azure Event Hubs](iot-hub-compare-event-hubs.md).

## <a name="event-hub-compatible-endpoint"></a>Punkt końcowy zgodny z Centrum zdarzeń
Aby przeczytać [urządzenia do chmury](#device-to-cloud) komunikaty wysyłane do usługi IoT hub, możesz nawiązać połączenie z punktem końcowym w Centrum i zastosować dowolną metodę zgodnego z Centrum zdarzeń do odczytywania tych komunikatów. Metody zgodnego z Centrum zdarzeń obejmują użycie [Event Hubs SDK](../event-hubs/event-hubs-programming-guide.md) i [usługi Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

## <a name="field-gateway"></a>Bramy w terenie
Bramy w terenie zapewnia łączność urządzeń, które nie może połączyć się bezpośrednio do [usługi IoT Hub](#iot-hub) i zwykle jest wdrożone lokalnie z urządzeniami. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure IoT Hub?](about-iot-hub.md)

## <a name="free-account"></a>Bezpłatne konto
Możesz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/) ukończenie samouczki usługi IoT Hub i eksperymentować z usługi IoT Hub (i innych usług platformy Azure).

## <a name="gateway"></a>Brama
Brama zapewnia łączność urządzeń, które nie może połączyć się bezpośrednio do [usługi IoT Hub](#iot-hub). Zobacz też [pola bramy](#field-gateway), [bramy w chmurze](#cloud-gateway), i [bram](#custom-gateway).

## <a name="identity-registry"></a>Rejestr tożsamości
[Rejestr tożsamości](iot-hub-devguide-identity-registry.md) wbudowany składnik usługi IoT hub, która przechowuje informacje o poszczególnych urządzeniach może nawiązać połączenie z Centrum IoT hub.

## <a name="interactive-message"></a>Interaktywne wiadomości
Interaktywne komunikat [chmury do urządzenia](#cloud-to-device) komunikat, który wywołuje natychmiastowego akcji w zapleczu rozwiązania. Na przykład urządzenie może wysyłać alarmu o awarii, które powinny być automatyczne logowanie do systemu CRM.

[!INCLUDE [azure-iot-hub-edge-glossary-includes](../../includes/azure-iot-hub-edge-glossary-includes.md)]

## <a name="iot-hub"></a>Usługa IoT Hub
IoT Hub to w pełni zarządzana usługa platformy Azure, która umożliwia bezpieczną i niezawodną dwukierunkową komunikację między milionami urządzeń a zapleczem rozwiązania. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure IoT Hub?](about-iot-hub.md) Za pomocą usługi [subskrypcji platformy Azure](#subscription), możesz utworzyć centra IoT Hub do obsługi komunikatów obciążeń IoT.

## <a name="iot-hub-metrics"></a>Metryki usługi IoT Hub
[Metryki usługi IoT Hub](iot-hub-metrics.md) udostępnia dane o stanie centrów IoT w swojej [subskrypcji platformy Azure](#subscription). Metryki usługi IoT Hub umożliwiają oceny ogólnej kondycji usługi i dołączone do niego urządzenia. Metryki usługi IoT Hub może pomóc w Zobacz, co się dzieje z Centrum IoT i badanie głównej przyczyny problemów bez konieczności skontaktuj się z działem pomocy technicznej platformy Azure.

## <a name="iot-hub-query-language"></a>Język zapytań usługi IoT Hub
[Język zapytań usługi IoT Hub](iot-hub-devguide-query-language.md) jest języka przypominającego SQL, która pozwala przesyłać zapytania usługi [](#job) i bliźniacze reprezentacje urządzeń.

## <a name="iot-hub-resource-rest-api"></a>Usługa IoT Hub zasobu interfejsu API REST
Możesz użyć [interfejsu API REST zasobów usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/iothubresource) Zarządzanie centrów IoT w swojej [subskrypcji platformy Azure](#subscription) wykonywanie operacji, takich jak tworzenie, aktualizowanie i Usuwanie koncentratorów.

## <a name="iot-solution-accelerators"></a>Akceleratory rozwiązań IoT
Akceleratory rozwiązań Azure IoT pakietu ze sobą wiele usług platformy Azure do rozwiązań. Rozwiązania te umożliwiają szybkie rozpoczynanie pracy z implementacjami end-to-end typowych scenariuszy IoT. Aby uzyskać więcej informacji, zobacz [co to są Akceleratory rozwiązań Azure IoT?](../iot-accelerators/about-iot-accelerators.md)

## <a name="the-iot-extension-for-azure-cli"></a>Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure 
[Rozszerzenia IoT dla interfejsu wiersza polecenia platformy Azure](https://github.com/Azure/azure-iot-cli-extension) to narzędzie dla wielu platform, wiersza polecenia. To narzędzie umożliwia zarządzanie urządzeniami w [rejestr tożsamości](#identity-registry), wysyłanie i odbieranie wiadomości i plików z urządzeń i monitorowanie operacji centrum IoT.

## <a name="job"></a>Zadanie
Można użyć w zapleczu rozwiązania [zadań](iot-hub-devguide-jobs.md) do planowania i śledzenia działań na zestawie urządzeń zarejestrowanych za pomocą usługi IoT hub. Działania obejmują aktualizacji bliźniaczej reprezentacji urządzenia [żądane właściwości](#desired-properties), zaktualizować bliźniaczą reprezentację urządzenia [tagi](#tags)i wywoływanie [metody bezpośrednie](#direct-method). [Usługa IoT Hub](#iot-hub) używa również do [zaimportować i wyeksportować](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) z [rejestr tożsamości](#identity-registry).

## <a name="modules"></a>Moduły
Na stronie urządzenia zestawy SDK urządzeń IoT Hub umożliwiają tworzenie [modułów](iot-hub-devguide-module-twins.md) gdzie każdy z nich zostanie otwarty niezależnie od połączenia usługi IoT Hub. Ta funkcja umożliwia użycie oddzielnych przestrzeni nazw dla różnych składników na urządzeniu.

Moduł tożsamości i bliźniaczą reprezentację modułu zapewniają takie same możliwości jak [tożsamości urządzenia](#device-identity) i [bliźniaczej reprezentacji urządzenia](#device-twin) , ale w bardziej szczegółowy. To bardziej szczegółowy umożliwia zdolne do urządzeń, takich jak urządzenia z systemem operacyjnym lub oprogramowania układowego urządzenia zarządzania wiele składników, aby odizolować konfiguracji i warunki dla każdego z tych składników.

## <a name="module-identity"></a>Moduł tożsamości
Tożsamość modułu jest unikatowy identyfikator przypisany do każdego modułu, który należy do urządzenia. Moduł tożsamości również jest zarejestrowany w [rejestr tożsamości](#identity-registry).

## <a name="module-twin"></a>Bliźniacza reprezentacja modułu
Podobnie jak w bliźniaczej reprezentacji urządzenia, bliźniacza reprezentacja modułu to dokument JSON, która przechowuje informacje o stanie modułu, takie jak metadane, konfiguracje i warunki. Usługa IoT Hub utrzymuje bliźniaczą reprezentację modułu tożsamości każdego modułu, która obsługi administracyjnej w obszarze tożsamość urządzenia w usłudze IoT hub. Bliźniaczych reprezentacjach modułów umożliwia synchronizowanie modułu warunków i konfiguracje między modułem i zapleczem rozwiązania. Można tworzyć zapytania bliźniaczych reprezentacjach modułów, aby znaleźć określone moduły i wykonać zapytanie o stan długotrwałych operacji.

## <a name="mqtt"></a>MQTT
[MQTT](https://mqtt.org/) jest jedną z wiadomości, protokoły [usługi IoT Hub](#iot-hub) obsługuje do komunikowania się z urządzeniami. Aby uzyskać więcej informacji na temat protokołów obsługi komunikatów, obsługiwanych przez usługę IoT Hub, zobacz [wysyłania i odbierania komunikatów za pomocą usługi IoT Hub](iot-hub-devguide-messaging.md).

## <a name="operations-monitoring"></a>Monitorowanie operacji
Usługa IoT Hub [monitorowanie operacji](iot-hub-operations-monitoring.md) umożliwia monitorowanie stanu operacji w Centrum IoT w czasie rzeczywistym. [Usługa IoT Hub](#iot-hub) śledzi zdarzenia w ramach kilku kategorii operacji. Możesz zdecydować się na wysyłanie zdarzeń z jedną lub więcej kategorii do punktu końcowego usługi IoT Hub na potrzeby przetwarzania. Można monitorować dane dotyczące błędów lub skonfigurować bardziej złożone przetwarzanie na podstawie wzorców danych.

## <a name="physical-device"></a>Urządzenie fizyczne
Urządzenie fizyczne to rzeczywistego urządzenia, takich jak Raspberry Pi, który nawiązuje połączenie z Centrum IoT hub. Dla wygody samouczki usługi IoT Hub korzystać [symulowane urządzenia](#simulated-device) umożliwiające uruchamianie przykładów na komputerze lokalnym.

## <a name="primary-and-secondary-keys"></a>Klucze podstawowe i pomocnicze
Podczas nawiązywania połączenia przeznaczonych dla określonego urządzenia lub przeznaczonych dla usług punktu końcowego w Centrum IoT hub z [parametry połączenia](#connection-string) zawiera klucz, aby przyznać dostęp. Po dodaniu urządzenia do [rejestr tożsamości](#identity-registry) lub dodać [współużytkowane zasady dostępu](#shared-access-policy) z Centrum usługi generuje klucz podstawowy i pomocniczy. Mając dwa klucze umożliwia przechodzą z jednego klucza do innego, po zaktualizowaniu klucza bez utraty dostępu do usługi IoT hub.

## <a name="protocol-gateway"></a>Brama protokołu
Brama protokołu jest zazwyczaj wdrażana w chmurze i zapewnia usługi translacji dla urządzenia łączące się z protokół [usługi IoT Hub](#iot-hub). Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure IoT Hub?](about-iot-hub.md)

## <a name="quotas-and-throttling"></a>Limity przydziału i ograniczanie wydajności
Istnieją różne [przydziały](iot-hub-devguide-quotas-throttling.md) przeznaczonych do używania [usługi IoT Hub](#iot-hub), wiele przydziały różnią się w zależności od warstwy usługi IoT hub. [Usługa IoT Hub](#iot-hub) ma również zastosowanie [ogranicza](iot-hub-devguide-quotas-throttling.md) dotyczące używania usługi w czasie wykonywania.

## <a name="reported-configuration"></a>Zgłoszone konfiguracji
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), zgłaszane, konfiguracji, który odwołuje się do pełnego zestawu właściwości i metadanych w bliźniaczej reprezentacji urządzenia, które powinny być raportowane do zaplecza rozwiązania.

## <a name="reported-properties"></a>Zgłaszane właściwości
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), zgłoszone właściwości jest podciągiem bliźniaczej reprezentacji urządzenia używane z [żądane właściwości](#desired-properties) zsynchronizować konfiguracji urządzenia lub warunek. Zgłaszane właściwości można ustawić tylko przy [aplikacji urządzenia](#device-app) i może odczytywać i sprawdzać za [aplikacji zaplecza](#back-end-app).

## <a name="resource-group"></a>Grupa zasobów
[Usługa Azure Resource Manager](#azure-resource-manager) korzysta z grup zasobów do grupowania powiązanych zasobów. Grupa zasobów służy do wykonywania operacji na wszystkie zasoby w grupie jednocześnie.

## <a name="retry-policy"></a>Zasady ponawiania
Użyj zasad ponawiania do obsługi [błędów przejściowych](/azure/architecture/best-practices/transient-faults) po nawiązaniu połączenia z usługą w chmurze.

## <a name="routing-rules"></a>Reguły routingu
Możesz skonfigurować [reguł routingu](iot-hub-devguide-messages-read-custom.md) w usłudze IoT hub do rozsyłania komunikatów z urządzenia do chmury do [wbudowany punkt końcowy](#built-in-endpoints) lub [niestandardowe punkty końcowe](#custom-endpoints) do przetworzenia przez w zapleczu rozwiązania .

## <a name="sasl-plain"></a>ZWYKŁY SASL
ZWYKŁY SASL jest protokołem, który korzysta z protokołu AMQP do przesyłania tokenów zabezpieczających.

## <a name="service-rest-api"></a>Interfejs API REST usługi
Możesz użyć [interfejsu API REST usługi](https://docs.microsoft.com/rest/api/iothub/service) z rozwiązania zaplecza do zarządzania urządzeniami. Interfejs API umożliwia pobieranie i aktualizowanie [bliźniaczej reprezentacji urządzenia](#device-twin) wywoływanie właściwości [metody bezpośrednie](#direct-method)i harmonogram [zadań](#job). Zwykle, należy użyć jednej z wyższego poziomu [usługi SDK](#azure-iot-service-sdks) pokazany w ramach samouczków usługi IoT Hub.

## <a name="shared-access-signature"></a>Sygnatura dostępu współdzielonego
Udostępnione sygnatur dostępu (SAS) to mechanizm uwierzytelniania, na podstawie bezpiecznego skróty SHA-256 lub identyfikatory URI. Uwierzytelniania sygnatury dostępu Współdzielonego ma dwa składniki: _zasady dostępu współużytkowanego_ i _sygnatura dostępu współdzielonego_ (często nazywanej tokenu). Urządzenie korzysta z sygnatury dostępu Współdzielonego do uwierzytelniania za pomocą usługi IoT hub. [Zaplecza aplikacji](#back-end-app) również użyć do uwierzytelniania za pomocą punktów końcowych przeznaczonych dla usługi w Centrum IoT hub sygnatury dostępu Współdzielonego. Zazwyczaj obejmują tokenu sygnatury dostępu Współdzielonego w [parametry połączenia](#connection-string) czy aplikacja używa do nawiązywania połączenia z Centrum IoT hub.

## <a name="shared-access-policy"></a>Zasady dostępu współdzielonego
Zasady dostępu współdzielonego definiuje uprawnienia nadane dla każdego, kto ma prawidłowe [klucz podstawowy lub pomocniczy](#primary-and-secondary-keys) skojarzone z tą zasadą. Możesz określić zasady dostępu współdzielonego i klucze Centrum w [portal](#azure-portal).

## <a name="simulated-device"></a>Symulowane urządzenie
Dla wygody wiele samouczków usługi IoT Hub umożliwia symulowane urządzenia umożliwiają uruchamianie przykładów na komputerze lokalnym. Z kolei [urządzenia fizycznego](#physical-device) jest rzeczywistego urządzenia, takich jak Raspberry Pi, który nawiązuje połączenie z Centrum IoT hub.

## <a name="solution"></a>Rozwiązanie
A _rozwiązania_ mogą odwoływać się do rozwiązania programu Visual Studio, który zawiera jeden lub więcej projektów. A _rozwiązania_ może również odnosić się do rozwiązania IoT, który zawiera elementy, takie jak urządzenia, [aplikacji dla urządzeń](#device-app), Centrum IoT hub, innych usług platformy Azure i [aplikacji zaplecza](#back-end-app).

## <a name="subscription"></a>Subskrypcja
Subskrypcja platformy Azure jest o tym, gdzie odbywa się rozliczanie. Tworzenie poszczególnych zasobów platformy Azure lub usługi platformy Azure, którego używasz jest skojarzony z jedną subskrypcją. Wiele przydziały są również stosowane na poziomie subskrypcji.

## <a name="system-properties"></a>Właściwości systemu
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), właściwości systemu są przeznaczone tylko do odczytu i zawierają informacje na temat użycia urządzenia, np. ostatni stan połączenia i czas działania.

## <a name="tags"></a>`Tags`
W kontekście [bliźniaczej reprezentacji urządzenia](iot-hub-devguide-device-twins.md), tagi są metadane urządzenia przechowywane i pobierane przez zaplecze rozwiązania w postaci dokumentów JSON. Tagi nie są widoczne dla aplikacji na urządzeniu.

## <a name="telemetry"></a>Telemetria
Urządzenia zbierania danych telemetrycznych, takich jak prędkość wiatru lub temperatury i umożliwia wysyłanie danych telemetrycznych do Centrum IoT komunikaty punktu danych.

## <a name="token-service"></a>Usługa tokenu
Usługa tokenu umożliwia zaimplementować mechanizm uwierzytelniania dla urządzeń. Używa usługi IoT Hub [współużytkowane zasady dostępu](#shared-access-policy) z **DeviceConnect** uprawnień do utworzenia *urządzenia o określonym zakresie* tokenów. Te tokeny włączyć w urządzeniu połączyć się z Centrum IoT hub. Urządzenie używa mechanizmu uwierzytelniania niestandardowego do uwierzytelniania za pomocą usługi tokenu. Jeśli urządzenie uwierzytelnia pomyślnie, usługi tokenu wystawia token SAS dla urządzenia na potrzeby dostępu do usługi IoT hub.

## <a name="twin-queries"></a>Zapytania dotyczące bliźniaczych reprezentacji
[Zapytania dotyczące bliźniaczych reprezentacji urządzeń i moduł](iot-hub-devguide-query-language.md) język zapytań przypominający SQL IoT Hub umożliwia pobieranie informacji z bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacjach modułów. Można użyć tego samego języka zapytań usługi IoT Hub można pobrać informacji o [](#job) działające w Twoim Centrum IoT hub.

## <a name="twin-synchronization"></a>Bliźniacza reprezentacja synchronizacji
Bliźniacza reprezentacja używa synchronizacji [żądane właściwości](#desired-properties) bliźniaczych reprezentacji urządzeń lub bliźniaczych reprezentacjach modułów, skonfiguruj urządzenia lub moduły i pobieranie [zgłaszanych właściwości](#reported-properties) od nich w celu przechowywania w bliźniaczej reprezentacji.

## <a name="x509-client-certificate"></a>Certyfikatu klienta X.509
Urządzenie umożliwia uwierzytelnianie za pomocą certyfikatu X.509 [usługi IoT Hub](#iot-hub). Za pomocą certyfikatu X.509 jest alternatywa dla użycia [tokenu sygnatury dostępu Współdzielonego](#shared-access-signature).
