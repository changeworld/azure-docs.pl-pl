---
title: Zdefiniuj ustawienia oferty dla oferty usługi konsultingowe | Portal Azure Marketplace
description: Zdefiniować ustawienia oferty na platformie Azure lub Dynamics 365 doradcze usługi oferty w portalu Cloud Partner, w portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 601ad62bddd1373742b0cab5a388a55cfd52f4bc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64942787"
---
# <a name="offer-settings-tab"></a>Karta ustawień oferty

Na **nowa oferta** ekranu, pierwszym krokiem jest utworzenie tożsamości oferty. Tożsamość oferty składa się z trzech części: **Identyfikator oferty**, **Identyfikatora wydawcy**, i **nazwa**. Każdy z tych elementów zostało opisane w poniższych sekcjach.

![Utwórz nową ofertę usługi doradcze — oferuje karta Ustawienia](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Identyfikator oferty *

Ten identyfikator jest unikatową nazwą, utworzonej po przesłaniu najpierw oferty. Musi ona zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia. **Identyfikatora oferty Offer ID** jest widoczna w adresie URL i wpływa na wynikach wyszukiwarki. Na przykład *yourcompanyname_exampleservice*.

Jak pokazano w przykładzie **identyfikatora oferty Offer ID** jest dołączany do Identyfikatora wydawcy do utworzenia unikatowego identyfikatora. Ten unikatowy identyfikator jest udostępniany jako łącze stałe, mogą być zapisane, która jest indeksowana przez aparaty wyszukiwania.

>[!Note]
>Po oferta jest przeznaczona na żywo, nie można zaktualizować jego identyfikator.


### <a name="publisher-id"></a>Wydawca identyfikator *

Ten identyfikator jest związane z Twoim kontem. Po zalogowaniu się przy użyciu konta organizacyjnego usługi **Identyfikatora wydawcy** pojawia się w menu rozwijanym.


### <a name="name"></a>Nazwa *

Ten ciąg wyświetla się jako nazwa oferty w usłudze AppSource lub w witrynie Azure Marketplace. **Nazwa** pole jest ograniczona do 50 znaków. Recenzent może być konieczne edytowanie tytułu Dołącz czas trwania i oferować typu na nazwę swojej oferty.

Poniższy przykład pokazuje, jak nazwa oferty jest składany. 

![Tworzenie nowej oferty usługi doradcze](media/cppsampleconsultingoffer.png)

Nazwa oferty składa się z czterech części:

-   **Czas trwania:** Zdefiniowane na **szczegóły Storefront** karta edytora. Czas trwania może być wyrażona w godzinach, dniach lub tygodniach.
-   **Typ usługi:** Zdefiniowane na **szczegóły Storefront** karta edytora. Typy usług `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, i `Workshop`.
-   **Preposition:** Wstawione przez recenzenta.
-   **Nazwa:** Zdefiniowane na **oferują ustawienia** strony.

>[!Note]
>**Nazwa** pole jest ograniczona do 50 znaków. Recenzent może być konieczne edytowanie tytułu Dołącz czas trwania i oferować typu na nazwę swojej oferty.

Poniższa lista zawiera kilka nazw dobrze nazwane oferty:

-   Podstawy dla profesjonalnych usług: 1-godzinnego widoków
-   Platforma usług w chmurze migracji: 1-godzinnego widoków
-   Usługa PowerApps i Microsoft Flow: 1-dniowych warsztatów
-   Usługi Azure Machine Learning Services: 3-Wk PoC
-   Kostki, a następnie kliknij przycisk handlu detalicznego rozwiązania: 1-godzinnego widoków
-   Możesz używać własnych danych: Warsztaty 1 tydzień
-   Cloud Analytics: 3-dniowych warsztatów
-   Usługa Power BI szkolenia: 3-dniowych warsztatów
-   Rozwiązanie do zarządzania sprzedaży: Implementacja 1 tydzień
-   Szybki Start CRM: 1-dniowych warsztatów
-   Dynamics 365 for Sales: 2-dniowych oceny

Po wypełnieniu **oferują ustawienia** karta, Zapisz swoje zgłoszenie. Nazwa oferty pojawi się powyżej edytora i znaleźć go w **oferuje wszystkie**.

## <a name="next-steps"></a>Kolejne kroki

Teraz możesz wprowadzić [storefront szczegóły i określić, czy mają być publikowane w witrynie Azure Marketplace lub w usłudze AppSource](./cpp-consulting-service-storefront-details.md).
