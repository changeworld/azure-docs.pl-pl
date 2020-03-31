---
title: Wyłączanie weryfikacji poczty e-mail podczas rejestracji klienta
titleSuffix: Azure AD B2C
description: Dowiedz się, jak wyłączyć weryfikację poczty e-mail podczas rejestracji klienta w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 369b4e13baa344a71a51b358ef810d1a66b4b6ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126720"
---
# <a name="disable-email-verification-during-customer-sign-up-in-azure-active-directory-b2c"></a>Wyłączanie weryfikacji poczty e-mail podczas rejestracji klienta w usłudze Azure Active Directory B2C

[!INCLUDE [disable email verification intro](../../includes/active-directory-b2c-disable-email-verification.md)]

Wykonaj następujące kroki, aby wyłączyć weryfikację poczty e-mail:

1. Logowanie się do [witryny Azure portal](https://portal.azure.com)
1. Użyj filtru **subskrypcja Katalog +** w górnym menu, aby wybrać katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **przepływy użytkownika**.
1. Wybierz przepływ użytkownika, dla którego chcesz wyłączyć weryfikację poczty e-mail. Na przykład *B2C_1_signinsignup*.
1. Wybierz **układy stron**.
1. Wybierz **stronę rejestracji konta lokalnego**.
1. W obszarze **Atrybuty użytkownika**wybierz pozycję **Adres e-mail**.
1. Z listy rozwijanej **WYMAGA WERYFIKACJI** wybierz pozycję **Nie**.
1. Wybierz **pozycję Zapisz**. Weryfikacja poczty e-mail jest teraz wyłączona dla tego przepływu użytkownika.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [dostosować interfejs użytkownika w usłudze Azure Active Directory B2C](customize-ui-overview.md)

