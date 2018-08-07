---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f70e0bcb68f059618f9b398a00e23498a10df23e
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2018
ms.locfileid: "39582955"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Wywołanie interfejsu API programu Microsoft Graph z poziomu pojedynczego JavaScript strony aplikacji (SPA)

Ten przewodnik pokazuje, jak zalogować się JavaScript jednej strony aplikacji (SPA) w pracę osobistą, a konta służbowe, Uzyskaj token dostępu i wywołania interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego usługi Azure Active Directory w wersji 2.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.png)

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia SPA JavaScript wysłać zapytanie interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego v2 usługi Azure Active Directory. W tym scenariuszu po użytkownik loguje się, token dostępu jest wymagane i dodać do żądań HTTP za pomocą nagłówka autoryzacji. Uzyskanie tokenu i odnawianie są obsługiwane przez Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Biblioteki

W tym przewodniku używane są następujące biblioteki:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka Microsoft Authentication Library dla JavaScript (wersja zapoznawcza)|

> [!NOTE]
> *msal.js* cele *punktu końcowego v2 usługi Azure Active Directory* -umożliwiająca osobistego, służbowego i służbowymi kontami logować się i uzyskać tokeny. *Punktu końcowego v2 usługi Azure Active Directory* ma [pewne ograniczenia](..\articles\active-directory\develop\active-directory-v2-limitations.md). Jeśli interesuje Cię tylko konta służbowe i pracy, użyj *adal.js* i *punktu końcowego V1*. Aby zrozumieć różnice między punktami końcowymi v1 i v2, przeczytaj [Porównanie wersji 1 v2](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
