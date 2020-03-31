---
title: Wymień obudowę na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób usuwania i wymiany obudowy obudowy podstawowej lub obudowy EBOD storsimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 7dfc39f4d08c8a49d1564a0a5bd7e3ef4156e3fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61312449"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Wymień obudowę urządzenia StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i wymienić obudowę w urządzeniu z serii StorSimple 8000. Model StorSimple 8100 jest pojedynczym urządzeniem obudowy (jedna obudowa), podczas gdy 8600 jest urządzeniem z podwójną obudową (dwie obudowy). W przypadku modelu 8600 w urządzeniu mogą wystąpić dwie obudowy: obudowa obudowy podstawowej lub obudowa obudowy EBOD.

W obu przypadkach obudowa zastępcza, która jest dostarczana przez firmę Microsoft, jest pusta. Brak modułów zasilania i chłodzenia (PCM), modułów kontrolerów, dysków PÓŁPRZEWODNIKOWYCH (SSD), dysków twardych (HDD) lub modułów EBOD.

> [!IMPORTANT]
> Przed wyjęciem i wymianą obudowy zapoznaj się z informacjami o bezpieczeństwie w [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Zdejmij podwozie
Wykonaj następujące czynności, aby usunąć obudowę urządzenia StorSimple.

#### <a name="to-remove-a-chassis"></a>Aby zdjąć podwozie
1. Upewnij się, że urządzenie StorSimple jest wyłączone i odłączone od wszystkich źródeł zasilania.
2. W razie potrzeby usuń wszystkie kable sieciowe i SAS.
3. Wyjmij urządzenie ze stojaka.
4. Wyjmij każdy z dysków i zanotuj gniazda, z których są usuwane. Aby uzyskać więcej informacji, zobacz [Usuwanie dysku .](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive)
5. W obudowie EBOD (jeśli jest to obudowa, która uległa awarii), usuń moduły kontrolera EBOD. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Na obudowie podstawowej (jeśli jest to obudowa, która uległa awarii), usuń kontrolery i zanotuj gniazda, z których są usuwane. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Zamontować obudowę
Wykonaj następujące czynności, aby zainstalować obudowę na urządzeniu StorSimple.

#### <a name="to-install-a-chassis"></a>Aby zainstalować obudowę
1. Zamontuj obudowę w stelażu. Aby uzyskać więcej informacji, zobacz [Urządzenie StorSimple 8100 montujące w stojaku w stojaku](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) lub [Urządzenie StorSimple 8600](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Po zamontowaniu obudowy w stelażu należy zainstalować moduły kontrolera w tych samych pozycjach, w których były wcześniej zainstalowane.
3. Instaluj dyski w tych samych pozycjach i gniazdach, w których były wcześniej zainstalowane.
   
   > [!NOTE]
   > Zaleca się najpierw zainstalowanie dysków SSD w gniazdach, a następnie zainstalowanie dysków twardych.
  
4. Po zamontowaniu urządzenia w stelażu i zainstalowanych komponentach podłącz urządzenie do odpowiednich źródeł zasilania i włącz urządzenie. Aby uzyskać szczegółowe informacje, zobacz [Kabel urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [Kabel urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

