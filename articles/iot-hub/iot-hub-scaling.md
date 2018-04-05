---
title: Centrum IoT Azure skalowanie | Dokumentacja firmy Microsoft
description: Jak skalować Centrum IoT do obsługi przewidywanego komunikat przepływności, a żądanych funkcji. Zawiera podsumowanie obsługiwana przepływność dla każdej warstwy i opcje dotyczące dzielenia na fragmenty.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f3237ee41f8596ed3ce508857adf7dc29cee1ada
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Wybierz prawa warstwy Centrum IoT dla rozwiązania

Każde rozwiązanie IoT jest inny, więc Centrum IoT Azure oferuje kilka opcji, w oparciu o cenach i skali. W tym artykule mają na celu ułatwienie oceny potrzeb Centrum IoT. Informacje o Centrum IoT warstw cenowych można znaleźć w temacie [Centrum IoT cennik](https://azure.microsoft.com/pricing/details/iot-hub). 

Aby zdecydować, której warstwie Centrum IoT jest odpowiednia do rozwiązania, spróbuj odpowiedzieć sobie dwa pytania:

**Jakie funkcje czy planuje używać?**
Centrum IoT Azure oferuje dwie warstwy, basic i standard, które różnią się liczby funkcji obsługiwanych przez te. Jeśli rozwiązania IoT jest oparta na zbieranie danych z urządzeń i analizowanie danych centralnie warstwa podstawowa jest prawdopodobnie odpowiednie dla Ciebie. Jeśli chcesz bardziej zaawansowane konfiguracje umożliwia zdalne sterowanie urządzenia IoT lub rozmieszczanie niektórych obciążeń na z samymi urządzeniami należy rozważyć warstwy standardowa. Podział szczegółowe, które funkcje dostępne w poszczególnych warstwach nadal [warstwy Basic i standard](#basic-and-standard-tiers).

**Ilość danych ma zostać przeniesiona codziennie?**
Każda warstwa Centrum IoT jest dostępne w trzech rozmiarach, na podstawie wokół jaka przepustowość danych może obsłużyć w dowolny dzień. Rozmiary numeryczne są identyfikowane jako 1, 2 i 3. Na przykład każdej jednostki Centrum IoT poziom 1 może obsługiwać tysięcy 400 wiadomości na dobę, gdy jednostki poziomu 3 może obsługiwać miliony 300. Aby uzyskać więcej informacji na temat danych nadal [przepływności komunikat](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Warstwy Basic i standard

Planu standard Centrum IoT umożliwia wszystkich funkcji i jest wymagany dla dowolnego rozwiązania IoT, które mają być wykorzystanie możliwości komunikację dwukierunkową. Warstwa podstawowa umożliwia podzbiór funkcji i jest przeznaczona dla rozwiązania IoT, które potrzebują tylko jednokierunkowe komunikację z urządzeniami w chmurze. Obu warstwach oferuje te same funkcje zabezpieczeń i uwierzytelniania.

Po utworzeniu Centrum IoT można uaktualnić z podstawowych warstwy do warstwy standardowa, bez przerywania istniejących. Aby uzyskać więcej informacji, zobacz [sposobu uaktualniania Centrum IoT](iot-hub-upgrade.md).

| Możliwości | Warstwa Podstawowa | Warstwa standardowa |
| ---------- | ---------- | ------------- |
| [Telemetrii urządzenia do chmury](iot-hub-devguide-messaging.md) | Yes | Yes |
| [Tożsamość na urządzenie](iot-hub-devguide-identity-registry.md) | Yes | Yes |
| [Rozsyłanie wiadomości](iot-hub-devguide-messages-read-custom.md) i [integracji siatki zdarzeń](iot-hub-event-grid.md) | Yes | Yes |
| [Protokoły HTTP, AMQP i MQTT](iot-hub-devguide-protocols.md) | Yes | Yes |
| [Usługa inicjowania obsługi urządzeń](../iot-dps/about-iot-dps.md) | Yes | Yes |
| [Monitorowania i diagnostyki](iot-hub-monitor-resource-health.md) | Yes | Yes |
| [Obsługa wiadomości chmury do urządzenia](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Urządzenie twins](iot-hub-devguide-device-twins.md) i [zarządzania urządzeniami](iot-hub-device-management-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Yes |

Centrum IoT oferuje również warstwę bezpłatna, który jest przeznaczony do testowania i oceny. Ma ona wszystkie możliwości warstwy standardowa, ale ograniczone dodatki obsługi wiadomości. Nie można uaktualnić warstwę bezpłatna do planu basic lub standard. 

### <a name="iot-hub-rest-apis"></a>Interfejsy API REST usługi IoT Hub

Różnica między warstwami podstawowa i standardowa Centrum IoT oznacza, że niektóre wywołania interfejsu API nie pracy z koncentratorami warstwa podstawowa obsługiwane możliwości. W poniższej tabeli przedstawiono, w których interfejsy API są dostępne: 

| Interfejs API | Warstwa Podstawowa | Warstwa standardowa |
| --- | ---------- | ------------- |
| [Usuwanie urządzenia](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | Yes | Yes |
| [Pobierz urządzenia](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | Yes | Yes |
| [Uzyskać statystyki rejestru](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | Yes | Yes |
| [Uzyskać statystyki usług](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | Yes | Yes |
| [Umieść urządzenia](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Yes | Yes |
| [Kwerenda urządzeń](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Yes | Yes |
| [Tworzenie przekazywania pliku identyfikatora URI połączenia SAS](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | Yes | Yes |
| [Otrzymywanie powiadomień urządzenie powiązane](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | Yes | Yes |
| [Wysyłanie zdarzeń urządzenia](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | Yes | Yes |
| [Zaktualizuj stan przekazywania pliku](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | Yes | Yes |
| [Operacja zbiorcza urządzenia](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | Tak, z wyjątkiem możliwości krawędzi IoT | Yes | 
| [Przeczyścić kolejki poleceń](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | Yes |
| [Pobierz dwie urządzenia](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | Yes |
| [Wywoływanie metody urządzenia](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | Yes |
| [Zaktualizuj dwie urządzenia](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | Yes | 
| [Opuszczenie urządzenie powiązane powiadomień](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | Yes |
| [Kompletne urządzenie powiązane powiadomień](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | Yes |
| [Anulowanie zadania](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | Yes |
| [Utwórz zadanie](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | Yes |
| [Pobierz zadanie](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | Yes |
| [Zadania zapytania](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | Yes |

## <a name="message-throughput"></a>Wydajność obsługi wiadomości

Najlepszym sposobem rozmiaru rozwiązania IoT Hub jest do oceny ruchu na podstawie jednostkę. W szczególności należy wziąć pod uwagę przepustowość wymagana szczytu w ramach następujących kategorii działań:

* Komunikaty z urządzenia do chmury
* Komunikaty chmury do urządzenia
* Operacje rejestru tożsamości

Ruch jest liczony na jednostkę podstawową, nie dla koncentratora. Wystąpienie Centrum IoT poziomu 1 lub 2 mogą mieć maksymalnie 200 jednostek skojarzonych z nim. Wystąpienie Centrum IoT poziom 3 może mieć maksymalnie 10 jednostek. Po utworzeniu Centrum IoT można zmienić liczbę jednostek lub przenosić między 1, 2 i 3 rozmiary w ramach określonej warstwy bez przerywania istniejących. Aby uzyskać więcej informacji, zobacz [sposobu uaktualniania Centrum IoT](iot-hub-upgrade.md).

Na przykład każdej warstwy ruchu funkcji urządzenia do chmury wiadomości zgodna z tymi wytycznymi długoterminowa przepustowość:

| Warstwa | Długoterminowa przepustowość | Wskaźnik wysyłania stałej |
| --- | --- | --- |
| B1, S1 |Maksymalnie 1111 KB na minutę na jednostkę<br/>(1,5 GB/dzień/jednostka) |Średnia 278 komunikatów na minutę na jednostkę<br/>(400 000 komunikatów/dzień na jednostkę) |
| B2, S2 |Maksymalnie 16 MB na minutę na jednostkę<br/>(22.8 GB/dzień/jednostka) |Średnia 4,167 komunikatów na minutę na jednostkę<br/>(6 mln wiadomości/dzień na jednostkę) |
| B3, S3 |Maksymalnie 814 MB na minutę na jednostkę<br/>(1144.4 GB/dzień/jednostka) |Średnia 208,333 komunikatów na minutę na jednostkę<br/>(300 milionów wiadomości/dzień na jednostkę) |

Oprócz tych informacji przepływności, zobacz [Centrum IoT przydziały i limity] [ IoT Hub quotas and throttles] i odpowiednio zaprojektować rozwiązanie.

### <a name="identity-registry-operation-throughput"></a>Przepływność operacji rejestru tożsamości
Operacje rejestru tożsamości Centrum IoT nie powinna być czasu wykonywania operacji, ponieważ przede wszystkim dotyczące inicjowania obsługi urządzeń.

Dla określonego serii numerów wydajności, zobacz [Centrum IoT przydziały i limity][IoT Hub quotas and throttles].

## <a name="sharding"></a>Dzielenie na fragmenty
Podczas jednego centrum IoT można skalować do milionów urządzeń, czasami rozwiązanie wymaga charakterystyk wydajności właściwe, które nie może zagwarantować pojedynczego Centrum IoT. W takim przypadku urządzenia można podzielić na wiele centra IoT. Centra IoT wielu smooth seria ruchu i uzyskiwanie wymagana przepustowość lub szybkości działania, które są wymagane.

## <a name="next-steps"></a>Kolejne kroki

* Aby uzyskać dodatkowe informacje na temat możliwości Centrum IoT i szczegółowe informacje dotyczące wydajności, zobacz [Centrum IoT cennik] [cennik łącze] lub [Centrum IoT przydziały i limity][IoT Hub quotas and throttles].
* Aby zmienić warstwę Centrum IoT, postępuj zgodnie z instrukcjami [uaktualnienia Centrum IoT](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
