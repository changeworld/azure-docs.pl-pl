---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
ms.openlocfilehash: 3b6f8ef5f2ec9f77dcba9c006fdd28b5ec668033
ms.sourcegitcommit: 9fba13cdfce9d03d202ada4a764e574a51691dcd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71315015"
---
Komunikacja z Azure AD B2C odbywa się za pomocą aplikacji zarejestrowanej w dzierżawie B2C. W tej sekcji przedstawiono kroki opcjonalne, które można wykonać, aby utworzyć aplikację testową, jeśli nie została jeszcze wykonana.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *testapp1*.
1. W przypadku **aplikacji sieci Web/internetowego interfejsu API**wybierz pozycję **tak**.
1. Dla **adresu URL odpowiedzi**wprowadź`https://jwt.ms`
1. Wybierz pozycję **Utwórz**.
