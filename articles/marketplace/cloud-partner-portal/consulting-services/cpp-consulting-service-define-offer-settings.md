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
ms.openlocfilehash: 6ce51dc9bbcdc7b46593a4800c4fce07a42f3171
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/13/2018
ms.locfileid: "53346223"
---
# <a name="offer-settings-tab"></a>Karta Ustawienia oferty

Na **nowa oferta** ekranu, pierwszym krokiem jest utworzenie tożsamości oferty. Tożsamość oferty składa się z trzech części: **Identyfikator oferty**, **Identyfikatora wydawcy**, i **nazwa**. Każdy z tych elementów zostało opisane w poniższych sekcjach.

![Tworzenie nowego doradcze usługi oferty — karta Ustawienia oferty](media/consultingoffer-settings-tab.png)

*Identyfikator oferty*

Ten identyfikator jest unikatową nazwą, utworzonej po przesłaniu najpierw oferty. Musi ona zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia. **Identyfikatora oferty Offer ID** będą widoczne w adresie URL i wpływ na wyniki aparatu wyszukiwania. Na przykład *yourcompanyname_exampleservice*

Jak pokazano w przykładzie **identyfikator oferty** pobiera dołączany do Identyfikatora wydawcy do utworzenia unikatowego identyfikatora. To jest udostępniany jako łącze stałe, mogą być zapisane, która jest indeksowana przez aparaty wyszukiwania.

*Po oferta jest przeznaczona na żywo, nie można zaktualizować identyfikatora*

*Identyfikator wydawcy*

Ten identyfikator jest związane z Twoim kontem. Jeśli zalogowano się za pomocą konta organizacyjnego usługi **wydawcy** identyfikator pojawią się w menu rozwijanym.

*Nazwa*

Ten ciąg jest o tym, co będzie wyświetlana jako nazwa oferty w usłudze AppSource lub witrynie Azure Marketplace. *Nazwa* pola jest ograniczona do 50 znaków.  Recenzent może być konieczne edytowanie tytułu umożliwiający dołączenie Nazwa oferty o czasie trwania i typ oferty.

>[!Note]
>Ważne: Wprowadź tylko nazwę nazwa rzeczywistej usługi. Nie dołączaj czas trwania i typ usługi.

Poniższy przykład przez Edgewater Fullscope pokazuje, jak nazwa oferty jest składany. Nazwa oferty wygląda następująco:

![Tworzenie nowej oferty usługi doradcze](media/cppsampleconsultingoffer.png)

Nazwa oferty składa się z czterech części:

-   **Czas trwania:** — to zdefiniowano w **szczegóły Storefront** karta edytora. Czas trwania może być wyrażona w godzinach, dniach lub tygodniach.
-   **Typ usługi:** — to zdefiniowano w **szczegóły Storefront** karta edytora. Typy usług `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, i `Workshop`.
-   **Preposition:** — wstawiony przez recenzenta
-   **Nazwa:** — to zdefiniowano w **oferują ustawienia** strony.

>[!Note]
>Pole nazwy jest ograniczona do 50 znaków. Nazwa, przesyłane może być konieczne można edytować przez recenzenta, aby zezwolić na czas trwania i oferują typ mają być dołączane do nazwy.

Poniższa lista zawiera kilka nazw dobrze nazwane oferty:

-   Podstawy dla profesjonalnych usług: 1-godzinnego widoków
-   Platforma usług w chmurze migracji: 1-godzinnego widoków
-   Usługa PowerApps i Microsoft Flow: 1-dniowych warsztatów
-   Usługi Azure Machine Learning Services: Weryfikacja koncepcji 3 tygodnie
-   Kostki, a następnie kliknij przycisk handlu detalicznego rozwiązania: 1-godzinnego widoków
-   Możesz używać własnych danych: Warsztaty 1 tydzień
-   Analiza w chmurze: 3-dniowych warsztatów
-   Usługa Power BI szkolenia: 3-dniowych warsztatów
-   Rozwiązanie do zarządzania sprzedaży: Implementacja 1 tydzień
-   Szybki Start CRM: 1-dniowych warsztatów
-   Dynamics 365 for Sales: 2-dniowych oceny

Po ukończeniu **oferują ustawienia** karty, możesz zapisać swoje zgłoszenie. Nazwa oferty będzie teraz wyświetlany powyżej edytora, a znajdziesz w oferuje wszystkie.

**Następne kroki**

Teraz możesz wprowadzić [Storefront szczegóły i określić, czy publikować w portalu Azure Marketplace lub w usłudze AppSource](./cpp-consulting-service-storefront-details.md).