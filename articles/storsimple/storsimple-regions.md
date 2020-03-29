---
title: Dostępność regionu StorSimple
description: W tym artykule wyjaśniono regiony platformy Azure, w których są dostępne różne modele urządzeń StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 11/16/2017
ms.author: alkohli
ms.openlocfilehash: a2f04d6faa7770eef7768437ae0e624b76713f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76275106"
---
# <a name="available-regions-for-your-storsimple"></a>Dostępne regiony dla Twojego StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Centra danych platformy Azure działają w wielu regionach geograficznych na całym świecie, aby sprostać wymaganiom klientów dotyczącym wydajności, wymagań i preferencji dotyczących lokalizacji danych. Geografia platformy Azure jest zdefiniowanym obszarem świata, który zawiera co najmniej jeden region platformy Azure. Region platformy Azure to obszar w obrębie lokalizacji geograficznej, zawierający co najmniej jedno centrum danych.

Wybór regionu platformy Azure jest bardzo ważne, a na wybór regionu mają wpływ takie czynniki, jak rezydencji danych i suwerenności, dostępność usługi, wydajność, koszt i nadmiarowość. Aby uzyskać więcej informacji na temat wybierania regionu, przejdź do [regionu platformy Azure, który jest dla mnie odpowiedni?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

W przypadku rozwiązania StorSimple wybór regionu jest określany przez następujące czynniki:

- Regiony, w których dostępna jest usługa StorSimple Device Manager.
- Kraje/regiony, w których jest dostępna urządzenie fizyczne, chmurowe lub wirtualne StorSimple.
- Regiony, w których konta magazynu, które przechowują dane StorSimple powinny znajdować się w celu uzyskania optymalnej wydajności.

W tym samouczku opisano dostępność regionu dla usługi StorSimple Device Manager, urządzeń fizycznych lokalnych i urządzeń w chmurze. Informacje zawarte w tym artykule mają zastosowanie do urządzeń z serii StorSimple 8000 i 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostępność regionu dla usługi StorSimple Device Manager

Usługa StorSimple Device Manager jest obecnie obsługiwana w 12 regionach publicznych i 2 regionach platformy Azure dla instytucji rządowych.

Region lub lokalizacja można zdefiniować podczas pierwszego tworzenia usługi StorSimple Device Manager. Ogólnie rzecz biorąc, wybierana jest lokalizacja najbliższa regionowi geograficznemu, w którym urządzenie jest wdrażane. Ale urządzenie i usługa można również wdrożyć w różnych lokalizacjach.

Oto lista regionów, w których usługa StorSimple Device Manager jest dostępna dla chmury publicznej platformy Azure i można ją wdrożyć.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

W chmurze dla instytucji rządowych platformy Azure usługa StorSimple Device Manager jest dostępna w centrach danych US Gov Iowa i US Gov Virginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostępność regionu dla danych przechowywanych w StorSimple

StorSimple dane są fizycznie przechowywane na kontach magazynu platformy Azure i te konta są dostępne we wszystkich regionach platformy Azure. Podczas tworzenia konta magazynu platformy Azure wybierana jest podstawowa lokalizacja konta magazynu, która określa region, w którym znajdują się dane.

Po pierwszym utworzeniu usługi StorSimple Device Manager i skojarzeniu z nią konta magazynu usługa StorSimple Device Manager i magazyn platformy Azure mogą znajdować się w dwóch oddzielnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie.

Ogólnie rzecz biorąc wybierz najbliższy region usługi dla konta magazynu. Jednak najbliższy region platformy Microsoft Azure może nie być regionem o najniższym opóźnieniu. Jest to opóźnienie, które dyktuje wydajność usługi sieciowej, a tym samym wydajność rozwiązania. Jeśli więc wybierasz konto magazynu w innym regionie, ważne jest, aby wiedzieć, jakie są opóźnienia między usługą a regionem skojarzonym z kontem magazynu.

Jeśli używasz StorSimple Cloud Appliance, zaleca się, że usługa i skojarzone konto magazynu znajdują się w tym samym regionie. Konta magazynu w innym regionie może spowodować niską wydajność.

## <a name="availability-of-storsimple-device"></a>Dostępność urządzenia StorSimple

W zależności od modelu urządzenia StorSimple mogą być dostępne w różnych lokalizacjach geograficznych lub krajach/regionach.

### <a name="storsimple-physical-device-models-81008600"></a>StorSimple urządzenie fizyczne (modele 8100/8600)

Jeśli używasz urządzenia fizycznego StorSimple 8100 lub 8600, urządzenie jest dostępne w następujących krajach/regionach.

| #  | Kraj/region        | #  | Kraj/region     | #  | Kraj/region      | #  | Kraj/region             |
|----|-----------------------|----|--------------------|----|---------------------|----|----------------------------|
| 1  | Australia             | 16 | SRA Hongkong      | 31 | Nowa Zelandia         | 46 | Republika Południowej Afryki               |
| 2  | Austria               | 17 | Węgry            | 32 | Nigeria             | 47 | Korea Południowa                |
| 3  | Bahrajn               | 18 | Islandia            | 33 | Norwegia              | 48 | Hiszpania                      |
| 4  | Belgia               | 19 | Indie              | 34 | Peru                | 49 | Sri Lanka                  |
| 5  | Brazylia                | 20 | Indonezja          | 35 | Filipiny         | 50 | Szwecja                     |
| 6  | Kanada                | 21 | Irlandia            | 36 | Polska              | 51 | Szwajcaria                |
| 7  | Chile                 | 22 | Izrael             | 37 | Portugalia            | 52 | Tajwan                     |
| 8  | Kolumbia              | 23 | Włochy              | 38 | Portoryko         | 53 | Tajlandia                   |
| 9  | Czechy        | 24 | Japonia              | 39 | Katar               | 54 | Turcja                     |
| 10 | Dania               | 25 | Kenia              | 40 | Rumunia             | 55 | Ukraina                    |
| 11 | Egipt                 | 26 | Kuwejt             | 41 | Rosja              | 56 | Zjednoczone Emiraty Arabskie       |
| 12 | Finlandia               | 27 | SRA Makau          | 42 | Arabia Saudyjska        | 57 | Wielka Brytania             |
| 13 | Francja                | 28 | Malezja           | 43 | Singapur           | 58 | Stany Zjednoczone              |
| 14 | Niemcy               | 29 | Meksyk             | 44 | Słowacja            | 59 | Wietnam                    |
| 15 | Grecja                | 30 | Holandia        | 45 | Słowenia            | 60 | Chorwacja                    |

Ta lista zmienia się wraz z dodaniem kolejnych krajów/regionów. Aby uzyskać najbardziej aktualną listę regionów geograficznych, przejdź do dodatku Postanowienia dotyczące tablicy magazynowania w [warunkach produktu](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Firma Microsoft może wysyłać sprzęt fizyczny i dostarczać sprzętowe części zamienne do storsimple do obszarów geograficznych na poprzedniej liście.

> [!IMPORTANT]
> Nie należy umieszczać urządzenia fizycznego StorSimple w regionie, w którym StorSimple nie jest obsługiwany. Firma Microsoft nie będzie mogła wysyłać żadnych części zamiennych do krajów/regionów, w których usługa StorSimple nie jest obsługiwana.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Urządzenie w chmurze StorSimple (modele 8010/8020)

Jeśli używasz StorSimple Cloud Appliance 8010 lub 8020, urządzenie jest obsługiwane i dostępne we wszystkich regionach, w których podstawowa maszyna wirtualna jest obsługiwana. 8010 używa maszyny wirtualnej _Standard_A3,_ która jest obsługiwana we wszystkich regionach platformy Azure.

8020 używa magazynu w jakości premium i _Standard_DS3_ maszyny Wirtualnej do tworzenia urządzenia w chmurze. 8020 jest obsługiwany w regionach platformy Azure, które obsługują usługi Premium Storage i _Standard_DS3_ maszyn wirtualnych platformy Azure. Przy użyciu [tej listy](https://azure.microsoft.com/regions/services/) sprawdź, czy w Twoim regionie jest dostępna zarówno opcja **Maszyny wirtualne > Seria DS**, jak i opcja **Magazyn > Magazyn na dysku**.

### <a name="storsimple-virtual-array-model-1200"></a>Tablica wirtualna StorSimple (model 1200)

Jeśli używasz 1200 serii StorSimple Virtual Array, następnie obraz dysku wirtualnego jest obsługiwany we wszystkich regionach platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [cenach dla różnych modeli StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Dowiedz się więcej o [zarządzaniu kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej o tym, jak [administrować urządzeniem StorSimple za pomocą usługi StorSimple Device Manager](storsimple-8000-manager-service-administration.md).
