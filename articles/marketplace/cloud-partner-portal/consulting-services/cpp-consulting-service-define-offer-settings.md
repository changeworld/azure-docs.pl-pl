---
title: Zdefiniuj platformy Azure i doradcze oferty usługi Dynamics 365 — ustawienia oferty | Dokumentacja firmy Microsoft
description: Przewodnik do definiowania oferują ustawienia w platformie Azure lub Dynamics 365 doradcze usługi oferty w portalu Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 590aa440f35f97e854fa827b0d8db1c3f9211faf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60749659"
---
# <a name="offer-settings-tab"></a>Karta ustawień oferty

Na **nowa oferta** ekranu, pierwszym krokiem jest utworzenie tożsamości oferty. Tożsamość oferty składa się z trzech części: **Identyfikator oferty**, **Identyfikatora wydawcy**, i **nazwa**. Każdy z tych elementów zostało opisane w poniższych sekcjach.

![Utwórz nową ofertę usługi doradcze — oferuje karta Ustawienia](media/consultingoffer-settings-tab.png)

### <a name="offer-id"></a>Identyfikator oferty

Ten identyfikator jest unikatową nazwą, utworzonej po przesłaniu najpierw oferty. Musi ona zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia. **Identyfikatora oferty Offer ID** jest widoczna w adresie URL i wpływa na wynikach wyszukiwarki. Na przykład *yourcompanyname_exampleservice*.

Jak pokazano w przykładzie **identyfikatora oferty Offer ID** jest dołączany do Identyfikatora wydawcy do utworzenia unikatowego identyfikatora. Ten unikatowy identyfikator jest udostępniany jako łącze stałe, mogą być zapisane, która jest indeksowana przez aparaty wyszukiwania.

>[!Note]
>Po oferta jest przeznaczona na żywo, nie można zaktualizować jego identyfikator.

### <a name="publisher-id"></a>Identyfikator wydawcy

Ten identyfikator jest związane z Twoim kontem. Po zalogowaniu się przy użyciu konta organizacyjnego usługi **Identyfikatora wydawcy** pojawia się w menu rozwijanym.

### <a name="name"></a>Name (Nazwa)

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
