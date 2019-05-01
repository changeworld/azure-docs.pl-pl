---
title: Dostępność w poszczególnych regionach StorSimple | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: e290feb278a1cddf1cfecfcb66458d8290ec122a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943591"
---
# <a name="available-regions-for-your-storsimple"></a>Dostępne regiony dla usługi StorSimple

## <a name="overview"></a>Omówienie

Centra danych platformy Azure działają w wielu regionach na świecie, w celu spełnienia wymagań wydajności, wymagania i preferencje klientów dotyczące lokalizacji danych klienta. Lokalizacja geograficzna platformy Azure jest zdefiniowany obszar świata, który zawiera co najmniej jednego regionu platformy Azure. Region platformy Azure jest obszar w lokalizacji geograficznej, zawierający co najmniej jedno centrum danych.

Wybór z regionu platformy Azure jest bardzo ważne, i wybranego regionu jest zależne od czynników, takich jak rezydencja i niezależność, dostępności usługi, wydajności, koszt i nadmiarowość. Aby uzyskać więcej informacji na temat sposobu wybierz region, przejdź do [region świadczenia usługi Azure który jest odpowiedni dla mnie?](https://azure.microsoft.com/overview/datacenters/how-to-choose/)

Dla rozwiązania StorSimple wybranego regionu specjalnie zależy od następujących czynników:

- Regiony, w których usługa Menedżer urządzeń StorSimple jest dostępna.
- Kraje/regiony, których fizycznych StorSimple, w chmurze lub na urządzenie wirtualne jest dostępna.
- Regiony, do których kont magazynu, w których są przechowywane dane usługi StorSimple powinien być zlokalizowany w celu uzyskania optymalnej wydajności.

W tym samouczku opisano dostępność regionów dla usługi Menedżer urządzeń StorSimple, fizycznego w środowisku lokalnym i urządzenia w chmurze. Informacje zawarte w tym artykule dotyczy StorSimple 8000 i urządzeń z serii 1200.

## <a name="region-availability-for-storsimple-device-manager-service"></a>Dostępność regionów dla usługi Menedżer urządzeń StorSimple

Usługa Menedżer urządzeń StorSimple jest obecnie obsługiwane w 2 regionach platformy Azure dla instytucji rządowych i 12 regionach publicznych.

Podczas tworzenia usługi Menedżer urządzeń StorSimple, określić region lub lokalizacji. Ogólnie rzecz biorąc aby wybrać lokalizację najbliżej regionu geograficznego, w której wdrożono urządzenie. Jednak urządzenia i usługi można także wdrożyć w różnych lokalizacjach.

Poniżej przedstawiono listę regionów, gdzie jest dostępny dla chmury publicznej platformy Azure i którą można wdrożyć w usłudze Menedżer urządzeń StorSimple.

![storsimple-device-manager-service-regions](./media/storsimple-region/storsimple-device-manager-service-regions.png)

Chmury Azure Government usługi Menedżer urządzeń StorSimple jest dostępna w centrach danych Administracja USA — Iowa i Administracja USA — Wirginia.

## <a name="region-availability-for-data-stored-in-storsimple"></a>Dostępność regionów dla danych przechowywanych w urządzeniu StorSimple

Danych w usłudze StorSimple fizycznie są przechowywane na kontach usługi Azure storage, a te konta są dostępne we wszystkich regionach platformy Azure. Podczas tworzenia konta usługi Azure storage lokalizacji głównej konta magazynu można wybrać i określa region, w którym znajdują się dane.

Gdy najpierw utworzyć usługę Menedżer urządzeń StorSimple i skojarzyć konto magazynu z nim swoje usługi Menedżer urządzeń StorSimple i Azure storage może być w dwóch różnych lokalizacjach. W takim przypadku należy utworzyć konta usługi Menedżer urządzeń StorSimple i usługi Azure Storage oddzielnie.

Ogólnie rzecz biorąc wybierz znajduje się najbliższy region usługi dla konta magazynu. Jednak znajduje się najbliższy region Microsoft Azure mogą być region zapewnia najniższe opóźnienie. To opóźnienie, który decyduje o wydajność usługi sieci i dlatego wydajność rozwiązania. Więc jeśli decyduje się na koncie magazynu w innym regionie, jest ważne, aby dowiedzieć się, co się między usługą i regionu skojarzonego z kontem magazynu.

Jeśli używasz urządzenia StorSimple w chmurze, następnie zaleca się że usługę i skojarzonego konta magazynu znajdują się w tym samym regionie. Konta magazynu w innym regionie mogą spowodować obniżenie wydajności.

## <a name="availability-of-storsimple-device"></a>Dostępność urządzenia StorSimple

W zależności od modelu urządzenia StorSimple może być dostępna w krajach/regionach lub w różnych lokalizacjach geograficznych.

### <a name="storsimple-physical-device-models-81008600"></a>Urządzenie fizyczne StorSimple (modeli 8100/8600)

Jeśli używasz StorSimple 8100 lub 8600 urządzenie fizyczne, urządzenie jest dostępna w następujących krajach/regionach.

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
| 12 | Finlandia               | 27 | SRA Makau          | 42 | Arabia Saudyjska        | 57 | Zjednoczone Królestwo             |
| 13 | Francja                | 28 | Malezja           | 43 | Singapur           | 58 | Stany Zjednoczone              |
| 14 | Niemcy               | 29 | Meksyk             | 44 | Słowacja            | 59 | Wietnam                    |
| 15 | Grecja                | 30 | Holandia        | 45 | Słowenia            | 60 | Chorwacja                    |

Ta lista zmienia się po dodaniu więcej krajów/regionów. Aby uzyskać najbardziej aktualną listę obszarów geograficznych, przejdź do dodatku warunki tablicy magazynu w [postanowieniach dotyczących produktu](https://www.microsoft.com/en-us/licensing/product-licensing/products).

Firmy Microsoft można wysłać sprzętu fizycznego i udostępniać wymiana części zamiennych sprzętu dla usługi StorSimple do różnych lokalizacji geograficznych na powyższej liście.

> [!IMPORTANT]
> Nie należy umieszczać urządzenia fizycznego StorSimple w regionie, w których StorSimple nie jest obsługiwane. Microsoft nie będzie dostarczanie dowolnej części zamiennych do krajów/regionów, których StorSimple nie jest obsługiwane.

### <a name="storsimple-cloud-appliance-models-80108020"></a>Urządzenia StorSimple w chmurze (modele 8010/8020)

Jeśli używasz urządzenie StorSimple Cloud Appliance 8010 lub 8020, następnie urządzenie jest obsługiwane i dostępne we wszystkich regionach, w których podstawowej maszyny Wirtualnej jest obsługiwana. Zastosowań 8010 _Standard_A3_ maszyny Wirtualnej, która jest obsługiwana we wszystkich regionach platformy Azure.

8020 korzysta z usługi premium storage i _Standard_DS3_ maszynę Wirtualną, aby utworzyć urządzenie w chmurze. 8020 jest obsługiwana w regionach świadczenia usługi Azure obsługujące usługę Premium Storage i _Standard_DS3_ maszyn wirtualnych platformy Azure. Przy użyciu [tej listy](https://azure.microsoft.com/regions/services/) sprawdź, czy w Twoim regionie jest dostępna zarówno opcja **Maszyny wirtualne > Seria DS**, jak i opcja **Magazyn > Magazyn na dysku**.

### <a name="storsimple-virtual-array-model-1200"></a>Rozwiązania StorSimple Virtual Array (Model 1200)

Jeśli używasz serii 1200 rozwiązania StorSimple Virtual Array, obrazu dysku wirtualnego jest obsługiwana we wszystkich regionach platformy Azure.

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [ceny w różnych modelach StorSimple](https://azure.microsoft.com/pricing/calculator/#storsimple2).
* Dowiedz się więcej o [Zarządzanie kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej na temat [korzystać z usługi Menedżer urządzeń StorSimple do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
