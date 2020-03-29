---
title: Integracja danych karty SIM z rozwiązaniem do zdalnego monitorowania — Azure| Dokumenty firmy Microsoft
description: W tym artykule opisano, jak zintegrować dane karty SIM Telefónica z rozwiązaniem do zdalnego monitorowania.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61442243"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integracja danych karty SIM z rozwiązaniem do zdalnego monitorowania

Urządzenia IoT często łączą się z chmurą za pomocą karty SIM, która umożliwia im wysyłanie strumieni danych z dowolnego miejsca. Rozwiązanie do zdalnego monitorowania usługi Azure IoT umożliwia integrację danych łączności zarządzanej przez IoT, dzięki czemu operatorzy mogą również śledzić kondycję urządzenia za pomocą danych dostarczonych przez kartę IoT SIM.

Zdalne monitorowanie zapewnia integrację po wyjęciu z łącznością IoT Telefónica, umożliwiając klientom korzystającym z platformy łączności IoT synchronizowanie danych łączności z urządzeniami SAM z ich rozwiązaniami. To rozwiązanie można rozszerzyć na obsługę innych dostawców łączności IoT za pośrednictwem [repozytorium](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)GitHub.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

* Integracja danych Telefónica IoT SIM z rozwiązaniem do zdalnego monitorowania
* Wyświetlanie danych telemetrycznych w czasie rzeczywistym
* Wyświetlanie danych karty SIM

## <a name="telefnica-iot-integration-setup"></a>Konfiguracja integracji Telefóniki IoT

### <a name="prerequisites"></a>Wymagania wstępne

Ta dodatkowa funkcja zdalnego monitorowania jest obecnie w wersji zapoznawczej. Aby zsynchronizować dane dotyczące łączności z rozwiązaniem do zdalnego monitorowania platformy Azure, wykonaj następujące kroki:

1. Wypełnij żądanie w [witrynie Telefóniki](https://iot.telefonica.com/contact), wybierz opcję **Azure Remote Monitoring**, w tym swoje dane kontaktowe.
2. Telefónica aktywuje Twoje konto.
3. Jeśli nie jesteś jeszcze klientem Telefóniki i chcesz korzystać z tych lub innych usług IoT Connectivity Cloud Ready, odwiedź [witrynę Telefóniki](https://iot.telefonica.com/) i wybierz opcję **Łączność**.

### <a name="telefnica-sim-setup"></a>Konfiguracja karty SIM Telefónica
Telefónica SIM & skojarzenia identyfikatora urządzenia Usługi Azure Twin opiera się na właściwości "alias" telefóniki IoT SIM. 

Przejdź do [portalu telefóniki IoT Connectivity Platform Portal](https://m2m-movistar-es.telefonica.com/) > SIM Inventory > Wybierz kartę SIM i zaktualizuj każdy "alias" karty SIM za pomocą żądanego identyfikatora bliźniaczego urządzenia. To zadanie można również wykonać w trybie zbiorczym (patrz Instrukcje obsługi platformy łączności Telefónica IoT).

To zadanie można również wykonać w trybie zbiorczym (patrz instrukcje obsługi telefóniki platformy łączności IoT)

![Aktualizacja Telefóniki](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Aby podłączyć urządzenie do zdalnego monitorowania, można wykonać te samouczki za pomocą [języka C](iot-accelerators-connecting-devices-linux.md) lub [Node](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Wyświetlanie danych telemetrycznych urządzenia i właściwości karty SIM

Po prawidłowym skonfigurowaniu konta Telefónica i podłączeniu urządzenia można wyświetlić szczegóły urządzenia i dane karty SIM.

Opublikowano następujące parametry łączności:

* Identyfikator ICCID
* Adres IP
* Obecność w sieci
* Stan karty SIM
* Lokalizacja sieciowa
* Zużyty ruch danych

![Pulpit nawigacyjny](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy masz przegląd sposobu integracji danych karty SIM z zdalnym monitorowaniem usługi Azure IoT, w tym miejscu przedstawiono następujące sugestie dotyczące kolejnych kroków dotyczących akceleratorów rozwiązań:

* [Obsługa rozwiązania do zdalnego monitorowania usługi Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Wykonywanie zaawansowanego monitorowania](iot-accelerators-remote-monitoring-monitor.md)
* [Zarządzanie urządzeniami](iot-accelerators-remote-monitoring-manage.md)
* [Rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md)

