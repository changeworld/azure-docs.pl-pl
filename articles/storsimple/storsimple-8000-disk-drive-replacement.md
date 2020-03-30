---
title: Wymień dysk na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak zastąpić dysk w obudowie podstawowej StorSimple lub w obudowie EBOD.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267913"
---
# <a name="replace-a-disk-drive-on-your-storsimple-8000-series-device"></a>Wymiana dysku w urządzeniu StorSimple z serii 8000

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak można usunąć i zastąpić uszkodzony lub uszkodzony dysk twardy na urządzeniu Microsoft Azure StorSimple. Aby wymienić dysk, należy:

* Odłączyć blokadę przeciwtampera
* Wyjmuje dysk
* Instalowanie zastępczego dysku

> [!IMPORTANT]
> Przed wyjęciem i wymianą dysku należy zapoznać się z informacjami o bezpieczeństwie w [części StorSimple.](storsimple-8000-hardware-component-replacement.md)
 

## <a name="disengage-the-antitamper-lock"></a>Odłączyć blokadę przeciwtampera
W tej procedurze wyjaśniono, jak można włączyć lub odłączyć blokadę antytampera na urządzeniu StorSimple podczas wymiany dysków. Zamki przeciwtamperowe są zamontowane w uchwytach bagażnika napędu i są dostępne przez mały otwór w sekcji zatrzasku uchwytu. Napędy są dostarczane z blokadami ustawionymi w pozycji zablokowanej.

#### <a name="to-unlock-the-antitamper-lock"></a>Aby odblokować blokadę antytampera
1. Ostrożnie włóż klucz blokady ("odporny na manipulacje" śrubokręt T10, który dostarczył Microsoft) do otworu w uchwycie i do gniazda. 
   
   Jeśli blokada przeciwtampera jest aktywna, czerwony wskaźnik jest widoczny w otworze.
  
    ![Zablokowany dysk](./media/storsimple-disk-drive-replacement/IC741056.png)
   
    **Rysunek 1** Blokada antysatrudła zerem
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Przysłona wskaźnika |
   | 2 |Blokada antytampera |
2. Obróć klawisz w kierunku przeciwnym do ruchu wskazówek zegara, aż czerwony wskaźnik nie będzie widoczny w otworze nad klawiszem.
3. Usuń klucz.
   
    ![Odblokowany dysk](./media/storsimple-disk-drive-replacement/IC741057.png)
   
    **Rysunek 2** Odblokowany dysk
4. Dysk można teraz usunąć.

Wykonaj kroki w odwrotnej kolejności, aby włączyć blokadę.

## <a name="remove-the-disk-drive"></a>Wyjmuje dysk
Urządzenie StorSimple obsługuje konfigurację 10-podobnych miejsc do przechowywania RAID. Oznacza to, że może działać normalnie z jednym dyskiem, dyskiem SSD lub dyskiem twardym (HDD).

> [!IMPORTANT]
> * Jeśli w systemie jest więcej niż jeden dysk, który uległ awarii, nie należy usuwać więcej niż jednego dysku SSD lub hdd z systemu w dowolnym momencie. Może to spowodować utratę danych.
> * Upewnij się, że w gnieździe, które wcześniej zawierało ssd, należy umieścić zastępczy ssd. Podobnie umieść zastępczy dysk twardy w gnieździe, które wcześniej zawierało dysk twardy.
> * W witrynie Azure portal gniazda są numerowane od 0 do 11. W związku z tym jeśli portal pokazuje, że dysk w gnieździe 2 nie powiodło się, na urządzeniu, poszukaj uszkodzonego dysku w trzecim gnieździe z lewego górnego rogu.
> 
> 

Dyski można wyjmować i wymieniać podczas pracy systemu.

#### <a name="to-remove-a-drive"></a>Aby usunąć dysk
1. Aby zidentyfikować dysk, który uległ awarii, w witrynie Azure portal przejdź do ustawień urządzenia **> kondycji sprzętu**. Ponieważ dysk może zakończyć się niepowodzeniem w obudowy podstawowej i/lub w obudowie EBOD (jeśli używasz modelu 8600), spójrz na stan dysków w obszarze **Składniki udostępnione** i składniki **udostępnione EBOD**. Dysk, który uległ awarii w obudowze, będzie wyświetlany o stanie czerwonym.
2. Zlokalizuj napędy z przodu obudowy podstawowej lub obudowy EBOD. 
3. Jeśli dysk jest odblokowany, przejdź do następnego kroku. Jeśli dysk jest zablokowany, odblokuj go, wykonując procedurę w [trybie Odłącz blokadę przeciwlotniku](#disengage-the-antitamper-lock).
4. Naciśnij czarny zatrzask na module bagażnika napędu i wyciągnij uchwyt bagażnika napędu z przodu obudowy.
   
    ![Zwalnianie uchwytu dysku](./media/storsimple-disk-drive-replacement/IC741051.png)
   
    **Rysunek 3** Zwalnianie uchwytu napędu
5. Gdy uchwyt napędu jest w pełni wysunięty, wysuń bagażnik napędu z podwozia. 
   
    ![Wysuwanie dysku z dysku](./media/storsimple-disk-drive-replacement/IC741052.png)
   
    **Rysunek 4** Wysuwanie dysku z nośnika

## <a name="install-the-replacement-disk-drive"></a>Instalowanie zastępczego dysku
Po awarii dysku w urządzeniu StorSimple i usunięciu go, wykonaj tę procedurę, aby zastąpić go nowym dyskiem.

#### <a name="to-insert-a-drive"></a>Aby wstawić dysk
1. Upewnij się, że uchwyt napędu jest w pełni wysunięty, jak pokazano na poniższej ilustracji.
   
    ![Dysk twardy z przedłużonym uchwytem](./media/storsimple-disk-drive-replacement/IC741044.png)
   
    **Rysunek 5** Napęd z przedłużonym uchwytem
2. Wsuń bagażnik napędu do końca w podwozie.
   
    ![Przesuwanie dysku do nośnika dysków](./media/storsimple-disk-drive-replacement/IC741045.png)
   
    **Rysunek 6**  Wsuwanie bagażnika napędu do podwozia
3. Po włożeniu bagażnika napędu zamknij uchwyt bagażnika napędu, kontynuując wciśnięcie bagażnika do podwozia, aż uchwyt napędu zatrzasnie się w pozycji zablokowanej.
4. Użyj klucza blokady dostarczonego przez firmę Microsoft (odporny na manipulacje śrubokręt Torx), aby zabezpieczyć uchwyt na miejsce, obracając śrubę blokującą o ćwierć obrotu zgodnie z ruchem wskazówek zegara.
5. Sprawdź, czy wymiana zakończyła się pomyślnie i dysk działa. Przejdź do witryny Azure portal i przejdź do **ustawień** > urządzenia**Kondycja sprzętu**. W obszarze **Składniki udostępnione** lub składniki **udostępnione EBOD**stan dysku powinien być zielony, co oznacza, że jest w dobrej kondycji.

   
   > [!NOTE]
   > Może upłynąć kilka godzin, aby stan dysku zmieni kolor na zielony po wymianie.
  
## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

