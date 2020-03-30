---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 51e1fd18b52d7e215ba43be540156199fb41778e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67183265"
---
#### <a name="to-attach-the-sas-cables"></a>Aby podłączyć kable SAS
1. Zidentyfikuj obudowy podstawowe i EBOD. Obie obudowy można zidentyfikować, patrząc na ich odpowiednie tylne płaszczyzny. Zapoznaj się z poniższą ilustracją, aby uzyskać wskazówki. 
   
    ![Tylna płaszczyzna obudów podstawowych i EBOD](./media/storsimple-sas-cable-8600/HCSBackplaneofprimaryandEBODenclosure.png)
   
    **Widok wstecz obudowy pierwotnej i EBOD**
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Obudowa podstawowa |
   | 2 |Obudowa EBOD |
2. Znajdź numery seryjne w obudowach podstawowych i EBOD. Naklejka z numerem seryjnym jest przymocowana do tylnego ucha każdej obudowy. Numery seryjne muszą być identyczne w obu obu obudowach. Jeśli numery seryjne nie są zgodne, należy natychmiast [skontaktować się z pomocą techniczną](../articles/storsimple/storsimple-contact-microsoft-support.md) firmy Microsoft. Zobacz poniższą ilustrację, aby zlokalizować numery seryjne.
   
    ![Widok z tyłu obudowy z numerem seryjnym](./media/storsimple-sas-cable-8600/HCSRearviewofenclosureindicatinglocationofserialnumbersticker.png)
   
    **Lokalizacja naklejki z numerem seryjnym**
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Ucho obudowy |
3. Za pomocą dostarczonych kabli SAS można podłączyć obudowę EBOD do obudowy podstawowej w następujący sposób:
   
   1. Zidentyfikuj cztery porty SAS w obudowie podstawowej i obudowie EBOD. Porty SAS są oznaczone jako EBOD w obudowie podstawowej i odpowiadają portowi A w obudowie EBOD, jak pokazano na ilustracji okablowania SAS poniżej.
   2. Użyj dostarczonych kabli SAS, aby podłączyć port EBOD do portu A.
   3. Port EBOD na kontrolerze 0 powinien być podłączony do portu A na kontrolerze EBOD 0. Port EBOD na kontrolerze 1 powinien być podłączony do portu A na kontrolerze EBOD 1. Zapoznaj się z poniższą ilustracją, aby uzyskać wskazówki. 
      
      ![Okablowanie SAS dla twojego urządzenia](./media/storsimple-sas-cable-8600/HCSSAScablingforyourdevice.png)
      
      **Okablowanie SAS**
      
      | Label | Opis |
      |:--- |:--- |
      | A |Obudowa podstawowa |
      | B |Obudowa EBOD |
      | 1 |Kontroler 0 |
      | 2 |Kontroler 1 |
      | 3 |Sterownik EBOD 0 |
      | 4 |Sterownik EBOD 1 |
      | 5, 6 |Porty SAS w obudowie podstawowej (oznaczone etykietą EBOD) |
      | 7, 8 |Porty SAS w obudowie EBOD (port A) |

