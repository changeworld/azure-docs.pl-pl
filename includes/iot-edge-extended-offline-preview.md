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
ms.openlocfilehash: a8160e677fa99d8cb691db39d7f29ba6eddbd261
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004683"
---
## <a name="enabling-extended-offline-operation-preview"></a>Włączanie rozszerzonej operacji w trybie offline (wersja zapoznawcza)
Począwszy od [wersji v1.0.2](https://aka.ms/edge102) środowiska uruchomieniowego usługi Edge, urządzenie brzegowe i podrzędnym urządzenia nawiązania z nim można tak skonfigurować rozszerzone operacji w trybie offline. 

Dzięki tej możliwości moduły lokalne lub podrzędnego urządzeń można ponownie uwierzytelnić urządzenia Edge zgodnie z potrzebami i komunikować się ze sobą przy użyciu wiadomości i metod, nawet wtedy, gdy jest odłączony od usługi IoT Hub. Zobacz ten [wpis w blogu](https://aka.ms/iot-edge-offline) i [artykułu pojęcie](../articles/iot-edge/offline-capabilities.md) bardziej szczegółowe informacje i zakresu tej funkcji.

Włączania rozszerzonych w trybie offline w scenariuszu bramy ustanawiania relacji nadrzędny podrzędny między urządzenie brzegowe i podrzędnym urządzenia, które będzie z nim połączyć.

1. W bloku szczegółów urządzenia usługi Edge w portalu usługi IoT Hub, kliknij **zarządzanie urządzeniami podrzędnych (wersja zapoznawcza)** przycisku na górnym pasku poleceń.

1. Kliknij przycisk **+ Dodaj** przycisku.

1. Z listy urządzeń wybierz urządzenia podrzędnych i użyj strzałki w prawo, aby wybrać te, które można dodać jako elementy podrzędne.

1. Kliknij przycisk **OK** o potwierdzenie.

1. W **Ustaw moduły** ekran szczegóły urządzenia Edge, kliknij przycisk **skonfiguruj zaawansowane ustawienia środowiska uruchomieniowego Edge**, a następnie w obszarze **Centrum usługi Edge** zmiennych środowiskowych, Dodaj odpowiedni wpis  **UpstreamProtocol** wartością **MQTT**. Dodaj tę samą zmienną środowiska i wartość **Agent usługi Edge** także. 

1. Kliknij przycisk **Zapisz** i pamiętaj, aby **przesyłania** zmiany po kliknięciu przycisku **dalej** dwa razy.

Urządzenie brzegowe i ich urządzenia podrzędnych dostępne są teraz rozszerzone operacji w trybie offline.  