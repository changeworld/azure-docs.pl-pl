---
title: Pobieranie par klucz-wartość z punktu w czasie
titleSuffix: Azure App Configuration
description: Pobieranie starych par klucz-wartość przy użyciu migawek typu "w czasie" w konfiguracji aplikacji platformy Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523664"
---
# <a name="point-in-time-snapshot"></a>Migawka punktu w czasie

Usługa Azure App Configuration przechowuje zapis zmian wprowadzonych w parach klucz-wartość. Ten rekord zawiera oś czasu zmian wartości klucza. Można zrekonstruować historię dowolnej wartości klucza i podać jej przeszłą wartość w dowolnym momencie w ciągu ostatnich siedmiu dni. Korzystając z tej funkcji, można "czas podróży" do tyłu i pobrać stary klucz wartość. Na przykład można odzyskać ustawienia konfiguracji używane przed najnowszym wdrożeniem w celu przywrócenia aplikacji do poprzedniej konfiguracji.

## <a name="key-value-retrieval"></a>Pobieranie wartości klucza

Za pomocą programu Azure PowerShell można pobrać wartości klucza przeszłości.  Użyj `az appconfig revision list`, dodając odpowiednie parametry, aby pobrać wymagane wartości.  Określ wystąpienie konfiguracji aplikacji platformy Azure,`--name {app-config-store-name}`podając nazwę sklepu`--connection-string {your-connection-string}`( ) lub przy użyciu ciągu połączenia ( ). Ogranicz dane wyjściowe, określając`--datetime`określony punkt w czasie ( ) i`--top`określając maksymalną liczbę elementów do zwrócenia ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pobierz wszystkie zarejestrowane zmiany wartości klucza.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Pobierz wszystkie zarejestrowane zmiany `environment` dla `test` klucza i etykiet oraz `prod`.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Pobierz wszystkie zarejestrowane zmiany w `environment:prod`hierarchicznym miejscu klucza .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Pobierz wszystkie zarejestrowane zmiany `color` dla klucza w określonym punkcie w czasie.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Pobierz ostatnie 10 zarejestrowanych zmian w wartościach kluczy `key` `label`i `last-modified` zwróć tylko wartości dla , i sygnatury czasowej.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej ASP.NET Core](./quickstart-aspnet-core-app.md)  
