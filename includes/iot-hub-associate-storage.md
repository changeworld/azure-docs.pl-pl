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
ms.openlocfilehash: 8aa4695ea1175fe9d558e02bae661c9610123299
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43087094"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Skojarz konto usługi Azure Storage do usługi IoT Hub

Ponieważ symulowanej aplikacji urządzenia przekazuje plik do obiektu blob, konieczne jest posiadanie [usługi Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) konta skojarzonego z usługą IoT Hub. Po skojarzeniu konta usługi Azure Storage z usługą IoT hub, IoT hub generuje identyfikator URI sygnatury dostępu Współdzielonego. Urządzenie może używać tego identyfikatora URI sygnatury dostępu Współdzielonego do bezpiecznego przekazania pliku do kontenera obiektów blob. Usługa IoT Hub i zestawy SDK urządzeń koordynowania procesu, który generuje identyfikator URI sygnatury dostępu Współdzielonego i udostępnia je do urządzenia na potrzeby przekazywania pliku.

Postępuj zgodnie z instrukcjami w [wysyłanie plików konfiguracji za pomocą witryny Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md) skojarzyć konto usługi Azure Storage do usługi IoT hub. Upewnij się, że kontener obiektów blob jest skojarzony z Twoim Centrum IoT hub i włączenia powiadomień pliku.

![Włącz pliku powiadomienia w portalu](./media/iot-hub-associate-storage/enable-file-notifications.png)