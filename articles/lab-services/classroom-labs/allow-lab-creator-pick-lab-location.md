---
title: Zezwalaj twórcy laboratorium na wybieranie lokalizacji w usługach Azure Lab Services
description: W tym artykule opisano, jak administrator konta laboratorium może zezwolić twórcom laboratoriów na wybieranie lokalizacji dla swoich laboratoriów.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 52d5628698d1f945a7f672595ee7ce4739b6d13c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444370"
---
# <a name="allow-lab-creator-to-pick-location-for-the-lab-in-azure-lab-services"></a>Zezwalaj twórcy laboratorium na wybieranie lokalizacji laboratorium w usłudze Azure Lab Services
W usłudze Azure Lab Services właściciel konta laboratorium może zezwolić twórcom laboratoriów (nauczycielom) na wybranie lokalizacji dla laboratorium, które tworzą. Ta lokalizacja może się różnić od lokalizacji konta laboratorium. Lokalizacja to grupa regionów platformy Azure. Na przykład lokalizacja w Stanach Zjednoczonych to grupa regionów, takich jak wschodnie stany USA, zachodnie stany USA i tak dalej. 

Ty, jako właściciel konta laboratorium, możesz wybrać opcję **Zezwalaj twórcy laboratorium na wybranie lokalizacji laboratorium** podczas tworzenia konta laboratorium i po utworzeniu konta laboratorium (lub istniejącego konta laboratorium). 

## <a name="at-the-time-of-lab-account-creation"></a>W momencie tworzenia konta laboratoryjnego
Podczas tworzenia konta laboratorium, zobaczysz tę opcję na pierwszym ekranie **(Karta Podstawy).** 

![Włącz opcję w momencie tworzenia laboratorium](../media/allow-lab-creator-pick-lab-location/create-lab-account.png)

Ta opcja jest wyłączona, jeśli wybierzesz sieć wirtualną równorzędną dla konta laboratorium na karcie **Zaawansowane.**  

![Gdy sieć wirtualna elementu równorzędnego jest włączona](../media/allow-lab-creator-pick-lab-location/peer-virtual-network.png)


## <a name="after-the-lab-account-is-created"></a>Po utworzeniu konta laboratorium
Po utworzeniu konta laboratorium można włączyć lub wyłączyć tę opcję, wykonując następujące kroki: 

1. Na stronie **Konto laboratorium** wybierz pozycję **Ustawienia laboratorium** w menu po lewej stronie.
2. Wybierz opcję **Zezwalaj twórcy laboratorium na wybranie lokalizacji laboratorium,** jeśli chcesz zezwolić twórcy laboratorium na wybranie lokalizacji laboratorium. Jeśli jest wyłączona, laboratoria są automatycznie tworzone w tej samej lokalizacji, w której istnieje konto laboratorium. 
    
    To pole jest wyłączone po wybraniu sieci wirtualnej dla pola **Sieć wirtualna równorzędna.** To dlatego, że laboratoria na koncie laboratorium musi znajdować się w tym samym regionie co konto laboratorium dla nich, aby uzyskać dostęp do zasobów w sieci wirtualnej równorzędnej. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi. 

    ![Ustawienia laboratorium](../media/allow-lab-creator-pick-lab-location/lab-settings.png)

## <a name="no-virtual-network-and-location-selection-isnt-allowed"></a>Wybór sieci wirtualnej i lokalizacji nie jest dozwolony
W tym scenariuszu nie włączono **zezwalaj twórcy laboratorium na wybranie** opcji lokalizacji laboratorium. 

![Brak lokalizacji laboratorium](../media/allow-lab-creator-pick-lab-location/lab-no-location.png)

Następnie twórcy laboratorium (edukatorzy) nie widzą opcji wyboru lokalizacji dla laboratorium. Zobaczą cenę za godzinę za każdą dostępną dla nich opcję rozmiaru. Podczas tworzenia laboratorium zostanie on utworzony w regionie platformy Azure, który znajduje się w tej samej lokalizacji co region platformy Azure, w którym znajduje się ich konto w laboratorium. Na przykład, jeśli konto laboratorium znajduje się w **zachodnie stany USA,** laboratorium może zostać utworzone w **południowo-środkowych stanach USA,** ale nie zostanie utworzone w **Kanadzie Wschodniej.** Nie gwarantujemy niczego o regionie, który wybieramy poza tym, że jest w lokalizacji. Jeśli rozmiar jest obecnie ograniczony, twórca laboratorium zobaczy pole wyboru, w którym będzie mógł zobaczyć rozmiary, które zwykle obsługujemy, ale są obecnie niedostępne. 

## <a name="in-virtual-network-and-location-selection-isnt-allowed"></a>W sieci wirtualnej i wybór lokalizacji jest niedozwolony
W tym scenariuszu **zezwalaj twórcy laboratorium na wybranie lokalizacji laboratorium** jest wyłączona, ponieważ wybrano równorzędną sieć wirtualną dla konta laboratorium. Następnie twórcy laboratorium zobaczą ten sam ekran, co w poprzedniej opcji. Ponieważ wszystkie maszyny wirtualne muszą znajdować się w tym samym regionie platformy Azure co sieć wirtualna, laboratorium zostanie utworzone w tym samym regionie platformy Azure, w jakim znajduje się sieć wirtualna. Jeśli ten region jest ograniczony dla rozmiaru, rozmiar będzie wyświetlany jako niedostępny. 

## <a name="location-selection-is-enabled"></a>Wybór lokalizacji jest włączony
Po wybraniu opcji **Zezwalaj twórcy laboratorium na wybranie lokalizacji laboratorium,** twórcy laboratorium (edukatorzy) zobaczą opcję wyboru lokalizacji podczas tworzenia laboratorium. 

![Wybieranie lokalizacji laboratorium](../media/allow-lab-creator-pick-lab-location/location-selection.png)

Twórcy laboratorium widzą zakres cen dla wszystkich lokalizacji, w których występuje rozmiar, i mogą wybrać lokalizację. Laboratorium zostanie utworzone w dowolnym regionie platformy Azure, który mapuje do tej lokalizacji.

Jeśli lokalizacja jest ograniczona, domyślnie nie jest wyświetlana na liście. Rozwiń listę rozwijaną i wybierz pozycję **Pokaż niedostępne lokalizacje dla tego rozmiaru**. 

![Pokaż niedostępne lokalizacje](../media/allow-lab-creator-pick-lab-location/show-unavailable-locations.png)

## <a name="cost"></a>Koszty
Wcześniej cennik był oparty na rozmiarze maszyny Wirtualnej, który można wybrać dla laboratorium. Teraz cena jest oparta na kombinacji systemu operacyjnego (OS), rozmiar i lokalizacja. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Łączenie sieci laboratorium za pomocą sieci wirtualnej równorzędnej](how-to-connect-peer-virtual-network.md)
- [Dołączanie udostępnionej galerii obrazów do laboratorium](how-to-attach-detach-shared-image-gallery.md)
- [Dodawanie użytkownika jako właściciela laboratorium](how-to-add-user-lab-owner.md)
- [Wyświetlanie ustawień zapory dla laboratorium](how-to-configure-firewall-settings.md)
- [Konfigurowanie innych ustawień dla laboratorium](how-to-configure-lab-accounts.md)