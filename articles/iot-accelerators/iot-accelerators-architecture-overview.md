---
title: Akceleratory rozwiązań IoT odwołania architektura — Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat architektury referencyjnej akceleratorów rozwiązań Azure IoT. Ta architektura referencyjna korzystać z istniejących akceleratorów rozwiązań. Umożliwia także architekturę referencyjną podczas tworzenia własnych niestandardowych rozwiązań IoT.
author: dominicbetts
ms.author: dobett
ms.date: 12/04/2018
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: philmea
ms.openlocfilehash: 01349dbc5fe98fd69ce7219ef6f2029d7bb815d0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067907"
---
# <a name="introduction-to-the-azure-iot-reference-architecture"></a>Wprowadzenie do architektura referencyjna IoT platformy Azure

W tym artykule przedstawiono [architektura referencyjna IoT platformy Azure](https://aka.ms/iotrefarchitecture) oraz przykłady sposób [akceleratorów rozwiązań Azure IoT](about-iot-accelerators.md) wykonaj zalecenia.

Open source [zdalne monitorowanie](iot-accelerators-remote-monitoring-sample-walkthrough.md) i [połączonej fabryki](iot-accelerators-connected-factory-features.md) akceleratorów rozwiązań, należy wykonać wiele zaleceń architektury odwołanie. Akceleratory rozwiązań służy jako punkt początkowy dla własnych rozwiązań IoT lub narzędzi do uczenia.

## <a name="overview"></a>Omówienie

Architektura referencyjna opisano elementy rozwiązania IoT, takich jak zasady technologii, skład usług Azure IoT i urządzeń. Architektura sprawia, że zalecenia dotyczące wdrożenia technologii.

Na najwyższym poziomie można wyświetlić jako stanowią rozwiązania IoT:

* Things generujące i wysyłające dane telemetryczne, takich jak pomiarów i zdarzenia. W akcelerator rozwiązań zdalnego monitorowania urządzeń, takich jak ciężarówek lub windy są rzeczy, które wysyłają dane telemetryczne.
* Szczegółowe dane jest generowany na podstawie danych telemetrycznych wysyłanych z rzeczy. W akcelerator rozwiązania monitorowania zdalnego reguła generuje szczegółowe informacje. Na przykład reguła rozpoznanie temperaturę w aparacie osiąga próg.
* Akcje w szczegółowe informacje, które Pomóż ulepszyć program business lub procesu. W akceleratora rozwiązania monitorowania zdalnego akcji poczty e-mail może powiadamiać operatora o potencjalnym problemie z aparatem.

[Architektura referencyjna IoT platformy Azure](https://aka.ms/iotrefarchitecture) jest dokumentem życia aktualizacji w miarę rozwoju technologii.

## <a name="core-subsystems"></a>Podsystemy Core

Architektura referencyjna identyfikuje podsystemów core pokazano na poniższym diagramie:

![Podsystemy Core](media/iot-accelerators-architecture-overview/coresubsystems1.png)

W poniższych sekcjach opisano, jak składniki akceleratora rozwiązania monitorowania zdalnego mapowania podstawowych podsystemów.

### <a name="iot-devices"></a>Urządzenia IoT

Rozwiązania IoT, należy włączyć bezpiecznych, wydajnych i niezawodnych komunikację między niemal dowolnego rodzaju urządzenia i bramy chmury. Urządzenia są trwałych biznesowe, które w zakresie od czujników temperatury proste do linii produkcyjnych fabryki złożonych z setkami czujniki i składniki.

Bramy w terenie lub urządzenia edge to wyspecjalizowane urządzenie urządzenie lub oprogramowanie ogólnego przeznaczenia, które mogą działać jako:

* Włącznik komunikacji do obsługi konwersji protokołu.
* System kontroli urządzenia lokalnego i centrum przetwarzania danych telemetrycznych urządzenia. Urządzenia usługi edge może przetwarzać dane telemetryczne lokalnie na sterowanie urządzeniami bez komunikowania się z chmurą. Urządzenia usługi edge można również filtrować lub telemetrii urządzenia agregacji, aby zmniejszyć ilość danych telemetrycznych przesyłanych do chmury.

W rozwiązaniu do zdalnego monitorowania urządzenia nawiązać połączenie z Centrum IoT i wysyłania telemetrii do przetworzenia. Rozwiązanie monitorowania zdalnego umożliwia również operatory zarządzanie urządzeniami za pomocą zadania lub urządzenia automatycznego zarządzania. Zestawy SDK urządzeń Azure IoT umożliwia Implementowanie urządzeń.

Wdrożyć rozwiązania do zdalnego monitorowania i zarządzania urządzeniami usługi IoT Edge. Przetwarzanie na urządzeniach brzegowych ułatwia zmniejszenie ilości telemetrii wysyłanych do chmury i przyspieszenie odpowiedzi na zdarzenia urządzenia.

### <a name="cloud-gateway"></a>Brama chmury

Brama chmury umożliwia komunikację do i z urządzeń i urządzenia brzegowe. Te urządzenia mogą być w wielu lokacjach zdalnych.

Brama zarządza komunikacją urządzeń, łącznie z zarządzaniem połączeniami, ochrona ścieżki komunikacji, uwierzytelniania i autoryzacji. Brama również wymusza połączenia i przepływności przydziałów i gromadzi dane telemetryczne, używane do rozliczeń, diagnostyki i innych zadań monitorowania.

Rozwiązania do zdalnego monitorowania wdraża do zapewnienia bezpiecznego punktu końcowego dla urządzeń w celu wysyłania danych telemetrycznych do Centrum IoT hub. Usługa IoT hub również:

* Zawiera Magazyn tożsamości urządzenia, do zarządzania urządzeniami mogą nawiązywać połączenie z rozwiązaniem.
* Włącza rozwiązanie do wysyłania poleceń do urządzeń. Na przykład, aby otworzyć zawór zwolnić ciśnienia.
* Umożliwia zbiorcze zarządzanie urządzeniami. Na przykład aktualizacja oprogramowania układowego na zestawie urządzeń.

### <a name="stream-processing"></a>Przetwarzanie strumienia

Jako rozwiązanie pozyskuje dane telemetryczne, ważne jest zrozumienie, jak przepływ przetwarzanie informacji telemetrycznych mogą się różnić. W zależności od scenariusza rekordów danych może przepływać za pośrednictwem różnych etapach:

* Magazyn, takich jak pamięci podręcznej w pamięci, tymczasowy kolejki i archiwach trwałych.

* Analiza, aby uruchomić wejściowe dane telemetryczne za pomocą zestawu warunków i mogą wygenerować różne wyniki rekordów danych. Na przykład telemetrii wejściowe zakodowane w Avro mogą zwracać dane telemetryczne wyjściowego zakodowane w formacie JSON.

* Oryginalny wejściowych telemetrii i analizy danych wyjściowych rekordy są zazwyczaj przechowywane i dostępne do wyświetlenia. Wejściowy telemetrii i rejestruje dane wyjściowe mogą również wyzwalać akcje, takie jak wiadomości e-mail, bilety zdarzenia i polecenia urządzenia.

Routing może wysyłać danych telemetrycznych do jednego lub więcej punktów końcowych magazynu, procesów analizy i akcje. To rozwiązanie może łączyć etapów w ramach różnych zleceniach i przetwarzać je z jednoczesnych zadań równoległych.

Rozwiązania do zdalnego monitorowania korzysta [usługi Azure Stream Analytics](/azure/stream-analytics/) potrzeby przetwarzania strumienia. Aparat reguł w rozwiązaniu używa zapytania usługi Stream Analytics do generowania alertów i akcji. Na przykład rozwiązanie można użyć zapytania do zidentyfikowania, gdy średnia temperatura w przedziale magazynu truck przez ponad pięć minut spadnie poniżej 36 stopni.

### <a name="storage"></a>Magazyn

Rozwiązania IoT mogą generować duże ilości danych, które są często danych szeregów czasowych. Te dane musi być przechowywany, gdzie może służyć do wizualizacji i raportowania. To rozwiązanie może być również konieczne dostęp do danych później do analizy lub dodatkowego przetwarzania. Jest to często mają podzielić ciepłych i zimnych danych magazynów danych. Magazyn danych bez wyłączania zasilania zawiera najnowsze dane dostępie z małymi opóźnieniami. Magazynu zimnych danych zazwyczaj przechowuje dane historyczne.

Rozwiązania do zdalnego monitorowania korzysta [Azure Time Series Insights](/azure/time-series-insights/) oraz jego magazynu ciepłego danych Cosmos DB jako jego magazynu zimnych danych.

### <a name="ui-and-reporting-tools"></a>Interfejs użytkownika i narzędzi do raportowania

Rozwiązanie zapewnia interfejs użytkownika:

* Dostęp do i wizualizacji, urządzenia, dane i analiza wyników.
* Odnajdywanie urządzeń za pomocą rejestru.
* Sterowanie urządzeniami i kontrola nad nimi.
* Przepływy pracy aprowizacji urządzenia.
* Powiadomienia i alerty.
* Integracja z dynamicznych, interakcyjnych pulpitów nawigacyjnych do wyświetlania danych z dużą liczbą urządzeń.  
* Lokalizacja geograficzna i usług obsługujących geograficznie.

Rozwiązania do zdalnego monitorowania zawiera internetowy interfejs użytkownika do świadczenia tej funkcji. Obejmuje interfejs użytkownika sieci web:

* Interaktywne mapy do określenia lokalizacji urządzenia.
* Dostęp do Eksploratora usługi Time Series Insights w celu odpytywania i analizowania danych telemetrycznych.

### <a name="business-integration"></a>Integracja biznesowa

Warstwy biznesowej w integracji obsługuje integrację rozwiązań IoT za pomocą systemów biznesowych, takich jak CRM, ERP i aplikacji — biznesowych. Przykłady obejmują rozliczanie obsługi, do obsługi klienta i dostarczać części zamiennych.

Rozwiązania do zdalnego monitorowania przy użyciu reguł do wysyłania wiadomości e-mail, gdy warunek jest spełniony. Na przykład rozwiązanie może powiadamianie operatora temperatura w ciężarówki spada poniżej 36 stopni.

## <a name="next-steps"></a>Kolejne kroki

W tym artykule omówiono architektura referencyjna IoT platformy Azure i przedstawiono przykłady jak akceleratora rozwiązania monitorowania zdalnego następuje zalecenia. Następnym krokiem jest do odczytu [architektura referencyjna IoT platformy Microsoft](https://aka.ms/iotrefarchitecture).