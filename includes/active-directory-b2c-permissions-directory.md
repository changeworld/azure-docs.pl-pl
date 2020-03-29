---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: bf8b9370abc58902d9c751505f633dee852210a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184360"
---
#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Na stronie **Przegląd zarejestrowanej aplikacji** wybierz pozycję **Ustawienia**.
1. W obszarze **Dostęp do interfejsu API**wybierz pozycję Wymagane **uprawnienia**.
1. Wybierz microsoft **graph**.
1. W obszarze **Uprawnienia aplikacji**zaznacz pole wyboru uprawnienia do udzielania aplikacji do zarządzania. Przykład:
    * **Przeczytaj wszystkie dane dziennika inspekcji:** Wybierz to uprawnienie do odczytywania dzienników inspekcji katalogu.
    * **Odczytywanie i zapisywanie danych katalogu:** Wybierz to uprawnienie dla scenariuszy migracji użytkowników lub zarządzania użytkownikami.
    * **Odczytuj i zapisuj zasady struktury zaufania organizacji:** wybierz to uprawnienie dla scenariuszy ciągłej integracji/ciągłego dostarczania (CI/CD). Na przykład niestandardowe wdrażanie zasad za pomocą usługi Azure Pipelines.
1. Wybierz **pozycję Zapisz**.
1. Wybierz **pozycję Udziel uprawnień**, a następnie wybierz pozycję **Tak**. Może upłynąć kilka minut, aby uprawnienia do pełnego propagacji.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. W obszarze **Zarządzanie**wybierz pozycję **Uprawnienia interfejsu API**.
1. W obszarze **Skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Interfejsy API firmy Microsoft,** a następnie wybierz pozycję **Microsoft Graph**.
1. Wybierz pozycję **Uprawnienia aplikacji**.
1. Rozwiń odpowiednią grupę uprawnień i zaznacz pole wyboru uprawnienia do udzielenia aplikacji zarządzania. Przykład:
    * **AuditLog** > **AuditLog.Read.All**: Do odczytywania dzienników inspekcji katalogu.
    * **Katalog** > **katalogów.ReadWrite.All**: Dla scenariuszy migracji użytkowników lub zarządzania użytkownikami.
    * **Policy** > **Policy.ReadWrite.TrustFramework**: Dla scenariuszy ciągłej integracji/ciągłego dostarczania (CI/CD). Na przykład niestandardowe wdrażanie zasad za pomocą usługi Azure Pipelines.
1. Wybierz pozycję **Dodaj uprawnienia**. Zgodnie z zaleceniami, odczekaj kilka minut, zanim przejdziesz do następnego kroku.
1. Wybierz **pozycję Przyznaj zgodę administratora dla (nazwa dzierżawy)**.
1. Wybierz aktualnie zalogowane konto administratora lub zaloguj się za pomocą konta w dzierżawie usługi Azure AD B2C, do których przypisano co najmniej rolę administratora aplikacji w *chmurze.*
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz **pozycję Odśwież**, a następnie sprawdź, czy "Przyznane dla ..." pojawia się w obszarze **Stan**. Może upłynąć kilka minut, aby uprawnienia do propagacji.
