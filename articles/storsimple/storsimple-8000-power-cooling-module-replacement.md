---
title: Wymień pcm na urządzeniu z serii StorSimple 8000 | Dokumenty firmy Microsoft
description: Wyjaśniono, jak usunąć i wymienić moduł zasilania i chłodzenia (PCM) na urządzeniu StorSimple
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60632505"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Wymiana modułu zasilania i chłodzenia w urządzeniu StorSimple
## <a name="overview"></a>Omówienie
Moduł zasilania i chłodzenia (PCM) w urządzeniu Microsoft Azure StorSimple składa się z wentylatorów zasilania i chłodzenia, które są sterowane za pośrednictwem obudów podstawowych i EBOD. Istnieje tylko jeden model PCM, który jest certyfikowany dla każdej obudowy. Obudowa podstawowa jest certyfikowana dla 764 W PCM, a obudowa EBOD jest certyfikowana dla PCM 580 W. Chociaż pcm dla obudowy podstawowej i obudowy EBOD są różne, procedura wymiany jest identyczna.

W tym samouczku wyjaśniono:

* Usuwanie pcm
* Instalowanie zastępczego pcm

> [!IMPORTANT]
> Przed usunięciem i wymianą pcm należy zapoznać się z informacjami o bezpieczeństwie w [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Przed wymianą pcm
Przed wymianą komputera PCM należy pamiętać o następujących ważnych kwestiach:

* Jeśli zasilanie pcm ulegnie awarii, pozostaw uszkodzony moduł zainstalowany, ale zdejmij przewód zasilający. Wentylator będzie nadal odbierał zasilanie z obudowy i nadal zapewniał odpowiednie chłodzenie. Jeśli wentylator ulegnie awarii, pcm należy natychmiast wymienić.
* Przed wyjęciem pcm odłącz zasilanie od pcm, wyłączając przełącznik główny (tam, gdzie jest obecny) lub fizycznie wyjmując przewód zasilający. Zapewnia to ostrzeżenie dla systemu, że zamknięcie zasilania jest nieuchronne.
* Przed wymianą wadliwego pcm upewnij się, że drugi pcm działa w celu kontynuowania pracy systemu. Wadliwy pcm musi zostać zastąpiony w pełni działającym PCM tak szybko, jak to możliwe.
* Wymiana modułu PCM zajmuje tylko kilka minut, ale musi zostać zakończona w ciągu 10 minut od usunięcia nieudanego pcm, aby zapobiec przegrzaniu.
* Należy pamiętać, że wymienne moduły PCM 764 W dostarczane fabrycznie nie zawierają zapasowego modułu baterii. Przed wymianą należy wyjąć baterię z uszkodzonego modułu PCM, a następnie włożyć ją do modułu zastępczego. Aby uzyskać więcej informacji, zobacz jak [usunąć i włożyć zapasowy moduł baterii](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Usuwanie pcm
Postępuj zgodnie z tymi instrukcjami, gdy wszystko będzie gotowe do usunięcia modułu zasilania i chłodzenia (PCM) z urządzenia Microsoft Azure StorSimple.

> [!NOTE]
> Przed usunięciem komputera PCM sprawdź, czy masz prawidłową wymianę (764 W dla obudowy podstawowej lub 580 W dla obudowy EBOD).

#### <a name="to-remove-a-pcm"></a>Aby usunąć pcm
1. W klasycznym portalu platformy Azure kliknij pozycję **Ustawienia > Monitor > kondycja sprzętu**. Sprawdź stan składników PCM w obszarze **Składniki udostępnione,** aby określić, który komputer PCM nie powiódł się:
   
   * Jeśli zasilacz w PCM 0 nie powiódł się, stan **zasilacza w PCM 0** będzie czerwony.
   * Jeśli zasilacz w PCM 1 nie powiódł się, stan **zasilacza w PCM 1** będzie czerwony.
   * Jeśli wentylator w PCM 1 nie powiódł się, stan **chłodzenia 0 dla PCM 0** lub **chłodzenia 1 dla PCM 0** będzie czerwony.
2. Znajdź niedanych PCM z tyłu obudowy podstawowej. Jeśli korzystasz z modelu 8600, zidentyfikuj obudowę podstawową, patrząc na numer identyfikacyjny jednostki systemowej wyświetlany na wyświetlaczu LED panelu przedniego. Domyślny identyfikator jednostki wyświetlany w obudowie podstawowej to **00,** natomiast domyślny identyfikator jednostki wyświetlany w obudowie EBOD to **01**. Poniższy schemat i tabela wyjaśniają przedni panel wyświetlacza LED.
   
    ![Identyfikator systemu na przednim panelu OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Rysunek 1** Przedni panel urządzenia  
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Przycisk Wycisz |
   | 2 |Moc systemu |
   | 3 |Usterka modułu |
   | 4 |Błąd logiczny |
   | 5 |Wyświetlacz identyfikatora jednostki |
3. Diody LED ze wskaźnikiem monitorowania z tyłu obudowy głównej mogą być również używane do identyfikacji wadliwego PCM. Zobacz poniższy diagram i tabelę, aby dowiedzieć się, jak korzystać z diod LED, aby zlokalizować wadliwy pcm. Na przykład, jeśli dioda LED odpowiadająca **awarii wentylatora** jest zapalona, wentylator uległ awarii. Podobnie, jeśli dioda LED odpowiadająca **awarii AC** świeci się, zasilacz uległ awarii. 
   
    ![Płyta montażowa diod led monitorujących PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Rysunek 2** Tył PCM z diodami LED
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |Awaria zasilania prądem przemienna |
   | 2 |Awaria wentylatora |
   | 3 |Awaria baterii |
   | 4 |PCM OK |
   | 5 |Awaria zasilania prądem stałym |
   | 6 |Bateria zdrowa |
4. Aby zlokalizować nieudany moduł PCM, należy zapoznać się z poniższym diagramem z tyłu urządzenia StorSimple. PCM 0 znajduje się po lewej stronie, a PCM 1 po prawej stronie. Poniższa tabela wyjaśnia moduły.
   
     ![Zaoplanowa obudowy podstawowego urządzenia](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Rysunek 3** Tył urządzenia z modułami wtykowymi 
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
5. Wyłącz uszkodzony pcm i odłącz przewód zasilający. Teraz można usunąć PCM.
6. Chwyć zatrzask i bok uchwytu PCM między kciukiem a palcem wskazującym i ściśnij je razem, aby otworzyć uchwyt.
   
    ![Otwieranie uchwytu PCM](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Rysunek 4** Otwieranie uchwytu PCM
7. Chwyć uchwyt i zdejmij pcm.
   
    ![Usuwanie pcm urządzenia](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Rysunek 5** Usuwanie pcm

## <a name="install-a-replacement-pcm"></a>Instalowanie zastępczego pcm
Postępuj zgodnie z tymi instrukcjami, aby zainstalować pcm w urządzeniu StorSimple. Przed zainstalowaniem zastępczego modułu PCM należy upewnić się, że włożono zapasowy moduł baterii (dotyczy tylko 764 W PCM). Aby uzyskać więcej informacji, zobacz jak [usunąć i włożyć zapasowy moduł baterii](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Aby zainstalować pcm
1. Sprawdź, czy dla tej obudowy jest odpowiedni zastępczy moduł PCM. Obudowa podstawowa wymaga 764 W PCM, a obudowa EBOD wymaga 580 W PCM. Nie należy podejmować prób użycia 580 W PCM w obudowy podstawowej lub 764 W PCM w obudowie EBOD. Na poniższej ilustracji pokazano, gdzie można zidentyfikować te informacje na etykiecie umieszczonej na pcm.
   
    ![Etykieta PCM urządzenia](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Rysunek 6** Etykieta PCM
2. Sprawdź, czy nie ma uszkodzeń obudowy, zwracając szczególną uwagę na złącza. 
   
   > [!NOTE]
   > **Nie należy instalować modułu, jeśli jakieś styki złącza są wygięte.**
   > 
   > 
3. Gdy uchwyt PCM znajduje się w pozycji otwartej, wsuń moduł do obudowy.
   
    ![Instalowanie urządzenia PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Rysunek 7** Instalowanie pcm
4. Ręcznie zamknij uchwyt PCM. Powinieneś usłyszeć kliknięcie, gdy zatrzask uchwytu się zatrzasnie.
   
   > [!NOTE]
   > Aby upewnić się, że sworznie złącza zostały włączone, można delikatnie holować na uchwycie bez zwalniania zatrzasku. Jeśli PCM wysunie się, oznacza to, że zatrzask został zamknięty przed włączeniem złączy.
   
5. Podłącz kable zasilające do źródła zasilania i do pcm.
6. Zabezpieczyć bele odciążenia.
7. Włącz pcm.
8. Sprawdź, czy wymiana powiodła się: w witrynie Azure portal usługi StorSimple Device Manager przejdź do urządzenia, a następnie do **pozycji Ustawienia > Monitor > kondycja sprzętu**. W obszarze **Składniki udostępnione**stan PCM powinien być zielony.
   
   > [!NOTE]
   > Całkowite zainicjowanie zastępczego pcm może potrwać kilka minut.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

