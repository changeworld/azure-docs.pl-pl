---
title: Wymiana modułu PCM, na urządzeniu StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak usunąć i Zastąp zasilania i chłodzenia modułu (PCM) na urządzeniu StorSimple
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
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632505"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Zastąp zasilania i chłodzenia modułu na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
Zasilania i chłodzenia modułu (PCM) na urządzeniu Microsoft Azure StorSimple składa się z źródło zasilania i chłodzenia fanów, które są kontrolowane za pomocą podstawowego i obudów EBOD. Istnieje tylko jeden model PCM, który posiada certyfikat dla każdej obudowie. Obudowa podstawowy jest certyfikowany dla 764 W PCM i obudowy EBOD jest certyfikowany dla 580 W PCM. Mimo że PCMs dla podstawowego obudowy i obudowy EBOD są różne, procedura zastępowania jest identyczna.

W tym samouczku wyjaśniono:

* Usuwanie modułu PCM
* Zainstalować zastępczy modułu PCM

> [!IMPORTANT]
> Zanim usunięcie i zastąpienie PCM, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Zanim użytkownik wymiana modułu PCM
Należy pamiętać o następujących istotne problemy przed wymianą PCM usługi:

* W przypadku awarii zasilania modułu PCM pozostawić zainstalowany moduł awaryjnym, ale Usuń przewód zasilający. Wentylator będą wciąż odbierały power obudowę i podaj odpowiednie chłodzenie w dalszym ciągu. W przypadku niepowodzenia wentylatora modułu PCM wymaga zastąpienia natychmiast.
* Przed usunięciem modułu PCM, wyłączyć zasilanie modułu PCM, wyłączając przełącznik głównego (o ile istnieje) lub usuwając fizycznie przewód zasilający. Zawiera ostrzeżenie do systemu, że wyłączenie zasilania jest bliska.
* Upewnij się, że inne PCM funkcjonalności dla operacji systemu ciągłej przed zastąpieniem wadliwe PCM. Uszkodzony PCM muszą zostać zastąpione pełną funkcjonalność PCM tak szybko, jak to możliwe.
* Zastąpienie modułu PCM zajmuje tylko kilka minut, ale muszą być wykonane w ciągu 10 minut usunięcia PCM nie powiodło się, aby zapobiec przegrzaniu.
* Należy pamiętać, zastąpienie 764 modułów W module PCM fabrycznej nie zawierają moduł baterii zapasowej. Należy usunąć baterii z Twojej wadliwe PCM i wstaw go do modułu zastąpienie przed wykonaniem zastąpienia. Aby uzyskać więcej informacji, zobacz instrukcje [usuwanie i wstawianie modułu baterii zapasowej](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Usuwanie modułu PCM
Wykonaj te instrukcje, gdy jesteś gotowy do usunięcia zasilania i chłodzenia modułu (PCM) na urządzeniu Microsoft Azure StorSimple.

> [!NOTE]
> Zanim usuniesz użytkownika PCM, sprawdź, czy prawidłowy zamiennik (764 T dla podstawowego obudowy) lub 580 W dla obudowy EBOD.

#### <a name="to-remove-a-pcm"></a>Aby usunąć PCM
1. W klasycznym portalu Azure, kliknij przycisk **Ustawienia > Monitor > kondycja sprzętu**. Sprawdź stan poszczególnych składników modułu PCM, w obszarze **udostępnione składniki** Aby identyfikować, które PCM nie powiodło się:
   
   * Jeśli zasilacz w module PCM 0 zakończyło się niepowodzeniem, stan **zasilacz w module PCM 0** przyjmie kolor czerwony.
   * Jeśli zasilacz w module PCM 1 zakończyło się niepowodzeniem, stan **zasilacz w module PCM 1** przyjmie kolor czerwony.
   * Jeśli wentylator w module PCM 1 zakończyło się niepowodzeniem, stan albo **chłodzenie 0 dla module PCM 0** lub **chłodzenie 1 w module PCM 0** przyjmie kolor czerwony.
2. Znajdź zakończone niepowodzeniem PCM na odwrocie podkładki głównej obudowy. Jeśli używasz 8600 model zidentyfikować głównej obudowy, analizując System numeru identyfikacyjnego jednostki, wyświetlane na panelu przednim wyświetlacz. Wartość domyślna to identyfikator jednostki, wyświetlane w podstawowej obudowy **00**, a wartość domyślna to identyfikator jednostki, wyświetlane na obudowę EBOD **01**. Poniższy diagram i tabeli wyjaśniono z panelu przedniego wyświetlacz.
   
    ![Identyfikator systemu na panelu przednim OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Rysunek 1** panelu Front urządzenia  
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Wycisz przycisku |
   | 2 |Zasilania systemu |
   | 3 |Błąd modułu |
   | 4 |Błąd logiczny |
   | 5 |Wyświetlanie Identyfikatora jednostki |
3. Diod LED wskaźnika monitorowania tyłu obudowy podstawowego można również zidentyfikować PCM awaryjnym. Zobacz poniższy diagram i tabeli, aby zrozumieć, jak użyć diody LED, aby zlokalizować wadliwe PCM. Na przykład jeśli LED odpowiadający **wentylator się nie powieść** jest świeci, wentylatora nie powiodło się. Podobnie jeśli LED odpowiadający **AC kończyć się niepowodzeniem** jest włączone, zasilania nie powiodło się. 
   
    ![Montażowa urządzenia PCM diod LED wskaźnika monitorowania](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Rysunek 2** PCM z powrotem przy użyciu diod LED wskaźnika
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Awarii zasilania programu AC |
   | 2 |Awaria wentylatora |
   | 3 |Błąd baterii |
   | 4 |PCM OK |
   | 5 |Kontroler domeny awarii zasilania |
   | 6 |Bateria w dobrej kondycji |
4. Zapoznaj się z poniższym diagramie tyłu urządzenia StorSimple, aby zlokalizować modułu PCM. Module PCM 0 jest po lewej stronie i module PCM 1 jest po prawej stronie. Tabeli poniżej opisano modułów.
   
     ![Płyty montażowej modułów głównej Obudowa urządzenia](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Rysunek 3** tylnej stronie urządzeń za pomocą wtyczki 
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |MODULE PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrolera 0 |
   | 4 |Kontrolera 1 |
5. Wyłącz uszkodzony PCM i Odłącz zasilający dostaw. Możesz teraz usunąć modułu PCM.
6. Zapoznanie się z nim zatrzaśnięcia i stronie uchwytu modułu PCM między thumb i palec i zmieścić je ze sobą, aby otworzyć uchwytu.
   
    ![Dojście do otwierania modułu PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Rysunek 4** otworzyć uchwytu modułu PCM
7. Wygodne do trzymania uchwyt, a następnie usuń modułu PCM.
   
    ![Usuwanie urządzenia modułu PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Rysunek 5** usunięcie modułu PCM

## <a name="install-a-replacement-pcm"></a>Zainstalować zastępczy modułu PCM
Wykonaj te instrukcje dotyczące instalowania modułu PCM w urządzeniu StorSimple. Upewnij się, że włożono moduł baterii zapasowej przed instalacją zastąpienia PCM (dotyczy tylko 764 PCMs W). Aby uzyskać więcej informacji, zobacz instrukcje [usuwanie i wstawianie modułu baterii zapasowej](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Do zainstalowania modułu PCM
1. Sprawdź, czy masz poprawne zastępuje PCM tej obudowie. Obudowa podstawowy musi 764 W PCM i obudowy EBOD musi 580 W PCM. Nie należy próbować użyć 580 PCM W w obudowie podstawowego lub 764 PCM W w obudowie EBOD. Na poniższej ilustracji przedstawiono, gdzie można zidentyfikować te informacje dotyczące etykiety, który jest dołączony do modułu PCM.
   
    ![Etykieta PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Rysunek 6** PCM etykiety
2. Sprawdź, czy uszkodzenia obudowy, zwracając szczególną uwagę na łączniki. 
   
   > [!NOTE]
   > **Nie należy instalować modułu, jeśli zgięte wszystkie numery PIN łącznika.**
   > 
   > 
3. Z dojściem PCM w pozycji otwarcia Przesuń moduł do obudowy.
   
    ![Instalowanie urządzenia modułu PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Rysunek 7** instalowania modułu PCM
4. Ręcznie zamknąć uchwytu modułu PCM. Kliknięcie powinno być słychać jako angażuje zatrzaśnięcia dojście.
   
   > [!NOTE]
   > Aby upewnić się, że numerów PIN łącznik już działalność, delikatnie można holownik na dojście bez zwalniania zatrzaśnięcia. Modułu PCM wysuwa się, oznacza, że zatrzaśnięcia zostało zamknięte przed zaangażowane łączników.
   
5. Do źródła zasilania i modułu PCM, podłącz kable zasilania.
6. Zabezpiecz obciążenie beli zwolnienia.
7. Włącz modułu PCM.
8. Weryfikacja pomyślnego wyniku zastąpienia: w portalu Azure usługi Menedżer urządzeń StorSimple przejdź do Twojego urządzenia i następnie **Ustawienia > Monitor > kondycja sprzętu**. W obszarze **udostępnione składniki**, stan modułu PCM powinien być w kolorze zielonym.
   
   > [!NOTE]
   > Może upłynąć kilka minut, zanim zastąpienia PCM całkowicie zainicjować.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

