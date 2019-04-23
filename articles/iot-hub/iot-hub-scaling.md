---
title: Skalowanie usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Jak skalować usługi IoT hub do obsługi przepływność przewidywanego komunikatów i żądanych funkcji. Zawiera podsumowanie obsługiwana przepływność dla każdej warstwy i opcje dotyczące dzielenia na fragmenty.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: wesmc
ms.openlocfilehash: 8be2e59898ea5fcca1dbf1710974d0420941973e
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002140"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wybrać właściwą warstwę usługi IoT Hub dla Twojego rozwiązania

Każde rozwiązanie IoT jest inny, dzięki czemu usługa Azure IoT Hub oferuje kilka opcji, w oparciu o cenach i skali. W tym artykule mają na celu ułatwić ocenę potrzeb usługi IoT Hub. Aby uzyskać informacje o warstwach usługi IoT Hub, zobacz [cenach usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub).

Aby zdecydować, którą warstwę usługi IoT Hub jest odpowiednia dla danego rozwiązania, spróbuj odpowiedzieć sobie dwa pytania:

**Jakie funkcje chcę użyć?**

Usługa Azure IoT Hub oferuje dwie warstwy, podstawowa i standardowa, które różnią się w wiele funkcji, które obsługują. Jeśli rozwiązanie IoT opiera się wokół zbieranie danych z urządzeń i centralnie je analizować, warstwa basic jest prawdopodobnie odpowiedni dla Ciebie. Jeśli chcesz, zdalne sterowanie urządzeniami IoT lub dystrybucji niektórych obciążeń na samych urządzeń za pomocą bardziej zaawansowanych ustawień konfiguracji, należy rozważyć warstwy standardowa. Aby uzyskać szczegółowy podział, które funkcje dostępne w poszczególnych warstwach w dalszym ciągu [warstw podstawowa i standardowa](#basic-and-standard-tiers).

**Jak dużo danych chcę przenieść codziennie?**

Każda warstwa usługi IoT Hub jest dostępna w trzech rozmiarach, na podstawie ogólnej przepływności ilości danych może obsługiwać w każdym dniu. Te rozmiary numerycznie są identyfikowane jako 1, 2 i 3. Na przykład każda jednostka w Centrum IoT poziomu 1 może obsługiwać tysiąc 400 komunikatów dziennie, gdy jednostka poziomu 3 może obsługiwać 300 milionów. Aby uzyskać więcej informacji na temat wytycznych dane w dalszym ciągu [przepływność komunikatów](#message-throughput).

## <a name="basic-and-standard-tiers"></a>W warstwach podstawowa i standardowa

Warstwy standardowa usługi IoT Hub umożliwia korzystanie z wszystkich funkcji i jest wymagana dla dowolnego rozwiązania IoT, które chcą ułatwić korzystanie z możliwości komunikacji dwukierunkowej. Warstwa podstawowa umożliwia podzbiór funkcji i jest przeznaczona dla rozwiązań IoT, które potrzebują tylko komunikację jednokierunkową z urządzeń do chmury. Obie warstwy oferują te same funkcje zabezpieczeń i uwierzytelniania.

Tylko jeden typ [wersji](https://azure.microsoft.com/pricing/details/iot-hub/) w obrębie warstwy można wybrać dla każdej usługi IoT Hub. Na przykład można utworzyć usługi IoT Hub przy użyciu wielu jednostek S1, ale nie kombinację jednostek z różnych wersji, takich jak S1 i B3 lub warstwy S1 i S2.

| Możliwości | Warstwa Podstawowa | Warstwa bezpłatna/Standard |
| ---------- | ---------- | ------------- |
| [Dane telemetryczne urządzenia do chmury](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Tożsamość na urządzenie](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [Routing komunikatów](iot-hub-devguide-messages-read-custom.md) i [integracji usługi Event Grid](iot-hub-event-grid.md) | Yes | Yes |
| [Protokoły HTTP, AMQP i MQTT](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Usługa Device Provisioning](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [Monitorowanie i Diagnostyka](iot-hub-monitor-resource-health.md) | Yes | Yes |
| [Komunikaty z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Bliźniacze reprezentacje urządzeń](iot-hub-devguide-device-twins.md), [bliźniaczych reprezentacjach modułów](iot-hub-devguide-module-twins.md), i [zarządzania urządzeniami](iot-hub-device-management-overview.md) |   | Yes |
| [Strumienie urządzenia (wersja zapoznawcza)](iot-hub-device-streams-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Yes |

Centrum IoT oferuje bezpłatną warstwę, która jest przeznaczona do testowania i oceny. Zawiera wszystkie funkcje warstwy standardowa, ale ograniczone limity obsługi komunikatów. Nie można uaktualnić z warstwy bezpłatna, podstawowa lub standardowa.

## <a name="partitions"></a>Partycje

Usługa Azure IoT Hubs zawiera wiele kluczowych elementów związanych z [usługi Azure Event Hubs](../event-hubs/event-hubs-features.md), w tym [partycje](../event-hubs/event-hubs-features.md#partitions). Strumienie zdarzeń dla centrów IoT Hub zwykle są wypełniane przy użyciu przychodzących danych telemetrycznych zgłoszonych przez różnych urządzeń IoT. Partycje strumienia zdarzeń służy do zmniejszenia rywalizacji, które występują, gdy jednocześnie odczytu i zapisu do strumieni zdarzeń.

Limit partycji jest wybierany, gdy usługa IoT Hub zostanie utworzona i nie można zmienić. Limit maksymalny partycji dla warstwy podstawowa usługi IoT Hub i IoT Hub w warstwie standardowa wynosi 32. Większość centrów IoT potrzebuje tylko 4 partycjami. Aby uzyskać więcej informacji na temat określania partycji, zobacz często zadawane pytania Event Hubs [partycje ile potrzebuję?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Uaktualnienie warstwy

Po utworzeniu Centrum IoT hub, możesz uaktualnić z warstwy podstawowa do warstwy standard bez przerywania istniejących. Aby uzyskać więcej informacji, zobacz [uaktualnianie Centrum IoT hub](iot-hub-upgrade.md).

Konfiguracja partycji zmienia się podczas migracji z warstwy podstawowa do warstwy standardowa.

## <a name="iot-hub-rest-apis"></a>Interfejsy API REST usługi IoT Hub

Różnica w obsługiwanych możliwości między warstwami podstawowa i standardowa usługi IoT Hub oznacza, że niektóre wywołania interfejsu API nie będą działać z hubs w warstwie podstawowa. W poniższej tabeli przedstawiono, które interfejsy API są dostępne:

| Interfejs API | Warstwa Podstawowa | Warstwa bezpłatna/Standard |
| --- | ---------- | ------------- |
| [Usuwanie urządzenia](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Yes | Yes |
| [Pobieranie urządzenia](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Yes | Yes |
| Usuń moduł | Yes | Yes |
| Uzyskiwanie modułu | Yes | Yes |
| [Zebranie statystyk, rejestru](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Yes | Yes |
| [Zebranie statystyk, usługi](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Yes | Yes |
| [Utwórz lub zaktualizuj urządzenie](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Yes | Yes |
| Umieść moduł | Yes | Yes |
| [Query IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Yes | Yes |
| Moduły zapytania | Yes | Yes |
| [Tworzenie przekazywania plików identyfikatora URI połączenia SAS](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Yes | Yes |
| [Otrzymywanie powiadomień urządzenie powiązane](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Yes | Yes |
| [Wyślij zdarzenia urządzenia](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Yes | Yes |
| Wysyłanie zdarzeń modułu | Yes | Yes |
| [Zaktualizuj stan przekazywania pliku](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Yes | Yes |
| [Operacja urządzenia zbiorczego](/rest/api/iot-dps/runbulkenrollmentoperation/runbulkenrollmentgroupoperation) | Tak, z wyjątkiem możliwości usługi IoT Edge | Yes | 
| [Wyczyść kolejkę poleceń](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Yes |
| [Pobierz bliźniaczej reprezentacji urządzenia](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Yes |
| Pobierz bliźniaczej reprezentacji modułu |   | Yes |
| [Wywoływanie metody urządzenia](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Yes |
| [Zaktualizować bliźniaczej reprezentacji urządzenia](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Yes | 
| Aktualizacji bliźniaczej reprezentacji modułu |   | Yes | 
| [Porzuć powiadomień urządzenie powiązane](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Yes |
| [Kompletne urządzenie powiązane powiadomień](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Yes |
| [Anulowanie zadania](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Yes |
| [Tworzenie zadania](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Yes |
| [Pobierz zadanie](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Yes |
| [Zapytanie zadania](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Yes |

## <a name="message-throughput"></a>Przepływność komunikatów

Najlepszym sposobem na rozmiar rozwiązanie IoT Hub jest ocena ruchu na podstawie na jednostkę. W szczególności należy wziąć pod uwagę przepływności szczytu wymagane w ramach następujących kategorii operacje:

* Komunikaty z urządzenia do chmury
* Komunikaty z chmury do urządzenia
* Operacje rejestru tożsamości

Ruch jest mierzony na jednostkę podstawową, nie dla koncentratora. Wystąpienie usługi IoT Hub poziomu 1 lub 2 może mieć maksymalnie 200 jednostek skojarzonych z nim. Wystąpienie usługi IoT Hub poziom 3 może mieć maksymalnie 10 jednostek. Po utworzeniu Centrum IoT hub można zmienić liczbę jednostek, lub przenosić między 1, 2 i 3 rozmiary w ramach określonej warstwy bez przerywania istniejących. Aby uzyskać więcej informacji, zobacz [uaktualnianie Centrum IoT Hub](iot-hub-upgrade.md).

Na przykład możliwości ruchu poszczególnych warstw komunikatów z urządzenia do chmury przestrzegać następujących wytycznych stałą przepływność na poziomie:

| Warstwa | Długoterminowa przepustowość | Wskaźnik wysyłania stałą |
| --- | --- | --- |
| B1, S1 |Maksymalnie minutę 1111 KB na jednostkę<br/>(1,5 GB/dzień/jednostkę) |Średnia krocząca 278 komunikatów na minutę na jednostkę<br/>(400 000 komunikatów/dzień na jednostkę) |
| B2, S2 |Maksymalnie 16 MB na minutę na jednostkę<br/>(22.8 GB/dzień/jednostkę) |Średnia krocząca 4,167 komunikatów na minutę na jednostkę<br/>(6 milionów komunikatów/dzień na jednostkę) |
| B3, S3 |Maksymalnie minutę 814 MB na jednostkę<br/>(1144.4 GB/dzień/jednostkę) |Średnia krocząca 208,333 komunikatów na minutę na jednostkę<br/>(300 milionów komunikatów/dzień na jednostkę) |

Oprócz tego informacji o przepływności, zobacz [usługi IoT Hub, przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md) i w związku z tym zaprojektować rozwiązanie.

### <a name="identity-registry-operation-throughput"></a>Przepływność operacji rejestru tożsamości

Operacje rejestru tożsamości usługi IoT Hub nie powinna być czasu wykonywania operacji, ponieważ są one głównie powiązane Inicjowanie obsługi administracyjnej urządzeń.

W przypadku serii określonych liczb wydajności, zobacz [usługi IoT Hub, przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatyczne skalowanie

Jeśli zbliża się limit dozwolony wiadomości w usłudze IoT Hub, możesz użyć tych [kroki, aby automatycznie skalować](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) się zwiększać jednostki usługi IoT Hub w tej samej warstwy usługi IoT Hub.

## <a name="sharding"></a>Dzielenie na fragmenty

W jednym centrum IoT umożliwia skalowanie do milionów urządzeń, czasami rozwiązanie wymaga charakterystyk wydajności zależnych, które nie może zagwarantować w jednym centrum IoT. W takim przypadku można podzielić urządzeń w wielu centrach IoT. Wiele centrów IoT smooth wzmożenia ruchu i uzyskać wymagany przepływności lub szybkości operacji, które są wymagane.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać więcej informacji na temat możliwości usługi IoT Hub i szczegółowe informacje dotyczące wydajności, zobacz [cenach usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub) lub [usługi IoT Hub, przydziały i ograniczenia](iot-hub-devguide-quotas-throttling.md).

* Aby zmienić warstwę usługi IoT Hub, wykonaj kroki opisane w [uaktualnianie Centrum IoT hub](iot-hub-upgrade.md).
