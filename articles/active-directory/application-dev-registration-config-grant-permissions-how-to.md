---
title: Jak udzielić uprawnień do aplikacji niestandardowej | Dokumentacja firmy Microsoft
description: Jak udzielić uprawnień do niestandardowej aplikacji przy użyciu portalu usługi Azure AD lub parametr adresu URL
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.openlocfilehash: 75beeb35b740bb126fff905f4cfa5a0b455e025e
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365247"
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
