---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/02/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 6eb7993b4dbec3ab4901dc7071d18eae98ab8ae4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54079259"
---
> [!NOTE]
> Wieloczęściowy żądań zwykle wymagają trzy części:
> * A **Content-Type** nagłówka:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * A **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Zawartość pliku do przekazania
>
> **Content-Type** i **Content-Disposition** różnią się w zależności od scenariusza użycia.

Wieloczęściowy żądań jest możliwe programowo (za pośrednictwem C#), za pomocą klienta REST lub narzędzia, takie jak [Postman](https://www.getpostman.com/). Narzędzia klienta REST mogą mieć różne poziomy pomocy technicznej w przypadku złożonych żądań wieloczęściową. Sprawdź, które narzędzia najlepiej nadaje się do Twoich potrzeb.

> [!IMPORTANT]
> Wieloczęściowy żądań cyfrowego Twins zarządzania interfejsów API usługi Azure ma dwie części:
> * Metadane obiektu blob (np. skojarzony typ MIME), która jest zadeklarowana za **Content-Type** i **Content-Disposition**
> * Zawartość obiektu blob, obejmujących bez określonej struktury zawartość pliku do przekazania
>
> Żadna z dwóch części nie jest wymagana dla **PATCH** żądań. Oba są wymagane dla **WPIS** lub operacji tworzenia.

[Szybki Start zajętość kodu źródłowego](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) zawiera pełną C# przykłady pokazujące sposób wprowadzania wieloczęściowego żądań cyfrowego Twins zarządzania interfejsów API usługi Azure.