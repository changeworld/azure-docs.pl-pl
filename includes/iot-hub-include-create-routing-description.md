---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183852"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

Planujesz rozsyłać komunikaty do różnych zasobów na podstawie właściwości dołączonych do komunikatu za pośrednictwem urządzenia symulowanego. Komunikaty bez tras niestandardowych są wysyłane do domyślnego punktu końcowego (wiadomości/zdarzenia). W następnym samouczku wysyłania komunikatów do Centrum IoT i wyświetlać je kierować do różnych miejsc docelowych.

|value |Wynik|
|------|------|
|level=„storage” |Zapis w usłudze Azure Storage.|
|level=„critical” |Zapis w kolejce usługi Service Bus. Aplikacja logiki pobiera komunikat z kolejki i używa usługi Office 365 do wysłania go pocztą e-mail.|
|default |Wyświetlanie tych danych przy użyciu usługi Power BI.|

Pierwszym krokiem jest do konfigurowania punktu końcowego, do której będą kierowane danych. Drugim krokiem jest skonfigurować trasy wiadomości, która używa tego punktu końcowego. Po skonfigurowaniu routingu, możesz wyświetlić punkty końcowe i trasy wiadomości w portalu.