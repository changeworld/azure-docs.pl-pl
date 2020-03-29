---
title: Uzyskaj & usuń konta z pamięci podręcznej tokenów (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyświetlać i usuwać konta z pamięci podręcznej tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla środowiska Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696201"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Pobierz i usuń konta z pamięci podręcznej tokenów przy użyciu usługi MSAL dla języka Java

Msal dla języka Java domyślnie zapewnia pamięć podręczną tokenów w pamięci. Pamięć podręczna tokenu w pamięci trwa czas trwania wystąpienia aplikacji.

## <a name="see-which-accounts-are-in-the-cache"></a>Zobacz, które konta znajdują się w pamięci podręcznej

Możesz sprawdzić, jakie konta znajdują `PublicClientApplication.getAccounts()` się w pamięci podręcznej, wywołując, jak pokazano w poniższym przykładzie:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Usuwanie kont z pamięci podręcznej

Aby usunąć konto z pamięci podręcznej, znajdź konto, `PublicClientApplicatoin.removeAccount()` które należy usunąć, a następnie zadzwoń, jak pokazano w poniższym przykładzie:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli używasz msal dla języka Java, dowiedz się więcej o [serializacji niestandardowej pamięci podręcznej tokenów w msal dla języka Java](msal-java-token-cache-serialization.md).
