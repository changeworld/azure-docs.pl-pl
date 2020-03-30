---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 01/22/2019
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: ebc23ce4238c736442fbc4507e858876f9192fd9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76021104"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Kojarzenie konta usługi Azure Storage z usługą IoT Hub

Ponieważ aplikacja symulowanego urządzenia przekazuje plik do obiektu blob, musisz mieć konto [usługi Azure Storage](../articles/storage/common/storage-account-create.md) skojarzone z centrum IoT hub. Po skojarzeniu konta usługi Azure Storage z centrum IoT hub centrum IoT generuje identyfikator URI sygnatury dostępu Współdzielonego. Urządzenie może użyć tego identyfikatora URI sygnatury dostępu Współdzielonego, aby bezpiecznie przekazać plik do kontenera obiektów blob. Usługa IoT Hub i sdk urządzeń koordynować proces, który generuje identyfikator URI sygnatury dostępu Współdzielonego i udostępnia go do urządzenia do przekazywania pliku.

Postępuj zgodnie z instrukcjami w [konfigurowanie przekazywania plików za pomocą witryny Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Upewnij się, że kontener obiektów blob jest skojarzony z centrum IoT hub i że powiadomienia o plikach są włączone.

![Włączanie powiadomień o plikach w portalu](./media/iot-hub-associate-storage/file-notifications-vs2019.png)
