---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/30/2019
ms.author: marsma
ms.openlocfilehash: 4ac69b51e69031a1a4ce83a2c09dc6cc010ccd62
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71720275"
---
Należy zarejestrować zasoby internetowego interfejsu API w dzierżawie, zanim będzie on mógł akceptować i odpowiadać na żądania chronionych zasobów wysyłane przez aplikacje klienckie przedstawiające token dostępu.

1. Zaloguj się do [portalu Azure](https://portal.azure.com).
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
