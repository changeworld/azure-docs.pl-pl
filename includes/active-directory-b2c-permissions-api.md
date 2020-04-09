---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 04/04/2020
ms.author: mimart
ms.openlocfilehash: ee63c1c0913baf3700d77e6fba1582fe7027ead7
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80875693"
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
1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz **pozycję Przyznaj zgodę administratora dla (nazwa dzierżawy)**.
1. Jeśli zostanie wyświetlony monit o wybranie konta, wybierz aktualnie zalogowane konto administratora lub zaloguj się za pomocą konta w dzierżawie usługi Azure AD B2C, do której przypisano co najmniej rolę administratora aplikacji w *chmurze.*
1. Wybierz **pozycję Tak**.
1. Wybierz **pozycję Odśwież**, a następnie sprawdź, czy "Przyznane dla ..." pojawia się w obszarze **Stan** dla obu zakresów.
