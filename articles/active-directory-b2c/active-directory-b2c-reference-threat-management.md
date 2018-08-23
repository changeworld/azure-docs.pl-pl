---
title: Zagrożeń, zarządzania w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Poznaj techniki wykrywania i łagodzenia skutków ataków typu "odmowa usługi" i złamania hasła w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1801fe9695aa15850d600300b957df2c7d7cd9ef
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/18/2018
ms.locfileid: "42056388"
---
# <a name="azure-active-directory-b2c-threat-management"></a>Usłudze Azure Active Directory B2C: Zarządzanie zagrożeniami

Zarządzanie zagrożeniami obejmuje planowanie dotyczące ochrony przed atakami systemu i sieci. Ataki typu "odmowa usługi" wprowadzać zasobów niedostępny oprogramowanie wyświetla odpowiednich użytkowników. Złamania hasła prowadzić do nieautoryzowanego dostępu do zasobów. Usługa Azure Active Directory B2C (Azure AD B2C) ma wbudowane funkcje, które mogą pomóc Ci chronić dane przed tymi zagrożeniami na wiele sposobów.

## <a name="denial-of-service-attacks"></a>Ataki typu "odmowa usługi"

Usługa Azure AD B2C używa wykrywania i łagodzenia skutków technik, takich jak pliki cookie SYN i limitów szybkości i połączenia do ochrony zasobów przed atakami typu "odmowa usługi".

## <a name="password-attacks"></a>Złamania hasła

Usługa Azure AD B2C ma również ograniczenia technik w celu złamania hasła. Środki zaradcze obejmuje ataków siłowych hasła i złamania hasła słownika. Hasła, które są ustawiane przez użytkowników muszą być zrealizowane złożone. Za pomocą różnych sygnały, usługi Azure AD B2C analizuje integralność żądań. Usługa Azure AD B2C jest przeznaczony do inteligentnie odróżnić oprogramowanie wyświetla odpowiednich użytkowników przed hakerami i botnetami. Usługa Azure AD B2C zapewnia zaawansowanej strategii blokowania kont w oparciu o wprowadzone w prawdopodobieństwa ataku hasła.

Aby uzyskać więcej informacji, odwiedź stronę [Microsoft Trust Center](https://www.microsoft.com/trustcenter/default.aspx).
