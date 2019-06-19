---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183836"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Logowania użytkowników i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji jednostronicowej JavaScript (SPA)

Ten przewodnik przedstawia, jak JavaScript aplikacja jednostronicowa (SPA) może zalogować się pracę osobistą, i kont służbowych, Uzyskaj token dostępu i wywołania interfejsu API programu Microsoft Graph lub innych interfejsów API, które wymagają tokenów dostępu z punktu końcowego platformy tożsamości firmy Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Jak działa przykładowej aplikacji wygenerowane przez ten przewodnik

![Przedstawia, jak przykładowa aplikacja wygenerowany przez ta działa samouczki](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Więcej informacji

Przykładowa aplikacja utworzona przez ten przewodnik umożliwia SPA JavaScript wysłać zapytanie interfejsu API programu Microsoft Graph i interfejsu API sieci Web, który akceptuje tokeny od punktu końcowego platformy tożsamości firmy Microsoft. W tym scenariuszu po użytkownik loguje się, token dostępu jest wymagane i dodać do żądań HTTP za pomocą nagłówka autoryzacji. Uzyskanie tokenu i odnawianie są obsługiwane przez Microsoft Authentication Library (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Biblioteki

W tym przewodniku używane są następujące biblioteki:

|Biblioteka|Opis|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteka Microsoft Authentication Library dla JavaScript (wersja zapoznawcza)|

> [!NOTE]
> *msal.js* cele *punktu końcowego platformy tożsamości firmy Microsoft* -umożliwiająca osobistego, służbowego i służbowymi kontami logować się i uzyskać tokeny. *Punktu końcowego platformy tożsamości firmy Microsoft* ma [pewne ograniczenia](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Aby zrozumieć różnice między punktami końcowymi w wersji 1.0 i 2.0, przeczytaj [Podręcznik: porównanie punktu końcowego](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
