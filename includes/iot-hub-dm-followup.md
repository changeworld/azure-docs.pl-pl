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
ms.openlocfilehash: 69fdc6cf678107ef64ea1fe7b819738fd4a4ff4f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66156373"
---
## <a name="customize-and-extend-the-device-management-actions"></a>Dostosowywanie i rozszerzanie akcje z zakresu zarządzania urządzeniami

Rozwiązania IoT można rozwinąć zdefiniowanym zestawem wzorców zarządzania urządzeniami lub włączyć wzorców niestandardowych za pomocą bliźniaczej reprezentacji urządzenia i podstawowych metoda chmury do urządzenia. Inne akcje z zakresu zarządzania urządzeniami przykładami resetowania do ustawień fabrycznych, aktualizacja oprogramowania układowego, aktualizacji oprogramowania, zarządzania energią, Zarządzanie sieciami i łączności i szyfrowania danych.

## <a name="device-maintenance-windows"></a>Okna obsługi urządzeń

Zwykle możesz skonfigurować urządzenia do wykonywania akcji w czasie, które minimalizuje przestoje i przestoje. Okna obsługi urządzenia są często używane wzorzec do zdefiniowania czasu, gdy urządzenie powinno zaktualizuje konfigurację swojej. Rozwiązania zaplecza umożliwia żądane właściwości bliźniaczej reprezentacji urządzenia zdefiniować i aktywować zasad na urządzeniu, umożliwiająca okna obsługi. Gdy urządzenie otrzyma zasady okna obsługi, służy zgłoszonych właściwości bliźniaka urządzenia zgłoszenia stanu zasad. Aplikacja wewnętrzna następnie można użyć zapytań bliźniaczych reprezentacji urządzeń potwierdzać zgodności urządzenia i każdej zasady.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku użyto metody bezpośredniej wyzwolić ponowne uruchomienie komputera zdalnego na urządzeniu. Używane zgłaszanych właściwości do raportowania czasu ostatniego ponownego uruchomienia z urządzenia, a następnie proszeni bliźniaczej reprezentacji urządzenia, aby odnaleźć ostatniego ponownego uruchomienia urządzenia w chmurze.

Aby kontynuować wprowadzenie do usługi IoT Hub i wzorców zarządzania urządzeniami, takich jak zdalne za pośrednictwem aktualizacji oprogramowania układowego air, zobacz [jak zaktualizować oprogramowanie układowe](../articles/iot-hub/tutorial-firmware-update.md)

Aby dowiedzieć się, jak rozszerzyć rozwiązanie i harmonogram metoda wywołuje na wielu urządzeniach IoT, zobacz [harmonogramu i zadań emisji](../articles/iot-hub/iot-hub-node-node-schedule-jobs.md).