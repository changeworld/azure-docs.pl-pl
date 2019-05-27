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
ms.openlocfilehash: c779147e464a592d45da8a9a2d8e812320dc23e8
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/23/2019
ms.locfileid: "66162740"
---
<!-- This is the note explaining about the avro and json formats when routing to blob storage. -->
> [!NOTE]
> Mogą być zapisywane dane do usługi blob storage w jednym [Apache Avro](https://avro.apache.org/) formacie, który jest domyślna, czyli JSON (wersja zapoznawcza). 
>    
> Funkcja do zakodowania w formacie JSON jest w wersji zapoznawczej we wszystkich regionach, w Centrum IoT Hub jest dostępna, z wyjątkiem wschodnie stany USA, zachodnie stany USA i Europa Zachodnia. Format kodowania można ustawić tylko w momencie konfigurowany jest punkt końcowy magazynu obiektów blob. Nie można zmienić format dla punktu końcowego, który już został skonfigurowany. Korzystając z formatu JSON, kodowanie, należy ustawić obiekt contentType na format JSON i contentEncoding na UTF-8 we właściwościach komunikatu systemu. 
>
> Aby uzyskać szczegółowe informacje o korzystaniu z punktu końcowego magazynu obiektów blob, zobacz [wskazówki dotyczące routingu w usłudze blob storage](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#azure-blob-storage).
>