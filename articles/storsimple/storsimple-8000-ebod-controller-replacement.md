---
title: Wymiana kontrolera StorSimple 8600 EBOD | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak usunąć i Zastąp jeden lub oba kontrolery EBOD na urządzeniu StorSimple 8600.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60578695"
---
# <a name="replace-an-ebod-controller-on-your-storsimple-device"></a>Wymiana kontrolera EBOD na urządzeniu StorSimple

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak zastąpić wadliwe modułu kontrolera EBOD na urządzeniu z systemem Microsoft Azure StorSimple. Aby zastąpić modułu kontrolera EBOD, należy:

* Usuń uszkodzony kontrolera EBOD
* Zainstalowanie nowego kontrolera EBOD

Przed rozpoczęciem należy wziąć pod uwagę następujące informacje:

* Puste modułów EBOD musi znajdować się do wszystkich gniazd nieużywane. Obudowa nie zostanie poprawnie chłodna, jeśli gniazdo pozostanie otwarte.
* Kontrolera EBOD jest wyłączania i mogą zostać usunięte lub zamienione. Nie usuwaj modułu nie powiodło się, dopóki nie uzyskasz zastępczy. Po zainicjowaniu procesu wymiany musi zakończyć je w ciągu 10 minut.

> [!IMPORTANT]
> Przed podjęciem próby. Usuń lub Zamień dowolny składnik usługi StorSimple, upewnij się, aby przejrzeć [konwencje ikonę bezpieczeństwa](storsimple-safety.md#safety-icon-conventions) i innych [środki ostrożności](storsimple-safety.md).

## <a name="remove-an-ebod-controller"></a>Usuwanie kontrolera EBOD
Przed zastąpieniem moduł kontrolera EBOD nie powiodło się, w urządzeniu StorSimple, upewnij się, że inny moduł kontrolera EBOD aktywności i działania. Następujące procedura i tabela wyjaśniają, jak usunąć moduł kontrolera EBOD.

#### <a name="to-remove-an-ebod-module"></a>Aby usunąć moduł EBOD
1. Otwórz witrynę Azure Portal.
2. Przejdź do urządzenia, a następnie przejdź do **ustawienia** > **kondycja sprzętu**i sprawdź, czy stan LED przez moduł kontrolera EBOD zielony i LED nie powiodło się kontrolera EBOD Moduł ma kolor czerwony.
3. Znajdź moduł nie powiodło się kontrolera EBOD tyłu urządzenia.
4. Usuń kable, które moduł kontrolera EBOD połączyć się z kontrolerem przed przełączeniem modułu EBOD z systemu.
5. Zanotuj dokładnie portów SAS modułu kontrolera EBOD, który został połączony z kontrolerem. Trzeba będzie przywrócić system do tej konfiguracji, po zastąpienie modułu EBOD.
   
   > [!NOTE]
   > Zazwyczaj jest to Port A, która jest oznaczona jako **hosta w** na poniższym diagramie.
   
    ![Kontroler płyty montażowej EBOD](./media/storsimple-ebod-controller-replacement/IC741049.png)
   
     **Rysunek 1** modułu z powrotem EBOD
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Odporność LED |
   | 2 |Dioda LED |
   | 3 |Złączy SAS |
   | 4 |Sygnatury dostępu Współdzielonego diody LED |
   | 5 |Porty szeregowe fabryki wyłącznie do użytku |
   | 6 |Port (Host w) |
   | 7 |Port B (Host poziomie) |
   | 8 |Port C (tylko do użytku fabryki) |

## <a name="install-a-new-ebod-controller"></a>Zainstalowanie nowego kontrolera EBOD
Poniższe procedury i tabela wyjaśniają, jak zainstalować moduł kontrolera EBOD w urządzeniu StorSimple.

#### <a name="to-install-an-ebod-controller"></a>Aby zainstalować kontrolera EBOD
1. Sprawdź urządzenia EBOD za szkody, szczególnie w celu łącznika interfejsu. Nie należy instalować nowego kontrolera EBOD, jeśli zgięte wszelkie numerów PIN.
2. Za pomocą zatrzaśnięcia w pozycji otwarcia Przesuń moduł do obudowy, do czasu zatrzaśnięcia zaangażowania.
   
    ![Instalowanie kontrolera EBOD](./media/storsimple-ebod-controller-replacement/IC741050.png)
   
    **Rysunek 2** Instalowanie modułu kontrolera EBOD
3. Zamknij zatrzaśnięcia. Kliknięcie powinno być słychać jako angażuje zatrzaśnięcia.
   
    ![Zwalnianie zatrzaśnięcia EBOD](./media/storsimple-ebod-controller-replacement/IC741047.png)
   
    **Rysunek 3** zamykanie zatrzaśnięcia modułu EBOD
4. Ponownie podłącz kable. Użyj dokładnej konfiguracji, która została użyta przed zastąpienia. Zobacz poniższy diagram i tabela zawiera szczegółowe informacje o sposobie Podłącz kable.
   
    ![Podłączanie kabli do urządzenia 4U zasilania](./media/storsimple-ebod-controller-replacement/IC770723.png)
   
    **Rysunek 4**. Ponowne łączenie kable
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Podstawowy obudowy |
   | 2 |PCM 0 |
   | 3 |PCM 1 |
   | 4 |Kontroler 0 |
   | 5 |Kontroler 1 |
   | 6 |Kontrolera EBOD 0 |
   | 7 |Kontrolera EBOD 1 |
   | 8 |Obudowa EBOD |
   | 9 |Jednostki dystrybucji zasilania |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

