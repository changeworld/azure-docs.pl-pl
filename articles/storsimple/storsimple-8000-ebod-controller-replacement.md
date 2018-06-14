---
title: Zastąp kontrolera StorSimple 8600 EBOD | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak usunąć i Zastąp jeden lub oba kontrolerów EBOD na urządzeniu StorSimple 8600.
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
ms.openlocfilehash: 45699c267d1009c4884dd164fd3f2950d6d5f555
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927579"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Zastąp kontrolera EBOD na urządzeniu StorSimple

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak zastąpić uszkodzony moduł kontrolera EBOD na urządzeniu Microsoft Azure StorSimple. Aby zastąpić moduł kontrolera EBOD, musisz:

* Usunąć uszkodzony kontroler EBOD
* Instalowanie nowego kontrolera EBOD

Przed rozpoczęciem należy wziąć pod uwagę następujące informacje:

* Puste EBOD moduły muszą być wstawiane do wszystkich miejsc nieużywane. Obudowa nie zostanie poprawnie cool, jeśli gnieździe pozostanie otwarte.
* Kontroler EBOD jest wyłączania i można go usunąć ani zastąpić. Nie usuwaj modułu nie powiodło się, dopóki nie uzyskasz zastępczy. Po zainicjowaniu procesu wymiany musi zakończyć w ciągu 10 minut.

> [!IMPORTANT]
> Przed próbą należy usunąć lub zamienić dowolny składnik StorSimple, upewnij się, należy przejrzeć [bezpieczeństwa ikona konwencje](storsimple-safety.md#safety-icon-conventions) i innych [środki ostrożności](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Usuwanie kontrolera EBOD
Przed zastąpieniem modułu kontrolera EBOD w urządzeniu StorSimple, upewnij się, że inny moduł kontrolera EBOD jest aktywne i uruchomiona. Poniższe procedury i tabela wyjaśniono, jak usunąć moduł kontrolera EBOD.

#### <a name="to-remove-an-ebod-module"></a>Aby usunąć moduł EBOD
1. Otwórz Azure portal.
2. Przejdź do urządzenia, a następnie przejdź do **ustawienia** > **kondycji sprzętu**i sprawdź, czy stan LED dla aktywnego modułu kontrolera EBOD jest zielony oraz LED kontrolera EBOD nie powiodło się Moduł jest czerwony.
3. Znajdź moduł kontrolera EBOD nie powiodło się z tyłu urządzenia.
4. Usuń kable łączące EBOD modułu kontrolera do kontrolera przed zmianą modułu EBOD z systemu.
5. Zanotuj dokładne port SAS EBOD moduł kontrolera, który był połączony z kontrolerem. Trzeba będzie przywrócić system do tej konfiguracji po zastąpienie modułu EBOD.
   
   > [!NOTE]
   > Zazwyczaj jest to Port A, który jest oznaczony jako **hosta w** na poniższym diagramie.
   
    ![Kontroler IDE EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Rysunek 1** EBOD z powrotem modułu
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Diodę LED awarii |
   | 2 |Power LED |
   | 3 |Złączy SAS |
   | 4 |LED SAS |
   | 5 |Porty szeregowe fabryki wyłącznie do użytku |
   | 6 |Port (hosta) |
   | 7 |Port B (Host out) |
   | 8 |Port C (tylko w przypadku używania fabryki) |

## <a name="install-a-new-ebod-controller"></a>Instalowanie nowego kontrolera EBOD
Poniższe procedury i tabela wyjaśniono, jak zainstalować moduł kontrolera EBOD w urządzeniu StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Aby zainstalować kontroler EBOD
1. Sprawdź urządzenie EBOD za szkody, szczególnie w celu łącznika interfejsu. Nie należy instalować na nowy kontroler EBOD, jeśli zgięte żadnych kodów PIN.
2. Z zamków w pozycji otwarcia dopóki Uwzględnij zamków slajd modułu do obudowy.
   
    ![Instalowanie kontrolera EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Rysunek 2** Instalowanie modułu kontrolera EBOD
3. Zamknij zatrzaśnięcia. Kliknięcie usłyszeć jako angażujący zatrzaśnięcia.
   
    ![Zwalnianie zatrzaśnięcia EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Rysunek 3** zamknięcia EBOD zatrzaśnięcia modułu
4. Ponownie podłącz kable. Użyj dokładnej konfiguracji, która została użyta przed zastąpienia. Zobacz poniższy diagram i tabela zawiera szczegółowe informacje o sposobie Podłącz kable.
   
    ![Podłączanie kabli do urządzenia 4U zasilania](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Rysunek 4**. Ponownie podłączyć kable
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Obudowa podstawowego |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontrolera 0 |
   | 5 |Kontrolera 1 |
   | 6 |EBOD kontrolera 0 |
   | 7 |EBOD kontrolera 1 |
   | 8 |Obudowa EBOD |
   | 9 |Jednostki dystrybucji zasilania |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

