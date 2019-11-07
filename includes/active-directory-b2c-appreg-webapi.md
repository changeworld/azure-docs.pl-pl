---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: a286f8d401ec98a5069c07b1a82acdf4af2b8185
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641840"
---
Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

Aby zarejestrować aplikację w dzierżawie Azure AD B2C, możesz użyć bieżącego środowiska **aplikacji** lub naszego nowego interfejsu Unified **rejestracje aplikacji (wersja zapoznawcza)** . [Dowiedz się więcej na temat nowego środowiska](https://aka.ms/b2cappregintro).

#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **Aplikacje**, a następnie wybierz polecenie **Dodaj**.
1. Wprowadź nazwę aplikacji. Na przykład *webapi1*.
1. W przypadku **aplikacji sieci Web/internetowego interfejsu API**wybierz pozycję **tak**.
1. W obszarze **Zezwalaj na niejawny przepływ**wybierz pozycję **tak**.
1. Dla pozycji **Adres URL odpowiedzi** wprowadź punkt końcowy, w którym usługa Azure AD B2C powinna zwracać wszelkie tokeny żądane przez Twoją aplikację. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `https://localhost:5000`.
1. W przypadku identyfikatora **URI aplikacji**Dodaj identyfikator punktu końcowego interfejsu API do pokazanego identyfikatora URI. Na potrzeby tego samouczka wprowadź `api`, aby pełny identyfikator URI był podobny do `https://contosob2c.onmicrosoft.com/api`.
1. Wybierz pozycję **Utwórz**.
1. Zapisz **Identyfikator aplikacji** do użycia w późniejszym kroku.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz pozycję **Nowa rejestracja**.
1. Wprowadź **nazwę** aplikacji. Na przykład *webapi1*.
1. W obszarze **Identyfikator URI przekierowania**wybierz pozycję **Sieć Web**, a następnie wprowadź punkt końcowy, w którym Azure AD B2C powinien zwrócić tokeny, których aplikacja żąda. W tym samouczku przykładowa aplikacja jest uruchamiana lokalnie i nasłuchuje na `http://localhost:5000`.
1. Wybierz pozycję **Zarejestruj**.
1. Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Następnie Włącz przepływ niejawnego przydzielenia:

1. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie**.
1. Wybierz opcję **Wypróbuj nowe środowisko** (jeśli zostało wyświetlone).
1. W obszarze **niejawne przyznanie**zaznacz pola wyboru **tokeny dostępu** i **tokeny identyfikatora** .
1. Wybierz pozycję **Zapisz**.