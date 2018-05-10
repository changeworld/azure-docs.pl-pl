---
title: Integrowanie danych SIM w zdalnym monitorowania w zakresie - Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób integracji danych Telefonica SIM rozwiązanie monitorowania zdalnego.
services: iot-suite
suite: iot-suite
author: hegate
manager: corywink
ms.author: hegate
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: c82bb8ff3d0f903e1de627f13337ae5fc633458c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrowanie danych SIM w rozwiązaniu Monitorowanie zdalne

## <a name="overview"></a>Przegląd
Urządzenia IoT często łączą w chmurze przy użyciu kart SIM, która pozwala na wysyłanie strumieni danych z dowolnego miejsca. To rozwiązanie monitorowanie zdalnego IoT Azure umożliwia integrację danych zarządzania SIM tak, aby operatorzy mogą również śledzić kondycji urządzenia przy użyciu danych dostarczonych przez SIM. Monitorowanie zdalnego umożliwia poza integracji pole z Telefonica IoT co klientów korzystających z platformy łączności IoT synchronizować swoje urządzenia kart SIM łączności danych rozwiązania. To rozwiązanie można rozszerzyć do obsługi innych dostawców firmy telefonu do repozytorium GitHub.
Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
* Integrowanie danych SIM rozwiązanie monitorowania zdalnego
* Widok telemetrii w czasie rzeczywistym
* Wyświetlanie danych SIM 

## <a name="telefonica-iot-integration-setup"></a>Ustawienia integracji Telefonica IoT

### <a name="prerequisites"></a>Wymagania wstępne
Synchronizacja danych łączności do zdalnego rozwiązanie monitorowanie Azure, wykonaj następujące kroki:

1.  Wypełnienia żądania w [witryny firmy Telefonica](https://iot.telefonica.com/contact), wybierz opcję **zdalnego monitorowania Azure**, w tym dane kontaktowe.
2.  Telefonica będzie aktywować konto. 
3.  Jeśli nie jesteś klientem Telefónica jeszcze i chcesz korzystać to lub inne usługi IoT łączności chmury gotowe, odwiedź stronę [witryny firmy Telefonica](https://iot.telefonica.com/contact) i wybierz opcję **łączności**.

### <a name="telefonica-sim-setup"></a>Instalator Telefonica SIM
Telefónica SIM & dwie Azure skojarzenie Identyfikatora urządzenia będą oparte na właściwość "alias" Telefónica IoT SIM. 

Przejdź do [Telefónica IoT łączności platformy portalu](https://m2m-movistar-es.telefonica.com/) > spisu SIM > Wybierz kartą SIM, a następnie zaktualizować każdego SIM "alias" z odpowiednią móc przeprowadzać dwie. 

To zadanie można również wykonać w trybie zbiorczo (zobacz instrukcje użytkownika Telefónica IoT łączności platformy)

![Aktualizacja Telefonica](media/iot-suite-remote-monitoring-telefonica/telefonica_site.png)

Podłącz urządzenie do monitorowania zdalnego, możesz wykonać te samouczki przy użyciu [C](iot-suite-connecting-devices-linux.md) lub [węzła](iot-suite-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Dane telemetryczne wyświetleń urządzeń i właściwości SIM
Konta Telefonica jest poprawnie skonfigurowany po, gdy urządzenie jest podłączone, można wyświetlić szczegóły urządzenia i danych SIM.
Mogą być publikowane następujące parametry połączenia:
* ICCID
* Adres IP
* Dostęp do sieci
* Stan SIM
* Lokalizacji sieciowej
* Ruch danych wykorzystanych

![Pulpit nawigacyjny](media/iot-suite-remote-monitoring-telefonica/dashboard.png)
 
## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz omówienie sposobu integracji Azure IoT zdalne monitorowanie danych SIM, Oto sugerowane kolejne kroki w celu rozwiązania akceleratorów:

* [Obsługi rozwiązania monitorowania zdalnego IoT Azure](iot-suite-remote-monitoring-explore.md)
* [Wykonywanie zaawansowanego monitorowania](iot-suite-remote-monitoring-monitor.md)
* [Zarządzanie urządzeniami](iot-suite-remote-monitoring-manage.md)
* [Rozwiązywanie problemów z urządzeniami](iot-suite-remote-monitoring-maintain.md)

