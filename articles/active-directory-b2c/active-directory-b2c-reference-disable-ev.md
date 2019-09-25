---
title: Wyłącz weryfikację poczty e-mail podczas rejestracji klienta w Azure Active Directory B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas tworzenia konta klienta w Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 9a7adc04991dd4a472bdaf1aa47aacaf6cdeb190
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256933"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Wyłącz weryfikację poczty e-mail podczas rejestracji klienta w Azure Active Directory B2C

Domyślnie program Azure Active Directory B2C (Azure AD B2C) weryfikuje adres e-mail klienta dla kont lokalnych (konta użytkowników logujących się przy użyciu adresu e-mail lub nazwy użytkownika). Azure AD B2C zapewnia prawidłowe adresy e-mail, wymagając od klientów zweryfikowania ich podczas procesu rejestracji. Zapobiega to również złośliwym podmiotom korzystającym z zautomatyzowanych procesów do generowania fałszywych kont w aplikacjach.

Niektórzy deweloperzy aplikacji wolą pominąć weryfikację wiadomości e-mail podczas procesu rejestracji, a zamiast tego ponownie sprawdzić swój adres e-mail przez klientów. Aby to umożliwić, Azure AD B2C można skonfigurować w celu wyłączenia weryfikacji wiadomości e-mail. Dzięki temu program tworzy płynny proces tworzenia konta i zapewnia deweloperom elastyczność w odróżnieniu od klientów, którzy sprawdzili swój adres e-mail od klientów.

Wykonaj następujące kroki, aby wyłączyć weryfikację wiadomości e-mail:

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).
1. Użyj filtru **katalogów i subskrypcji** w górnym menu, aby wybrać katalog, który zawiera dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz wyłączyć weryfikację wiadomości e-mail. Na przykład *B2C_1_signinsignup*.
1. Wybierz pozycję **układy strony**.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. W obszarze **atrybuty użytkownika**wybierz opcję **adres e-mail**.
1. Z listy rozwijanej **wymaganie weryfikacji** wybierz pozycję **nie**.
1. Wybierz pozycję **Zapisz**. Weryfikacja poczty e-mail jest teraz wyłączona dla tego przepływu użytkownika.

> [!WARNING]
> Wyłączenie weryfikacji poczty e-mail w procesie tworzenia konta może prowadzić do spamu. W przypadku wyłączenia domyślnej weryfikacji poczty e-mail dostarczonej przez Azure AD B2C zalecamy zaimplementowanie systemu weryfikacji zamiennej.
