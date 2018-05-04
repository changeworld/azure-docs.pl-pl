---
title: Centrum IoT Azure jak | Dokumentacja firmy Microsoft
description: Deweloper jak używać różnych funkcji Centrum IoT?
services: iot-hub
documentationcenter: ''
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 24376318-5344-4a81-a1e6-0003ed587d53
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2017
ms.author: dobett
ms.openlocfilehash: 9b112d2d7fc1756b74e98335831175f5d4c13320
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-use-azure-iot-hub"></a>Jak używać Centrum IoT Azure

Istnieją różne opcje, aby dowiedzieć się, jak opracowywać usługi Centrum IoT:

* Przeczytaj koncepcyjnej artykuły, które opisano szczegółowo funkcji Centrum IoT.
* Wykonaj jedną z samouczków, które obejmują różnych funkcji Centrum IoT.

## <a name="developer-guide"></a>Przewodnik dla deweloperów

Projektant, możesz przeczytać szczegółowe ogólne wskazówki dotyczące Centrum IoT w [przewodnik dewelopera po][lnk-devguide]. Ten przewodnik zawiera:

* Szczegółowy opis wszystkich funkcji Centrum IoT ułatwiają informacje na temat korzystania z nich.
* Wskazówki na temat wybierania, jeśli jest dostępnych kilka opcji.

## <a name="tutorials"></a>Samouczki

Jeśli wolisz informacje o określonych funkcji Centrum IoT pracy nad Ćwiczenia praktyczne istnieje kilka samouczków do wyboru. Wiele z tych samouczki są dostępne w wielu języków programowania. Te samouczki obejmują:

- [Przetwarzanie wiadomości urządzenia do chmury Centrum IoT przy użyciu trasy][lnk-routes-tutorial]. W tym samouczku przedstawiono sposób wysyłania wiadomości urządzenia do chmury w sposób łatwy, oparta na konfiguracji za pomocą reguł routingu Centrum IoT.

- [Wysyłanie komunikatów chmury do urządzenia z Centrum IoT][lnk-c2d-tutorial]. W tym samouczku przedstawiono sposób wysyłania wiadomości chmury do urządzenia za pośrednictwem Centrum IoT i odbierania wiadomości chmury do urządzenia na urządzeniu.

- [Przekazywanie plików z urządzenia do chmury z Centrum IoT][lnk-upload-tutorial]. Ten samouczek pokazuje, jak używać funkcji przekazywania plików z Centrum IoT.

- [Rozpoczynanie pracy z urządzenia twins][lnk-twin-tutorial]. W tym samouczku przedstawiono twins urządzenia, właściwości zgłoszone odpowiednie właściwości i tagów. Twins urządzenia jest służy do synchronizowania wartości z urządzenia.

- [Użyj metody bezpośredniego][lnk-methods-tutorial]. W tym samouczku przedstawiono sposób użycia metody bezpośredniego. Dodaj obsługę metoda bezpośrednia w symulowane urządzenie i wywołać metodę bezpośrednio z Centrum IoT.

- [Wprowadzenie do zarządzania urządzeniami][lnk-dm-tutorial]. Ten samouczek pokazuje, jak używać funkcji zarządzania urządzeniami klucza, takich jak twins i bezpośrednie metody. Zdalnie uruchom ponownie symulowane urządzenie przy użyciu tych funkcji.

- [Konfigurowanie urządzeń za pomocą właściwości żądanego][lnk-properties-tutorial]. Ten samouczek pokazuje, że sposobu do urządzenia przez dwie potrzeby a zgłosił właściwości, do zdalnego konfigurowania urządzenia.

- [Zaktualizuj oprogramowanie układowe urządzenia za pomocą przystawki Zarządzanie urządzeniami][lnk-jobs-tutorial]. Ten samouczek pokazuje, jak używać funkcji zarządzania urządzeniami klucza, takich jak twins i bezpośrednie metody. Jak używać tych funkcji można zdalnie zaktualizować oprogramowanie układowe urządzenia.

- [Zadania harmonogramu i emisji][lnk-schedule-tutorial]. W tym samouczku przedstawiono sposób użyć żądanej właściwości i metody bezpośrednio do interakcji z wieloma urządzeniami w zaplanowanym czasie.

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o usłudze IoT Hub, zobacz [przewodnik dewelopera po][lnk-devguide].

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-routes-tutorial]: ./iot-hub-csharp-csharp-process-d2c.md
[lnk-c2d-tutorial]: ./iot-hub-csharp-csharp-c2d.md
[lnk-upload-tutorial]: ./iot-hub-csharp-csharp-file-upload.md
[lnk-twin-tutorial]: ./iot-hub-node-node-twin-getstarted.md
[lnk-methods-tutorial]: ./iot-hub-node-node-direct-methods.md
[lnk-dm-tutorial]: ./iot-hub-node-node-device-management-get-started.md
[lnk-properties-tutorial]: ./iot-hub-node-node-twin-how-to-configure.md
[lnk-jobs-tutorial]: ./iot-hub-node-node-firmware-update.md
[lnk-schedule-tutorial]: ./iot-hub-node-node-schedule-jobs.md