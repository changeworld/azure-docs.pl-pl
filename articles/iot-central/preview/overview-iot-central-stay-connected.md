---
title: Łączność urządzeń w usłudze Azure IoT Central | Microsoft Docs
description: Ten artykuł zawiera podsumowanie sposobu, w jaki usługa Azure IoT Central pomaga zapewnić łączność i kondycję urządzeń.
author: aaronbjork
ms.author: abjork
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: ''
ms.openlocfilehash: 7c102e7ffde7faab5d864c1d9acaafe141664ebf
ms.sourcegitcommit: 5bbe87cf121bf99184cc9840c7a07385f0d128ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2020
ms.locfileid: "76119554"
---
# <a name="stay-connected-with-azure-iot-central-preview-features"></a>Pozostawanie połączone z usługą Azure IoT Central (funkcje w wersji zapoznawczej)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Ten artykuł zawiera omówienie sposobu, w jaki usługa Azure IoT Central pomaga zapewnić łączność i kondycję urządzeń.

W przypadku każdego rozwiązania IoT zaprojektowanego do pracy w odpowiedniej skali, dobrze skonstruowane podejście do zarządzania urządzeniami jest ważne. Nie wystarczy tylko uzyskać urządzenia "połączone" z chmurą, dlatego należy zadbać o to, aby urządzenia były połączone i w dobrej kondycji, gdy rozwiązanie zostanie zmienione, powiększone i w wieku. Usługa Azure IoT Central udostępnia funkcje konieczne do zapewnienia, że urządzenia w Twoim rozwiązaniu IoT są dobrze dobre dla całego cyklu życia aplikacji.

## <a name="dashboards"></a>Pulpity nawigacyjne 
Wbudowane [pulpity nawigacyjne](howto-manage-devices.md#import-devices) zapewniają dostosowywany obszar powierzchni do monitorowania kondycji i danych telemetrycznych urządzenia. Zacznij od wstępnie skompilowanego pulpitu nawigacyjnego z [szablonu aplikacji](howto-use-app-templates.md) lub Utwórz własne pulpity nawigacyjne dostosowane do potrzeb aplikacji. Pulpity nawigacyjne mogą być udostępniane wszystkim użytkownikom w aplikacji lub w sposób prywatny.

## <a name="rules-and-actions"></a>Reguły i akcje 
Szybko Zidentyfikuj urządzenia wymagające uwagi, tworząc [niestandardowe reguły](tutorial-create-telemetry-rules.md) na podstawie stanu urządzenia i telemetrii. Skonfiguruj akcje powiadamiania właściwych osób, które zachodzą w odpowiednim czasie.

## <a name="jobs"></a>Zadania 
[Zadania](../core/howto-run-a-job.md?toc=/azure/iot-central/preview/toc.json&bc=/azure/iot-central/preview/breadcrumb/toc.json) umożliwiają wykonywanie pojedynczych lub zbiorczych aktualizacji właściwości, ustawień i poleceń urządzeń. 

## <a name="user-roles-and-permissions"></a>Role i uprawnienia użytkownika
[Role i uprawnienia](howto-manage-users-roles.md) umożliwiają dostosowanie środowiska każdego użytkownika. Twórz role bezpośrednio za pomocą interfejsu użytkownika i łatwo przypisuj odpowiednie uprawnienia. 




