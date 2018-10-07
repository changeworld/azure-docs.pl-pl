---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 66aaabb942e6296243aada9951a232496e7734e9
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2018
ms.locfileid: "48843417"
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
> *msal.js* cele *punktu końcowego v2 usługi Azure Active Directory* -umożliwiająca osobistego, służbowego i służbowymi kontami logować się i uzyskać tokeny. *Punktu końcowego v2 usługi Azure Active Directory* ma [pewne ograniczenia](..\articles\active-directory\develop\active-directory-v2-limitations.md).
> Aby zrozumieć różnice między punktami końcowymi v1 i v2, przeczytaj [Porównanie wersji 1 v2](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
