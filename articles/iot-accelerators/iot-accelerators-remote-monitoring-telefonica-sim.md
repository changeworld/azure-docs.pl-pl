---
title: Integrowanie danych SIM w zdalnym monitorowania w zakresie - Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób integracji danych Telefónica SIM rozwiązanie monitorowania zdalnego.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: 8ba187fd82fa6a698905313bfda2802324a9034d
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37097084"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrowanie danych SIM w rozwiązaniu Monitorowanie zdalne

Urządzenia IoT często łączą w chmurze przy użyciu kart SIM, która pozwala na wysyłanie strumieni danych z dowolnego miejsca. To rozwiązanie monitorowanie zdalnego IoT Azure umożliwia integrację danych IoT zarządzane łączności tak, aby operatorzy mogą również śledzić kondycji urządzenia przy użyciu danych dostarczonych przez IoT SIM.

Monitorowanie zdalnego umożliwia poza integracji pole Telefónica IoT łączności, z, co klientów korzystających z platformy łączności IoT synchronizować swoje urządzenia kart SIM połączenie danych do ich rozwiązania. To rozwiązanie może zostać rozszerzony do obsługi innych dostawców IoT łączności za pośrednictwem usługi GitHub [repozytorium](http://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Integrowanie danych Telefónica IoT SIM rozwiązanie monitorowania zdalnego
* Widok telemetrii w czasie rzeczywistym
* Wyświetlanie danych SIM

## <a name="telefnica-iot-integration-setup"></a>Ustawienia integracji Telefónica IoT

### <a name="prerequisites"></a>Wymagania wstępne

Ta funkcja dodatkowe monitorowania zdalnego jest obecnie w przeglądzie. Synchronizacja danych łączności do zdalnego rozwiązanie monitorowanie Azure, wykonaj następujące kroki:

1. Wypełnienia żądania w [witryny firmy Telefónica](https://iot.telefonica.com/contact), wybierz opcję **zdalnego monitorowania Azure**, w tym dane kontaktowe.
2. Telefónica aktywuje konto.
3. Jeśli nie jesteś klientem Telefónica jeszcze i chcesz korzystać to lub inne usługi IoT łączności chmury gotowe, odwiedź stronę [witryny firmy Telefónica](https://iot.telefonica.com/) i wybierz opcję **łączności**.

### <a name="telefnica-sim-setup"></a>Instalator Telefónica SIM
Telefónica SIM & dwie Azure skojarzenie identyfikator urządzenia jest oparta na właściwość "alias" Telefónica IoT SIM. 

Przejdź do [Telefónica IoT łączności platformy portalu](https://m2m-movistar-es.telefonica.com/) > spisu SIM > Wybierz kartą SIM, a następnie zaktualizować każdego SIM "alias" z odpowiednią móc przeprowadzać dwie. To zadanie można również wykonać w trybie zbiorczo (zobacz instrukcje użytkownika Telefónica IoT łączności platformy).

To zadanie można również wykonać w trybie zbiorczo (zobacz instrukcje użytkownika Telefónica IoT łączności platformy)

![Aktualizacja Telefónica](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Podłącz urządzenie do monitorowania zdalnego, możesz wykonać te samouczki przy użyciu [C](iot-accelerators-connecting-devices-linux.md) lub [węzła](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Dane telemetryczne wyświetleń urządzeń i właściwości SIM

Konta Telefónica jest poprawnie skonfigurowany po, gdy urządzenie jest podłączone, można wyświetlić szczegóły urządzenia i danych SIM.

Są publikowane następujące parametry połączenia:

* ICCID
* Adres IP
* Dostęp do sieci
* Stan SIM
* Lokalizacji sieciowej
* Ruch danych wykorzystanych

![Pulpit nawigacyjny](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy masz omówienie sposobu integracji Azure IoT zdalne monitorowanie danych SIM, Oto sugerowane kolejne kroki w celu rozwiązania akceleratorów:

* [Obsługi rozwiązania monitorowania zdalnego IoT Azure](iot-accelerators-remote-monitoring-explore.md)
* [Wykonywanie zaawansowanego monitorowania](iot-accelerators-remote-monitoring-monitor.md)
* [Zarządzanie urządzeniami](iot-accelerators-remote-monitoring-manage.md)
* [Rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md)

