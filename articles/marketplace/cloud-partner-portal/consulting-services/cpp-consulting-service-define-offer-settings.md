---
title: Zdefiniuj ustawienia oferty dla oferty usługi konsultingowej | Portal Azure Marketplace
description: Zdefiniuj ustawienia oferty w ofercie usługi konsultingowej platformy Azure lub Dynamics 365 w portal Cloud Partner portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 8a89c1c548469b568a34521b240683af285c2342
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485181"
---
# <a name="offer-settings-tab"></a>Karta ustawień oferty

Na ekranie **Nowa oferta** pierwszy krok to utworzenie tożsamości oferty. Tożsamość oferty składa się z trzech części: **Identyfikator oferty**, **identyfikator wydawcy**i **Nazwa**. Każda z tych części została omówiona w poniższych sekcjach.

![Tworzenie nowej oferty usługi konsultingowej — karta Ustawienia oferty](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Identyfikator oferty *

Ten identyfikator jest unikatową nazwą utworzoną podczas pierwszego przesyłania oferty. Musi zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia. **Identyfikator oferty** jest widoczny w adresie URL i ma wpływ na wyniki aparatu wyszukiwania. Przykładem jest *yourcompanyname_exampleservice*.

Jak pokazano w przykładzie, **Identyfikator oferty** jest dołączany do identyfikatora wydawcy w celu utworzenia unikatowego identyfikatora. Ten unikatowy identyfikator jest udostępniany jako trwałe łącze, które może być zapisane i jest indeksowane przez aparaty wyszukiwania.

>[!Note]
>Po zakończeniu oferty nie można zaktualizować jej identyfikatora.


### <a name="publisher-id"></a>IDENTYFIKATOR wydawcy *

Ten identyfikator jest powiązany z Twoim kontem. Po zalogowaniu się przy użyciu konta organizacyjnego **identyfikator wydawcy** zostanie wyświetlony w menu rozwijanym.


### <a name="name"></a>Nazwij

Ten ciąg jest wyświetlany jako nazwa oferty w witrynie AppSource lub w portalu Azure Marketplace. Pole **nazwy** jest ograniczone do 50 znaków. Osoba dokonująca przeglądu może potrzebować edycji tytułu w celu dołączenia typu czasu trwania i oferty do nazwy oferty.

Poniższy przykład pokazuje, jak jest składana nazwa oferty. 

![Utwórz nową ofertę usługi konsultingowej](media/cppsampleconsultingoffer.png)

Nazwa oferty składa się z czterech części:

-   **Czas trwania:** Zdefiniowane na karcie **szczegóły sklepu** w edytorze. Czas trwania może być wyrażony w godzinach, dniach lub tygodniach.
-   **Typ usługi:** Zdefiniowane na karcie **szczegóły sklepu** w edytorze. Typy usług to `Assessment`, `Briefing`, `Implementation`, `Proof of concept`i `Workshop`.
-   **Położenie w miejscu:** Wstawione przez recenzenta.
-   **Nazwa:** Zdefiniowane na stronie **Ustawienia oferty** .

>[!Note]
>Pole **nazwy** jest ograniczone do 50 znaków. Osoba dokonująca przeglądu może potrzebować edycji tytułu w celu dołączenia typu czasu trwania i oferty do nazwy oferty.

Poniższa lista zawiera kilka dobrze nazwanych ofert:

-   Podstawowe informacje dla usług profesjonalnych: 1-HR — krótkie
-   Platforma Migracja do chmury: 1-HR
-   PowerApps i Microsoft Flow: 1-dniowa warsztat
-   Azure Machine Learning: tydz ZK
-   Kostka i kliknięcie rozwiązania do sprzedaży detalicznej: 1-HR
-   Przenoszenie własnych danych: 1 — tydz warsztatów
-   Analiza w chmurze: 3-dniowa warsztat
-   Uczenie Power BIowe: 3-dniowe warsztaty
-   Rozwiązanie do zarządzania sprzedażą: implementacja 1-tygodniowa
-   Samouczek szybkiego startu programu CRM: 1-dniowa warsztat
-   Dynamics 365 for Sales: Ocena 2-dniowa

Po wypełnieniu karty **Ustawienia oferty** Zapisz przesłane dane. Nazwa oferty jest teraz widoczna nad edytorem i można ją znaleźć we **wszystkich ofertach**.

## <a name="next-steps"></a>Następne kroki

Teraz możesz wprowadzić [szczegóły witryny Marketplace i określić, czy publikować w witrynie Azure Portal, czy w witrynie AppSource](./cpp-consulting-service-storefront-details.md).
