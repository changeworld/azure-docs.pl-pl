---
title: Architektura OPC Twin - Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera omówienie architektury OPC Twin. Opisano w nim odnajdowanie, aktywację, przeglądanie i monitorowanie serwera.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819854"
---
# <a name="opc-twin-architecture"></a>Architektura OPC Twin

Na poniższych diagramach przedstawiono architekturę OPC Twin.

## <a name="discover-and-activate"></a>Odkryj i aktywuj

1. Operator umożliwia skanowanie sieci na module lub dokonuje jednorazowego odnajdywania przy użyciu adresu URL odnajdywania. Odnalezione punkty końcowe i informacje o aplikacji są wysyłane za pośrednictwem danych telemetrycznych do agenta dołączania do przetwarzania.  Agent dołączania urządzenia OPC UA przetwarza zdarzenia odnajdowania serwera OPC UA wysyłane przez moduł OPC Twin IoT Edge w trybie odnajdywania lub skanowania. Zdarzenia odnajdowania powodują rejestrację aplikacji i aktualizacje w rejestrze urządzeń OPC UA.

   ![Jak działa OPC Twin](media/overview-opc-twin-architecture/opc-twin1.png)

1. Operator sprawdza certyfikat odnalezionego punktu końcowego i aktywuje zarejestrowany bliźniaczej reprezentacji punktu końcowego dla dostępu. 

   ![Jak działa OPC Twin](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Przeglądanie i monitorowanie

1. Po aktywacji operator może użyć interfejsu API REST usługi twin do przeglądania lub sprawdzania modelu informacji o serwerze, zmiennych obiektów odczytu/zapisu i metod wywołania.  Użytkownik korzysta z uproszczonego interfejsu API OPC UA wyrażonego w pełni w http i JSON.

   ![Jak działa OPC Twin](media/overview-opc-twin-architecture/opc-twin3.png)

1. Interfejs REST usługi bliźniaczej można również używać do tworzenia monitorowanych elementów i subskrypcji w programie OPC Publisher. Wydawca OPC umożliwia przesyłanie danych telemetrycznych z systemów serwera OPC UA do usługi IoT Hub. Aby uzyskać więcej informacji o programie OPC Publisher, zobacz [Co to jest OPC Publisher](overview-opc-publisher.md).

   ![Jak działa OPC Twin](media/overview-opc-twin-architecture/opc-twin4.png)
