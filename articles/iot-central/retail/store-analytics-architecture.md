---
title: Architektura analizy sklepu
description: Dowiedz się, jak utworzyć aplikację analityczną w sklepie przy użyciu szablonu aplikacji realizacji transakcji w centrum IoT
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f1f83fdd73816e6e30c5cac7d193719591bb8dc1
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80999019"
---
# <a name="in-store-analytics-architecture"></a>Architektura analityczna w sklepie



Rozwiązania analityczne w sklepie umożliwiają monitorowanie różnych warunków w środowisku sklepu detalicznego. Rozwiązania te można sbudować przy użyciu jednego z szablonów aplikacji w UIO IoT Central i architektury poniżej jako wskazówki.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Zestaw czujników IoT wysyłających dane telemetryczne do urządzenia bramy
- Urządzenia bramy wysyłające dane telemetryczne i zagregowane szczegółowe informacje do usługi IoT Central
- Ciągły eksport danych do żądanej usługi platformy Azure w celu manipulacji
- Dane mogą być uporządkowane w żądanym formacie i wysyłane do usługi przechowywania
- Aplikacje biznesowe mogą wyszukiwać dane i generować szczegółowe informacje, które zasilają operacje detaliczne
 
Przyjrzyjmy się kluczowym składnikom, które zazwyczaj odgrywają rolę w rozwiązaniu analitycznym w sklepie.

## <a name="condition-monitoring-sensors"></a>Czujniki monitorowania stanu

Rozwiązanie IoT rozpoczyna się od zestawu czujników przechwytujących znaczące sygnały z poziomu środowiska sklepu detalicznego. Jest to odzwierciedlone przez różne rodzaje czujników po lewej stronie diagramu architektury powyżej.

## <a name="gateway-devices"></a>Urządzenia bramy

Wiele czujników IoT może przesyłać surowe sygnały bezpośrednio do chmury lub do urządzenia bramy znajdującego się w ich pobliżu. Urządzenie bramy wykonuje agregację danych na krawędzi przed wysłaniem szczegółowych informacji podsumowujących do aplikacji IoT Central. Urządzenia bramy są również odpowiedzialne za przekazywanie operacji poleceń i sterowania do urządzeń czujników, jeśli ma to zastosowanie. 

## <a name="iot-central-application"></a>Aplikacja IoT Central

Aplikacja Azure IoT Central pobuje dane z różnych rodzajów czujników IoT, a także urządzeń bramy w środowisku sklepu detalicznego i generuje zestaw istotnych informacji.

Usługa Azure IoT Central zapewnia również dostosowane środowisko operatorowi sklepu, umożliwiając zdalne monitorowanie urządzeń infrastruktury i zarządzanie nimi.

## <a name="data-transform"></a>Transformacja danych
Aplikacja Azure IoT Central w ramach rozwiązania można skonfigurować do eksportowania szczegółowych lub zagregowanych szczegółowych informacji do zestawu usług Azure PaaS (Platform-as-a Service), które mogą wykonywać manipulowanie danymi i wzbogacać te informacje przed lądowaniem ich w aplikacji biznesowej. 

## <a name="business-application"></a>Aplikacja biznesowa
Dane IoT mogą służyć do zasilania różnych rodzajów aplikacji biznesowych wdrożonych w środowisku sieci sprzedaży detalicznej. Kierownik sklepu detalicznego lub pracownik może używać tych aplikacji do wizualizacji informacji biznesowych i podejmowania znaczących działań w czasie rzeczywistym. Aby dowiedzieć się, jak zbudować pulpit nawigacyjny usługi Power BI w czasie rzeczywistym dla zespołu handlu detalicznego, wykonaj [samouczek](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do szablonów aplikacji [do realizacji transakcji analytics w sklepie](https://aka.ms/checkouttemplate) i monitorowania stanu analizy w [sklepie.](https://aka.ms/conditiontemplate) 
* Zapoznaj się z [samouczkiem end](https://aka.ms/storeanalytics-tutorial) to end, który przeprowadzi Cię przez sposób tworzenia rozwiązania przy użyciu jednego z szablonów aplikacji analizy w sklepie.
