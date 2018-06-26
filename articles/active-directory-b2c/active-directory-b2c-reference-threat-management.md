---
title: Zagrożenia zarządzania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Więcej informacji na temat techniki wykrywanie i zapobieganie atakom typu "odmowa usługi" i prób złamania hasła w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 0a439a2bb485829b863d6aa666938ab58c90c61f
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/25/2018
ms.locfileid: "36750781"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Usługa Azure Active Directory B2C: Zarządzanie zagrożeniami

Zarządzanie zagrożeniami obejmuje planowanie dotyczące ochrony przed atakami systemu i sieci. Ataki typu "odmowa usługi" może spowodować zasobów niedostępny uprawnionych użytkowników. Złamania hasła prowadzić do nieautoryzowanego dostępu do zasobów. Usługa Azure Active Directory B2C (Azure AD B2C) ma wbudowane funkcje, które mogą pomóc chronić dane przed zagrożeniami tych na wiele sposobów.

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Azure AD B2C używa wykrywanie i zapobieganie technik, takich jak pliki cookie SYN i limitów szybkości i połączenia w celu ochrony zasobów przed atakami typu "odmowa usługi".

## <a name="password-attacks"></a>Złamania hasła

Usługa Azure AD B2C ma również techniki środki zaradcze w przypadku złamania hasła. Środki zaradcze obejmuje ataków siłowych hasła i słownikowymi hasła. Hasła, które są ustawiane przez użytkowników muszą być rozsądnych złożonych. Za pomocą różnych sygnałów, usługi Azure AD B2C analizuje integralności żądań. Usługa Azure AD B2C jest przeznaczona do inteligentnie rozróżnianie uprawnionych użytkowników przed hakerami i zakłócanych. Usługi Azure AD B2C udostępnia zaawansowane strategii blokady konta oparte na wprowadzone w prawdopodobieństwo ataku hasła.

Aby uzyskać więcej informacji, odwiedź stronę [Microsoft Trust Center](https://www.microsoft.com/en-us/trustcenter/default.aspx).
