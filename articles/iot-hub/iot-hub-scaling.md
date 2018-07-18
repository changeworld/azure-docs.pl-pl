---
title: Skalowanie usługi Azure IoT Hub | Dokumentacja firmy Microsoft
description: Jak skalować usługi IoT hub do obsługi przepływność przewidywanego komunikatów i żądanych funkcji. Zawiera podsumowanie obsługiwana przepływność dla każdej warstwy i opcje dotyczące dzielenia na fragmenty.
author: kgremban
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: kgremban
ms.openlocfilehash: 1ad2dc9d826ec42dfca8547a73117a6c967a8ae7
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39113652"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wybrać właściwą warstwę usługi IoT Hub dla Twojego rozwiązania

Każde rozwiązanie IoT jest inny, dzięki czemu usługa Azure IoT Hub oferuje kilka opcji, w oparciu o cenach i skali. W tym artykule mają na celu ułatwić ocenę potrzeb usługi IoT Hub. Cennik informacji na temat warstw usługi IoT Hub można znaleźć [cenach usługi IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub). 

Aby zdecydować, którą warstwę usługi IoT Hub jest odpowiednia dla danego rozwiązania, spróbuj odpowiedzieć sobie dwa pytania:

**Jakie funkcje chcę użyć?**
Usługa Azure IoT Hub oferuje dwie warstwy, podstawowa i standardowa, które różnią się w wiele funkcji, które obsługują. Jeśli rozwiązanie IoT opiera się wokół zbieranie danych z urządzeń i centralnie je analizować, warstwa basic jest prawdopodobnie odpowiedni dla Ciebie. Jeśli zdalne sterowanie urządzeniami IoT lub dystrybucji niektórych obciążeń na samych urządzeń za pomocą bardziej zaawansowanych ustawień konfiguracji należy rozważyć w warstwie standardowa. Aby uzyskać szczegółowy podział, które funkcje dostępne w poszczególnych warstwach w dalszym ciągu [warstw podstawowa i standardowa](#basic-and-standard-tiers).

**Jak dużo danych chcę przenieść codziennie?**
Każda warstwa usługi IoT Hub jest dostępna w trzech rozmiarach, na podstawie ogólnej przepływności ilości danych może obsługiwać w każdym dniu. Te rozmiary numerycznie są identyfikowane jako 1, 2 i 3. Na przykład każda jednostka w Centrum IoT poziomu 1 może obsługiwać tysiąc 400 komunikatów dziennie, gdy jednostka poziomu 3 może obsługiwać 300 milionów. Aby uzyskać więcej informacji na temat wytycznych dane w dalszym ciągu [przepływność komunikatów](#message-throughput).

## <a name="basic-and-standard-tiers"></a>W warstwach podstawowa i standardowa

Warstwy standardowa usługi IoT Hub umożliwia korzystanie z wszystkich funkcji i jest wymagana dla dowolnego rozwiązania IoT, które chcą ułatwić korzystanie z możliwości komunikacji dwukierunkowej. Warstwa podstawowa umożliwia podzbiór funkcji i jest przeznaczona dla rozwiązań IoT, które potrzebują tylko komunikację jednokierunkową z urządzeń do chmury. Obie warstwy oferują te same funkcje zabezpieczeń i uwierzytelniania.

Po utworzeniu Centrum IoT hub można uaktualnić z warstwy podstawowa do warstwy standard bez przerywania istniejących. Aby uzyskać więcej informacji, zobacz [uaktualnianie Centrum IoT hub](iot-hub-upgrade.md). Należy pamiętać, że limit partycji dla basic warstwy usługi IoT Hub jest 8. Ten limit pozostaje niezmieniony, podczas migracji z warstwy podstawowa do warstwy standardowa.

| Możliwości | Warstwa Podstawowa | Warstwa standardowa |
| ---------- | ---------- | ------------- |
| [Dane telemetryczne urządzenia do chmury](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Tożsamość na urządzenie](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [Routing komunikatów](iot-hub-devguide-messages-read-custom.md) i [integracji usługi Event Grid](iot-hub-event-grid.md) | Yes | Yes |
| [Protokoły HTTP, AMQP i MQTT](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Usługa Device Provisioning](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [Monitorowanie i Diagnostyka](iot-hub-monitor-resource-health.md) | Yes | Yes |
| [Komunikaty z chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Bliźniacze reprezentacje urządzeń](iot-hub-devguide-device-twins.md), [bliźniaczych reprezentacjach modułów](iot-hub-devguide-module-twins.md) i [zarządzania urządzeniami](iot-hub-device-management-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Yes |

Centrum IoT oferuje bezpłatną warstwę, która jest przeznaczona do testowania i oceny. Zawiera wszystkie funkcje warstwy standardowa, ale ograniczone limity obsługi komunikatów. Nie można uaktualnić z warstwy bezpłatna, podstawowa lub standardowa. 

### <a name="iot-hub-rest-apis"></a>Interfejsy API REST usługi IoT Hub

Różnica w obsługiwanych możliwości między warstwami podstawowa i standardowa usługi IoT Hub oznacza, że niektóre wywołania interfejsu API nie będą działać z hubs w warstwie podstawowa. W poniższej tabeli przedstawiono, które interfejsy API są dostępne: 

| Interfejs API | Warstwa Podstawowa | Warstwa standardowa |
| --- | ---------- | ------------- |
| [Usuwanie urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/deletedevice) | Yes | Yes |
| [Pobieranie urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdevice) | Yes | Yes |
| Usuń moduł | Yes | Yes |
| Uzyskiwanie modułu | Yes | Yes |
| [Zebranie statystyk, rejestru](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getdeviceregistrystatistics) | Yes | Yes |
| [Zebranie statystyk, usługi](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getservicestatistics) | Yes | Yes |
| [Utwórz lub zaktualizuj urządzenie](https://docs.microsoft.com/rest/api/iothub/service/service/createorupdatedevice) | Yes | Yes |
| Umieść moduł | Yes | Yes |
| [Zapytanie usługi IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/service/queryiothub) | Yes | Yes |
| Moduły zapytania | Yes | Yes |
| [Tworzenie przekazywania plików identyfikatora URI połączenia SAS](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/createfileuploadsasuri) | Yes | Yes |
| [Otrzymywanie powiadomień urządzenie powiązane](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/receivedeviceboundnotification) | Yes | Yes |
| [Wyślij zdarzenia urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/senddeviceevent) | Yes | Yes |
| Wysyłanie zdarzeń modułu | Yes | Yes |
| [Zaktualizuj stan przekazywania pliku](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/updatefileuploadstatus) | Yes | Yes |
| [Operacja urządzenia zbiorczego](https://docs.microsoft.com/en-us/rest/api/iot-dps/deviceenrollment/bulkoperation) | Tak, z wyjątkiem możliwości usługi IoT Edge | Yes | 
| [Wyczyść kolejkę poleceń](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/purgecommandqueue) |   | Yes |
| [Pobierz bliźniaczej reprezentacji urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/gettwin) |   | Yes |
| Pobierz bliźniaczej reprezentacji modułu |   | Yes |
| [Wywoływanie metody urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/invokedevicemethod) |   | Yes |
| [Zaktualizować bliźniaczej reprezentacji urządzenia](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/updatetwin) |   | Yes | 
| Aktualizacji bliźniaczej reprezentacji modułu |   | Yes | 
| [Porzuć powiadomień urządzenie powiązane](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/abandondeviceboundnotification) |   | Yes |
| [Kompletne urządzenie powiązane powiadomień](https://docs.microsoft.com/en-us/rest/api/iothub/device/device/completedeviceboundnotification) |   | Yes |
| [Anulowanie zadania](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/canceljob) |   | Yes |
| [Tworzenie zadania](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/createjob) |   | Yes |
| [Pobierz zadanie](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/getjob) |   | Yes |
| [Zapytanie zadania](https://docs.microsoft.com/en-us/rest/api/iothub/service/service/queryjobs) |   | Yes |

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

Oprócz tego informacji o przepływności, zobacz [usługi IoT Hub, przydziały i ograniczenia] [ IoT Hub quotas and throttles] i w związku z tym zaprojektować rozwiązanie.

### <a name="identity-registry-operation-throughput"></a>Przepływność operacji rejestru tożsamości
Operacje rejestru tożsamości usługi IoT Hub nie powinna być czasu wykonywania operacji, ponieważ są one głównie powiązane Inicjowanie obsługi administracyjnej urządzeń.

W przypadku serii określonych liczb wydajności, zobacz [usługi IoT Hub, przydziały i ograniczenia][IoT Hub quotas and throttles].

## <a name="sharding"></a>Dzielenie na fragmenty
W jednym centrum IoT umożliwia skalowanie do milionów urządzeń, czasami rozwiązanie wymaga charakterystyk wydajności zależnych, które nie może zagwarantować w jednym centrum IoT. W takim przypadku można podzielić urządzeń w wielu centrach IoT. Wiele centrów IoT smooth wzmożenia ruchu i uzyskać wymagany przepływności lub szybkości operacji, które są wymagane.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać dodatkowe informacje na temat możliwości usługi IoT Hub i szczegółowe informacje dotyczące wydajności, zobacz [cenach usługi IoT Hub] [link-pricing] lub [usługi IoT Hub, przydziały i ograniczenia][IoT Hub quotas and throttles].
* Aby zmienić warstwę usługi IoT Hub, wykonaj kroki opisane w [uaktualnianie Centrum IoT hub](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
