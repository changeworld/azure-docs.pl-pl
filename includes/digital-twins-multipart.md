---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 12/12/2019
ms.custom: include file
ms.openlocfilehash: 6fa21d78d766ee8e541d6d60f64628b55a84fe41
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75466784"
---
> [!NOTE]
> Żądania wieloczęściowe wymagają zwykle trzech kawałków:
> * Nagłówek **Content-Type** :
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * **Dyspozycja zawartości**:
>   * `form-data; name="metadata"`
> * Zawartość pliku do przekazania
>
> **Typ zawartości** i **Dyspozycja zawartości** będą się różnić w zależności od scenariusza użycia.

Wieloczęściowe żądania można wprowadzać programowo ( C#przez) za pomocą klienta REST lub narzędzia, takiego jak [program.](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request) Narzędzia klienta REST mogą mieć różne poziomy obsługi złożonych żądań wieloczęściowych. Ustawienia konfiguracji mogą być również nieco inne niż narzędzia do narzędzia. Sprawdź, które narzędzie jest najlepiej dopasowane do Twoich potrzeb.

> [!IMPORTANT]
> Wieloczęściowe żądania interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management mają zwykle dwie części:
> * Metadane obiektu BLOB (takie jak skojarzony typ MIME) zadeklarowane przez **Typ zawartości** i/lub **dyspozycję zawartości**
> * Zawartość obiektu BLOB, która obejmuje zawartość bez struktury pliku do przekazania
>
> Żadna z tych dwóch części nie jest wymagana w przypadku żądań **patch** . Oba są wymagane dla operacji **post** lub CREATE.

[Kod źródłowy przewodnika Szybki Start](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) zawiera C# pełne przykłady przedstawiające sposób tworzenia wieloczęściowych żądań względem interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.