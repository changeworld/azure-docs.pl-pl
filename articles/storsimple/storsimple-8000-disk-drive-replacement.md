---
title: Wymiana dysku, na urządzeniu StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak wymiana dysku, obudowy do głównej StorSimple lub obudowy EBOD.
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
ms.date: 8/25/2017
ms.author: alkohli
ms.openlocfilehash: 3d6ef22e4df36996d68194589f43ea0f57def22c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60576955"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Wymiana dysku, na urządzeniu StorSimple 8000 series

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak zostanie usunięty i zastąpiony nieprawidłowe działanie lub uszkodzonego dysku twardym na urządzeniu z systemem Microsoft Azure StorSimple. Aby zastąpić dysk, musisz:

* Odłączyć antitamper blokady
* Usuń dysk
* Zainstaluj dysk zastępczy

> [!IMPORTANT]
> Zanim usunięcie i zastąpienie dysku, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Odłączyć antitamper blokady
W tej procedurze wyjaśniono, jak antitamper blokady na urządzeniu StorSimple może być urządzenie włączone lub wyłączone podczas zastępowania dysków. Antitamper blokady są zainstalowane w uchwyty operatora dysku i są one dostępne za pośrednictwem małych przysłony w sekcji zatrzaśnięcia uchwytu. Dyski są dostarczane z blokadami równa pozycji blokady.

#### <a name="to-unlock-the-antitamper-lock"></a>Aby odblokować antitamper blokady
1. Klucz blokady ("tamperproof" T10 śrubokręt, które firma Microsoft udostępniła) należy wstawić dokładnie w przysłony w uchwyt oraz w jego gniazda. 
   
   Jeśli antitamper blokada jest aktywna, czerwony wskaźnik jest widoczny w przysłony.
  
    ![Zablokowane dysku](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Rysunek 1** blokady przed odporne zaangażowanie
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Przysłony wskaźnika |
   | 2 |Antitamper blokady |
2. Wymienić główny klucz w kierunku przeciwnym kierunku, dopóki czerwony wskaźnik nie jest widoczna w przysłony powyżej klucza.
3. Usuń klucz.
   
    ![Odblokowany dysku](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Rysunek 2** odblokowany dysku
4. Teraz można usunąć dysku.

Postępuj zgodnie z instrukcjami w odwrotnej kolejności, dzięki którym można zaangażować blokady.

## <a name="remove-the-disk-drive"></a>Usuń dysk
Urządzenie StorSimple obsługuje konfiguracji RAID 10 przypominającej magazynu miejsca do magazynowania. Oznacza to, że może działać normalnie z jednym dyskiem zakończonych niepowodzeniem dysków półprzewodnikowych (SSD), lub dysku twardego dysku (HDD).

> [!IMPORTANT]
> * Jeśli system ma więcej niż jeden dysk nie powiodło się, nie usuwaj więcej niż jednego dysku SSD lub dysk twardy z systemu w dowolnym momencie w czasie. Ten sposób może spowodować utratę danych.
> * Upewnij się, umieść zastępowania dysków SSD w miejscu, które wcześniej zawierało dyski SSD. Podobnie umieść zastępowania dysku twardego w miejscu, które wcześniej zawierało dysk twardy.
> * W witrynie Azure portal, miejsc, są numerowane od 0 – 11. W związku z tym jeśli w portalu jest wyświetlany czy dysk w gnieździe 2 uległ awarii, na urządzeniu, poszukaj uszkodzony dysk w gnieździe trzeci od lewego górnego rogu.
> 
> 

Dyski można usunięte i zastąpione, gdy system działa.

#### <a name="to-remove-a-drive"></a>Aby usunąć dysk
1. Aby zidentyfikować uszkodzony dysk, w witrynie Azure portal przejdź do Twojego urządzenia **Ustawienia > kondycja sprzętu**. Ponieważ dysk może zakończyć się niepowodzeniem w obudowie podstawowego i/lub obudowy EBOD (jeśli jest używany model 8600), sprawdź stan dysków w ramach **udostępnione składniki** i w obszarze **składniki współużytkowane EBOD**. Uszkodzony dysk w każdej obudowie pojawią się w stanie czerwonym.
2. Odszukaj dyski uzyskać podstawowy obudowy lub obudowy EBOD. 
3. Jeśli dysk jest odblokowany, przejdź do następnego kroku. Jeśli dysk jest zablokowany, odblokuj go, wykonując poniższe kroki w [odłączyć antitamper blokady](#disengage-the-antitamper-lock).
4. Naciśnij zatrzaśnięcia czarny na moduł operatora stacji, a następnie ściągnąć uchwyt operatora dysku na zewnątrz i natychmiast wiodących obudowy.
   
    ![Zwolnienie uchwytu dysku](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Rysunek 3** zwalniania dojścia dysku
5. Gdy uchwyt operatora dysku pełni zostanie przedłużony, przesuń operatora dysku poza obudowy. 
   
    ![Przedłużanie dysk z dysku twardego](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Rysunek 4** przedłużanie dysku poza operatora

## <a name="install-the-replacement-disk-drive"></a>Zainstaluj dysk zastępczy
Po dysku nie powiodło się w urządzeniu StorSimple i usunięto go, wykonaj tę procedurę, aby zamienić go na nowy dysk.

#### <a name="to-insert-a-drive"></a>Aby wstawić dysku
1. Upewnij się, że dojście operatora dysku pełni zostanie przedłużony, jak pokazano na poniższej ilustracji.
   
    ![Dysk z dojściem rozszerzone](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Rysunek 5** dysku z dojściem rozszerzone
2. Przesuń operatora dysku aż do obudowy.
   
    ![Przedłużanie dysku w stacji dysków operatora](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Rysunek 6** przedłużanie operatora dysku do obudowy
3. Za pomocą operatora dysku wstawiony należy zamknąć dojście operatora dysku przerywając Wypychanie do obudowy, operatora dysku, dopóki uchwyt operatora dysku przymocować w stanie zablokowanym.
4. Użyj klucza blokady, dostarczony przez firmę Microsoft (tamperproof Torx śrubokręt) do bezpiecznego dojścia operatora w miejscu, włączając gwintowanym blokady Włącz kwartału z ruchem wskazówek zegara.
5. Sprawdź, czy zamiana powiodła się, jak i dysk będzie działać. Dostęp do witryny Azure portal i przejdź do **ustawienia urządzenia** > **kondycja sprzętu**. W obszarze **udostępnione składniki** lub **składniki współużytkowane EBOD**, stan dysku powinna być zielona, wskazujący, że jest w dobrej kondycji.

   
   > [!NOTE]
   > Może upłynąć kilka godzin, aż stan dysku i kolor na zielony po zastąpienia.
  
## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

