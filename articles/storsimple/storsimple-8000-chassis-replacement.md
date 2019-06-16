---
title: Wymiana obudowy na urządzenia StorSimple 8000 series | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak usunąć i Zastąp podstawę dla podstawowego obudowy StorSimple lub EBOD obudowy.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61312449"
---
# <a name="replace-the-chassis-on-your-storsimple-device"></a>Wymiana obudowy na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i Zastąp podstawę urządzenia serii StorSimple 8000. Model StorSimple 8100 to urządzenie pojedynczego obudowy (wspólnej obudowie), 8600 jest urządzeniem podwójną obudowy (dwie obudowy). W przypadku modelu 8600 są potencjalnie dwie obudowy, które może nie działać na urządzeniu: podstawę dla podstawowego obudowy lub podstawę dla obudowy EBOD.

W obu przypadkach obudowy zastępowania, dostarczanej przez firmę Microsoft jest pusty. Nie zasilania i chłodzenia modułów (PCMs), moduły kontrolera, dyski półprzewodnikowe (SSD), stacje dysków twardych (HDD) lub modułów EBOD zostaną dołączone.

> [!IMPORTANT]
> Zanim usunięcie i zastąpienie obudowy, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-chassis"></a>Usuń obudowy
Wykonaj poniższe kroki, aby usunąć obudowy na urządzeniu StorSimple.

#### <a name="to-remove-a-chassis"></a>Aby usunąć podstawę
1. Upewnij się, że urządzenie StorSimple jest zamknięta i odłączone od źródła zasilania.
2. Usuń wszystkie sieci i kabli SAS, tak, jeśli ma to zastosowanie.
3. Usuń jednostkę w stojaku.
4. Każdy z stacje usunąć i zanotuj miejsc, w których są usuwane. Aby uzyskać więcej informacji, zobacz [Usuń dysk](storsimple-8000-disk-drive-replacement.md#remove-the-disk-drive).
5. Na obudowę EBOD (jeśli jest to podstawę, która nie powiodła się) Usuń moduły kontrolera EBOD. Aby uzyskać więcej informacji, zobacz [Usuwanie kontrolera EBOD](storsimple-8000-ebod-controller-replacement.md#remove-an-ebod-controller).
   
    Na głównej obudowy (jeśli jest to podstawę, która nie powiodła się) Usuń kontrolery i zanotuj miejsc, w których są usuwane. Aby uzyskać więcej informacji, zobacz [usunąć kontroler](storsimple-8000-controller-replacement.md#remove-a-controller).

## <a name="install-the-chassis"></a>Zainstaluj obudowy
Wykonaj poniższe kroki, aby zainstalować obudowy na urządzeniu StorSimple.

#### <a name="to-install-a-chassis"></a>Aby zainstalować podstawę
1. Zainstaluj obudów w stojaku. Aby uzyskać więcej informacji, zobacz [urządzenia usługi StorSimple 8100 zamontować w stojaku](storsimple-8100-hardware-installation.md#rack-mount-your-storsimple-8100-device) lub [urządzenia usługi StorSimple 8600 zamontować w stojaku](storsimple-8600-hardware-installation.md#rack-mount-your-storsimple-8600-device).
2. Po obudowy jest zamontowane w stojaku, należy zainstalować moduły kontrolera w tej samej pozycji, które poprzednio były zainstalowane w.
3. Zainstaluj dyski w tej samej pozycji i miejsc, które poprzednio były zainstalowane w.
   
   > [!NOTE]
   > Firma Microsoft zaleca, aby najpierw zainstalować dyski SSD w gniazdach, a następnie zainstaluj dysków twardych.
  
4. Łączenie urządzenia ze źródłami zasilania odpowiednich urządzenia zamontowane w stojaku i zainstalowanych składników i Włącz urządzenie. Aby uzyskać więcej informacji, zobacz [Podłączanie kabli do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md#cable-your-storsimple-8100-device) lub [Podłączanie kabli do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md#cable-your-storsimple-8600-device).

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

