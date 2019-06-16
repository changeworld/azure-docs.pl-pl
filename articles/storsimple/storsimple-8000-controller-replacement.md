---
title: Wymiana kontrolera urządzeń StorSimple 8000 series | Dokumentacja firmy Microsoft
description: Wyjaśnia, jak usunąć i Zastąp jeden lub oba moduły kontrolera na urządzeniu serii StorSimple 8000.
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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61482874"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Zastąp moduł kontrolera na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i Zastąp jeden lub oba moduły kontrolera na urządzeniu StorSimple. Omawia także podstawowej logiki dla scenariuszy zastąpienie jednym lub dwoma kontrolera.

> [!NOTE]
> Przed wykonaniem zastępczego kontrolera, zalecamy zawsze zaktualizować oprogramowanie układowe kontrolera do najnowszej wersji.
> 
> Aby zapobiec uszkodzeniu urządzenia StorSimple, nie wysunąć kontrolera do momentu diody LED są wyświetlane jako jedna z następujących czynności:
> 
> * Wszystkie światła są wyłączone.
> * LED 3 ![zielona ikona znacznika wyboru](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), i ![ikonę czerwony krzyżyk](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) miga, a LED 0 i LED 7 są **ON**.


W poniższej tabeli przedstawiono scenariusze zastąpienie obsługiwanych kontrolera.

| przypadek | Scenariusza wymiany | Odpowiednie procedury |
|:--- |:--- |:--- |
| 1 |Jeden kontroler jest w stanie niepowodzenia, inny kontroler jest w dobrej kondycji i aktywne. |[Pojedynczy kontroler zastąpienie](#replace-a-single-controller), która opisuje [logiki stojącej za zastępuje pojedynczy kontroler](#single-controller-replacement-logic), jak również [zamienne](#single-controller-replacement-steps). |
| 2 |Oba kontrolery nie powiodło się i wymaga zastąpienia. Podstawa montażowa, dyski i obudowy dysku są w dobrej kondycji. |[Zastąpienie podwójną kontrolera](#replace-both-controllers), opisano [logiki stojącej za zastępczego kontrolera podwójną](#dual-controller-replacement-logic), jak również [zamienne](#dual-controller-replacement-steps). |
| 3 |Kontrolerów z tego samego urządzenia lub z różnych urządzeń zostały zamienione. Podstawa montażowa, dysków i obudów dysków są w dobrej kondycji. |Zostanie wyświetlony komunikat alertu niezgodność miejsca. |
| 4 |Brak jednego kontrolera i inne kontroler nie powiedzie się. |[Zastąpienie podwójną kontrolera](#replace-both-controllers), opisano [logiki stojącej za zastępczego kontrolera podwójną](#dual-controller-replacement-logic), jak również [zamienne](#dual-controller-replacement-steps). |
| 5 |Jeden lub oba kontrolery nie powiodło się. Nie można uzyskać dostęp do urządzenia za pośrednictwem konsoli szeregowej lub komunikacji zdalnej programu Windows PowerShell. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) procedury wymiany ręczne kontrolera. |
| 6 |Kontrolery są w wersji różne kompilacje, które mogą okazać się ze względu na:<ul><li>Kontrolery mają różne programowa wersja.</li><li>Kontrolery mają wersję innego oprogramowania układowego.</li></ul> |W przypadku różnych wersji oprogramowania kontrolera logiki zastąpienie wykrywa ich brak i aktualizuje wersji oprogramowania na kontrolerze zastępczy.<br><br>Jeśli kontroler wersji oprogramowania układowego są różne, i jest stara wersja oprogramowania układowego **nie** automatycznie możliwość uaktualnienia alertu zostanie wyświetlony komunikat w witrynie Azure portal. Należy skanowania w poszukiwaniu aktualizacji i zainstalować aktualizacje oprogramowania układowego.</br></br>Jeśli wersje oprogramowania układowego kontrolera są różne, a stara wersja oprogramowania układowego jest możliwość automatycznego uaktualnienia, logiki zastępczego kontrolera wykryje to i po uruchomieniu kontrolera, oprogramowanie układowe zostaną automatycznie zaktualizowane. |

Musisz usunąć moduł kontrolera, jeśli nie powiodła się. Jeden lub oba moduły kontroler może zakończyć się niepowodzeniem, co może spowodować zastąpienie jednego kontrolera lub zastąpienie podwójną kontrolera. Procedury wymiany i logiki zobacz następujące tematy:

* [Wymiana jednego kontrolera](#replace-a-single-controller)
* [Zastąp oba kontrolery](#replace-both-controllers)
* [Usunąć kontroler](#remove-a-controller)
* [Wstaw kontrolera](#insert-a-controller)
* [Identyfikowanie aktywnego kontrolera na urządzeniu z systemem](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Zanim usunięcie i zastąpienie kontrolera, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Wymiana jednego kontrolera
Jeden z dwóch kontrolerów na urządzeniu Microsoft Azure StorSimple nie powiodło się, działa prawidłowo lub brakuje musisz wymiana jednego kontrolera.

### <a name="single-controller-replacement-logic"></a>Logika zastąpienie jednego kontrolera
W zastąpienia jednego kontrolera należy najpierw usunąć kontroler nie powiodło się. (Pozostałe kontrolera na urządzeniu jest aktywny kontroler). Podczas wstawiania kontrolera zastąpienia są wykonywane następujące akcje:

1. Kontroler zastąpienie natychmiast uruchamia komunikacji z urządzeniem StorSimple.
2. Migawki wirtualnego dysku twardego (VHD) dla aktywnego kontrolera jest kopiowana na kontrolerze zastępczy.
3. Migawki został zmodyfikowany tak, aby po uruchomieniu kontrolera wymiany z tego dysku VHD zostanie rozpoznana jako kontroler zapasowy.
4. Po zakończeniu modyfikacji kontrolera zastąpienie rozpocznie się jako kontroler zapasowy.
5. Gdy oba kontrolery są uruchomione, klaster przejściu do trybu online.

### <a name="single-controller-replacement-steps"></a>Kroki zastąpienie jednego kontrolera
Wykonaj poniższe kroki, jeśli jeden z kontrolerów w urządzeniu StorSimple systemu Azure firmy Microsoft nie powiedzie się. (Inne kontroler musi być aktywności i działania. Jeśli oba kontrolery się nie powieść lub nieprawidłowe działanie, przejdź do strony [kroki zastąpienie podwójną kontrolera](#dual-controller-replacement-steps).)

> [!NOTE]
> Może potrwać 30 – 45 minut dla kontrolera ponownie uruchomić i całkowicie odzyskać z procedury wymiany jednego kontrolera. Łączny czas całą procedurę dołączania kable, w tym to około 2 godziny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Aby usunąć moduł jednego kontrolera nie powiodło się
1. W witrynie Azure portal przejdź do usługi Menedżer urządzeń StorSimple, kliknij przycisk **urządzeń**, a następnie kliknij nazwę urządzenia, które chcesz monitorować.
2. Przejdź do **Monitor > kondycja sprzętu**. Stan kontrolera 0 i Controller 1 należy czerwony, która wskazuje błąd.
   
   > [!NOTE]
   > Nie powiodło się kontrolera w zastępuje pojedynczy kontroler jest zawsze kontroler zapasowy.
   
3. Znajdź moduł kontrolera nie powiodło się, korzystając rysunek 1 i w poniższej tabeli.
   
    ![Płyty montażowej modułów głównej Obudowa urządzenia](./media/storsimple-controller-replacement/IC740994.png)
   
    **Rysunek 1** urządzenia StorSimple z tyłu
   
   | Label | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontroler 0 |
   | 4 |Kontroler 1 |
4. Na kontrolerze nie powiodło się Usuń wszystkie kable sieciowe połączone z portów danych. Jeśli jest używany model 8600, należy także usunąć kabli SAS, łączących się kontrolera EBOD z kontrolerem.
5. Postępuj zgodnie z instrukcjami w [usunąć kontroler](#remove-a-controller) Aby usunąć kontroler nie powiodło się.
6. W tym samym miejsce, z którego usunięto kontrolera nie powiodło się, należy zainstalować zastępczy fabryki. Spowoduje to wyzwolenie logiki zastąpienie jednego kontrolera. Aby uzyskać więcej informacji, zobacz [pojedynczy kontroler zastąpienie logiki](#single-controller-replacement-logic).
7. Gdy logiki zastąpienie jednego kontrolera w miarę w tle, ponownie podłącz kable. Należy zadbać, aby połączyć wszystkie kable ten sam sposób, że zostały one połączone przed zastąpienia.
8. Po ponownym uruchomieniu kontrolera, sprawdź **stan kontrolera** i **klastra stan** w witrynie Azure portal, aby sprawdzić, czy kontroler jest w dobrej kondycji i jest w stanie wstrzymania.

> [!NOTE]
> Jeśli monitorujesz urządzenia za pośrednictwem konsoli szeregowej, może zostać wyświetlony wielokrotne ponowne uruchomienie, gdy kontroler jest w trakcie odzyskiwania z procedury wymiany. Umieszczeniem przedstawionym menu konsoli szeregowej następnie wiesz, że zastąpienie zostało zakończone. Jeśli w ciągu dwóch godzin od zastępczego kontrolera nie ma menu, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Począwszy od aktualizacji Update 4, możesz również użyć polecenia cmdlet `Get-HCSControllerReplacementStatus` w interfejsie programu Windows PowerShell urządzenia, aby monitorować stan procesu wymiany kontrolera.
> 

## <a name="replace-both-controllers"></a>Zastąp oba kontrolery
Gdy obu kontrolerów, na urządzeniu Microsoft Azure StorSimple nie powiodło się, jest uszkodzona lub brakuje, musisz zastąpić obu kontrolerów. 

### <a name="dual-controller-replacement-logic"></a>Podwójna kontrolera zastąpienie logiki
W zastąpienia podwójną kontrolera najpierw usuń oba kontrolery nie powiodło się, a następnie Wstaw zamiany. Podczas wstawiania kontrolerów dwa zastąpienia są wykonywane następujące akcje:

1. Kontroler zastąpienia w gnieździe 0 sprawdza następujące elementy:
   
   1. To korzystasz aktualne wersje oprogramowania układowego i oprogramowania?
   2. To jest częścią klastra?
   3. Jest kontroler elementu równorzędnego z systemem i czy jest klastrowany?
      
      Jeśli żaden z tych warunków jest spełniony, kontroler szuka najnowsze codzienne wykonywanie kopii zapasowych (znajdujący się w **nonDOMstorage** na dysku S). Kontroler kopiuje najnowszą migawkę wirtualnego dysku twardego z kopii zapasowej.
2. Kontroler w gnieździe 0 korzysta z migawki utworzonej na sam obraz.
3. W tym samym czasie kontroler w gnieździe 1 czeka, aż kontrolera 0, aby ukończyć tworzenia obrazu i uruchomić.
4. Po uruchomieniu kontrolera 0 kontrolera 1 wykrywa klastra, utworzone przez kontrolera 0, wyzwala logiki zastąpienie jednego kontrolera. Aby uzyskać więcej informacji, zobacz [pojedynczy kontroler zastąpienie logiki](#single-controller-replacement-logic).
5. Później ma działać na obu kontrolerach, a klaster zostanie przejdzie w tryb online.

> [!IMPORTANT]
> Następujące zastąpienia podwójną kontrolera po skonfigurowaniu urządzenia StorSimple niezbędne jest wykonanie ręcznego tworzenia kopii zapasowych urządzenia. Codzienne kopie zapasowe konfiguracji urządzenia nie są wyzwalane do czasu, po upływie 24 godzin. Praca z [Microsoft Support](storsimple-8000-contact-microsoft-support.md) się ręcznego tworzenia kopii zapasowej na urządzeniu.


### <a name="dual-controller-replacement-steps"></a>Kroki zastąpienie podwójną kontrolera
Ten przepływ pracy jest wymagany, gdy oba kontrolery w urządzeniu StorSimple systemu Azure firmy Microsoft nie powiodło się. Może się to zdarzyć w centrum danych, w której chłodzenia systemu przestaje działać, a w rezultacie obu kontrolerów się nie powieść w krótkim okresie czasu. W zależności od tego, czy urządzenie StorSimple jest wyłączona i tego, czy używasz urządzenia 8600 lub 8100 model innego zestawu kroków jest wymagana.

> [!IMPORTANT]
> Do 1 godziny dla kontrolera ponownie uruchomić i całkowicie odzyskać z procedury wymiany podwójną kontroler może potrwać 45 minut. Łączny czas przez cały czas w tym dołączenie kable, jest około 2,5 godziny.

#### <a name="to-replace-both-controller-modules"></a>Aby zastąpić oba moduły kontrolera
1. Jeśli urządzenie jest wyłączone, Pomiń ten krok i przejść do następnego kroku. Jeśli urządzenie jest włączone, należy wyłączyć urządzenia.
   
   1. Jeśli jest używany model 8600, wyłączyć funkcję podstawowego obudowy pierwszy, a następnie wyłącz obudowy EBOD.
   2. Zaczekaj, aż urządzenie został całkowicie zamknięty. Wszystkie diod LED tyłu urządzenia zostaną wyłączone.
2. Usuń wszystkie kable sieciowe, które są podłączone do portów danych. Jeśli jest używany model 8600, należy także usunąć kabli SAS, łączących głównej obudowy obudową EBOD.
3. Usuń oba kontrolery z urządzeniem StorSimple. Aby uzyskać więcej informacji, zobacz [usunąć kontroler](#remove-a-controller).
4. Najpierw Wstaw zastąpienia fabryki dla kontrolera 0, a następnie Wstaw kontrolera 1. Aby uzyskać więcej informacji, zobacz [Wstaw kontrolera](#insert-a-controller). Spowoduje to wyzwolenie logiką zastąpienie podwójną kontrolera. Aby uzyskać więcej informacji, zobacz [logiką zastępczego kontrolera podwójną](#dual-controller-replacement-logic).
5. Podczas gdy logiką zastępczego kontrolera w miarę w tle, ponownie podłącz kable. Należy zadbać, aby połączyć wszystkie kable ten sam sposób, że zostały one połączone przed zastąpienia. Zobacz szczegółowe instrukcje dotyczące modelu kabla sekcji urządzenia [instalowania urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [instalowania urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Włącz urządzenie StorSimple. Jeśli jest używany model 8600:
   
   1. Upewnij się, czy obudowa EBOD jest włączony w pierwszej.
   2. Zaczekaj, aż obudowy EBOD jest uruchomiona.
   3. Włącz funkcję podstawowego obudowy.
   4. Po ponownym uruchomieniu pierwszego kontrolera są w dobrej kondycji, system zostanie uruchomiony.
      
      > [!NOTE]
      > Jeśli monitorujesz urządzenia za pośrednictwem konsoli szeregowej, może zostać wyświetlony wielokrotne ponowne uruchomienie, gdy kontroler jest w trakcie odzyskiwania z procedury wymiany. Gdy pojawi się menu konsoli szeregowej, następnie wiesz, że zastąpienie zostało zakończone. Jeśli nie ma menu w ramach 2,5 godziny od zastępczego kontrolera, [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Usunąć kontroler
Użyj poniższej procedury można usunąć modułu kontroler awaryjnym z urządzenia StorSimple.

> [!NOTE]
> Poniższa ilustracja są przeznaczone dla kontrolera 0. Dla kontrolera 1 te będzie można cofnąć.


#### <a name="to-remove-a-controller-module"></a>Aby usunąć moduł kontrolera
1. Zapoznanie się z nim zatrzaśnięcia modułu między thumb i palec.
2. Delikatnie zmieścić swoje thumb i palec ze sobą, aby zwolnić zatrzaśnięcia kontrolera.
   
    ![Zwalnianie zatrzaśnięcia kontrolera](./media/storsimple-controller-replacement/IC741047.png)
   
    **Rysunek 2** zwolnienie kontrolera zatrzaśnięcie
3. Użyj zatrzaśnięcia jako uchwyt do slajdu kontrolera poza obudowy.
   
    ![Przedłużanie kontrolera poza obudowy](./media/storsimple-controller-replacement/IC741048.png)
   
    **Rysunek 3** przedłużanie kontrolera poza obudowy

## <a name="insert-a-controller"></a>Wstaw kontrolera
Użyj poniższej procedury do zainstalowania modułu dostarczony fabryki kontrolerów, po usunięciu wadliwe modułu na urządzeniu StorSimple.

#### <a name="to-install-a-controller-module"></a>Aby zainstalować moduł kontrolera
1. Sprawdź, czy szkody z łącznikami interfejsu. Nie należy instalować modułu, jeśli jakiekolwiek numerów PIN łącznika są uszkodzone lub psia.
2. Umożliwia wsunąć moduł kontrolera do obudowy, podczas gdy zatrzaśnięcia jest w pełni wydane.
   
    ![Przedłużanie kontrolera do obudowy](./media/storsimple-controller-replacement/IC741053.png)
   
    **Rysunek 4** ruchomej kontrolera do obudowy
3. Z modułem kontrolera wstawione rozpoczyna zamykanie zatrzaśnięcia przerywając wypychanie moduł kontrolera do obudowy. Zatrzaśnięcia będą angażować się przeprowadzenie kontrolera w miejscu.
   
    ![Zamykanie zatrzaśnięcia kontrolera](./media/storsimple-controller-replacement/IC741054.png)
   
    **Rysunek 5** zamykanie zatrzaśnięcia kontrolera
4. Wszystko gotowe, gdy zatrzaśnięcia zostanie zablokowany na miejscu. **OK** LED będą znajdować się na.
   
   > [!NOTE]
   > Może upłynąć do 5 minut, zanim kontroler i diod LED, aby aktywować.
  
5. Aby sprawdzić, czy zastąpienia zakończy się powodzeniem, w witrynie Azure portal, przejdź do urządzenia, a następnie przejdź do **Monitor** > **kondycja sprzętu**i upewnij się, że zarówno kontrolera 0 i kontrolera 1 są w dobrej kondycji (stan jest zielony).

## <a name="identify-the-active-controller-on-your-device"></a>Identyfikowanie aktywnego kontrolera na urządzeniu z systemem
Istnieje wiele sytuacji, takie jak zastąpienie rejestracji lub kontrolera urządzenia po raz pierwszy, wymagających do zlokalizowania aktywnego kontrolera na urządzeniu StorSimple. Aktywny kontroler przetwarza wszystkie dysku oprogramowania układowego i sieci operacje. Na identyfikowanie aktywnego kontrolera, można użyć dowolnej z następujących metod:

* [Identyfikowanie aktywnego kontrolera za pomocą witryny Azure portal](#use-the-azure-portal-to-identify-the-active-controller)
* [Identyfikowanie aktywnego kontrolera za pomocą programu Windows PowerShell dla usługi StorSimple](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Sprawdź urządzenie fizyczne na identyfikowanie aktywnego kontrolera](#check-the-physical-device-to-identify-the-active-controller)

Każda z tych procedur jest opisane w dalszej części.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Identyfikowanie aktywnego kontrolera za pomocą witryny Azure portal
W witrynie Azure portal przejdź do Twojego urządzenia i następnie **Monitor** > **kondycja sprzętu**i przewiń do **kontrolerów** sekcji. W tym miejscu możesz sprawdzić, który kontroler jest aktywny.

![Identyfikowanie aktywnego kontrolera w witrynie Azure portal](./media/storsimple-controller-replacement/IC752072.png)

**Rysunek 6** witryny Azure portal pokazujący aktywnego kontrolera

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Identyfikowanie aktywnego kontrolera za pomocą programu Windows PowerShell dla usługi StorSimple
Gdy uzyskujesz dostęp do urządzenia za pośrednictwem konsoli szeregowej, zobaczy komunikat transparentu. Komunikat transparentu zawiera urządzenia podstawowe informacje, takie jak modelu, nazwa, wersja zainstalowanego oprogramowania i stan kontrolera, które uzyskują dostęp do. Na poniższej ilustracji przedstawiono przykładowy komunikat transparentu:

![Komunikat transparentu szeregowej](./media/storsimple-controller-replacement/IC741098.png)

**Rysunek 7** komunikat transparentu przedstawiający kontrolera 0 jako aktywny

Komunikat transparentu służy do określenia, czy są podłączone do kontrolera jest aktywnych lub pasywnych.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Sprawdź urządzenie fizyczne na identyfikowanie aktywnego kontrolera
Identyfikowanie aktywnego kontrolera na urządzeniu, zlokalizować niebieski prowadzone dane 5 portu na odwrocie podkładki głównej obudowy.

Jeśli migotania to LED kontrolera jest aktywny i inny kontroler jest w stanie wstrzymania. Skorzystaj z następujących diagram i tabeli jako pomoc.

![Montażowa głównej Obudowa urządzenia przy użyciu portów danych](./media/storsimple-controller-replacement/IC741055.png)

**Rysunek 8** tylnej stronie głównej obudowy za pomocą danych portów i diod LED monitorowania

| Label | Opis |
|:--- |:--- |
| 1-6 |Interfejs DATA 0 – 5 porty sieciowe |
| 7 |Diody LED |

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o [wymiana składników sprzętu StorSimple](storsimple-8000-hardware-component-replacement.md).

