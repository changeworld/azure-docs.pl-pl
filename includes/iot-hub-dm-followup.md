---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
ms.service: iot-hub
author: dominicbetts
ms.topic: include
ms.date: 02/17/2019
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 2514325c93f0f72aa979f871e86e465093479402
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69558856"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Dostosowywanie i rozszerzanie akcji zarządzania urządzeniami

Rozwiązania IoT można rozszerzyć zdefiniowany zestaw wzorców zarządzania urządzeniami lub włączyć wzorce niestandardowe przy użyciu bliźniaczej reprezentacji urządzenia i egoistyki metody chmury do urządzenia. Inne przykłady akcji zarządzania urządzeniami obejmują przywrócenie ustawień fabrycznych, aktualizację oprogramowania układowego, aktualizację oprogramowania, zarządzanie energią, zarządzanie siecią i łącznością oraz szyfrowanie danych.

## <a name="device-maintenance-windows"></a>Okna konserwacji urządzeń

Zazwyczaj urządzenia można skonfigurować do wykonywania akcji w czasie, który minimalizuje przerwy i przestoje. Okna konserwacji urządzeń są powszechnie używanym wzorcem do definiowania czasu, w którym urządzenie powinno zaktualizować swoją konfigurację. Rozwiązania zaplecza można użyć żądanych właściwości bliźniaczej reprezentacji urządzenia do definiowania i aktywowania zasad na urządzeniu, który umożliwia okno konserwacji. Gdy urządzenie odbiera zasady okna konserwacji, może użyć zgłoszonej właściwości bliźniaczej reprezentacji urządzenia, aby zgłosić stan zasad. Aplikacja zaplecza można następnie użyć zapytań bliźniaczej reprezentacji urządzenia, aby potwierdzić zgodność urządzeń i każdej zasady.

## <a name="next-steps"></a>Następne kroki

W tym samouczku użyto metody bezpośredniej, aby wyzwolić zdalny ponowny rozruch na urządzeniu. Użyto zgłoszonych właściwości do raportowania ostatniego czasu ponownego uruchomienia z urządzenia i zapytano bliźniaczej reprezentacji urządzenia, aby odkryć ostatni czas ponownego uruchomienia urządzenia z chmury.

Aby kontynuować pracę z usługą IoT Hub i wzorcami zarządzania urządzeniami, takimi jak zdalna aktualizacja oprogramowania układowego, zobacz [Jak wykonać aktualizację oprogramowania układowego](../articles/iot-hub/tutorial-firmware-update.md).

Aby dowiedzieć się, jak rozszerzyć rozwiązanie IoT i zaplanować wywołania metod na wielu urządzeniach, zobacz [Planowanie i emisja zadań](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).