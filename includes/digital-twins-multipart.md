---
title: plik dołączany
description: plik dołączany
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 10/02/2019
ms.author: v-adgera
ms.custom: include file
ms.openlocfilehash: 76a05817ae91bb49240e5d72988c776ce67ec1c0
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71845531"
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