---
title: Plik dyrektywy include
description: usługi iot edge
author: kgremban
manager: timlt
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 09/18/2018
ms.author: kgremban
ms.openlocfilehash: be4d82577584e83e29f2511d51256fda0970e917
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51264411"
---
## <a name="enabling-extended-offline-operation-preview"></a>Włączanie rozszerzonej operacji w trybie offline (wersja zapoznawcza)
Za pomocą [wersja v1.0.4](https://github.com/Azure/azure-iotedge/releases/tag/1.0.4) środowiska uruchomieniowego usługi Edge, urządzenie brzegowe i podrzędnym urządzenia nawiązania z nim można tak skonfigurować rozszerzone operacji w trybie offline. 

Dzięki tej możliwości moduły lokalne lub podrzędnego urządzeń można ponownie uwierzytelnić urządzenia Edge zgodnie z potrzebami i komunikować się ze sobą przy użyciu wiadomości i metod, nawet wtedy, gdy jest odłączony od usługi IoT Hub. Zobacz ten [wpis w blogu](https://aka.ms/iot-edge-offline) i [artykułu pojęcie](../articles/iot-edge/offline-capabilities.md) bardziej szczegółowe informacje i zakresu tej funkcji.

Włączania rozszerzonych w trybie offline w scenariuszu bramy ustanawiania relacji nadrzędny podrzędny między urządzenie brzegowe i podrzędnym urządzenia, które będzie z nim połączyć.

1. W bloku szczegółów urządzenia usługi Edge w portalu usługi IoT Hub, kliknij **zarządzanie urządzeniami podrzędnych (wersja zapoznawcza)** przycisku na górnym pasku poleceń.

1. Kliknij przycisk **+ Dodaj** przycisku.

1. Z listy urządzeń wybierz urządzenia podrzędnych i użyj strzałki w prawo, aby wybrać te, które można dodać jako elementy podrzędne.

1. Kliknij przycisk **OK** o potwierdzenie.

Urządzenie brzegowe i ich urządzenia podrzędnych dostępne są teraz rozszerzone operacji w trybie offline.  