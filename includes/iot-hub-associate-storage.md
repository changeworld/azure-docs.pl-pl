---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/20/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 892dd050829dce242035e2b875ea43ed13910d4a
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/21/2018
ms.locfileid: "40246078"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Skojarz konto usługi Azure Storage do usługi IoT Hub

Ponieważ symulowanej aplikacji urządzenia przekazuje plik do obiektu blob, konieczne jest posiadanie [usługi Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) konta skojarzonego z usługą IoT Hub. Po skojarzeniu konta usługi Azure Storage z usługą IoT hub, IoT hub generuje identyfikator URI sygnatury dostępu Współdzielonego. Urządzenie może używać tego identyfikatora URI sygnatury dostępu Współdzielonego do bezpiecznego przekazania pliku do kontenera obiektów blob. Usługa IoT Hub i zestawy SDK urządzeń koordynowania procesu, który generuje identyfikator URI sygnatury dostępu Współdzielonego i udostępnia je do urządzenia na potrzeby przekazywania pliku.

Postępuj zgodnie z instrukcjami w [wysyłanie plików konfiguracji za pomocą witryny Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) skojarzyć konto usługi Azure Storage do usługi IoT hub. Upewnij się, że kontener obiektów blob jest skojarzony z Twoim Centrum IoT hub i włączenia powiadomień pliku.

![Włącz pliku powiadomienia w portalu](media/iot-hub-associate-storage/enable-file-notifications.png)