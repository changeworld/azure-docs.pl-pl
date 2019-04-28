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
ms.openlocfilehash: 8693c48905155ed757bb727e42f4180f36c015f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60399168"
---
## <a name="associate-an-azure-storage-account-to-iot-hub"></a>Skojarz konto usługi Azure Storage do usługi IoT Hub

Ponieważ symulowanej aplikacji urządzenia przekazuje plik do obiektu blob, konieczne jest posiadanie [usługi Azure Storage](../articles/storage/common/storage-quickstart-create-account.md) konta skojarzonego z Centrum IoT hub. Po skojarzeniu konta usługi Azure Storage z usługą IoT hub, IoT hub generuje identyfikator URI sygnatury dostępu Współdzielonego. Urządzenie może używać tego identyfikatora URI sygnatury dostępu Współdzielonego do bezpiecznego przekazania pliku do kontenera obiektów blob. Usługa IoT Hub i zestawy SDK urządzeń koordynowania procesu, który generuje identyfikator URI sygnatury dostępu Współdzielonego i udostępnia je do urządzenia na potrzeby przekazywania pliku.

Postępuj zgodnie z instrukcjami w [wysyłanie plików konfiguracji za pomocą witryny Azure portal](../articles/iot-hub/iot-hub-configure-file-upload.md). Upewnij się, że kontener obiektów blob jest skojarzony z Twoim Centrum IoT hub i włączenia powiadomień pliku.

![Włącz pliku powiadomienia w portalu](./media/iot-hub-associate-storage/enable-file-notifications.png)