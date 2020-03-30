---
title: Wymień kontroler EBOD StorSimple 8600 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak usunąć i wymienić jeden lub oba kontrolery EBOD na urządzeniu StorSimple 8600.
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
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: b05d1f36d1e74b3d915e216676859654fbcbacf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254887"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Wymień kontroler EBOD na urządzeniu StorSimple

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak zastąpić wadliwy moduł kontrolera EBOD na urządzeniu Microsoft Azure StorSimple. Aby wymienić moduł kontrolera EBOD, należy:

* Wyjmij uszkodzony kontroler EBOD
* Instalowanie nowego kontrolera EBOD

Przed rozpoczęciem należy wziąć pod uwagę następujące informacje:

* Puste moduły EBOD muszą być wstawione do wszystkich nieużywanych gniazd. Obudowa nie ostygnie prawidłowo, jeśli szczelina pozostanie otwarta.
* Kontroler EBOD można wymieniać na gorąco i można go wyjąć lub wymienić. Nie usuwaj modułu, który uległ awarii, dopóki nie zostanie wymienny. Po zainicjowaniu procesu wymiany należy zakończyć go w ciągu 10 minut.

> [!IMPORTANT]
> Przed podjęciem próby usunięcia lub wymiany dowolnego komponentu StorSimple należy zapoznać się z [konwencjami ikon bezpieczeństwa](storsimple-safety.md#safety-icon-conventions) i innymi [środkami ostrożności.](storsimple-safety.md)

## <a name="remove-an-ebod-controller"></a>Usuwanie kontrolera EBOD
Przed wymianą uszkodzonego modułu kontrolera EBOD w urządzeniu StorSimple upewnij się, że drugi moduł kontrolera EBOD jest aktywny i uruchomiony. Poniższa procedura i tabela wyjaśniają, jak usunąć moduł kontrolera EBOD.

#### <a name="to-remove-an-ebod-module"></a>Aby usunąć moduł EBOD
1. Otwórz witrynę Azure Portal.
2. Przejdź do urządzenia i przejdź do **ustawienia** > **kondycja sprzętu**i sprawdź, czy stan diody LED dla aktywnego modułu kontrolera EBOD jest zielony, a dioda LED dla nieudanego modułu kontrolera EBOD jest czerwona.
3. Znajdź nieudany moduł kontrolera EBOD z tyłu urządzenia.
4. Przed wyjęciem modułu EBOD z systemu należy wyjąć kable łączące moduł kontrolera EBOD z kontrolerem.
5. Zanotuj dokładny port SAS modułu kontrolera EBOD, który był podłączony do kontrolera. Po wymianie modułu EBOD konieczne będzie przywrócenie systemu do tej konfiguracji.
   
   > [!NOTE]
   > Zazwyczaj będzie to port A, który jest oznaczony jako **host w** poniższym diagramie.
   
    ![Zasysła sterownika EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Rysunek 1** Powrót modułu EBOD
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Dioda LED usterek |
   | 2 |Dioda LED zasilania |
   | 3 |Złącza SAS |
   | 4 |Diody LED SAS |
   | 5 |Porty szeregowe przeznaczone wyłącznie do użytku fabrycznego |
   | 6 |Port A (host w) |
   | 7 |Port B (host out) |
   | 8 |Port C (tylko do użytku fabrycznego) |

## <a name="install-a-new-ebod-controller"></a>Instalowanie nowego kontrolera EBOD
Poniższa procedura i tabela wyjaśniają, jak zainstalować moduł kontrolera EBOD w urządzeniu StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Aby zainstalować kontroler EBOD
1. Sprawdź, czy urządzenie EBOD nie ma uszkodzeń, szczególnie w przypadku złącza interfejsu. Nie należy instalować nowego kontrolera EBOD, jeśli jakieś piny są wygięte.
2. Gdy zatrzaski są w pozycji otwartej, wsuń moduł do obudowy, aż zatrzaśną się zatrzaski.
   
    ![Instalowanie kontrolera EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Rysunek 2**  Instalowanie modułu kontrolera EBOD
3. Zamknij zatrzask. Powinieneś usłyszeć kliknięcie, gdy zatrzask się zatrzasnie.
   
    ![Zwalnianie zatrzasku EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Rysunek 3**  Zamykanie zatrzasku modułu EBOD
4. Ponownie podłączyć kable. Użyj dokładnej konfiguracji, która była obecna przed wymianą. Szczegółowe informacje na temat podłączania kabli można znaleźć na poniższym diagramie i tabeli.
   
    ![Kabel urządzenia 4U do zasilania](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Rysunek 4**. Ponowne podłączanie kabli
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Obudowa podstawowa |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontroler 0 |
   | 5 |Kontroler 1 |
   | 6 |Sterownik EBOD 0 |
   | 7 |Sterownik EBOD 1 |
   | 8 |Obudowa EBOD |
   | 9 |Jednostki dystrybucji mocy |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

