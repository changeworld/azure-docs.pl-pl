---
title: Dostępność w danym regionie StorSimple | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono regiony platformy Azure, w których są dostępne różne modele urządzenia StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: d47109d541a3df93d9234e27e53d1538f6bc4c6e
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2017
ms.locfileid: "24879062"
---
# <a name="available-regions-for-your-storsimple"></a>Dostępne regiony dla Twojego urządzenia StorSimple

## <a name="overview"></a>Przegląd

Centrach danych platformy Azure działają w wielu lokalizacji geograficznych na całym świecie do spełnienia wymagań wydajności, wymagań i preferencji dotyczących danych lokalizacji klienta. Geograficzne Azure jest zdefiniowany obszar świata, który zawiera co najmniej jeden Region platformy Azure. Region platformy Azure jest obszar wewnątrz obiektu geograficznego, zawierający co najmniej jeden centrów danych.

Bardzo ważne jest wybranie region platformy Azure i wyboru region jest zależne od czynników, takich jak siedziby danych i suwerenności, dostępność usługi wydajności, koszt i nadmiarowość. Aby uzyskać więcej informacji na temat sposobu wybierz region, przejdź do [regionu Azure którego jest dla mnie odpowiednia?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

W rozwiązaniu StorSimple wybór obszaru specjalnie zależy od następujących czynników:

- Regionach, gdzie usługi Menedżer StorSimple urządzenia jest dostępny.
- Krajów, w którym fizyczne StorSimple, chmurowego lub urządzenie wirtualne jest dostępna.
- Regionach, gdzie kont magazynu, które przechowują dane StorSimple powinien być zlokalizowany w celu uzyskania optymalnej wydajności.

Ten samouczek przedstawia dostępność region usługi Menedżer StorSimple urządzenia, fizyczny lokalne i urządzenia chmury. Informacje zawarte w tym artykule ma zastosowanie do StorSimple 8000 i urządzeń z serii 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostępność usługi Menedżer StorSimple urządzenia w danym regionie

Usługę Menedżer StorSimple urządzenie jest obecnie obsługiwane w 12 regionów publicznego i 2 regiony platformy Azure dla instytucji rządowych.

Po utworzeniu usługi Menedżer StorSimple urządzenia należy zdefiniować w regionie lub lokalizacji. Ogólnie rzecz biorąc aby wybrać lokalizację najbliżej regionu geograficznego, w którym jest wdrażany urządzenia. Jednak urządzenia i usługi można także wdrożyć w różnych lokalizacjach.

Poniżej przedstawiono listę regionów, w którym usługi Menedżer StorSimple urządzenia jest dostępna dla platformy Azure chmury publicznej i może zostać wdrożony.

![storsimple — urządzenia manager usługi regionów](./media/storsimple-region/storsimple-device-manager-service-regions.png)

W chmurze Azure dla instytucji rządowych usługi Menedżer StorSimple urządzenia jest dostępna w centrach danych Iowa nam wersji dla instytucji rządowych i Virginia nam wersji dla instytucji rządowych.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostępność danych przechowywanych w StorSimple w danym regionie

Dane StorSimple fizycznie są przechowywane na kontach magazynu Azure, a konta te są dostępne we wszystkich regionach platformy Azure. Podczas tworzenia konta magazynu platformy Azure jest wybierany lokalizacji głównej konta magazynu i określa regionu, w którym znajdują się dane.

Gdy najpierw Utwórz usługę Menedżer StorSimple urządzenia i skojarzyć z nią konto magazynu, sieci usługi Menedżera urządzeń StorSimple i magazynu Azure może być w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie.

Ogólnie rzecz biorąc wybierz region najbliższy do usługi dla konta magazynu. Najbliższy region Microsoft Azure nie może być jednak faktycznie być region o najniższym opóźnieniu. Jest opóźnienia, które nakazują wydajność usługi sieciowej i dlatego wydajność rozwiązania. Dlatego jeśli wybierzesz konto magazynu w innym regionie jest musisz znać, co to są opóźnienia między usługą i regionu skojarzonego z kontem magazynu.

Jeśli korzystasz z urządzenia chmury StorSimple, następnie zaleca się czy usługa i skojarzonego konta magazynu są w tym samym regionie. Konta magazynu w innym regionie może obniżyć wydajność.

## <a name="availability-of-storsimple-device"></a>Dostępność urządzenia StorSimple

W zależności od modelu urządzenia StorSimple może być dostępne w różnych lokalizacjach geograficznych i krajów.

### <a name="storsimple-physical-device-models-81008600"></a>Urządzenia fizycznego StorSimple (modele 8100/8600)

Jeśli przy użyciu StorSimple 8100 lub 8600 urządzenia fizycznego, urządzenie jest dostępne w następujących krajach.

| #  | Kraj        | #  | Kraj     | #  | Kraj      | #  | Kraj              |
|----|----------------|----|-------------|----|--------------|----|----------------------|
| 1  | Australia      | 16 | Hongkong   | 31 | Nowa Zelandia  | 46 | Republika Południowej Afryki         |
| 2  | Austria        | 17 | Węgry     | 32 | Nigeria      | 47 | Korea Południowa          |
| 3  | Bahrajn        | 18 | Islandia     | 33 | Norwegia       | 48 | Hiszpania                |
| 4  | Belgia        | 19 | Indie       | 34 | Peru         | 49 | Sri Lanka            |
| 5  | Brazylia         | 20 | Indonezja   | 35 | Filipiny  | 50 | Szwecja               |
| 6  | Kanada         | 21 | Irlandia     | 36 | Polska       | 51 | Szwajcaria          |
| 7  | Chile          | 22 | Izrael      | 37 | Portugalia     | 52 | Tajwan               |
| 8  | Kolumbia       | 23 | Włochy       | 38 | Portoryko  | 53 | Tajlandia             |
| 9  | Czechy | 24 | Japonia       | 39 | Katar        | 54 | Turcja               |
| 10 | Dania        | 25 | Kenia       | 40 | Rumunia      | 55 | Ukraina              |
| 11 | Egipt          | 26 | Kuwejt      | 41 | Rosja       | 56 | Zjednoczone Emiraty Arabskie |
| 12 | Finlandia        | 27 | Makau       | 42 | Arabia Saudyjska | 57 | Zjednoczone Królestwo       |
| 13 | Francja         | 28 | Malezja    | 43 | Singapur    | 58 | Stany Zjednoczone Ameryki        |
| 14 | Niemcy        | 29 | Meksyk      | 44 | Słowacja     | 59 | Wietnam              |
| 15 | Grecja         | 30 | Holandia | 45 | Słowenia     | 60 | Chorwacja              |

Ta lista Zmienia miarę dodawania więcej krajów. Najbardziej aktualną listę lokalizacji geograficznych, przejdź do dodatku warunki tablicy magazynu w [produktu](https://www.microsoft.com/en-us/Licensing/product-licensing).

Microsoft może wysłać sprzętu fizycznego i podaj wymiany zamiennych sprzętu dla urządzenia StorSimple do lokalizacji geograficznych z powyższej listy.

> [!IMPORTANT]
> Nie umieszczaj urządzenia fizycznego StorSimple w regionie, w których StorSimple nie jest obsługiwana. Microsoft nie będzie mógł wysłać wszystkie części do krajów, w których StorSimple nie jest obsługiwana.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Urządzenia StorSimple chmury (modele 8010/8020)

Jeśli używać StorSimple chmury urządzenia 8010 i 8020, następnie urządzenia jest obsługiwana lub dostępna we wszystkich regionach, gdzie podstawowej maszyny Wirtualnej jest obsługiwany. Używa 8010 _Standard_A3_ maszyny Wirtualnej, która jest obsługiwana we wszystkich regionach platformy Azure.

Magazyn w warstwie premium używa 8020 i _Standard_DS3_ maszyny Wirtualnej, aby utworzyć urządzenia chmury. 8020 jest obsługiwany w regionach platformy Azure, które obsługują magazyn w warstwie Premium i _Standard_DS3_ maszynach wirtualnych platformy Azure. Przy użyciu [tej listy](https://azure.microsoft.com/regions/services/) sprawdź, czy w Twoim regionie jest dostępna zarówno opcja **Maszyny wirtualne > Seria DS**, jak i opcja **Magazyn > Magazyn na dysku**.

### <a name="storsimple-virtual-array-model-1200"></a>Tablica wirtualnego StorSimple (Model 1200)

Jeśli przy użyciu serii 1200 tablicy wirtualnego StorSimple, obrazu wirtualnego dysku twardego jest obsługiwane we wszystkich regionach platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [ceny różne modele StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Dowiedz się więcej o [Zarządzanie kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej o sposobie [zarządzać urządzenia StorSimple przy użyciu usługi Menedżer StorSimple urządzenia](storsimple-8000-manager-service-administration.md).
