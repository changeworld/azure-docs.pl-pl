---
title: Niestandardowa serializacja pamięci podręcznej tokenów (MSAL4j)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak serializować pamięć podręczną tokenów dla usługi MSAL dla języka Java
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696167"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Niestandardowa serializacja pamięci podręcznej tokenów w języku MSAL dla języka Java

Aby utrwalić pamięć podręczną tokenu między wystąpieniami aplikacji, należy dostosować serializacji. Klasy Java i interfejsy zaangażowane w serializacji pamięci podręcznej tokenów są następujące:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Interfejs reprezentujący pamięć podręczną tokenów zabezpieczających.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Interfejs reprezentujący działanie wykonywania kodu przed i po dostępie. @Override *PrzedCacheAccess* i *afterCacheAccess* z logiką odpowiedzialną za serializowanie i deserializację pamięci podręcznej.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Interfejs reprezentujący kontekst, w którym jest dostępna pamięć podręczna tokenów. 

Poniżej znajduje się naiwna implementacja niestandardowej serializacji serializacji/deserializacji pamięci podręcznej tokenów. Nie należy kopiować i wklejać tego do środowiska produkcyjnego.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Dowiedz się więcej

Dowiedz się więcej [o pobierz i usuń konta z pamięci podręcznej tokenów przy użyciu usługi MSAL dla języka Java](msal-java-get-remove-accounts-token-cache.md).
