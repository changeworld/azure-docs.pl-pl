---
title: Zastąp PCM na urządzeniu z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak usunięty i zastąpiony zasilania i chłodzenia modułu (PCM) na urządzeniu StorSimple
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
ms.openlocfilehash: 7d181e6e434c998573dbea4b541cfacf7a28ee66
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875060"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Zamień na urządzeniu StorSimple zasilania i chłodzenia modułu
## <a name="overview"></a>Omówienie
Zasilania i chłodzenia modułu (PCM) w urządzeniu Microsoft Azure StorSimple składa się z źródła zasilania i chłodzenia wentylatory, które są kontrolowane przez serwer podstawowy i obudowy EBOD. Istnieje tylko jeden model PCM, który jest certyfikowany do każdej obudowy. Obudowa podstawowy jest certyfikowany do 764 W PCM i obudowy EBOD jest certyfikowany do 580 W PCM. Mimo że PCMs obudowa podstawowego i obudowy EBOD są różne, procedura zastępczy jest taka sama.

Ten samouczek wyjaśnia, jak:

* Usuń PCM
* Zainstaluj serwer zamienny PCM

> [!IMPORTANT]
> Przed usunięcie i zastąpienie PCM, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Aby zastąpić PCM
Należy pamiętać o następujących istotne problemy przed wymianą programu PCM:

* W przypadku niepowodzenia zasilania PCM pozostaw zainstalowany moduł uszkodzony, ale usunąć przewód zasilający. Wentylator będzie otrzymywać zasilania obudowa i podaj odpowiednie chłodzenia w dalszym ciągu. W przypadku niepowodzenia wentylatora PCM wymaga zastąpienia natychmiast.
* Przed usunięciem PCM, odłącz zasilanie z PCM wyłączając przełącznika głównego (o ile istnieje) lub usuwając fizycznie przewód zasilający. Zawiera ostrzeżenie w systemie, czy wyłączania zasilania jest bezpośrednie.
* Upewnij się, że inne PCM funkcjonalności systemu ciągłej operacji przed wymianą PCM uszkodzony. Błędny PCM muszą zostać zastąpione pełnej funkcjonalności PCM tak szybko, jak to możliwe.
* Zastąpienie modułu PCM zajmuje tylko kilka minut, ale musi zostać ukończone w ciągu 10 minut usunięcia PCM nie powiodło się, aby zapobiec przegrzaniu.
* Zauważ, że zastępczy 764 modułów W PCM fabrycznej nie zawierać modułu baterii kopii zapasowej. Należy usunąć baterii z programu PCM uszkodzony i wstawić go w module zastępczy przed wykonaniem zastąpienia. Aby uzyskać więcej informacji, zobacz temat jak [usuwanie i wstawianie modułu baterii kopii zapasowej](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Usuń PCM
Wykonaj te instrukcje, gdy wszystko będzie gotowe do usunięcia z urządzenia Microsoft Azure StorSimple zasilania i chłodzenia modułu (PCM).

> [!NOTE]
> Przed usunięciem programu PCM, sprawdź, czy poprawne zastąpienia (764 T dla obudowa podstawowego) lub 580 W przypadku obudowy EBOD.

#### <a name="to-remove-a-pcm"></a>Aby usunąć PCM
1. W klasycznym portalu Azure, kliknij przycisk **Ustawienia > Monitor > kondycji sprzętu**. Sprawdź stan poszczególnych składników PCM w obszarze **udostępnione składniki** do określenia, na które PCM nie powiodła się:
   
   * Jeśli zasilacz w PCM 0 nie powiodło się. stan **zasilacz w PCM 0** czerwony.
   * Jeśli zasilacz PCM 1 nie powiodło się. stan **zasilacz 1 PCM** czerwony.
   * Jeśli wentylator PCM 1 nie powiodło się. stan albo **chłodzenia 0 dla PCM 0** lub **chłodzenia 1 dla PCM 0** czerwony.
2. Znajdź niepowodzenia PCM z tyłu obudowy podstawowego. Jeśli używasz modelu 8600 określić podstawowy obudowa za wyświetlany na panelu przednim wyświetlacz numer identyfikacyjny jednostki systemu. Wartość domyślna to identyfikator jednostki wyświetlany na głównej obudowy **00**, a wartość domyślna to identyfikator jednostki wyświetlany na obudowę EBOD **01**. Poniższy diagram i tabeli opisano na panelu przednim wyświetlacz.
   
    ![Identyfikator systemu na panelu przednim OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Rysunek 1** panelu przodu urządzenia  
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Przycisk wyciszenia |
   | 2 |Zasilania systemu |
   | 3 |Błąd modułu |
   | 4 |Błąd logiczny |
   | 5 |Wyświetlanie Identyfikatora jednostki |
3. Monitorowania wskaźnika LED tyłu obudowy głównej mogą służyć do identyfikowania PCM uszkodzony. Zobacz następujące diagram i tabeli na sposób używania LED zlokalizować PCM uszkodzony. Na przykład jeśli LED odpowiadający **wentylator się nie powieść** jest włączone, wentylatora nie powiodło się. Podobnie jeśli LED odpowiadający **AC niepowodzenie** jest włączone, zasilania nie powiodło się. 
   
    ![Płyty montażowej wskaźnika monitorowania urządzenia PCM LED](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Rysunek 2** PCM z powrotem z wskaźnik LED
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |Ak awarii zasilania |
   | 2 |Wentylator awarii |
   | 3 |Uszkodzenia |
   | 4 |PCM OK |
   | 5 |Kontroler domeny awarii zasilania |
   | 6 |Baterii dobrej kondycji |
4. Zapoznaj się z poniższym diagramie tyłu urządzenia StorSimple można znaleźć modułu PCM. PCM 0 jest po lewej stronie i PCM 1 jest po prawej stronie. Tabela poniżej wyjaśniono modułów.
   
     ![Płyty montażowej urządzenia podstawowego obudowy modułów](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Rysunek 3** obu urządzenia z wtyczki 
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrolera 0 |
   | 4 |Kontrolera 1 |
5. Wyłącz PCM uszkodzony i odłącz przewód zasilający dostaw. Można teraz usunąć PCM.
6. Ujmij zatrzaśnięcia i po stronie dojście PCM między thumb i wskazującym i zmieścić je, aby otworzyć uchwytu.
   
    ![Otwierania PCM dojścia](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Rysunek 4** Otwieranie dojścia PCM
7. Dojście wygodne do trzymania i Usuń PCM.
   
    ![Usuwanie urządzenia PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Rysunek 5** usuwanie PCM

## <a name="install-a-replacement-pcm"></a>Zainstaluj serwer zamienny PCM
Wykonaj te instrukcje, aby zainstalować PCM w urządzeniu StorSimple. Upewnij się, że włożono moduł baterii kopii zapasowej przed instalacją zastąpienia PCM (dotyczy tylko 764 PCMs W). Aby uzyskać więcej informacji, zobacz temat jak [usuwanie i wstawianie modułu baterii kopii zapasowej](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Aby zainstalować PCM
1. Sprawdź, czy masz poprawne zastępuje PCM ten załącznik. Podstawowy Obudowa musi 764 W PCM i obudowy EBOD musi 580 W PCM. Nie należy próbować użyć 580 PCM W w obudowie podstawowego lub 764 PCM W w obudowie EBOD. Na poniższej ilustracji przedstawiono where zidentyfikować te informacje na etykiecie, który jest dołączony do PCM.
   
    ![Etykieta PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Rysunek 6** PCM etykiety
2. Sprawdź, czy uszkodzenie obudowy, ze szczególnym uwzględnieniem łączników. 
   
   > [!NOTE]
   > **Nie należy instalować moduł, jeśli zgięte żadnych kodów PIN łącznika.**
   > 
   > 
3. Z uchwytem PCM w pozycji otwarcia Przesuń modułu do obudowy.
   
    ![Instalowanie urządzenia PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Rysunek 7** instalowanie PCM
4. Ręcznie zamknąć dojścia PCM. Kliknięcie usłyszeć jako angażujący zatrzaśnięcia dojścia.
   
   > [!NOTE]
   > Aby upewnić się, że numery PIN łącznika ma zaangażowane, ostrożnie można holownik uchwyt bez zwolnienia zatrzaśnięcia. PCM slajdów wychodzących, oznacza, że zatrzaśnięcia został zamknięty przed zaangażowane łączników.
   
5. Podłącz kable zasilania do źródła zasilania i PCM.
6. Zabezpiecz naprężenia bele zwolnienia.
7. Włącz PCM.
8. Sprawdź, czy zastąpienia zakończyła się pomyślnie: w portalu Azure usługi Menedżer StorSimple urządzeń, przejdź do urządzenia, a następnie do **Ustawienia > Monitor > kondycji sprzętu**. W obszarze **udostępnione składniki**, stan PCM powinna być zielona.
   
   > [!NOTE]
   > Może upłynąć kilka minut, aż do zastąpienia PCM całkowicie zainicjować.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

