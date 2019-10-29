---
title: Łączność urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Ten artykuł zawiera podsumowanie sposobu, w jaki usługa Azure IoT Central pomaga zapewnić łączność i kondycję urządzeń.
author: aaronbjork
ms.author: abjork
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 23e9feea49f10a2fbc27961813cf02a4993467e9
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72991991"
---
# <a name="stay-connected-with-azure-iot-central-preview-features"></a>Pozostawanie połączone z usługą Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten artykuł zawiera omówienie sposobu, w jaki usługa Azure IoT Central pomaga zapewnić łączność i kondycję urządzeń.

W przypadku każdego rozwiązania IoT zaprojektowanego do pracy w odpowiedniej skali, dobrze skonstruowane podejście do zarządzania urządzeniami jest ważne. Nie wystarczy tylko, aby uzyskać dostęp do urządzeń "podłączony" do programu, musisz mieć podejście, aby zapewnić łączność i kondycję urządzeń w miarę rozwoju i rozwoju rozwiązania. Usługa Azure IoT Central udostępnia funkcje konieczne do zapewnienia, że urządzenia w Twoim rozwiązaniu IoT są dobrze dobre dla całego cyklu życia aplikacji.

## <a name="dashboards"></a>Pulpity nawigacyjne 
Wbudowane [pulpity nawigacyjne](howto-manage-devices.md#import-devices) zapewniają dostosowywany obszar powierzchni do monitorowania kondycji i danych telemetrycznych urządzenia. Zacznij od wstępnie skompilowanego pulpitu nawigacyjnego z [szablonu aplikacji](howto-use-app-templates-pnp.md) lub Utwórz własne pulpity nawigacyjne dostosowane do potrzeb aplikacji. Pulpity nawigacyjne mogą być udostępniane wszystkim użytkownikom w aplikacji lub w sposób prywatny.

## <a name="rules-and-actions"></a>Reguły i akcje 
Szybko Zidentyfikuj urządzenia wymagające uwagi, tworząc [niestandardowe reguły](howto-create-event-rules.md) na podstawie stanu urządzenia i telemetrii. Skonfiguruj [Akcje](howto-create-event-rules.md#configure-actions) powiadamiania właściwych osób, które zachodzą w odpowiednim czasie.

## <a name="jobs"></a>Stanowiska 
[Zadania](howto-run-a-job.md) umożliwiają wykonywanie pojedynczych lub zbiorczych aktualizacji właściwości, ustawień i poleceń urządzeń. 

## <a name="user-roles-and-permissions"></a>Role i uprawnienia użytkownika
[Role i uprawnienia](howto-manage-users-roles-pnp.md) umożliwiają dostosowanie środowiska każdego użytkownika. Twórz role bezpośrednio za pomocą interfejsu użytkownika i łatwo przypisuj odpowiednie uprawnienia. 




