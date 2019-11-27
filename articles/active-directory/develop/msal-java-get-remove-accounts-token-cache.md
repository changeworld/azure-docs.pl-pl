---
title: Pobieranie i usuwanie kont z pamięci podręcznej tokenów przy użyciu programu MSAL for Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyświetlać i usuwać konta z pamięci podręcznej tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka Java.
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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452483"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Pobieranie i usuwanie kont z pamięci podręcznej tokenów przy użyciu programu MSAL for Java

MSAL for Java domyślnie udostępnia pamięć podręczną tokenów w pamięci. Pamięć podręczna tokenów w pamięci jest okresem trwania wystąpienia aplikacji.

## <a name="see-which-accounts-are-in-the-cache"></a>Zobacz, które konta znajdują się w pamięci podręcznej

Możesz sprawdzić, jakie konta znajdują się w pamięci podręcznej, wywołując `PublicClientApplication.getAccounts()` jak pokazano w następującym przykładzie:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Usuwanie kont z pamięci podręcznej

Aby usunąć konto z pamięci podręcznej, Znajdź konto, które należy usunąć, a następnie Wywołaj `PublicClientApplicatoin.removeAccount()` jak pokazano w następującym przykładzie:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Dowiedz się więcej

Jeśli używasz programu MSAL for Java, Dowiedz się więcej o [serializacji niestandardowego buforu tokenów w MSAL for Java](msal-java-token-cache-serialization.md).
