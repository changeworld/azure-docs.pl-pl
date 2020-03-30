---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/07/2020
ms.custom: include file
ms.openlocfilehash: 0e7cb7e4aaa9862a2b4af51593c29793ea54dd14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111239"
---
> [!NOTE]
> Żądania wieloczęściowe zazwyczaj wymagają trzech części:
> * Nagłówek **typu zawartości:**
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Dyspozycja **treści:**
>   * `form-data; name="metadata"`
> * Zawartość pliku do przesłania
>
> **Typ zawartości** i **dyspozycja zawartości** będą się różnić w zależności od scenariusza użycia.

Wieloczęściowe żądania mogą być składane programowo (za pośrednictwem języka C#), za pośrednictwem klienta REST lub narzędzia, takiego jak [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#make-a-multipart-post-request). Narzędzia klienckie REST mogą mieć różne poziomy obsługi złożonych żądań wieloczęściowych. Ustawienia konfiguracji mogą się również nieznacznie różnić w zależności od narzędzia. Sprawdź, które narzędzie najlepiej odpowiada Twoim potrzebom.

> [!IMPORTANT]
> Wieloczęściowe żądania do interfejsów API zarządzania cyfrowymi urządzeniami dydazy azure zazwyczaj mają dwie części:
> * Metadane obiektów blob (takie jak skojarzony typ MIME), które są zadeklarowane przez **typ zawartości** i/lub **dyspozycję zawartości**
> * Zawartość obiektu Blob, która zawiera nieustrukturyzowana zawartość pliku, który ma zostać przesłany
>
> Żadna z tych dwóch części nie jest wymagana dla żądań **PATCH.** Oba są wymagane dla **operacji POST** lub tworzenia.

[Kod źródłowy szybkiego startu dla obłożenia](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) zawiera pełne przykłady języka C#, które pokazują, jak wykonywać żądania wieloczęściowe względem interfejsów API zarządzania cyfrowymi obrazami cyfrowymi usługi Azure.