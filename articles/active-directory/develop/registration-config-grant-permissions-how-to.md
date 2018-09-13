---
title: Jak udzielić uprawnień do aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak udzielić uprawnień do niestandardowej aplikacji przy użyciu portalu usługi Azure AD lub parametr adresu URL
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: celested
ms.openlocfilehash: 10cf04fca8379f41587b1ea680c5b52a26193b53
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "44724165"
---
# <a name="how-to-grant-permissions-to-a-custom-developed-application"></a>Jak udzielić uprawnień do aplikacji niestandardowej

Jeśli chcesz wyrazić zgody prewencyjnego w aplikacji lub zostały przekroczone błąd, który nie wyrażono zgodę na aplikację, wypróbuj następujące kroki.

## <a name="how-to-perform-admin-consent-for-your-application"></a>Jak przeprowadzić zgody administratora dla swojej aplikacji

Ma to wpływ udzielania zgody dla aplikacji dla wszystkich użytkowników w Twojej organizacji.

1. Przejdź do **rejestracje aplikacji** bloku jako **administratora globalnego**, następnie wybierz aplikację.

2. Wybierz **wymagane uprawnienia**i na koniec kliknij przycisk **Udziel uprawnień** znajdujący się u góry bloku.

Alternatywnie możesz utworzyć żądanie, aby *login.microsoftonline.com* o swojej konfiguracji aplikacji i Dołącz na *& Monituj = administrator\_zgody*. Po zarejestrowaniu się przy użyciu poświadczeń administratora, aplikację udzielono zgody dla wszystkich użytkowników.

## <a name="how-to-force-user-consent-for-your-application"></a>Jak wymusić zgody użytkownika dla aplikacji

* Dołącz do uwierzytelniania żądań *& Monituj = zgody* wymagające zgody każdorazowo podczas uwierzytelniania użytkowników końcowych.

## <a name="next-steps"></a>Kolejne kroki

[Integrowanie aplikacji do usługi Azure AD i zgody](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)

[Wyrażania zgody i udzielania do nich uprawnień dla usługi Azure AD v2.0 zbieżne aplikacje](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Usługi Azure AD w witrynie StackOverflow](http://stackoverflow.com/questions/tagged/azure-active-directory)
