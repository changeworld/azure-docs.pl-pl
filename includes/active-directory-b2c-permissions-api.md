---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 10/16/2019
ms.author: marsma
ms.openlocfilehash: ce49e9e26160017d64d745c9c7ad5402bc3ae2ca
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73641553"
---
#### <a name="applicationstabapplications"></a>[Aplikacje](#tab/applications/)

1. Wybierz pozycję **aplikacje**, a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. Wybierz pozycję **Dostęp do interfejsu API**, a następnie wybierz polecenie **Dodaj**.
1. Z listy rozwijanej **Wybierz interfejs API** wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. Z listy rozwijanej **Wybierz zakresy** wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Kliknij przycisk **OK**.

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[Rejestracje aplikacji (wersja zapoznawcza)](#tab/app-reg-preview/)

1. Wybierz pozycję **rejestracje aplikacji (wersja zapoznawcza)** , a następnie wybierz aplikację sieci Web, która powinna mieć dostęp do interfejsu API. Na przykład *webapp1*.
1. W obszarze **Zarządzaj**wybierz pozycję **uprawnienia interfejsu API**.
1. W obszarze **skonfigurowane uprawnienia**wybierz pozycję **Dodaj uprawnienie**.
1. Wybierz kartę **Moje interfejsy API** .
1. Wybierz interfejs API, do którego aplikacja sieci Web ma otrzymać dostęp. Na przykład *webapi1*.
1. W obszarze **uprawnienie**rozwiń pozycję **Demonstracja**, a następnie wybierz zdefiniowane wcześniej zakresy. Na przykład *Demonstracja. odczyt* i *Demonstracja. Write*.
1. Wybierz pozycję **Dodaj uprawnienia**. Poczekaj kilka minut, zanim przejdziesz do kolejnego kroku.
1. Wybierz pozycję **Udziel zgody administratora (nazwa dzierżawy)** .
1. Wybierz obecnie zalogowane konto administratora lub Zaloguj się przy użyciu konta w dzierżawie Azure AD B2C, do którego przypisano co najmniej rolę *administratora aplikacji w chmurze* .
1. Wybierz pozycję **Zaakceptuj**.
1. Wybierz pozycję **Odśwież**, a następnie sprawdź, czy "udzielono dla..." pojawia się w obszarze **stan** dla obu zakresów. Propagowanie uprawnień może potrwać kilka minut.