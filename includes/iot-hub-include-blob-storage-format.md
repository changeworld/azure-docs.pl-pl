---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 27a254b664e2035cc66109f7a473a3e9ef4d730e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73612133"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Dane mogą być zapisywane do magazynu obiektów blob w formacie [Apache Avro,](https://avro.apache.org/) który jest domyślny, lub JSON (wersja zapoznawcza). 
>    
> Możliwość kodowania formatu JSON jest w wersji zapoznawczej we wszystkich regionach, w których jest dostępny centrum IoT Hub, z wyjątkiem wschodnich stanów USA, zachodnich stanów USA i Europy Zachodniej. Format kodowania można ustawić tylko w momencie skonfigurowania punktu końcowego magazynu obiektów blob. Nie można zmienić formatu dla punktu końcowego, który został już skonfigurowany. Podczas korzystania z kodowania JSON, należy ustawić contentType do JSON i contentEncoding do UTF-8 we właściwościach systemu wiadomości. 
>
> Aby uzyskać bardziej szczegółowe informacje na temat korzystania z punktu końcowego magazynu obiektów blob, zobacz [wskazówki dotyczące routingu do magazynu](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-storage).
>