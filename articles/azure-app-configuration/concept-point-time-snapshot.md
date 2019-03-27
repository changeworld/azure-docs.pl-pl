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
ms.openlocfilehash: 6238f96c9e8df0431e42caa5e5846af3fc60e681
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484019"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Konfiguracja aplikacji Azure przechowuje rekordy dokładne razy, gdy nową parę klucz wartość jest tworzony i następnie zmodyfikować. Te rekordy tworzą pełną oś czasu zmian wartości klucza. Konfiguracji sklepu z aplikacjami można odtworzyć historię wszystkie wartości klucza i odtwarzania jego wartość w ciągu ostatnich w danym momencie, do chwili obecnej. Dzięki tej funkcji można "czas podróży" Wstecz i pobrania starej wartości klucza. Na przykład możesz uzyskać przeszła ustawienia konfiguracji, tuż przed najnowszych wdrożenia, aby można było odzyskać poprzednią konfigurację i wycofywanie aplikacji.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Aby pobrać ostatnie wartości klucza, określ czas, w którym wartości klucza są migawki w nagłówku HTTP wywołania interfejsu API REST. Na przykład:

        GET /kv HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Obecnie konfiguracji aplikacji przechowuje siedem dni historii zmian.

## <a name="next-steps"></a>Kolejne kroki

* [Szybki start: tworzenie aplikacji internetowej platformy ASP.NET](quickstart-aspnet-core-app.md)  
