---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 31d4b65c6cd6a3dfe3cb779022329f338436c69b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184361"
---
Aby zarejestrować aplikację w dzierżawie usługi Azure AD B2C, można użyć **bieżącego środowiska aplikacji** lub naszego nowego środowiska rejestracji aplikacji ujednoliconej **(w wersji zapoznawczej).** [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W obszarze **Zarządzanie**wybierz pozycję **Rejestracje aplikacji (Starsza wersja)**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę aplikacji. Na przykład *managementapp1*.
1. W przypadku **typu aplikacji**wybierz pozycję Aplikacja sieci Web / **INTERFEJS API**.
1. Wprowadź dowolny prawidłowy adres URL w **adresie URL logowania**. Na przykład `https://localhost`. Punkt końcowy nie musi być osiągalny, ale musi być prawidłowym adresem URL.
1. Wybierz **pozycję Utwórz**.
1. Zarejestruj **identyfikator aplikacji** wyświetlany na stronie **Przegląd zarejestrowanej aplikacji.** Tej wartości używasz w późniejszym kroku.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. Wybierz ikonę **Katalog + Subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W witrynie Azure portal wyszukaj i wybierz **pozycję Azure AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *managementapp1*.
1. Wybierz **pozycję Konta tylko w tym katalogu organizacyjnym**.
1. W obszarze **Uprawnienia wyczyść**pole wyboru *Przyznanie zgody administratora na uprawnienia openid i offline_access.*
1. Wybierz pozycję **Zarejestruj**.
1. Zarejestruj **identyfikator aplikacji (klienta),** który pojawia się na stronie przeglądu aplikacji. Tej wartości używasz w późniejszym kroku.