---
title: Przewodnik po rozwiązaniu połączonej fabryki — Azure | Microsoft Docs
description: Opis akceleratora rozwiązania Azure IoT połączonej fabryki oraz jego architektury.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 950d248d2525f053981c8642ee2d39021b9a0494
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/11/2019
ms.locfileid: "59490363"
---
# <a name="connected-factory-solution-accelerator-walkthrough"></a>Przewodnik po akceleratorze rozwiązania połączonej fabryki

[Akcelerator rozwiązania][lnk-preconfigured-solutions] połączonej fabryki to implementacja kompleksowego rozwiązania przemysłowego, które:

* Łączy się zarówno z symulowanymi urządzeniami przemysłowymi, na których działają serwery OPC UA na liniach produkcyjnych symulowanej fabryki, jak i z rzeczywistymi urządzeniami serwerów OPC UA. Aby uzyskać więcej informacji na temat OPC UA, zobacz [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md).
* Pokazuje operacyjne kluczowe wskaźniki wydajności oraz ogólną wydajność sprzętu dla tych urządzeń i linii produkcyjnych.
* Demonstruje, jak aplikacja w chmurze może zostać użyta na potrzeby interakcji z systemami serwerów OPC UA.
* Umożliwia połączenie własnych urządzeń serwerów OPC UA.
* Umożliwia przeglądanie i modyfikowanie danych serwera OPC UA.
* Integruje się z usługą Azure Time Series Insights (TSI) w celu udostępnienia niestandardowych widoków danych z serwerów OPC UA.

Możesz go użyć jako punktu wyjścia dla Twojej własnej implementacji i je [dostosować][lnk-customize], aby spełniało Twoje własne określone wymagania biznesowe.

Ten artykuł przeprowadzi Cię przez niektóre kluczowe elementy rozwiązania połączonej fabryki, aby umożliwić Ci zrozumienie jego sposobu działania. W artykule opisano również sposób przepływu danych przez rozwiązanie. Ta wiedza ułatwi Ci:

* Rozwiązywanie problemów w rozwiązaniu.
* Planowanie sposobu dostosowywania rozwiązania, aby spełniało Twoje wymagania.
* Projektowanie własnego rozwiązania IoT korzystającego z usług Azure.

Aby uzyskać więcej informacji, zobacz [Connected Factory FAQ (Połączona fabryka — często zadawane pytania)](iot-accelerators-faq-cf.md).

## <a name="logical-architecture"></a>Architektura logiczna

Na poniższym diagramie przedstawiono składniki logiczne tego akceleratora rozwiązania:

![Architektura logiczna połączonej fabryki][connected-factory-logical]

## <a name="communication-patterns"></a>Wzorce komunikacji

Rozwiązanie używa [specyfikacji OPC UA Pub/Sub](https://opcfoundation.org/news/opc-foundation-news/opc-foundation-announces-support-of-publish-subscribe-for-opc-ua/) do wysyłania danych telemetrycznych OPC UA do centrum IoT Hub w formacie JSON. W tym celu rozwiązanie używa modułu [OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) IoT Edge.

Rozwiązanie zawiera również klienta OPC UA zintegrowanego w aplikacji internetowej, który może nawiązywać połączenia z lokalnymi serwerami OPC UA. Klient używa [zwrotnego serwera proxy](https://wikipedia.org/wiki/Reverse_proxy) i otrzymuje pomoc z centrum IoT Hub, aby nawiązywać połączenie bez potrzeby otwierania portów w lokalnej zaporze. Ten wzorzec komunikacji jest zwany komunikacji korzystającej z usług. W tym celu rozwiązanie używa modułu [OPC Proxy](https://github.com/Azure/iot-edge-opc-proxy/) IoT Edge.


## <a name="simulation"></a>Symulacja

Symulowane stacje i symulowane systemy zarządzania produkcją (MES) składają się na linię produkcyjną fabryki. Symulowane urządzenia i moduł wydawcy OPC są oparte na [standardzie OPC UA .NET][lnk-OPC-UA-NET-Standard] opublikowanym przez organizację OPC Foundation.

Składniki serwer proxy OPC i wydawca OPC są implementowane jako moduły oparte na usłudze [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Każda symulowana linia produkcyjna ma bramę dołączone.

Wszystkie składniki symulacji działają w kontenerach platformy Docker hostowanych na maszynie wirtualnej platformy Azure z systemem Linux. Symulacja jest domyślnie skonfigurowana do uruchamiania ośmiu symulowanych linii produkcyjnych.

## <a name="simulated-production-line"></a>Symulowana linia produkcyjna

Na linii produkcyjnej są produkowane części. Składa się z różnych stacji: stacji montażowej, stacji testowania i stacji pakowania.

Symulacja przetwarza i aktualizuje dane, które są udostępniane za pośrednictwem węzłów OPC UA. Wszystkie stacje symulowanej linii produkcyjnej są zarządzane przez system MES za pośrednictwem serwera OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Symulowany system zarządzania produkcją

System MES monitoruje poszczególne stacje na linii produkcyjnej za pośrednictwem serwera OPC UA w celu wykrycia zmian stanu stacji. Ona wywołuje metody służące do sterowania z stacje OPC UA i przekazuje produkt z jednej stację do następnej do czasu jego zakończenia.

## <a name="gateway-opc-publisher-module"></a>Moduł wydawcy OPC bramy

Moduł wydawcy OPC łączy się z serwerami OPC UA stacji i subskrybuje węzły OPC do opublikowania. Moduł:

1. Konwertuje dane węzła do formatu JSON.
1. Szyfruje za pomocą pliku JSON.
1. Wysyła dane JSON do usługi IoT Hub jako OPC UA komunikatów publikowania/subskrybowania.

Moduł wydawcy OPC wymaga tylko wychodzącego portu https (443) i może współdziałać z istniejącą infrastrukturą przedsiębiorstwa.

## <a name="gateway-opc-proxy-module"></a>Moduł serwera proxy OPC bramy

Moduł serwera proxy OPC UA bramy tworzy tunel do przesyłania poleceń binarnych oraz komunikatów sterujących serwera OPC UA i wymaga tylko wychodzącego portu https (443). Może on współdziałać z istniejącą infrastrukturą przedsiębiorstwa, w tym z internetowymi serwerami proxy.

Używa metod urządzeń usługi IoT Hub do transferowania pakietowych danych TCP/IP w warstwie aplikacji, aby upewnić się, zaufanie punktu końcowego, szyfrowanie danych i integralność przy użyciu protokołu SSL/TLS.

Protokół przekazywania danych binarnych serwera OPC UA za pośrednictwem serwera proxy korzysta z uwierzytelniania i szyfrowania UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Moduł wydawcy OPC bramy subskrybuje węzły serwera OPC UA w celu wykrycia zmian w wartościach danych. Jeśli w jednym z węzłów zostanie wykryta zmiana danych, ten moduł wysyła komunikaty do usługi Azure IoT Hub.

Usługa IoT Hub stanowi źródło zdarzeń dla usługi Azure TSI. Dane są przechowywane w usłudze TSI przez 30 dni na podstawie sygnatur czasowych dołączonych do komunikatów. Te dane obejmują:

* Identyfikator ApplicationUri serwera OPC UA
* Identyfikator NodeId serwera OPC UA
* Wartość węzła
* Sygnaturę czasową źródła
* Nazwę DisplayName serwera OPC UA

Obecnie usługa TSI Przesyła nie zezwala klientom dostosować, jak długo chcesz przechowywać dane dla.

Usługa TSI Przesyła zapytania dane węzła przy użyciu opartych na czasie **SearchSpan** i agreguje przez **identyfikator applicationuri serwera OPC UA** lub **OPC UA NodeId** lub **OPC UA DisplayName**.

Aby pobrać dane dla mierników ogólnej wydajności sprzętu i kluczowych wskaźników wydajności oraz wykresów serii czasu, rozwiązanie agreguje dane według liczby zdarzeń, **suma**, **Avg**, **Min**, i  **Maksymalna liczba**.

Serie czasu są tworzone w inny sposób. Rozwiązanie oblicza wartości ogólnej wydajności sprzętu i kluczowych wskaźników wydajności na podstawie danych podstawowych stacji i propaguje wartości dla linii produkcyjnych, fabryk i enterprise.

Ponadto serie czasu dla topologii ogólnej wydajności sprzętu i kluczowych wskaźników wydajności są obliczane w aplikacji za każdym razem, kiedy wyświetlany przedział czasu jest gotowy. Na przykład widok dnia jest aktualizowany zawsze o pełnej godzinie.

Widok serii czasu danych węzła pochodzi bezpośrednio z usługi TSI i jest tworzony za pomocą agregacji dla przedziału czasu.

## <a name="iot-hub"></a>Usługa IoT Hub
Usługa [IoT Hub][lnk-IoT Hub] odbiera dane wysyłane z modułu wydawcy OPC do chmury i udostępnia je usłudze Azure TSI. 

Usługa IoT Hub w rozwiązaniu wykonuje ponadto następujące czynności:
- Obsługuje rejestr tożsamości, w którym są przechowywane identyfikatory dla wszystkich modułów wydawcy OPC i wszystkich modułów serwera proxy OPC.
- Służy jako kanał transportowy komunikacji dwukierunkowej modułu serwera proxy OPC.

## <a name="azure-storage"></a>Azure Storage
To rozwiązanie używa usługi Azure Blob Storage jako magazynu dysków dla maszyny wirtualnej i do przechowywania danych wdrożenia.

## <a name="web-app"></a>Aplikacja internetowa
Aplikacja internetowa wdrożona w ramach akcelerator rozwiązań obejmuje zintegrowanego klienta OPC UA, przetwarzanie alertów i wizualizację danych telemetrycznych.

## <a name="telemetry-data-flow"></a>Przepływ danych telemetrycznych

![Przepływ danych telemetrycznych](./media/iot-accelerators-connected-factory-sample-walkthrough/telemetry_dataflow.png)

### <a name="flow-steps"></a>Kroki przepływu

1. Wydawca OPC odczytuje wymagane certyfikaty OPC UA X 509 i poświadczenia zabezpieczeń usługi IoT Hub z lokalnego magazynu certyfikatów.
    - W razie potrzeby wydawca OPC tworzy i zapisuje wszelkie brakujące certyfikaty lub poświadczenia w magazynie certyfikatów.

2. Wydawca OPC rejestruje się za pomocą usługi IoT Hub.
    - Używa skonfigurowanego protokołu. Może użyć dowolnego obsługiwanego protokołu zestawu SDK klienta usługi IoT Hub. Domyślnie jest to MQTT.
    - Protokół komunikacji jest chroniony przez protokół TLS.

3. Wydawca OPC odczytuje plik konfiguracji.

4. Wydawca OPC tworzy sesję OPC za pomocą każdego skonfigurowanego serwera OPC UA.
    - Używa połączenia TCP.
    - Wydawca OPC i serwer OPC UA wzajemne się uwierzytelniają przy użyciu certyfikatów X509.
    - Cały dalszy ruch OPC UA jest szyfrowany przez skonfigurowany mechanizm szyfrowania OPC UA.
    - Wydawca OPC tworzy subskrypcję OPC w sesji OPC dla każdego skonfigurowanego odstępu czasu publikowania.
    - Tworzy monitorowane elementy OPC dla węzłów OPC w celu opublikowania w subskrypcji OPC.

5. Jeśli monitorowana wartość węzła OPC ulegnie zmianie, serwer OPC UA wysyła aktualizacje do wydawcy OPC.

6. Wydawca OPC transkoduje nową wartość.
    - Dzieli wiele zmian na partie, jeśli włączone jest dzielenie na partie.
    - Tworzy komunikat usługi IoT Hub.

7. Wydawca OPC wysyła komunikat do usługi IoT Hub.
    - Używa skonfigurowanego protokołu.
    - Komunikacja jest chroniona przez protokół TLS.

8. Usługa Time Series Insights (TSI) odczytuje komunikaty z usługi IoT Hub.
    - Używa protokołu AMQP za pośrednictwem protokołu TCP/TLS.
    - Ten krok jest wewnętrzny dla centrum danych.

9. Dane magazynowane w usłudze TSI.

10. Aplikacja internetowa połączonej fabryki w zapytaniach usługi Azure AppService wymaga danych z usługi TSI.
    - Używa komunikacji zabezpieczonej protokołem TCP/TLS.
    - Ten krok jest wewnętrzny dla centrum danych.

11. Przeglądarka internetowa łączy się z aplikacją internetową połączonej fabryki.
    - Przedstawia pulpit nawigacyjny połączonej fabryki.
    - Nawiązuje połączenie za pośrednictwem protokołu HTTPS.
    - Dostęp do aplikacji połączonej fabryki wymaga uwierzytelnienia użytkownika za pośrednictwem usługi Azure Active Directory.
    - Wszelkie wywołania internetowego interfejsu API do aplikacji połączonej fabryki są chronione przez tokeny zabezpieczające przed fałszerstwem.

12. Podczas aktualizacji danych internetowa aplikacja połączonej fabryki wysyła zaktualizowane dane do przeglądarki internetowej.
    - Korzysta z protokołu SignalR.
    - Zabezpieczone przez protokół TCP/TLS.

## <a name="browsing-data-flow"></a>Przeglądanie przepływu danych

![Przeglądanie przepływu danych](./media/iot-accelerators-connected-factory-sample-walkthrough/browsing_dataflow.png)

### <a name="flow-steps"></a>Kroki przepływu

1. Jest uruchamiany serwer proxy OPC (składnik serwera).
    - Odczytuje udostępnione klucze dostępu z lokalnego magazynu.
    - W razie potrzeby zapisuje brakujące klucze dostępu w magazynie.

2. Serwer proxy OPC (składnik serwera) rejestruje się za pomocą usługi IoT Hub.
    - Odczytuje wszystkie sobie znane urządzenia z usługi IoT Hub.
    - Używa protokołu MQTT za pośrednictwem protokołu TLS przez gniazdo lub bezpieczny protokół WebSocket.

3. Przeglądarka internetowa nawiązanie połączenia z połączonych aplikacji sieci Web fabryki i przedstawia pulpit nawigacyjny połączonej fabryki.
    - Używa protokołu HTTPS.
    - Użytkownik wybierze serwer OPC UA do nawiązania połączenia.

4. Aplikacja internetowa połączonej fabryki ustanawia sesję OPC UA z wybranym serwerem OPC UA.
    - Używa stosu OPC UA.

5. Transport serwera proxy OPC odbiera żądanie ze stosu OPC UA w celu ustanowienia połączenia gniazda TCP z serwerem OPC UA.
    - Po prostu pobiera ładunek TCP i korzysta z niego bez zmian.
    - Ten krok jest wewnętrzny dla aplikacji internetowej połączonej fabryki.

6. Serwer proxy OPC (składnik klienta) wyszukuje urządzenie serwera proxy OPC (składnik serwera) w rejestrze urządzeń usługi IoT Hub. Następnie wywołuje on metodę urządzenia serwera proxy OPC (składnik serwera) w usłudze IoT Hub.
    - Używa protokołu HTTPS za pośrednictwem protokołu TCP/TLS do wyszukiwania serwera proxy OPC.
    - Używa protokołu HTTPS za pośrednictwem protokołu TCP/TLS, aby nawiązać połączenie gniazda TCP z serwerem OPC UA.
    - Ten krok jest wewnętrzny dla centrum danych.

7. Usługa IoT Hub wywołuje metodę urządzenia w urządzeniu serwera proxy OPC (składnik serwera).
    - Używa nawiązanego połączenia protokołu MQTT za pośrednictwem protokołu TLS za pośrednictwem gniazda lub bezpiecznego protokołu Websocket, aby nawiązać połączenie gniazda TCP z serwerem OPC UA.

8. Serwer proxy OPC (składnik serwera) wysyła ładunek TCP do sieci produkcyjnej.

9. Serwer OPC UA przetwarza ładunek i odsyła odpowiedź.

10. Odpowiedź jest odbierana przez gniazdo serwera proxy OPC (składnik serwera).
    - Serwer proxy OPC wysyła dane jako wartość zwracaną metody urządzenia do usługi IoT Hub i serwera proxy OPC (składnik klienta).
    - Te dane są dostarczane do stosu OPC UA w aplikacji połączonej fabryki.

11. Internetowa aplikacja połączonej fabryki zwraca UX przeglądarki OPC wzbogacone o informacje specyficzne dla OPC UA otrzymane z serwera OPC UA, do przeglądarki internetowej w celu renderowania.
    - Gdy użytkownik przegląda przestrzeni adresowej OPC i dotyczą funkcji do węzłów w przestrzeni adresowej OPC, klienta UX przeglądarki OPC używa wywołań AJAX za pośrednictwem protokołu HTTPS zabezpieczony za pomocą tokenów zabezpieczających przed sfałszowaniem można pobrać danych z połączonych aplikacji sieci Web fabryki.
    - W razie potrzeby klient używa komunikacji objaśnionej w krokach od 4 do 10 w celu wymiany informacji z serwerem OPC UA.

> [!NOTE]
> Serwer proxy OPC (składnik serwera) i składnik serwera proxy OPC (klient) wykonuje kroki od 4 do 10 dla całego ruchu TCP związanego z komunikacją OPC UA.

> [!NOTE]
> Dla serwera OPC UA i stosu OPC UA w aplikacji internetowej połączonej fabryki komunikacja serwera proxy OPC jest niewidoczna i mają zastosowanie wszystkie funkcje zabezpieczeń OPC UA dla uwierzytelniania i szyfrowania.

## <a name="next-steps"></a>Kolejne kroki

Możesz kontynuować poznawanie akceleratorów rozwiązań IoT, czytając następujące artykuły:

* [Uprawnienia w witrynie azureiotsolutions.com][lnk-permissions]
* [Wdrażanie bramy w systemie Windows lub Linux dla akceleratora rozwiązania połączonej fabryki](iot-accelerators-connected-factory-gateway-deployment.md)
* [OPC Publisher reference implementation (Implementacja referencyjna wydawcy OPC)](https://github.com/Azure/iot-edge-opc-publisher/blob/master/README.md).

[connected-factory-logical]:media/iot-accelerators-connected-factory-sample-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]:about-iot-accelerators.md
[lnk-customize]: iot-accelerators-connected-factory-customize.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-accelerators-faq.md
