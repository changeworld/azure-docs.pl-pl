---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183267"
---
#### <a name="to-cable-your-device-for-power"></a>Aby sprzewod urządzenie było zasilane
> [!NOTE]
> Obie obudowy na urządzeniu StorSimple zawierają nadmiarowe pcm. Dla każdej obudowy pcms muszą być zainstalowane i podłączone do różnych źródeł zasilania, aby zapewnić wysoką dostępność.
> 
> 

1. Upewnij się, że przełączniki zasilania na wszystkich pcms są w pozycji OFF.
2. W obudowie podstawowej podłącz przewody zasilające do obu pcm. Przewody zasilające są identyfikowane na czerwono na schemacie okablowania zasilania poniżej.
3. Upewnij się, że dwa pcms w obudowy podstawowej używać oddzielnych źródeł zasilania.
4. Podłącz przewody zasilające do zasilania w jednostkach rozdzielczych stelaża, jak pokazano na schemacie okablowania zasilania.
5. Powtórz kroki od 2 do 4 dla obudowy EBOD.
6. Włącz obudowę EBOD, przerzucając przełącznik zasilania na każdym pcm do pozycji ON.
7. Sprawdź, czy obudowa EBOD jest włączona, sprawdzając, czy zielone diody LED z tyłu kontrolera EBOD są włączone.
8. Włącz obudowę podstawową, przerzucając każdy przełącznik PCM do pozycji ON.
9. Sprawdź, czy system jest włączony, upewniając się, że diody LED kontrolera urządzenia zostały włączone.
10. Upewnij się, że połączenie między kontrolerem EBOD a kontrolerem urządzenia jest aktywne, sprawdzając, czy cztery diody LED obok portu SAS na kontrolerze EBOD są zielone.
    
    > [!IMPORTANT]
    > Aby zapewnić wysoką dostępność systemu, zaleca się ścisłe przestrzeganie schematu okablowania zasilania przedstawionego na poniższym diagramie.
    > 
    > 
    
    ![Kabel urządzenia 4U do zasilania](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Okablowanie zasilania**
    
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
    | 9 |Procesory PDU |

