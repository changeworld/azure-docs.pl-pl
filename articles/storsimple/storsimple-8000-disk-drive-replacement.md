---
title: Zamień na dysku w urządzeniu serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak zastąpić na dysku w StorSimple obudowa podstawowego lub obudowa EBOD.
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
ms.date: 073/2017
ms.author: alkohli
ms.openlocfilehash: a8616eb51b177a9447a7c466c9d934b9139afedf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874892"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Zamień na urządzeniu z serii StorSimple 8000 dysku

## <a name="overview"></a>Omówienie
Ten samouczek wyjaśnia, jak zostanie usunięty i zastąpiony nieprawidłowe działanie lub uszkodzonego dysku twardego na urządzeniu Microsoft Azure StorSimple. Aby zastąpić dysk, musisz:

* Odłączyć antitamper blokady
* Usuń dysk
* Zainstaluj dysk zastępczy

> [!IMPORTANT]
> Przed usunięcie i zastąpienie stacji dysków, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).
 

## <a name="disengage-the-antitamper-lock"></a>Odłączyć antitamper blokady
Ta procedura wyjaśnia, jak antitamper blokad na urządzeniu StorSimple może być urządzenie włączone lub wyłączone podczas zastępowania dysków. Antitamper blokady są zainstalowane w uchwytach operatora dysku, i są one dostępne za pośrednictwem małych otwarcie w sekcji zatrzaśnięcia uchwytu. Dyski są dostarczane z ustawioną pozycji blokady blokad.

#### <a name="to-unlock-the-antitamper-lock"></a>Aby odblokować antitamper blokady
1. Starannie wstawić klucz blokady ("tamperproof" T10 śrubokręt, otrzymany od firmy Microsoft) do otwarcie w dojście i jego gniazda. 
   
   Jeśli włączono antitamper blokady, red wskaźnika jest widoczne w otwarcie.
  
    ![Zablokowane dysku](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Rysunek 1** blokady przed wykrycie zaangażowane
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Otwarcie wskaźnika |
   | 2 |Antitamper blokady |
2. Obróć klucz, w przeciwnym kierunku, dopóki czerwony wskaźnik nie jest widoczny w otwarcie powyżej klucza.
3. Usuń klucz.
   
    ![Odblokowany dysku](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Rysunek 2** odblokowany dysku
4. Teraz można usunąć dysku.

Postępuj zgodnie z instrukcjami wstecznego nawiązanie blokady.

## <a name="remove-the-disk-drive"></a>Usuń dysk
Urządzenia StorSimple obsługuje konfiguracji RAID 10 przypominającej magazynu spacji. Oznacza to, że aplikacja może działać normalnie z jednego dysku nie powiodło się, dysków półprzewodnikowych (SSD), lub dysku twardego dysku (HDD).

> [!IMPORTANT]
> * Jeśli w systemie jest więcej niż jednego dysku nie powiodło się, nie należy usuwać więcej niż jeden dysków SSD i HDD z systemu w dowolnym momencie w czasie. W ten sposób może spowodować utratę danych.
> * Upewnij się, umieść zastępczy dysków SSD w miejscu, które wcześniej zawierało dysków SSD. Podobnie umieść zastępczy dysk twardy w miejscu, które wcześniej zawierało dysk twardy.
> * W portalu Azure gniazda są ponumerowane od 0 – 11. W związku z tym jeśli portalu wskazuje, że dysk w gnieździe 2 nie powiodło się na urządzeniu, poszukaj uszkodzony dysk w gnieździe trzeci od góry po lewej.
> 
> 

Dyski można usunięty i zastąpiony podczas działania systemu.

#### <a name="to-remove-a-drive"></a>Aby usunąć dysk
1. Aby zidentyfikować uszkodzonym dysku, w portalu Azure, przejdź do Twojego urządzenia **Ustawienia > kondycji sprzętu**. Ponieważ dysk może zakończyć się niepowodzeniem w obudowie podstawowej i/lub w obudowie EBOD (Jeśli używasz modelu 8600), sprawdź stan dysków w obszarze **udostępnione składniki** i w obszarze **EBOD udostępnione składniki**. Uszkodzony dysk w każdej obudowie będą wyświetlane z czerwonym oznaczeniem stanu.
2. Odszukaj dyski przodu obudowy podstawowego lub obudowa EBOD. 
3. Jeśli dysk jest odblokowany, przejdź do następnego kroku. Jeśli dysk jest zablokowany, odblokuj go, wykonując poniższe kroki w [odłączyć antitamper blokady](#disengage-the-antitamper-lock).
4. Naciśnij czarny zatrzaśnięcia na dysku modułu operatora i wylogowanie i optymalizacji ściągnięcia dojście operatora dysku z przodu obudowy.
   
    ![Zwolnienie uchwytu dysku](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Rysunek 3** zwolnienie uchwytu dysku
5. Gdy uchwyt operatora dysku pełni zostanie rozszerzony, przesuń operatora dysku poza obudowy. 
   
    ![Przedłużanie dysku z dysku](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Rysunek 4** przedłużanie dysku poza operatora

## <a name="install-the-replacement-disk-drive"></a>Zainstaluj dysk zastępczy
Po dysku nie powiodło się w urządzeniu StorSimple i usunięto go, wykonaj poniższą procedurę, aby zamienić go na nowy dysk.

#### <a name="to-insert-a-drive"></a>Aby wstawić dysku
1. Upewnij się, że dojście operatora dysku pełni zostanie rozszerzony, jak pokazano na poniższej ilustracji.
   
    ![Dysk z dojściem rozszerzone](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Rysunek 5** dysku z dojściem rozszerzone
2. Przesuń operatora dysku, aż do obudowy.
   
    ![Przedłużanie dysku na dysk nośnika](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Rysunek 6** przedłużanie operatora dysku do obudowy
3. Z nośnikiem dysku wstawiony zamknąć dojścia operatora dysku, pozostawiając wypchnąć operatora dysku do obudowy, dopóki dojście operatora dysku przyciąganie w stanie zablokowanym.
4. Za pomocą klawisza blokady, dostarczony przez firmę Microsoft (tamperproof Torx śrubokręt) do zabezpieczania dojście operatora w miejscu przez włączenie śrubę blokady Włącz kwartału z ruchem wskazówek zegara.
5. Sprawdź, czy zastąpienia zakończyło się pomyślnie i działa dysku. Dostęp do portalu Azure i przejdź do **ustawienia urządzenia** > **kondycji sprzętu**. W obszarze **udostępnione składniki** lub **EBOD udostępnione składniki**, stan dysku powinna być zielona, wskazując, że jest w dobrej kondycji.

   
   > [!NOTE]
   > Może upłynąć kilka godzin stanu dysku włączyć zielonego po wymianie.
  
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

