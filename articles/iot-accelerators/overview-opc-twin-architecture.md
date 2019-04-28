---
title: Architektura bliźniaczej reprezentacji OPC — Azure | Dokumentacja firmy Microsoft
description: Architektura bliźniaczej reprezentacji OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: d1c10e056de1bc61b5365550bf2543d2cdf61229
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106834"
---
# <a name="opc-twin-architecture"></a>Architektura bliźniaczej reprezentacji OPC

Następujące diagramy przedstawiają architektury OPC bliźniaczej reprezentacji.

## <a name="discover-and-activate"></a>Odnajdywanie i Aktywuj

1. Operator włącza skanowanie w module sieci lub ułatwia odnajdywanie jednorazowa, przy użyciu adresu URL odnajdywania. Odnalezione punkty końcowe i informacje o aplikacji są wysyłane za pośrednictwem danych telemetrycznych do dołączania agenta do przetworzenia.  Agent dołączania urządzenia OPC UA przetwarza zdarzenia odnajdywania serwera OPC UA wysyłane przez moduł OPC bliźniaczej reprezentacji IoT Edge w trybie odnajdowania lub skanowania. Wynik zdarzenia odnajdywania w rejestracji aplikacji i aktualizacji w rejestrze urządzenia OPC UA.

   ![Jak działa bliźniaczej reprezentacji OPC](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operator sprawdza certyfikat odnalezionych punktu końcowego i aktywuje twin zarejestrowany punkt końcowy dostępu. 

   ![Jak działa bliźniaczej reprezentacji OPC](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Przeglądaj i Monitoruj

1. Po uaktywnieniu operator umożliwia interfejs API REST usługi bliźniaczych przeglądanie lub sprawdzanie modelu informacji o serwerze, odczytu/zapisu obiektu zmiennych i wywoływać metody.  Użytkownik korzysta z uproszczonego OPC UA interfejsu API będzie wyrażana w pełni HTTP i JSON.

   ![Jak działa bliźniaczej reprezentacji OPC](media/overview-opc-twin-architecture/opc-twin3.png)

1. Bliźniacza reprezentacja interfejsu REST usługi można również tworzyć monitorowanych elementów i subskrypcje w wydawcy OPC. Wydawca OPC umożliwia telemetrii do wysłania z systemami serwerów OPC UA do Centrum IoT Hub. Aby uzyskać więcej informacji na temat OPC Publisher zobacz [wydawca OPC](https://github.com/Azure/iot-edge-opc-publisher) repozytorium w witrynie GitHub.

   ![Jak działa bliźniaczej reprezentacji OPC](media/overview-opc-twin-architecture/opc-twin4.png)