---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 9b9922602218280d58331a755ed0dfed7df96f40
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2019
ms.locfileid: "67183267"
---
#### <a name="to-cable-your-device-for-power"></a>Aby Podłączanie kabli do urządzenia zasilania
> [!NOTE]
> Zarówno obudów w urządzeniu StorSimple obejmują PCMs nadmiarowe. Dla każdej obudowie PCMs musi być zainstalowane i połączone z różnymi źródłami zasilania aby zapewnić wysoką dostępność.
> 
> 

1. Upewnij się, że przełączniki zasilania na wszystkich PCMs znajdują się w pozycji wył.
2. Podstawowy obudowy Połącz się zarówno PCMs przewodów zasilania. Kable są oznaczone na czerwono na diagramie okablowania power poniżej.
3. Należy upewnić się czy dwa PCMs na podstawowym obudowy źródłami zasilania oddzielne.
4. Dołącz do włączania stojak jednostki dystrybucji zasilania przewodów zasilania, jak pokazano w usłudze Power BI okablowania diagramu.
5. Powtórz kroki od 2 do 4 dla obudowy EBOD.
6. Włącz obudowy EBOD przestawiając przycisk zasilania na każdy PCM pozycji dalej.
7. Upewnij się, że obudowa EBOD jest włączona, sprawdzając, czy diod LED na odwrocie podkładki kontrolera EBOD są włączone.
8. Włącz głównej obudowy, przestawiając każdego PCM przełącznika w pozycji włączone.
9. Sprawdź, czy system jest włączony, zapewniając z kontrolerem urządzenia, których włączono diod LED.
10. Upewnij się, że połączenie między kontrolera EBOD i z kontrolerem urządzenia jest aktywny, upewniając się, że cztery diod LED obok portów SAS kontrolera EBOD są zielone.
    
    > [!IMPORTANT]
    > Aby zapewnić wysoką dostępność w systemie, firma Microsoft zaleca ściśle przestrzegać możliwości okablowania schemat pokazano na poniższym diagramie.
    > 
    > 
    
    ![Podłączanie kabli do urządzenia 4U zasilania](./media/storsimple-cable-8600-for-power/HCSCableYour4UDeviceforPower.png)
    
    **Przewody zasilania**
    
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
    | 9 |PDU |

