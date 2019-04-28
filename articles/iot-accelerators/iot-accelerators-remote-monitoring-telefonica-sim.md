---
title: Integrowanie danych SIM w rozwiązaniu do zdalnego monitorowania — Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób integrowanie danych Telefónica SIM rozwiązania do zdalnego monitorowania.
author: hegate
manager: ''
ms.author: hegate
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 05/15/2018
ms.topic: conceptual
ms.openlocfilehash: b07e21131d9560a49d99644525835ac5ee3bac9e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61442243"
---
# <a name="integrate-sim-data-in-the-remote-monitoring-solution"></a>Integrowanie danych SIM w rozwiązaniu do zdalnego monitorowania

Urządzenia IoT często łączą w chmurze za pomocą karty SIM, umożliwiającego wysyłanie strumieni danych z dowolnego miejsca. Rozwiązania do zdalnego monitorowania usługi Azure IoT umożliwia integrację danych IoT zarządzane łączności, tak aby operatorzy mogą również śledzić kondycji urządzenia za pomocą danych dostarczanych przez IoT SIM.

Monitorowania zdalnego udostępnia poza integracji z usługą Telefónica IoT łączności, w którym klienci korzystający z platformy IoT łączności synchronizowanie ich urządzenia kart SIM połączenia danych do swoich rozwiązań. To rozwiązanie do obsługi innych dostawców łączności IoT za pośrednictwem witryny GitHub można rozszerzyć [repozytorium](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

* Integrowanie danych Telefónica IoT SIM rozwiązania do zdalnego monitorowania
* Wyświetlanie danych telemetrycznych w czasie rzeczywistym
* Wyświetlanie danych SIM

## <a name="telefnica-iot-integration-setup"></a>Instalacja środowiska integration Telefónica IoT

### <a name="prerequisites"></a>Wymagania wstępne

Tej dodatkowej funkcji monitorowania zdalnego jest obecnie w wersji zapoznawczej. Synchronizowanie danych połączenia na platformie Azure rozwiązanie do monitorowania zdalnego, wykonaj następujące kroki:

1. Wypełnij wniosek na [witryny firmy Telefónica](https://iot.telefonica.com/contact), wybierz opcję **zdalnego monitorowania platformy Azure**, w tym dane kontaktowe.
2. Telefónica aktywuje konto.
3. Jeśli nie jesteś klientem Telefónica jeszcze, a użytkownik chce Ciesz się to lub inne IoT łączności chmury gotowe usługi, odwiedź stronę [witryny firmy Telefónica](https://iot.telefonica.com/) i wybierz opcję **łączności**.

### <a name="telefnica-sim-setup"></a>Telefónica SIM setup
Skojarzenie identyfikator Telefónica SIM & Azure bliźniaczej reprezentacji urządzenia zależy od właściwości "alias" Telefónica IoT SIM. 

Przejdź do [Telefónica IoT łączność platformy portalu](https://m2m-movistar-es.telefonica.com/) > Spis SIM > Wybierz kartą SIM, a następnie zaktualizuj każdego SIM "alias" przy użyciu usługi żądaną deviceID bliźniaczej reprezentacji. To zadanie można również wykonać w trybie zbiorczo (zobacz platformy łączności IoT Telefónica podręczniki użytkownika).

To zadanie można również wykonać w trybie zbiorczo (zobacz podręczniki użytkownika platformy łączności IoT Telefónica)

![Telefónica Update](./media/iot-accelerators-remote-monitoring-telefonica-sim/telefonica_site.png)

Aby Podłącz urządzenie do monitorowania zdalnego, możesz wykonać tych samouczków przy użyciu [C](iot-accelerators-connecting-devices-linux.md) lub [węzła](iot-accelerators-connecting-devices-node.md). 

## <a name="view-device-telemetry-and-sim-properties"></a>Wyświetlanie danych telemetrycznych z urządzeń i właściwości SIM

Gdy konto Telefónica jest prawidłowo skonfigurowane i urządzenie jest podłączone, możesz wyświetlić szczegóły urządzenia i danych SIM.

Następujące parametry połączenia są publikowane:

* ICCID
* Adres IP
* Dostęp do sieci
* Stan SIM
* Lokalizacji sieciowej
* Ruch związany z wykorzystanych danych

![Pulpit nawigacyjny](./media/iot-accelerators-remote-monitoring-telefonica-sim/dashboard.png)

## <a name="next-steps"></a>Kolejne kroki

Teraz, gdy podstawowe informacje o integrowanie danych SIM do zdalnego monitorowania usługi Azure IoT, Oto zalecane kolejne kroki dla akceleratorów rozwiązań:

* [Działanie rozwiązania do zdalnego monitorowania usługi Azure IoT](quickstart-remote-monitoring-deploy.md)
* [Wykonywanie zaawansowanego monitorowania](iot-accelerators-remote-monitoring-monitor.md)
* [Zarządzanie urządzeniami](iot-accelerators-remote-monitoring-manage.md)
* [Rozwiązywanie problemów z urządzeniami](iot-accelerators-remote-monitoring-maintain.md)

