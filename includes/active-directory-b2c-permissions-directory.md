---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78184360"
---
#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Na stronie Przegląd **zarejestrowanej aplikacji** wybierz pozycję **Ustawienia**.
1. W obszarze **dostęp do interfejsu API**wybierz pozycję **wymagane uprawnienia**.
1. Wybierz **Microsoft Graph**.
1. W obszarze **uprawnienia aplikacji**zaznacz pole wyboru uprawnienia do udzielenia aplikacji zarządzania. Na przykład:
    * **Przeczytaj wszystkie dane dziennika inspekcji**: wybierz to uprawnienie do odczytywania dzienników inspekcji katalogu.
    * **Odczytuj i zapisuj dane katalogu**: wybierz to uprawnienie do migracji użytkowników lub scenariuszy zarządzania użytkownikami.
    * **Odczytuj i zapisuj zasady struktury zaufania organizacji**: wybierz to uprawnienie do scenariuszy ciągłej integracji/ciągłego dostarczania (Ci/CD). Na przykład niestandardowe wdrożenie zasad z Azure Pipelines.
1. Wybierz pozycję **Zapisz**.
1. Wybierz pozycję **Udziel uprawnień**, a następnie wybierz pozycję **tak**. Pełne propagowanie uprawnień może potrwać kilka minut.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **interfejsy API firmy Microsoft** , a następnie wybierz pozycję **Microsoft Graph**.
1. Wybierz pozycję **Uprawnienia aplikacji**.
1. Rozwiń odpowiednią grupę uprawnień i zaznacz pole wyboru uprawnienia do udzielenia aplikacji zarządzania. Na przykład:
    * **Auditlog** > **auditlog. Read. All**: na potrzeby odczytywania dzienników inspekcji katalogu.
    * **Katalog** > **katalog. wszystkie**: na potrzeby migracji użytkowników lub scenariuszy zarządzania użytkownikami.
    * **Zasady** > **Policy. ReadWrite. TrustFramework**: w scenariuszach ciągłej integracji/ciągłego dostarczania (Ci/CD). Na przykład niestandardowe wdrożenie zasad z Azure Pipelines.
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)** .
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan**. Propagowanie uprawnień może potrwać kilka minut.
