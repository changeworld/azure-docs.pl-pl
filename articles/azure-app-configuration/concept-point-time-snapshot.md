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
ms.openlocfilehash: 4f26f3a31dc6303e991c39fc7f85d9bf254d57bc
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885140"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Usługa Azure App Configuration zapisuje dokładny czas utworzenia nowej pary klucz-wartość oraz jej późniejszych modyfikacji. Te rekordy tworzą pełną oś czasu zmian wartości klucza. Magazyn konfiguracji aplikacji może zrekonstruować historię wartości dowolnego klucza i odtworzyć dowolną z poprzednich wartości, aż do aktualnej. Ta funkcja umożliwia „cofanie się w czasie” w celu pobrania wcześniejszej wartości klucza. Możesz na przykład pobrać ustawienia konfiguracji z dnia wczorajszego, poprzedzającego najnowsze wdrożenie, co pozwala odzyskać poprzednią konfigurację w przypadku konieczności wycofania wersji aplikacji.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Aby pobrać poprzednie wartości klucza, należy określić czas utworzenia migawki wartości klucza w nagłówku HTTP wywołania interfejsu API REST. Na przykład:

        GET /revisions HTTP/1.1
        Accept-Datetime: Sat, 1 Jan 2019 02:10:00 GMT

Obecnie usługa App Configuration przechowuje historię zmian z 7 dni.

## <a name="next-steps"></a>Następne kroki

* [Szybki start: tworzenie aplikacji internetowej platformy ASP.NET](quickstart-aspnet-core-app.md)  
