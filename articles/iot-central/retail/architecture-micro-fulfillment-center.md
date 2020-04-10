---
title: Centrum mikrorealizacji Usługi Azure IoT Central | Dokumenty firmy Microsoft
description: Dowiedz się, jak zbudować aplikację centrum mikrorealizacji za pomocą naszego szablonu aplikacji Micro-fulfillment center w IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: b0d030240ebe22886826b7a25bd5ca7b8f54e358
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000427"
---
# <a name="micro-fulfillment-center-architecture"></a>Architektura centrum mikrorealizacji

Rozwiązania centrów mikrorealizacji umożliwiają cyfrowe łączenie, monitorowanie i zarządzanie wszystkimi aspektami w pełni zautomatyzowanego centrum realizacji zamówień w celu zmniejszenia kosztów poprzez eliminację przestojów przy jednoczesnym zwiększeniu bezpieczeństwa i ogólnej wydajności. Rozwiązania te można sbudować przy użyciu jednego z szablonów aplikacji w UIO IoT Central i architektury poniżej jako wskazówki.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Zestaw czujników IoT wysyłających dane telemetryczne do urządzenia bramy
- Urządzenia bramy wysyłające dane telemetryczne i zagregowane szczegółowe informacje do usługi IoT Central
- Ciągły eksport danych do żądanej usługi platformy Azure w celu manipulacji
- Dane mogą być uporządkowane w żądanym formacie i wysyłane do usługi przechowywania
- Aplikacje biznesowe mogą wyszukiwać dane i generować szczegółowe informacje, które zasilają operacje detaliczne
 
Przyjrzyjmy się kluczowym komponentom, które zazwyczaj odgrywają rolę w rozwiązaniu centrum mikrorealizacji.

## <a name="robotic-carriers"></a>Wózki zrobotyzowane

Rozwiązanie centrum mikro-fulfillment prawdopodobnie będzie miało duży zestaw zrobotyzowanych nośników generujących różnego rodzaju sygnały telemetryczne. Sygnały te mogą być pozyskiwania przez urządzenie bramy, agregowane, a następnie wysyłane do usługi IoT Central, co znajduje odzwierciedlenie po lewej stronie diagramu architektury.  

## <a name="condition-monitoring-sensors"></a>Czujniki monitorowania stanu

Rozwiązanie IoT rozpoczyna się od zestawu czujników przechwytujących znaczące sygnały z poziomu centrum realizacji zamówień. Odbija się to na różnych rodzajach czujników po lewej stronie diagramu architektury powyżej.

## <a name="gateway-devices"></a>Urządzenia bramy

Wiele czujników IoT może przesyłać surowe sygnały bezpośrednio do chmury lub do urządzenia bramy znajdującego się w ich pobliżu. Urządzenie bramy wykonuje agregację danych na krawędzi przed wysłaniem szczegółowych informacji podsumowujących do aplikacji IoT Central. Urządzenia bramy są również odpowiedzialne za przekazywanie operacji poleceń i sterowania do urządzeń czujników, jeśli ma to zastosowanie. 

## <a name="iot-central-application"></a>Aplikacja IoT Central

Aplikacja Azure IoT Central pobuje dane z różnych rodzajów czujników IoT, robotów, a także urządzeń bramy w środowisku centrum realizacji zamówień i generuje zestaw znaczących spostrzeżeń.

Usługa Azure IoT Central zapewnia również dostosowane środowisko operatorowi sklepu, umożliwiając zdalne monitorowanie urządzeń infrastruktury i zarządzanie nimi.

## <a name="data-transform"></a>Transformacja danych
Aplikacja Azure IoT Central w ramach rozwiązania można skonfigurować do eksportowania szczegółowych lub zagregowanych szczegółowych informacji do zestawu usług Azure PaaS (Platform-as-a-Service), które mogą wykonywać manipulowanie danymi i wzbogacać te informacje przed lądowaniem ich w aplikacji biznesowej. 

## <a name="business-application"></a>Aplikacja biznesowa
Dane IoT mogą służyć do zasilania różnych rodzajów aplikacji biznesowych wdrożonych w środowisku sieci sprzedaży detalicznej. Menedżer centrum realizacji zamówień lub pracownik może używać tych aplikacji do wizualizacji informacji biznesowych i podejmowania znaczących działań w czasie rzeczywistym. Aby dowiedzieć się, jak zbudować pulpit nawigacyjny usługi Power BI w czasie rzeczywistym dla zespołu handlu detalicznego, wykonaj [samouczek](./tutorial-in-store-analytics-create-app.md).

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do szablonu aplikacji [Micro-fulfillment Center.](https://aka.ms/checkouttemplate) 
* Zapoznaj się z [samouczkiem,](https://aka.ms/mfc-tutorial) który przeprowadzi Cię przez sposób tworzenia rozwiązania przy użyciu szablonu aplikacji Centrum mikrorealizacji.
