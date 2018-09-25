---
title: Usługa Azure Active Directory zmiany odwołania | Dokumentacja firmy Microsoft
description: Zmiany wprowadzone do usługi Azure AD protokołów, które mogą mieć wpływ na aplikację.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 68517c83-1279-4cc7-a7c1-c7ccc3dbe146
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2018
ms.author: celested
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: 14217d03cdb56c5c641ab8f8c3fc0748e8e815a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46987777"
---
# <a name="whats-new-for-authentication"></a>What's new do uwierzytelniania? 

|
>Otrzymaj powiadomienie o tym, kiedy Odwiedzaj tę stronę w celu aktualizacji, dodając następujący kod [adresu URL](https://docs.microsoft.com/api/search/rss?search=%22whats%20new%20for%20authentication%22&locale=en-us) swoje RSS czytnik źródła.

System uwierzytelniania zmienia i dodaje funkcje w sposób ciągły w celu zwiększenia bezpieczeństwa i zwiększyć zgodność ze standardami. Aby uzyskać najnowsze informacje o najnowszych zmianach, w tym artykule przedstawiono informacje o:

- Najnowsze funkcje
- Znane problemy
- Protokół zmian
- Przestarzałe funkcje

> [!TIP] 
> Ta strona jest regularnie aktualizowana, więc ponownie często. Jeśli nie określono inaczej, te zmiany są tylko wprowadzone dla nowo zarejestrowanych aplikacji.  

## <a name="upcoming-changes"></a>Nadchodzących zmianach

### <a name="authorization-codes-can-no-longer-be-reused"></a>Kody autoryzacji nie będzie możliwe ponowne użycie

**Data rozpoczęcia obowiązywania**: 10 października 2018 r. **dotyczy punktów końcowych**: zarówno w wersji 1.0 i 2.0 **protokołu wpływ na**: [kodu przepływu](v2-oauth2-auth-code-flow.md)

10 października 2018 r. usługi Azure AD będzie akceptować kody wcześniej używane do uwierzytelniania w przypadku nowych aplikacji. Każda aplikacja utworzona przed 10 października 2018 r. nadal będzie można ponownie użyć kody uwierzytelniania. Ta zmiana zabezpieczeń pomaga do usługi Azure AD zgodnie ze specyfikacją protokołu OAuth i będzie wymuszany w punktach końcowych v1 i v2.

Jeśli aplikacja używa kody autoryzacji uzyskiwanie tokenów dla wielu zasobów, zalecamy Użyj kodu, aby uzyskać token odświeżania, a następnie używać tego tokenu odświeżania w celu pobrania dodatkowe tokeny dla innych zasobów. Kody autoryzacji należy używać tylko raz, ale tokenów odświeżania mogą być wielokrotnie używane w wielu zasobach. Nowa aplikacja, która podejmuje próbę ponownego użycia kodu uwierzytelniania podczas przepływu kodu uwierzytelniania OAuth otrzymają komunikat o błędzie invalid_grant.

Aby uzyskać więcej informacji na temat tokeny odświeżania, zobacz [odświeżanie tokenów dostępu](v1-protocols-oauth-code.md#refreshing-the-access-tokens).

## <a name="may-2018"></a>Maj 2018 r.

### <a name="id-tokens-cannot-be-used-for-the-obo-flow"></a>Nie można używać tokenów identyfikator przepływu OBO

**Data**: 1 maja 2018 **dotyczy punktów końcowych**: zarówno w wersji 1.0 i 2.0 **protokołów wpływ na**: niejawny przepływ i [przepływu OBO](v1-oauth2-on-behalf-of-flow.md)

Po 1 maja 2018 r id_tokens nie może służyć jako potwierdzenie przepływu OBO dla nowych aplikacji.  Tokeny dostępu należy zamiast tego zabezpieczanie interfejsów API, nawet między klientem a warstwy środkowej w tej samej aplikacji.  Aplikacje zarejestrowane przed 1 maja 2018 będą nadal działać i wymieniać id_tokens tokenu dostępu — jednak to nie jest uważany za najlepszym rozwiązaniem.

Aby obejść tę zmianę, możesz wykonać następujące czynności:

1. Tworzenie internetowego interfejsu API warstwy środkowej aplikacji, za pomocą jednego lub więcej zakresów.  Dzięki temu będzie bardziej szczegółową kontrolę i zabezpieczeń.
1. W manifeście aplikacji [witryny Azure Portal](https://portal.azure.com), lub [portalu rejestracji aplikacji](https://apps.dev.microsoft.com), upewnij się, czy aplikacja może wystawiać tokeny dostępu za pośrednictwem niejawny przepływ. To jest kontrolowany za pośrednictwem `oauth2AllowImplicitFlow` klucza.
1. Gdy Twoja aplikacja kliencka żąda id_token za pośrednictwem `response_type=id_token`, również żądać tokenu dostępu (`response_type=token`) dla interfejsu API sieci Web utworzonego powyżej.  W związku z tym korzystając z punktu końcowego v2.0 `scope` parametr powinien wyglądać podobnie do `api://GUID/SCOPE`.  W punkcie końcowym v1.0 `resource` parametr powinien być aplikacji URI interfejsu API sieci web.
1. Przekaż ten token dostępu do warstwy środkowej zamiast id_token.  