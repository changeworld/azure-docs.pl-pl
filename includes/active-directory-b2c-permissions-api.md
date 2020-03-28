---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: mimart
ms.openlocfilehash: 71a6654acd436c27bd2370646dede81778113860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186149"
---
#### <a name="applications"></a>[Aplikacje](#tab/applications/)

1. Wybierz **pozycję Aplikacje**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. W wybierz **interfejs API** wybierz interfejs API, do którego aplikacji sieci web powinny mieć dostęp. Na przykład *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zakresy zdefiniowane wcześniej. Na przykład *demo.read* i *demo.write*.
1. Kliknij przycisk **OK**.

#### <a name="app-registrations-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. W obszarze **Zarządzanie**wybierz pozycję **Uprawnienia interfejsu API**.
1. W obszarze **Skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API.**
1. Wybierz interfejs API, do którego aplikacja sieci web ma mieć dostęp. Na przykład *webapi1*.
1. W obszarze **Uprawnienie**rozwiń **demo**, a następnie wybierz zakresy zdefiniowane wcześniej. Na przykład *demo.read* i *demo.write*.
1. Wybierz pozycję **Dodaj uprawnienia**. Zgodnie z zaleceniami, odczekaj kilka minut, zanim przejdziesz do następnego kroku.
1. Wybierz **pozycję Przyznaj zgodę administratora dla (nazwa dzierżawy)**.
1. Wybierz aktualnie zalogowane konto administratora lub zaloguj się za pomocą konta w dzierżawie usługi Azure AD B2C, do których przypisano co najmniej rolę administratora aplikacji w *chmurze.*
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz **pozycję Odśwież**, a następnie sprawdź, czy "Przyznane dla ..." pojawia się w obszarze **Stan** dla obu zakresów. Może upłynąć kilka minut, aby uprawnienia do propagacji.