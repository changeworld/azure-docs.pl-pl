---
title: Migawka punktu w czasie dla usługi Azure App Configuration
description: Omówienie działania migawki punktu w czasie w usłudze Azure App Configuration
services: azure-app-configuration
author: yegu-ms
ms.author: yegu
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/24/2019
ms.openlocfilehash: 4db52ce1897aa5a2b809cb7044b9764baffd0767
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/26/2019
ms.locfileid: "75495261"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Usługa Azure App Configuration pozwala rejestrować precyzyjne informacje o tym, kiedy nowa para klucz-wartość zostanie utworzona, a następnie zmodyfikowana. Te rekordy tworzą pełną oś czasu zmian wartości klucza. Magazyn konfiguracji aplikacji może odtworzyć historię dowolnej wartości klucza i powtórzyć jego poprzednią wartość w dowolnym momencie, do obecnej. Dzięki tej funkcji możesz "podróż czasowa" do tyłu i pobrać starą wartość klucza. Można na przykład uzyskać wczoraj ustawienia konfiguracji, tuż przed najnowszym wdrożeniem, aby odzyskać poprzednią konfigurację i wycofać aplikację.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Aby pobrać poprzednie wartości klucza, określ godzinę, o której kluczowe wartości są migawką w nagłówku HTTP wywołania interfejsu API REST. Przykład:

```rest
GET /kv HTTP/1.1
Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT
```

Obecnie konfiguracja aplikacji przechowuje siedem dni historii zmian.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
