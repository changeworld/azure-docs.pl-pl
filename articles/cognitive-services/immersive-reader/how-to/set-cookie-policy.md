---
title: Ustawianie zasad plików cookie programu Immersive Reader
titleSuffix: Azure Cognitive Services
description: W tym artykule pokazano, jak ustawić zasady dotyczące plików cookie dla czytnika Immersive Reader.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946112"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak ustawić zasady dotyczące plików cookie dla czytnika Immersive Reader

Czytnik Immersive domyślnie wyłączy użycie plików cookie. Jeśli włączysz korzystanie z plików cookie, czytnik Immersive reader może używać plików cookie do obsługi preferencji użytkownika i śledzenia wykorzystania funkcji. Jeśli włączysz korzystanie z plików cookie w Czytniku Immersive, zapoznaj się z wymaganiami polityki zgodności plików cookie UE. Zgodnie z unijną polityką w zakresie zgodności z przepisami plików cookie użytkownik jest odpowiedzialny za uzyskanie niezbędnej zgody użytkownika.

Zasady dotyczące plików cookie można ustawić za pomocą [opcji](../reference.md#options)Immersive Reader . Zobacz [CookiePolicy enum,](../reference.md#cookiepolicy-enum) aby uzyskać więcej informacji.

## <a name="enable-cookie-usage"></a>Włącz korzystanie z plików cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Wyłącz użycie plików cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Następne kroki

* Wyświetl [przewodnik Szybki start w pliku Node.js,](../quickstart-nodejs.md) aby zobaczyć, co jeszcze można zrobić z modułem Immersive Reader SDK przy użyciu pliku Node.js
* Zobacz [samouczek Pythona,](../tutorial-python.md) aby zobaczyć, co jeszcze możesz zrobić z zestawu Immersive Reader SDK przy użyciu języka Python
* Zobacz [samouczek Swift,](../tutorial-ios-picture-immersive-reader.md) aby zobaczyć, co jeszcze możesz zrobić z zestawu Immersive Reader SDK za pomocą programu Swift
* Poznaj [immersyjny moduł SDK czytnika](https://github.com/microsoft/immersive-reader-sdk) i [immersyjny moduł SDK czytnika](../reference.md)