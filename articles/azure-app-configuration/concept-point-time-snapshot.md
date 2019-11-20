---
title: Migawka punktu w czasie w usłudze Azure App Configuration | Microsoft Docs
description: Omówienie działania migawki punktu w czasie w usłudze Azure App Configuration
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: fdbe4846700c690261dbc734063f4420478666a8
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2019
ms.locfileid: "74185216"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Usługa Azure App Configuration pozwala rejestrować precyzyjne informacje o tym, kiedy nowa para klucz-wartość zostanie utworzona, a następnie zmodyfikowana. Te rekordy tworzą pełną oś czasu zmian wartości klucza. Magazyn konfiguracji aplikacji może odtworzyć historię dowolnej wartości klucza i powtórzyć jego poprzednią wartość w dowolnym momencie, do obecnej. Dzięki tej funkcji możesz "podróż czasowa" do tyłu i pobrać starą wartość klucza. Można na przykład uzyskać wczoraj ustawienia konfiguracji, tuż przed najnowszym wdrożeniem, aby odzyskać poprzednią konfigurację i wycofać aplikację.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Aby pobrać poprzednie wartości klucza, określ godzinę, o której kluczowe wartości są migawką w nagłówku HTTP wywołania interfejsu API REST. Na przykład:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Obecnie konfiguracja aplikacji przechowuje siedem dni historii zmian.

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji sieci Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
