---
title: Serializacja niestandardowego buforu tokenów w MSAL dla języka Java
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zdeserializować pamięć podręczną tokenów dla MSAL for Java
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0bd8cb07d30aa5223cf3d78d636f0d593fefbecf
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905500"
---
# <a name="custom-token-cache-serialization-in-msal-for-java-msal4j"></a>Serializacja niestandardowego buforu tokenów w programie MSAL for Java (MSAL4J)

Aby mieć trwałą aplikację pamięci podręcznej tokenów, należy dostosować serializację. Klasy i interfejsy języka Java wykorzystywane w serializacji pamięci podręcznej tokenu są następujące:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): interfejs reprezentujący pamięć podręczną tokenów zabezpieczających.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): interfejs reprezentujący działanie wykonywania kodu przed i po Access. Należy @Override *beforeCacheAccess* i *afterCacheAccess* z logiką odpowiedzialną za Serializowanie i deserializacja pamięci podręcznej.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): interfejs reprezentujący kontekst, w którym uzyskuje się dostęp do pamięci podręcznej tokenów. 

Poniżej znajduje się algorytmie implementacja niestandardowa serializacji serializacji/deserializacji pamięci podręcznej tokenów. Nie należy kopiować i wklejać do środowiska produkcyjnego.

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

Dowiedz się więcej na temat [pobierania i usuwania kont z pamięci podręcznej tokenów przy użyciu programu MSAL for Java](msal-java-get-remove-accounts-token-cache.md).
