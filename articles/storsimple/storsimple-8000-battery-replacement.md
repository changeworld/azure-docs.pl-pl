---
title: Wymiana baterii na urządzenia Microsoft Azure StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu usunięcia, Zastąp i obsługa modułu baterii zapasowej na urządzeniu StorSimple.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 4ebf3f28d40e0461d140a3fe74fb940720f26db6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64693751"
---
# <a name="replace-the-backup-battery-module-on-your-storsimple-device"></a>Zastąp moduł baterii zapasowej na urządzeniu StorSimple

## <a name="overview"></a>Omówienie
Podstawowy obudowy zasilania i chłodzenia modułu (PCM) na urządzeniu z systemem Microsoft Azure StorSimple zawiera pakiet dodatkowych baterii. Ten pakiet zawiera zasilania, tak, aby urządzenia StorSimple może zapisywać dane w przypadku utraty zasilaniem obudową podstawowego. Ten pakiet baterii jest określany jako *modułu baterii zapasowej*. Moduł baterii zapasowej istnieje tylko w przypadku podstawowego obudowy w urządzeniu StorSimple (obudowy EBOD nie zawiera moduł baterii zapasowej).

W tym samouczku wyjaśniono:

* Usuń moduł baterii zapasowej
* Zainstaluj nowy moduł baterii zapasowej
* Obsługa modułu baterii zapasowej

> [!IMPORTANT]
> Zanim usunięcie i zastąpienie moduł baterii zapasowej bezpieczeństwa informacje przedstawione w [wprowadzenie do zastąpienia składnika sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="remove-the-backup-battery-module"></a>Usuń moduł baterii zapasowej
Moduł baterii zapasowej dla urządzenia StorSimple jest wymienne pola jednostki. Zanim zostanie ona zainstalowana w module PCM, moduł baterii powinny być przechowywane w oryginalnym opakowaniu. Wykonaj poniższe kroki, aby usunąć baterii zapasowej.

#### <a name="to-remove-the-backup-battery-module"></a>Aby usunąć moduł baterii zapasowej
1. W witrynie Azure portal przejdź do bloku usługi Menedżer urządzeń StorSimple. Przejdź do **urządzeń** a następnie wybierz urządzenie z listy urządzeń. Przejdź do **Monitor** > **kondycja sprzętu**. W obszarze **Shared Components**, Przyjrzyj się stan baterii.
2. Identyfikowanie modułu PCM, w którym baterii nie powiodło się. Rysunek 1 pokazuje z tyłu urządzenia StorSimple.
   
    ![Płyty montażowej modułów głównej Obudowa urządzenia](./media/storsimple-battery-replacement/IC740994.png)
   
    **Rysunek 1** tylnej stronie urządzenie podstawowe, przedstawiający PCM i kontroler modułów
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
   
    Przedstawiony przez numer 3 na rysunku 2 diod prowadzone w module PCM 0, który odpowiada **uszkodzenia** powinny być włączone.
   
    ![Montażowa LED wskaźnika monitorowania PCM urządzenia](./media/storsimple-battery-replacement/IC740992.png)
   
    **Rysunek 2** PCM z powrotem przedstawiający diod LED wskaźnika monitorowania
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Awarii zasilania programu AC |
   | 2 |Awaria wentylatora |
   | 3 |Błąd baterii |
   | 4 |PCM OK |
   | 5 |Kontroler domeny awarii zasilania |
   | 6 |Bateria w dobrej kondycji |
3. Aby usunąć PCM z baterii nie powiodło się, wykonaj kroki opisane w [Usuń PCM](storsimple-8000-power-cooling-module-replacement.md#remove-a-pcm).
4. PCM, usunięte przenoszenie i Obróć uchwytu modułu baterii w górę, wskazane na poniższej ilustracji i wyciągniesz go do usuwania baterii.
   
    ![Usuwanie bateria w module PCM](./media/storsimple-battery-replacement/IC741019.png)
   
    **Rysunek 3** usuwanie baterii modułu PCM
5. Umieść moduł w jednostkę field-replaceable unit pakowania.
6. Zwróć wadliwe urządzenie do firmy Microsoft odpowiednie obsługi i obsługi.

## <a name="install-a-new-backup-battery-module"></a>Zainstaluj nowy moduł baterii zapasowej
Wykonaj poniższe kroki, aby zainstalować moduł baterii zastąpienia w module PCM w głównej Obudowa urządzenia StorSimple.

#### <a name="to-install-the-battery-module"></a>Aby zainstalować moduł baterii
1. Moduł baterii zapasowej należy umieścić w odpowiednią orientację w module PCM.
2. Naciśnij uchwyt modułu baterii, aż do stanowisko łącznika.
3. Wymiana modułu PCM w obudowie podstawowego, postępując zgodnie z wytycznymi podanymi w [Zastąp zasilania i chłodzenia modułu na urządzeniu StorSimple](storsimple-8000-power-cooling-module-replacement.md).
4. Po zakończeniu zastąpienia go do Twojego urządzenia, a następnie przejdź do **Monitor** > **kondycja sprzętu** w witrynie Azure portal. Sprawdź stan baterii, aby upewnić się, że instalacja zakończyła się pomyślnie. Status zielony wskazuje, że baterii jest w dobrej kondycji.

## <a name="maintain-the-backup-battery-module"></a>Obsługa modułu baterii zapasowej
W urządzeniu StorSimple modułu baterii zapasowej zapewnia możliwości kontrolera podczas zdarzenia utraty zasilania. Umożliwia urządzeniu StorSimple można zapisać krytycznych danych przed zamykanie w kontrolowany sposób. Za pomocą dwóch pełni naładowana baterii w PCMs system może obsłużyć dwóch następujących po sobie utracie.

W witrynie Azure portal **kondycja sprzętu** w obszarze **Monitor** bloku wskazuje, czy działa poprawnie baterii zbliża się do zakończenia eksploatacji. Stan baterii jest wskazywany przez **bateria w module PCM 0** lub **bateria w module PCM 1** w obszarze **Shared Components**. Wyświetli ten blok **OBNIŻONY** dla zbliża się z eksploatacji, i dla **osiągnięto** koniec z eksploatacji.

> [!NOTE]
> Bateria może raportować po prostu zajdzie potrzeba jego **naliczane** opłaty.


Jeśli **OBNIŻONY** pojawi się stan, zaleca się następujące sposobu działania:

* System mogło dojść do ostatnich utraty zasilania lub baterie może poddawana okresowej konserwacji. Obserwacji systemu na 12 godzin przed kontynuowaniem.
  
  * Jeśli stan jest nadal **OBNIŻONY** po 12 godzin ciągłego połączenie programu AC power za pomocą kontrolerów i PCMs systemem, bateria musi zostać zastąpiony. Proszę [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) modułu baterii zapasowej zastępczy.
  * Jeśli stan staje się OK po 12 godzinach, bateria działa i wymagane tylko opłat konserwacji.
* Jeśli nie zostało powiązane straty zasilaniem, modułu PCM jest włączone i podłączone do zasilania Sieciowego baterii musi zostać zastąpiony. [Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) nakazać modułu baterii zapasowej zastępczy.

> [!IMPORTANT]
> Usuwanie nie powiodło się baterii, zgodnie z przepisami krajowych i regionalnych.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

