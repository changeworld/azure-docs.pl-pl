---
title: Technologie i rozwiązania Internetu rzeczy (IoT) na platformie Azure
description: W tym artykule opisano kolekcję technologii i usług, których można użyć do utworzenia rozwiązania Azure IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.openlocfilehash: 0b04b5170c13f6f6c3fd74976461f03e4367060a
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77046063"
---
# <a name="azure-technologies-and-services-for-creating-iot-solutions"></a>Technologie i usługi platformy Azure do tworzenia rozwiązań IoT

Technologie i usługi Azure IoT zapewniają opcje tworzenia szerokiej gamy rozwiązań IoT, które umożliwiają transformację cyfrową w organizacji. Można na przykład:

- Użyj [usługi Azure IoT Central](https://apps.azureiotcentral.com), zarządzanej platformy aplikacji IoT, aby utworzyć i wdrożyć bezpieczne rozwiązanie IoT klasy korporacyjnej. Usługa IoT Central zawiera zbiór szablonów aplikacji specyficznych dla branży, takich jak handel detaliczny i opieka zdrowotna, aby przyspieszyć proces opracowywania rozwiązań.
- Rozszerz bazę kodu open source dla [akceleratora rozwiązań](https://www.azureiotsolutions.com) IoT platformy Azure, aby zaimplementować typowy scenariusz IoT, taki jak zdalne monitorowanie lub konserwacja predykcyjna.
- Użyj usług platformy Azure IoT platform, takich jak [Usługa Azure IoT Hub](../iot-hub/about-iot-hub.md) i [zestawów SDK urządzeń IoT platformy Azure,](../iot-hub/iot-hub-devguide-sdks.md) aby utworzyć niestandardowe rozwiązanie IoT od podstaw.

![Technologie, usługi i rozwiązania Azure IoT](./media/iot-services-and-technologies/iot-technologies-services.png)

## <a name="azure-iot-central"></a>Azure IoT Central

[Platforma aplikacji IoT Central](https://apps.azureiotcentral.com) zmniejsza obciążenie i koszty opracowywania, zarządzania i utrzymywania rozwiązań IoT klasy korporacyjnej. Konfigurowalny internetowy interfejs użytkownika usługi IoT Central umożliwia monitorowanie warunków urządzenia, tworzenie reguł i zarządzanie milionami urządzeń i ich danych w całym cyklu życia. Powierzchnia interfejsu API w centrum IoT zapewnia programowy dostęp do konfigurowania rozwiązania IoT i interakcji z nim.

Usługa Azure IoT Central to w pełni zarządzana platforma aplikacji, której można używać do tworzenia niestandardowych rozwiązań IoT. Usługa IoT Central używa szablonów aplikacji do tworzenia rozwiązań. Istnieją szablony dla rozwiązań generycznych i dla konkretnych branż, takich jak energetyka, opieka zdrowotna, rząd i handel detaliczny. Szablony aplikacji IoT Central umożliwiają wdrażanie aplikacji IoT Central w ciągu kilku minut, które można następnie dostosować za pomocą motywów, pulpitów nawigacyjnych i widoków.

Wybierz urządzenia z [katalogu urządzeń certyfikatu platformy Azure dla IoT,](https://catalog.azureiotsolutions.com) aby szybko połączyć się z rozwiązaniem. Interfejs użytkownika sieci Web IoT Central umożliwia monitorowanie urządzeń i zarządzanie nimi w celu utrzymania ich w dobrej kondycji i łączności. Użyj łączników i interfejsów API, aby zintegrować aplikację IoT Central z innymi aplikacjami biznesowymi.

Jako w pełni zarządzana platforma aplikacji, IoT Central ma prosty, przewidywalny model cenowy.

## <a name="azure-iot-solution-accelerators"></a>Akceleratory rozwiązań Azure IoT

[Akceleratory rozwiązań Azure IoT](https://www.azureiotsolutions.com) to zbiór rozwiązań klasy korporacyjnej, które można dostosowywać. Można wdrożyć te rozwiązania w stanie ich zob. lub opracować niestandardowe rozwiązanie IoT przy użyciu kodu źródłowego Java lub .NET.

Akceleratory rozwiązań Azure IoT zapewniają wysoki poziom kontroli nad rozwiązaniem IoT. Akceleratory rozwiązań obejmują wstępnie utworzone rozwiązania dla typowych scenariuszy IoT, które można wdrożyć w ramach subskrypcji platformy Azure w ciągu kilku minut. Scenariusze obejmują:

  - Zdalne monitorowanie
  - Połączona fabryka
  - Konserwacja zapobiegawcza
  - Symulacja urządzenia

Baza kodu open source dla wszystkich akceleratorów rozwiązań jest dostępna w usłudze GitHub. Pobierz kod, aby dostosować akcelerator rozwiązań do określonych wymagań IoT.

Akceleratory rozwiązań używają usług platformy Azure, takich jak Usługa Azure IoT Hub i usługi Azure Storage, którymi należy zarządzać w ramach subskrypcji platformy Azure.

## <a name="custom-solutions"></a>Niestandardowe rozwiązania

Aby utworzyć rozwiązanie IoT od podstaw lub rozszerzyć rozwiązanie utworzone przy użyciu centrum IoT lub akceleratora rozwiązań, użyj co najmniej jednej z następujących technologii i usług Azure IoT:

### <a name="devices"></a>Urządzenia

Twórz swoje urządzenia IoT przy użyciu jednego z [zestawów startowych Usługi Azure IoT](https://catalog.azureiotsolutions.com/kits) lub wybierz urządzenie do użycia z [katalogu urządzeń certyfikatu platformy Azure dla IoT.](https://catalog.azureiotsolutions.com) Zaimplementuj osadzony kod przy użyciu [sdk urządzeń](../iot-hub/iot-hub-devguide-sdks.md)typu open source . Zestaw SDK urządzenia obsługuje wiele systemów operacyjnych, takich jak Linux, Windows i systemy operacyjne w czasie rzeczywistym. Istnieją zestawy SDK dla wielu języków programowania, takich jak [C](https://github.com/Azure/azure-iot-sdk-c), [Node.js](https://github.com/Azure/azure-iot-sdk-node), [Java](https://github.com/Azure/azure-iot-sdk-java), [.NET](https://github.com/Azure/azure-iot-sdk-csharp)i [Python](https://github.com/Azure/azure-iot-sdk-python).

Można dodatkowo uprościć sposób tworzenia osadzonego kodu dla urządzeń za pomocą usługi [IoT Plug and Play Preview.](../iot-pnp/overview-iot-plug-and-play.md) IoT Plug and Play umożliwia deweloperom rozwiązań integrowanie urządzeń z ich rozwiązaniami bez pisania kodu osadzonego. Sednem funkcji IoT Plug and Play jest schemat _modelu możliwości urządzenia,_ który opisuje możliwości urządzenia. Model możliwości urządzenia służy do generowania wbudowanego kodu urządzenia i konfigurowania rozwiązania chmurowego, takiego jak aplikacja IoT Central.

[Usługa Azure IoT Edge](../iot-edge/about-iot-edge.md) umożliwia odciążanie części obciążenia IoT z usług w chmurze platformy Azure do urządzeń. Usługa IoT Edge może zmniejszyć opóźnienia w rozwiązaniu, zmniejszyć ilość danych wymienianych przez urządzenia z chmurą i włączyć scenariusze off-line. Urządzenia IoT Edge można zarządzać zaniku urządzeń IoT Central i niektórych akceleratorów rozwiązań.

[Usługa Azure Sphere](https://docs.microsoft.com/azure-sphere/product-overview/what-is-azure-sphere) to zabezpieczona platforma aplikacji wysokiego poziomu z wbudowanymi funkcjami komunikacji i zabezpieczeń dla urządzeń podłączonych do Internetu. Zawiera zabezpieczoną jednostkę mikrokontrolera, niestandardowy system operacyjny oparty na systemie Linux oraz chmurową usługę zabezpieczeń, która zapewnia ciągłe, odnawialne zabezpieczenia.

### <a name="cloud-connectivity"></a>Łączność z chmurą

Usługa [Azure IoT Hub](../iot-hub/about-iot-hub.md) umożliwia niezawodną i bezpieczną dwukierunkową komunikację między milionami urządzeń IoT a rozwiązaniem chmurowym. [Usługa inicjowania obsługi administracyjnej urządzeń usługi Azure IoT Hub](../iot-dps/about-iot-dps.md) to usługa pomocnicza dla usługi IoT Hub. Usługa zapewnia bezdotykowe, just-in-time in-time inicjowania obsługi administracyjnej urządzeń do odpowiedniego centrum IoT bez konieczności interwencji człowieka. Te funkcje umożliwiają klientom aprowizowanie milionów urządzeń w bezpieczny i skalowalny sposób.

Centrum IoT hub jest podstawowym składnikiem akceleratorów rozwiązań i można go używać do sprostania wyzwaniom związanym z implementacją IoT, takimi jak:

* Utrzymywanie łączności zapewniającej przepływ dużej ilości danych między urządzeniami i zarządzanie nimi.
* Pozyskiwanie dużej ilości danych telemetrycznych.
* Sterowanie urządzeniami i kontrola nad nimi.
* Wymuszanie zabezpieczeń urządzeń.

### <a name="bridging-the-gap-between-the-physical-and-digital-worlds"></a>Niwelowanie przepaści między światem fizycznym i cyfrowym

[Usługa Azure Digital Twins](../digital-twins/about-digital-twins.md) to usługa IoT, która umożliwia modelowanie środowiska fizycznego. Używa wykresu analizy przestrzennej do modelowania relacji między osobami, przestrzeniami i urządzeniami. Dzięki corelatingowi danych w świecie cyfrowym i fizycznym można tworzyć rozwiązania kontekstowe.

Iot Central wykorzystuje cyfrowe bliźniaki do synchronizowania urządzeń i danych w świecie rzeczywistym z modelami cyfrowymi, które umożliwiają użytkownikom monitorowanie i zarządzanie tymi podłączonymi urządzeniami.

### <a name="data-and-analytics"></a>Dane i analiza

Urządzenia IoT zazwyczaj generują duże ilości danych szeregów czasowych, takich jak odczyty temperatury z czujników. [Usługa Azure Time Series Insights](../time-series-insights/time-series-insights-overview.md) może łączyć się z centrum IoT hub, odczytywać strumień danych telemetrycznych z urządzeń, przechowywać te dane i umożliwiać wykonywanie zapytań i wizualizację.

[Usługa Azure Maps](/azure/azure-maps) to zbiór usług geoprzestrzennych, które używają świeżych danych mapowania w celu zapewnienia dokładnego kontekstu geograficznego do aplikacji sieci Web i aplikacji mobilnych. Do tworzenia aplikacji można użyć interfejsu API REST, kontrolki JavaScript opartej na sieci Web lub sdk systemu Android.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać praktyczne doświadczenie, wypróbuj jeden z przewodników Szybki start:

- [Tworzenie aplikacji usługi Azure IoT Central](../iot-central/core/quick-deploy-iot-central.md)
- [Wysyłanie danych telemetrycznych z urządzenia do centrum IoT](../iot-hub/quickstart-send-telemetry-cli.md)
- [Testowanie opartego na chmurze rozwiązania do monitorowania zdalnego](../iot-accelerators/quickstart-remote-monitoring-deploy.md)
