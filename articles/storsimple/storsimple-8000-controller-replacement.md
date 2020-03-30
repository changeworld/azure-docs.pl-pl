---
title: Wymień kontroler urządzenia storsimple serii 8000 | Dokumenty firmy Microsoft
description: W tym artykule wyjaśniono, jak usunąć i wymienić jeden lub oba moduły kontrolera na urządzeniu z serii StorSimple 8000.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267926"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Wymień moduł kontrolera na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i zastąpić jeden lub oba moduły kontrolera w urządzeniu StorSimple. W tym artykule omówiono również podstawową logikę scenariuszy wymiany pojedynczego i podwójnego kontrolera.

> [!NOTE]
> Przed wykonaniem wymiany kontrolera zaleca się, aby zawsze aktualizować oprogramowanie układowe kontrolera do najnowszej wersji.
> 
> Aby zapobiec uszkodzeniu urządzenia StorSimple, nie wysuwaj kontrolera, dopóki diody LED nie będą wyświetlane jako jedna z następujących czynności:
> 
> * Wszystkie światła są wyłączone.
> * LED ![3, zielona](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png)ikona wyboru i ![ikona](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) czerwonego krzyża migają, a diody LED 0 i LED 7 są **włączone.**


W poniższej tabeli przedstawiono scenariusze wymiany obsługiwanego kontrolera.

| Sprawa | Scenariusz wymiany | Stosowana procedura |
|:--- |:--- |:--- |
| 1 |Jeden kontroler jest w stanie awarii, drugi kontroler jest w dobrej kondycji i aktywny. |[Wymiana pojedynczego kontrolera,](#replace-a-single-controller)która opisuje [logikę wymiany pojedynczego kontrolera,](#single-controller-replacement-logic)a także [kroki wymiany.](#single-controller-replacement-steps) |
| 2 |Oba kontrolery uległy awarii i wymagają wymiany. Obudowa, dyski i obudowa dysku są w dobrej kondycji. |[Wymiana podwójnego kontrolera,](#replace-both-controllers)która opisuje [logikę wymiany podwójnego kontrolera,](#dual-controller-replacement-logic)a także [kroki wymiany.](#dual-controller-replacement-steps) |
| 3 |Kontrolery z tego samego urządzenia lub z różnych urządzeń są wymieniane. Obudowy, dyski i obudowy dysków są w dobrej kondycji. |Pojawi się komunikat alertu o niezgodności gniazda. |
| 4 |Brakuje jednego kontrolera, a drugi kontroler kończy się niepowodzeniem. |[Wymiana podwójnego kontrolera,](#replace-both-controllers)która opisuje [logikę wymiany podwójnego kontrolera,](#dual-controller-replacement-logic)a także [kroki wymiany.](#dual-controller-replacement-steps) |
| 5 |Jeden lub oba kontrolery nie powiodły się. Nie można uzyskać dostępu do urządzenia za pośrednictwem konsoli szeregowej lub komunikacji zdalnej programu Windows PowerShell. |[Skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu uzyskania procedury ręcznej wymiany kontrolera. |
| 6 |Kontrolery mają inną wersję kompilacji, co może być spowodowane:<ul><li>Kontrolery mają inną wersję oprogramowania.</li><li>Kontrolery mają inną wersję oprogramowania układowego.</li></ul> |Jeśli wersje oprogramowania kontrolera są różne, logika zastępowania wykrywa to i aktualizuje wersję oprogramowania na kontrolerze zastępczym.<br><br>Jeśli wersje oprogramowania układowego kontrolera są różne, a stara wersja oprogramowania układowego **nie** jest automatycznie uaktualniany, w witrynie Azure portal pojawi się komunikat alertu. Należy skanować w poszukiwaniu aktualizacji i instalować aktualizacje oprogramowania układowego.</br></br>Jeśli wersje oprogramowania układowego kontrolera są różne, a stara wersja oprogramowania układowego jest automatycznie uaktualniany, logika wymiany kontrolera wykryje to, a po uruchomieniu kontrolera oprogramowanie układowe zostanie automatycznie zaktualizowane. |

Należy usunąć moduł kontrolera, jeśli nie powiodło się. Jeden lub oba moduły kontrolera mogą zakończyć się niepowodzeniem, co może spowodować wymianę pojedynczego kontrolera lub wymianę podwójnego kontrolera. Procedury zastępowania i logika za nimi, zobacz następujące:

* [Wymiana jednego kontrolera](#replace-a-single-controller)
* [Wymień oba kontrolery](#replace-both-controllers)
* [Usuwanie kontrolera](#remove-a-controller)
* [Wstawianie kontrolera](#insert-a-controller)
* [Identyfikowanie aktywnego kontrolera w urządzeniu](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Przed wyjęciem i wymianą kontrolera należy zapoznać się z informacjami o bezpieczeństwie w [części Wymiany komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Wymiana jednego kontrolera
Gdy jeden z dwóch kontrolerów na urządzeniu Microsoft Azure StorSimple uległ awarii, działa nieprawidłowo lub brakuje, należy zastąpić jeden kontroler.

### <a name="single-controller-replacement-logic"></a>Logika wymiany pojedynczego kontrolera
W przypadku wymiany pojedynczego kontrolera należy najpierw usunąć kontroler, który uległ awarii. (Pozostały kontroler w urządzeniu jest aktywnym kontrolerem). Po wstawieniu kontrolera zastępczego występują następujące akcje:

1. Kontroler zastępczy natychmiast rozpoczyna komunikację z urządzeniem StorSimple.
2. Migawka wirtualnego dysku twardego (VHD) dla aktywnego kontrolera jest kopiowana na kontrolerze zastępczym.
3. Migawka jest modyfikowana tak, że po uruchomieniu kontrolera zastępczego z tego dysku VHD, zostanie rozpoznany jako kontroler rezerwowy.
4. Po zakończeniu modyfikacji kontroler zastępczy zostanie uruchomiony jako kontroler rezerwowy.
5. Gdy oba kontrolery są uruchomione, klaster jest w trybie online.

### <a name="single-controller-replacement-steps"></a>Pojedyncze kroki wymiany kontrolera
Wykonaj następujące kroki, jeśli jeden z kontrolerów w urządzeniu Microsoft Azure StorSimple ulegnie awarii. (Drugi kontroler musi być aktywny i uruchomiony. Jeśli oba kontrolery uchylią awarię lub usterkę, przejdź do [kroków wymiany z dwoma kontrolerami](#dual-controller-replacement-steps).)

> [!NOTE]
> Może upłynąć 30 – 45 minut, aby kontroler ponownie uruchomić i całkowicie odzyskać z procedury wymiany pojedynczego kontrolera. Całkowity czas całej procedury, w tym podłączania kabli, wynosi około 2 godzin.


#### <a name="to-remove-a-single-failed-controller-module"></a>Aby usunąć pojedynczy moduł kontrolera, który uległ awarii
1. W witrynie Azure portal przejdź do usługi StorSimple Device Manager, kliknij pozycję **Urządzenia**, a następnie kliknij nazwę urządzenia, które chcesz monitorować.
2. Przejdź do **monitoruj > kondycję sprzętu**. Stan kontrolera 0 lub kontrolera 1 powinien być czerwony, co oznacza błąd.
   
   > [!NOTE]
   > Nieudany kontroler w wymianie pojedynczego kontrolera jest zawsze kontrolerem rezerwowym.
   
3. Użyj rysunek 1 i poniższej tabeli, aby zlokalizować moduł kontrolera nie powiodło się.
   
    ![Zaoplanowa obudowy podstawowego urządzenia](./media/storsimple-controller-replacement/IC740994.png)
   
    **Rysunek 1** Tylne urządzenie StorSimple
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na kontrolerze, który uległ awarii, usuń wszystkie podłączone kable sieciowe z portów danych. Jeśli używasz modelu 8600, należy również usunąć kable SAS, które łączą kontroler z kontrolerem EBOD.
5. Wykonaj kroki opisane w [umyć kontroler,](#remove-a-controller) aby usunąć kontroler, który uległ awarii.
6. Zainstaluj wymianę fabryczną w tym samym gnieździe, z którego usunięto nieudany kontroler. Spowoduje to wyzwolenie logiki zastępowania pojedynczego kontrolera. Aby uzyskać więcej informacji, zobacz [logikę wymiany pojedynczego kontrolera](#single-controller-replacement-logic).
7. Podczas gdy logika wymiany pojedynczego kontrolera postępuje w tle, podłącz ponownie kable. Należy uważać, aby podłączyć wszystkie kable dokładnie tak samo, jak były podłączone przed wymianą.
8. Po ponownym uruchomieniu kontrolera sprawdź **stan kontrolera** i **stan klastra** w witrynie Azure Portal, aby sprawdzić, czy kontroler jest z powrotem w dobrej kondycji i jest w trybie gotowości.

> [!NOTE]
> Jeśli monitorujesz urządzenie za pośrednictwem konsoli szeregowej, może być widocznych wiele ponownych uruchomień, gdy kontroler jest odzyskiwanie z procedury wymiany. Po wyświetleniu menu konsoli szeregowej wiadomo, że wymiana została zakończona. Jeśli menu nie pojawi się w ciągu dwóch godzin od rozpoczęcia wymiany kontrolera, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
>
> Począwszy od aktualizacji 4, można `Get-HCSControllerReplacementStatus` również użyć polecenia cmdlet w interfejsie programu Windows PowerShell urządzenia do monitorowania stanu procesu zastępowania kontrolera.
> 

## <a name="replace-both-controllers"></a>Wymień oba kontrolery
Jeśli oba kontrolery na urządzeniu Microsoft Azure StorSimple uległy awarii, działają nieprawidłowo lub brakuje, należy wymienić oba kontrolery. 

### <a name="dual-controller-replacement-logic"></a>Logika wymiany dwóch kontrolerów
W wymianie z dwoma kontrolerami najpierw usunięto oba kontrolery, a następnie wstawisz zamienniki. Po wstawieniu dwóch kontrolerów zastępczych występują następujące akcje:

1. Kontroler zastępczy w gnieździe 0 sprawdza, co następuje:
   
   1. Czy używa aktualnych wersji oprogramowania układowego i oprogramowania?
   2. Czy jest to część klastra?
   3. Czy kontroler równorzędny jest uruchomiony i czy jest klastrowany?
      
      Jeśli żaden z tych warunków nie jest spełniony, kontroler wyszukuje najnowszą codzienną kopię zapasową (znajdującą się w **nonDOMstorage** na dysku S). Kontroler kopiuje najnowszą migawkę dysku VHD z kopii zapasowej.
2. Kontroler w gnieździe 0 używa migawki do obrazu.
3. Tymczasem kontroler w gnieździe 1 czeka na kontroler 0, aby zakończyć obrazowanie i uruchomić.
4. Po uruchomieniu kontrolera 0 kontroler 1 wykrywa klaster utworzony przez kontroler 0, który wyzwala logikę wymiany pojedynczego kontrolera. Aby uzyskać więcej informacji, zobacz [logikę wymiany pojedynczego kontrolera](#single-controller-replacement-logic).
5. Następnie oba kontrolery będą uruchomione, a klaster przejdzie do trybu online.

> [!IMPORTANT]
> Po wymianie z dwoma kontrolerami, po skonfigurowaniu urządzenia StorSimple, konieczne jest wykonywanie ręcznej kopii zapasowej urządzenia. Codzienne kopie zapasowe konfiguracji urządzenia są wyzwalane dopiero po upływie 24 godzin. Skontaktuj się z [pomocą techniczną firmy Microsoft,](storsimple-8000-contact-microsoft-support.md) aby wykonać ręczną kopię zapasową urządzenia.


### <a name="dual-controller-replacement-steps"></a>Kroki wymiany dwóch kontrolerów
Ten przepływ pracy jest wymagany, gdy oba kontrolery w urządzeniu Microsoft Azure StorSimple uległy awarii. Może się to zdarzyć w centrum danych, w którym system chłodzenia przestaje działać, a w rezultacie oba kontrolery uchylić się w krótkim czasie. W zależności od tego, czy urządzenie StorSimple jest wyłączone lub włączone oraz czy używasz modelu 8600 czy 8100, wymagany jest inny zestaw kroków.

> [!IMPORTANT]
> Może upłynąć od 45 minut do 1 godziny, aby kontroler ponownie uruchomić i całkowicie odzyskać po podwójnej procedurze wymiany kontrolera. Całkowity czas całej procedury, w tym podłączania kabli, wynosi około 2,5 godziny.

#### <a name="to-replace-both-controller-modules"></a>Aby wymienić oba moduły kontrolera
1. Jeśli urządzenie jest wyłączone, pomiń ten krok i przejdź do następnego kroku. Jeśli urządzenie jest włączone, wyłącz urządzenie.
   
   1. Jeśli używasz modelu 8600, najpierw wyłącz obudowę podstawową, a następnie wyłącz obudowę EBOD.
   2. Poczekaj, aż urządzenie całkowicie się wyłączy. Wszystkie diody LED z tyłu urządzenia będą wyłączone.
2. Usuń wszystkie kable sieciowe podłączone do portów danych. Jeśli używasz modelu 8600, należy również usunąć kable SAS, które łączą obudowę podstawową z obudową EBOD.
3. Usuń oba kontrolery z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [usuwanie kontrolera](#remove-a-controller).
4. Najpierw włóż fabrycznie zamiennik sterownika 0, a następnie włóż kontroler 1. Aby uzyskać więcej informacji, zobacz [wstawianie kontrolera](#insert-a-controller). Spowoduje to uruchomienie logiki wymiany dwóch kontrolerów. Aby uzyskać więcej informacji, zobacz [logikę wymiany dwóch kontrolerów](#dual-controller-replacement-logic).
5. Podczas gdy logika wymiany kontrolera postępuje w tle, podłącz ponownie kable. Należy uważać, aby podłączyć wszystkie kable dokładnie tak samo, jak były podłączone przed wymianą. Szczegółowe instrukcje dotyczące modelu można znaleźć w sekcji Kabel urządzenia [do zainstalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstalować urządzenie StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Włącz urządzenie StorSimple. Jeśli używasz modelu 8600:
   
   1. Upewnij się, że obudowa EBOD jest włączona jako pierwsza.
   2. Poczekaj, aż obudowa EBOD jest uruchomiona.
   3. Włącz obudowę podstawową.
   4. Po ponownym uruchomieniu pierwszego kontrolera i jest w stanie dobrej kondycji, system będzie uruchomiony.
      
      > [!NOTE]
      > Jeśli monitorujesz urządzenie za pośrednictwem konsoli szeregowej, może być widocznych wiele ponownych uruchomień, gdy kontroler jest odzyskiwanie z procedury wymiany. Gdy pojawi się menu konsoli szeregowej, wiesz, że wymiana została zakończona. Jeśli menu nie pojawi się w ciągu 2,5 godziny od rozpoczęcia wymiany kontrolera, [skontaktuj się z pomocą techniczną firmy Microsoft](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Usuwanie kontrolera
Poniższa procedura służy do usuwania wadliwego modułu kontrolera z urządzenia StorSimple.

> [!NOTE]
> Poniższe ilustracje są dla kontrolera 0. W przypadku kontrolera 1 zostaną one odwrócone.


#### <a name="to-remove-a-controller-module"></a>Aby usunąć moduł kontrolera
1. Chwyć zatrzask modułu między kciukiem a palcem wskazującym.
2. Delikatnie ściśnij kciuk i palec wskazujący, aby zwolnić zatrzask kontrolera.
   
    ![Zwalnianie zatrzasku kontrolera](./media/storsimple-controller-replacement/IC741047.png)
   
    **Rysunek 2** Zwalnianie zatrzasku kontrolera
3. Za pomocą zatrzasku należy wysunąć kontroler z obudowy.
   
    ![Sterownik przesuwny z obudowy](./media/storsimple-controller-replacement/IC741048.png)
   
    **Rysunek 3** Wysuwanie sterownika z obudowy

## <a name="insert-a-controller"></a>Wstawianie kontrolera
Poniższa procedura służy do instalowania fabrycznie dostarczonego modułu kontrolera po usunięciu wadliwego modułu z urządzenia StorSimple.

#### <a name="to-install-a-controller-module"></a>Aby zainstalować moduł kontrolera
1. Sprawdź, czy nie ma uszkodzeń złączy interfejsu. Nie należy instalować modułu, jeśli którykolwiek z pinów złącza jest uszkodzony lub wygięty.
2. Wsuń moduł kontrolera do obudowy, gdy zatrzask zostanie całkowicie zwolniony.
   
    ![Sterownik przesuwny do obudowy](./media/storsimple-controller-replacement/IC741053.png)
   
    **Rysunek 4** Sterownik przesuwny do obudowy
3. Po włożeniu modułu sterownika zacznij zamykać zatrzask, kontynuując wciskanie modułu kontrolera do obudowy. Zatrzask załączy się, aby poprowadzić kontroler na miejsce.
   
    ![Zatrzaśnięcia kontrolera zamykania](./media/storsimple-controller-replacement/IC741054.png)
   
    **Rysunek 5** Zamykanie zatrzasku sterownika
4. Skończysz, gdy zatrzask zaskoczy na swoim miejscu. Dioda **LED OK** powinna być teraz wprowadzona.
   
   > [!NOTE]
   > Aktywacja sterownika i diody LED może potrwać do 5 minut.
  
5. Aby sprawdzić, czy zastąpienie zakończyło się pomyślnie, w witrynie Azure Portal przejdź do urządzenia, a następnie przejdź do > **monitoruj kondycję sprzętu**i upewnij się, że zarówno kontroler 0, jak i kontroler 1 są w dobrej kondycji (stan jest zielony). **Monitor**

## <a name="identify-the-active-controller-on-your-device"></a>Identyfikowanie aktywnego kontrolera w urządzeniu
Istnieje wiele sytuacji, takich jak rejestracja urządzenia po raz pierwszy lub zastąpienie kontrolera, które wymagają zlokalizowania aktywnego kontrolera na urządzeniu StorSimple. Aktywny kontroler przetwarza wszystkie operacje oprogramowania układowego dysku i sieci. Do identyfikacji aktywnego kontrolera można użyć dowolnej z następujących metod:

* [Identyfikowanie aktywnego kontrolera za pomocą portalu Azure](#use-the-azure-portal-to-identify-the-active-controller)
* [Identyfikowanie aktywnego kontrolera za pomocą programu Windows PowerShell for StorSimple](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Sprawdź urządzenie fizyczne, aby zidentyfikować aktywny kontroler](#check-the-physical-device-to-identify-the-active-controller)

Każda z tych procedur jest opisana w następnej kolejności.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Identyfikowanie aktywnego kontrolera za pomocą portalu Azure
W witrynie Azure portal przejdź do urządzenia, a następnie do **monitorowania** > **kondycji sprzętu**i przewiń do sekcji **Kontrolery.** W tym miejscu można sprawdzić, który kontroler jest aktywny.

![Identyfikowanie aktywnego kontrolera w witrynie Azure portal](./media/storsimple-controller-replacement/IC752072.png)

**Rysunek 6** Portal Azure z aktywnym kontrolerem

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Identyfikowanie aktywnego kontrolera za pomocą programu Windows PowerShell for StorSimple
Po dodaniu dostępu do urządzenia za pośrednictwem konsoli szeregowej zostanie wyświetlony komunikat banera. Komunikat banera zawiera podstawowe informacje o urządzeniu, takie jak model, nazwa, zainstalowana wersja oprogramowania i stan kontrolera, do którego uzyskujesz dostęp. Na poniższej ilustracji przedstawiono przykład komunikatu banerowego:

![Seryjny komunikat banera](./media/storsimple-controller-replacement/IC741098.png)

**Rysunek 7** Komunikat banera z kontrolerem 0 jako aktywny

Za pomocą komunikatu banera można określić, czy kontroler, z którego masz połączenie, jest aktywny czy pasywny.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Sprawdź urządzenie fizyczne, aby zidentyfikować aktywny kontroler
Aby zidentyfikować aktywny kontroler w urządzeniu, znajdź niebieską diodę LED nad portem DATA 5 z tyłu obudowy podstawowej.

Jeśli ta dioda LED miga, kontroler jest aktywny, a drugi kontroler jest w trybie czuwania. Użyj poniższego diagramu i tabeli jako pomocy.

![Obudowa podstawowa urządzenia z portami danych](./media/storsimple-controller-replacement/IC741055.png)

**Wykres 8** Tylna obudowa podstawowa z portami danych i diodami LED do monitorowania

| Label | Opis |
|:--- |:--- |
| 1-6 |DATA 0 – 5 portów sieciowych |
| 7 |Niebieska dioda LED |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymianie komponentów sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

