---
title: Definiowanie ustawień oferty dla oferty usług konsultingowych | Azure Marketplace
description: Zdefiniuj ustawienia oferty w ofercie usługi konsultingowej platformy Azure lub Dynamics 365 w portalu cloud partner dla portalu Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 7b2c9d136a32e54ce5b5db8f6ce9ba8cd63a4f3c
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745632"
---
# <a name="offer-settings-tab"></a>Karta ustawień oferty

>[!Important]
>Od 30 marca 2020 r. rozpoczniemy przenoszenie zarządzania ofertami usług konsultingowych do Centrum partnerskiego. Po migracji utworzysz oferty i zarządzasz nimi w Centrum partnerów. Postępuj zgodnie z instrukcjami w [przeglądzie tworzenia usług konsultingowych,](https://aka.ms/AzureCreateConsultingService) aby zarządzać zmigrowanymi ofertami.

Na ekranie **Nowa oferta** pierwszym krokiem jest utworzenie tożsamości oferty. Tożsamość oferty składa się z trzech części: **Identyfikator oferty,** **Identyfikator wydawcy**i **Nazwa**. Każda z tych części jest omówiona w poniższych sekcjach.

![Tworzenie nowej oferty usług konsultingowych - Karta Ustawienia oferty](media/consultingoffer-settings-tab.png)


### <a name="offer-id"></a>Identyfikator oferty*

Ten identyfikator jest unikatową nazwą utworzyną przy pierwszym przesłaniu oferty. Musi składać się tylko z małych liter alfanumeryczne znaki, kreski lub podkreślenia. Identyfikator **oferty** jest widoczny w adresie URL i wpływa na wyniki wyszukiwania. Przykładem jest *yourcompanyname_exampleservice*.

Jak pokazano w przykładzie, **identyfikator oferty** jest dołączany do identyfikatora wydawcy, aby utworzyć unikatowy identyfikator. Ten unikatowy identyfikator jest narażony jako stały link, który może być zarezerwowany i jest indeksowany przez wyszukiwarki.

>[!Note]
>Po zakończeniu oferty nie można zaktualizować jej identyfikatora.


### <a name="publisher-id"></a>Identyfikator wydawcy*

Ten identyfikator jest powiązany z Twoim kontem. Po zalogowaniu się za pomocą konta instytucji identyfikator **wydawcy** pojawi się w menu rozwijanym.


### <a name="name"></a>Imię i nazwisko*

Ten ciąg jest wyświetlany jako nazwa oferty w usłudze AppSource lub w portalu Azure Marketplace. Pole **Nazwa** jest ograniczone do 50 znaków. Recenzent może być konieczne edytowanie tytułu, aby dołączyć czas trwania i typ oferty do nazwy oferty.

W poniższym przykładzie pokazano, jak nazwa oferty jest zmontowana. 

![Tworzenie nowej oferty usług konsultingowych](media/cppsampleconsultingoffer.png)

Nazwa oferty składa się z czterech części:

-   **Czas trwania:** Zdefiniowano na karcie **Szczegóły sklepu** edytora. Czas trwania może być wyrażony w godzinach, dniach lub tygodniach.
-   **Rodzaj usługi:** Zdefiniowano na karcie **Szczegóły sklepu** edytora. Rodzaje usług `Assessment`to `Briefing` `Implementation`, `Proof of concept`, `Workshop`, i .
-   **Przyimek:** Wstawiony przez recenzenta.
-   **Nazwa:** Zdefiniowane na stronie **Ustawienia oferty.**

>[!Note]
>Pole **Nazwa** jest ograniczone do 50 znaków. Recenzent może być konieczne edytowanie tytułu, aby dołączyć czas trwania i typ oferty do nazwy oferty.

Poniższa lista zawiera kilka nazw ofert o nazwie:

-   Podstawowe informacje o profesjonalnych usługach: 1-Hr Briefing
-   Platforma migracji do chmury: 1-hr Briefing
-   PowerApps i Microsoft Flow: jednodniowe warsztaty
-   Uczenie maszynowe platformy Azure: 3-Wk PoC
-   Cegła i kliknij detalicznej rozwiązanie: 1-Hr Briefing
-   Przynieś własne dane: Warsztat 1-Wk
-   Analityka w chmurze: 3-dniowe warsztaty
-   Szkolenie usługi Power BI: 3-dniowe warsztaty
-   Rozwiązanie do zarządzania sprzedażą: wdrożenie 1-tygodniowe
-   Szybki start CRM: 1-dniowe warsztaty
-   Dynamics 365 dla sprzedaży: ocena 2-dniowa

Po wypełnieniu karty **Ustawienia oferty** zapisz zgłoszenie. Nazwa oferty pojawia się teraz nad edytorem i można ją znaleźć we **wszystkich ofertach.**

## <a name="next-steps"></a>Następne kroki

Teraz możesz wprowadzić [szczegóły witryny sklepu i określić, czy chcesz publikować w portalu Azure Marketplace, czy w usłudze AppSource.](./cpp-consulting-service-storefront-details.md)
