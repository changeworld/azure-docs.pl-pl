---
title: Zastąp kontrolera urządzenia z serii StorSimple 8000 | Dokumentacja firmy Microsoft
description: Wyjaśniono, jak usunąć i Zastąp jeden lub oba moduły kontroler na urządzeniu z serii StorSimple 8000.
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
ms.openlocfilehash: 849eccff114c2fd6d952e44d095d0cc89a238675
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875389"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Zastąp modułu kontroler na urządzeniu StorSimple
## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak usunąć i Zastąp jeden lub oba modułów kontrolera w urządzeniu StorSimple. Zawiera omówienie również podstawowej logiki w scenariuszach zastępczy jednym lub dwoma kontrolera.

> [!NOTE]
> Przed wykonaniem zastępczy kontrolera, zalecamy zawsze aktualizacji oprogramowania układowego kontrolera do najnowszej wersji.
> 
> Aby zapobiec uszkodzeniu urządzenia StorSimple, nie wysuwania kontrolera do momentu LED są wyświetlane jako jedną z następujących czynności:
> 
> * Wszystkie świateł są wyłączone.
> * LED 3 ![zielona ikona znacznika wyboru](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), i ![między czerwona ikona](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) miga, a LED 0 i LED 7 **ON**.


W poniższej tabeli przedstawiono scenariusze zastępczy obsługiwanych kontrolera.

| Case | Scenariusza wymiany | Odpowiednie procedury |
|:--- |:--- |:--- |
| 1 |Jeden kontroler jest w stanie niepowodzenia, inny kontroler jest w dobrej kondycji i aktywne. |[Pojedynczy kontroler zastępczy](#replace-a-single-controller), która opisuje [logiki zastępuje pojedynczy kontroler](#single-controller-replacement-logic), jak również [zamienne](#single-controller-replacement-steps). |
| 2 |Zarówno kontrolery nie powiodło się i wymaga zastąpienia. Obudowa dysków i obudowy dysku są w dobrej kondycji. |[Zastąpienie podwójną kontrolera](#replace-both-controllers), która opisuje [logiki zastępczy podwójną kontrolera](#dual-controller-replacement-logic), jak również [zamienne](#dual-controller-replacement-steps). |
| 3 |Kontrolerów z tego samego urządzenia lub z różnych urządzeń są zamienione. Obudowa dysków i obudów dysków są w dobrej kondycji. |Zostanie wyświetlony komunikat alertu niezgodność miejsca. |
| 4 |Brak jednego kontrolera i inny kontroler kończy się niepowodzeniem. |[Zastąpienie podwójną kontrolera](#replace-both-controllers), która opisuje [logiki zastępczy podwójną kontrolera](#dual-controller-replacement-logic), jak również [zamienne](#dual-controller-replacement-steps). |
| 5 |Nie ma jednego lub obu kontrolerów. Nie można uzyskać dostęp do urządzenia za pośrednictwem konsoli szeregowej lub komunikacji zdalnej programu Windows PowerShell. |[Skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md) procedury wymiany ręczne kontrolera. |
| 6 |Kontrolery są w wersji różnych kompilacji, która może być:<ul><li>Kontrolery mają wersję innego oprogramowania.</li><li>Kontrolery mają wersję innego oprogramowania układowego.</li></ul> |W przypadku różnych wersji oprogramowania kontrolera logiki zastępczy wykrycia, że i aktualizuje wersję oprogramowania na zastępczego kontrolera.<br><br>Jeśli kontroler wersji oprogramowania układowego są różne, a jest stara wersja oprogramowania układowego **nie** automatycznie możliwej komunikat ostrzegawczy będą wyświetlane w portalu Azure. Należy skanowania w poszukiwaniu aktualizacji i zainstaluj aktualizacje oprogramowania układowego.</br></br>Wersje oprogramowania układowego kontrolera są różne, stara wersja oprogramowania układowego jest automatycznie możliwej logiki zastępczego kontrolera wykryje to i po uruchomieniu kontrolera, oprogramowanie układowe zostanie automatycznie zaktualizowana. |

Należy usunąć moduł kontrolera, jeśli nie powiodło się. Jeden lub oba moduły kontroler może zakończyć się niepowodzeniem, co może spowodować zastąpienie pojedynczy kontroler lub zastąpienie podwójną kontrolera. Procedury wymiany i logiki zobacz następujące tematy:

* [Wymiana jednego kontrolera](#replace-a-single-controller)
* [Zastąp obu kontrolerów](#replace-both-controllers)
* [Usuwanie kontrolera](#remove-a-controller)
* [Wstaw kontrolera](#insert-a-controller)
* [Zidentyfikuj active kontroler na urządzeniu](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Przed usunięcie i zastąpienie kontrolera, zapoznaj się z informacjami bezpieczeństwa w [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Zastąp jednego kontrolera
Podczas jeden z dwóch kontrolerów na urządzeniu Microsoft Azure StorSimple nie powiodła się, jest uszkodzona lub brakuje, należy zastąpić pojedynczy kontroler.

### <a name="single-controller-replacement-logic"></a>Pojedynczy kontroler zastępczy logiki
W zastąpienia pojedynczy kontroler najpierw należy usunąć kontrolera nie powiodło się. (Pozostałe kontroler w urządzeniu jest aktywny.) Po włożeniu zastępczego kontrolera są wykonywane następujące akcje:

1. Zastępczego kontrolera natychmiast rozpoczyna komunikacji z urządzeniem StorSimple.
2. Migawki wirtualnego dysku twardego (VHD) na aktywnym kontrolerze jest kopiowana na zastępczego kontrolera.
3. Migawki są modyfikowane, aby po uruchomieniu zastępczego kontrolera z tym dyskiem VHD zostanie rozpoznany jako kontroler wstrzymania.
4. Po zakończeniu modyfikacji zastępczego kontrolera zostanie uruchomione jako rezerwy kontrolera.
5. Uruchamiając obu kontrolerów, klaster przejściu do trybu online.

### <a name="single-controller-replacement-steps"></a>Pojedynczy kontroler zastępczy kroki
Jeśli jeden z kontrolerów w urządzeniu Microsoft Azure StorSimple ulegnie awarii, wykonaj następujące kroki. (Inne kontrolera musi być aktywne i uruchomiona. Jeśli zarówno kontrolery się nie powieść lub nieprawidłowe działanie, przejdź do [kontrolera dwa zastąpienia kroki](#dual-controller-replacement-steps).)

> [!NOTE]
> Może potrwać 30 – 45 minut dla kontrolera ponownie uruchomić i całkowicie odzyskać z procedury wymiany pojedynczy kontroler. Łączny czas całą procedurę dołączanie kable, w tym jest około 2 godziny.


#### <a name="to-remove-a-single-failed-controller-module"></a>Aby usunąć moduł pojedynczy kontroler nie powiodło się
1. W portalu Azure, przejdź do usługi Menedżer urządzeń StorSimple, kliknij przycisk **urządzeń**, a następnie kliknij nazwę urządzenia, które chcesz monitorować.
2. Przejdź do **Monitor > kondycji sprzętu**. Stan kontrolera 0 lub 1 kontroler powinien być czerwony, co wskazuje błąd.
   
   > [!NOTE]
   > Nie powiodło się kontrolera w zastępuje pojedynczy kontroler jest zawsze rezerwy kontrolera.
   
3. Użyj rysunek 1 i poniższej tabeli można znaleźć modułu kontrolera nie powiodło się.
   
    ![Płyty montażowej urządzenia podstawowego obudowy modułów](./media/storsimple-controller-replacement/IC740994.png)
   
    **Rysunek 1** urządzenia StorSimple z powrotem
   
   | Etykieta | Opis |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Kontrolera 0 |
   | 4 |Kontrolera 1 |
4. Na kontrolerze nie powiodło się Usuń wszystkie kable połączenia sieciowego z portów danych. Jeśli używasz modelu 8600 także usunąć kabli SAS, łączących się z kontrolerem EBOD z kontrolerem.
5. Postępuj zgodnie z instrukcjami [usunąć kontroler](#remove-a-controller) Aby móc usunąć kontroler nie powiodło się.
6. W tym samym gnieździe, z którego usunięto kontroler nie powiodło się, należy zainstalować zastępczy fabryki. Spowoduje to zainicjowanie logiki zastępczy pojedynczy kontroler. Aby uzyskać więcej informacji, zobacz [jednego kontrolera zastępczy logiki](#single-controller-replacement-logic).
7. Podczas logiki zastępczy pojedynczy kontroler kontynuowana w tle, ponownie połącz kable. Należy zadbać, aby połączyć wszystkie kable ten sam sposób, że zostały one połączone przed zastąpienia.
8. Po ponownym uruchomieniu kontrolera, sprawdź **stan kontrolera** i **klastra stan** w portalu Azure, aby sprawdzić, czy kontroler ma dobrej kondycji i jest w stanie wstrzymania.

> [!NOTE]
> Jeśli monitorowanych urządzeń za pośrednictwem konsoli szeregowej, mogą pojawić się wielokrotne ponowne uruchomienie, gdy kontroler Trwa odzyskiwanie z procedury wymiany. Gdy menu konsoli szeregowej jest wyświetlana, następnie wiesz o ukończeniu zastąpienia. Jeśli menu nie zostanie wyświetlone w ciągu dwóch godzin początkowych zastąpienia kontrolera, skontaktuj się z [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> Począwszy od aktualizacji 4, można również użyć polecenia cmdlet `Get-HCSControllerReplacementStatus` w interfejsie programu Windows PowerShell urządzenia do monitorowania stanu procesu wymiany kontrolera.
> 

## <a name="replace-both-controllers"></a>Zastąp obu kontrolerów
Po obu kontrolerów na urządzeniu Microsoft Azure StorSimple nie powiodło się, jest uszkodzona lub brakuje, należy zastąpić obu kontrolerów. 

### <a name="dual-controller-replacement-logic"></a>Podwójna kontrolera zastępczy logiki
W zastąpienia podwójną kontrolera najpierw usuń obu kontrolerów nie powiodło się, a następnie wstawić zamian. Gdy kontrolery dwa zastąpienia są wstawiane, wykonywane są następujące akcje:

1. Kontroler zastąpienia w miejscu 0 sprawdza następujące elementy:
   
   1. Jest on przy użyciu bieżącej wersji oprogramowania układowego i oprogramowania?
   2. Jest on częścią klastra?
   3. Jest uruchomiony kontroler elementu równorzędnego i się w klastrze?
      
      Jeśli żaden z tych warunków nie jest spełniony, kontrolera szuka najnowszej kopii zapasowej codziennie (znajdujące się w **nonDOMstorage** na dysku w S). Kontroler kopiuje najnowszą migawkę wirtualnego dysku twardego z kopii zapasowej.
2. Kontroler gniazda 0 używa migawki samego obrazu.
3. W tym samym czasie kontroler do gniazda 1 oczekuje dla kontrolera 0, aby zakończyć tworzenie obrazów i uruchomić.
4. Po uruchomieniu kontrolera 0 kontrolera 1 wykryje klastra utworzone przez kontrolera 0, wyzwalające logiki zastępczy pojedynczy kontroler. Aby uzyskać więcej informacji, zobacz [jednego kontrolera zastępczy logiki](#single-controller-replacement-logic).
5. Później będzie działać zarówno kontrolery i klaster przejdzie w tryb online.

> [!IMPORTANT]
> Następujące zastąpienia podwójną kontrolera po skonfigurowaniu urządzenia StorSimple, bardzo ważne jest wykonanie ręcznego tworzenia kopii zapasowych urządzenia. Codzienne kopie zapasowe konfiguracji urządzenia nie są wyzwalane dopiero po upływie 24 godzin. Praca z [Microsoft Support](storsimple-8000-contact-microsoft-support.md) dokonanie ręcznego tworzenia kopii zapasowych urządzenia.


### <a name="dual-controller-replacement-steps"></a>Kroki zastępczy podwójną kontrolera
Ten przepływ pracy jest wymagany, gdy oba kontrolerów w Microsoft Azure StorSimple urządzenia nie powiodło się. Taka sytuacja może wystąpić w centrum danych, w której chłodzenia system przestanie działać, a w związku z tym obu kontrolerów w trybie w krótkim czasie. W zależności od tego, czy urządzenie StorSimple jest wyłączona i określa, czy używasz 8600 lub 8100 model innego zestawu kroków jest wymagana.

> [!IMPORTANT]
> Może upłynąć 45 minut na 1 godzinę dla kontrolera ponownie uruchomić i całkowicie odzyskać z podwójnym kontrolerem procedury zastąpienia. Łączny czas całą procedurę dołączanie kable, w tym jest około 2,5 godzin.

#### <a name="to-replace-both-controller-modules"></a>Aby zastąpić zarówno modułów kontrolera
1. Jeśli urządzenie jest wyłączone, Pomiń ten krok i przejdź do następnego kroku. Jeśli urządzenie jest włączone, wyłącz urządzenie.
   
   1. Jeśli używasz modelu 8600 Wyłącz głównej obudowa pierwszy, a następnie wyłącz obudowa EBOD.
   2. Zaczekaj, aż urządzenie ma całkowicie wyłączyć. Wszystkie LED tyłu urządzenia będą wyłączone.
2. Usuń wszystkie kable sieciowe, które są podłączone do portów danych. Jeśli używasz modelu 8600 również usunąć kabli SAS, które nawiązać głównej obudowa obudowa EBOD.
3. Usuń obu kontrolerów z urządzenia StorSimple. Aby uzyskać więcej informacji, zobacz [usunąć kontroler](#remove-a-controller).
4. Najpierw Wstaw zastąpienia fabryki dla kontrolera 0, a następnie Wstaw kontrolera 1. Aby uzyskać więcej informacji, zobacz [Wstaw kontrolera](#insert-a-controller). Spowoduje to zainicjowanie logiki zastępczy podwójną kontrolera. Aby uzyskać więcej informacji, zobacz [kontrolera dwa zastąpienia logiki](#dual-controller-replacement-logic).
5. Podczas logiki zastępczego kontrolera kontynuowana w tle, ponownie połącz kable. Należy zadbać, aby połączyć wszystkie kable ten sam sposób, że zostały one połączone przed zastąpienia. Zobacz szczegółowe instrukcje dotyczące modelu kabla sekcji urządzenia [zainstalować do urządzenia StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstalować do urządzenia StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Włącz na urządzeniu StorSimple. Jeśli używasz modelu 8600:
   
   1. Upewnij się, czy obudowa EBOD jest włączony w pierwszej.
   2. Zaczekaj, aż obudowa EBOD jest uruchomiona.
   3. Włącz obudowa podstawowego.
   4. Po uruchomieniu pierwszego kontrolera jest w dobrej kondycji, system zostanie uruchomiony.
      
      > [!NOTE]
      > Jeśli monitorowanych urządzeń za pośrednictwem konsoli szeregowej, mogą pojawić się wielokrotne ponowne uruchomienie, gdy kontroler Trwa odzyskiwanie z procedury wymiany. Po wyświetleniu menu konsoli szeregowej następnie wiadomo, że zastąpienia została zakończona. Jeśli menu nie zostanie wyświetlone w ciągu godziny 2,5 początkowych zastąpienia kontrolera, skontaktuj się z [skontaktuj się z Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Usuwanie kontrolera
Poniższa procedura umożliwia usunąć moduł uszkodzony kontroler z urządzenia StorSimple.

> [!NOTE]
> Poniższe ilustracje są przeznaczone dla kontrolera 0. Dla kontrolera 1 te będzie można cofnąć.


#### <a name="to-remove-a-controller-module"></a>Aby usunąć moduł kontrolera
1. Ujmij zatrzaśnięcia modułu między thumb i wskazującym.
2. Ostrożnie zmieścić przycisku suwaka, a wskazującym ze sobą w celu zwolnienia zatrzaśnięcia kontrolera.
   
    ![Zwalnianie zatrzaśnięcia kontrolera](./media/storsimple-controller-replacement/IC741047.png)
   
    **Rysunek 2** zwolnienie zatrzaśnięcia kontrolera
3. Umożliwia zatrzaśnięcia jako uchwyt slajd kontrolera poza obudowy.
   
    ![Przedłużanie kontrolera poza obudowy](./media/storsimple-controller-replacement/IC741048.png)
   
    **Rysunek 3** przedłużanie kontrolera poza podstawę montażową

## <a name="insert-a-controller"></a>Wstaw kontrolera
Poniższej procedury należy zainstalować moduł dostarczone przez fabrykę kontrolerów, po usunięciu błędny modułu z urządzenia StorSimple.

#### <a name="to-install-a-controller-module"></a>Aby zainstalować moduł kontrolera
1. Sprawdź, czy jest szkody łączniki interfejsu. Nie należy instalować moduł, jeśli dowolne numerów PIN łącznika są uszkodzone lub psia.
2. Slajd moduł kontrolera do obudowy podczas zatrzaśnięcia pełni zostanie zwolniony.
   
    ![Przedłużanie kontrolera do podstawy](./media/storsimple-controller-replacement/IC741053.png)
   
    **Rysunek 4** ruchomej kontrolera do obudowy
3. Modułem kontrolera wstawiony rozpoczyna zamykanie zatrzaśnięcia pozostawiając wypchnąć moduł kontrolera do obudowy. Zatrzaśnięcia zastosują prowadzące z kontrolerem na miejscu.
   
    ![Zamykanie zatrzaśnięcia kontrolera](./media/storsimple-controller-replacement/IC741054.png)
   
    **Rysunek 5** zamknięcie zatrzaśnięcia kontrolera
4. Wszystko będzie gotowe, gdy zatrzaśnięcia zostanie zablokowany na miejscu. **OK** LED powinno być teraz na.
   
   > [!NOTE]
   > Może potrwać do 5 minut kontroler i LED do aktywacji.
  
5. Aby sprawdzić, czy zastąpienia zakończy się powodzeniem, w portalu Azure, przejdź do urządzenia, a następnie przejdź do **Monitor** > **kondycji sprzętu**i upewnij się, że zarówno kontrolera 0 i kontrolera 1 są w dobrej kondycji (stan jest zielony).

## <a name="identify-the-active-controller-on-your-device"></a>Zidentyfikuj active kontroler na urządzeniu
Istnieje wiele sytuacji, takie jak zastąpienie rejestracji lub kontrolera urządzenia po raz pierwszy, wymagających zlokalizować active kontroler na urządzeniu StorSimple. Kontroler active przetwarza wszystkie dysku oprogramowania układowego i sieci operacje. Do identyfikowania kontroler usługi active, można użyć dowolnej z następujących metod:

* [Użyj portalu Azure, aby zidentyfikować kontroler active](#use-the-azure-portal-to-identify-the-active-controller)
* [Zidentyfikuj kontroler active za pomocą programu Windows PowerShell dla urządzenia StorSimple](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Sprawdź urządzenie fizyczne do identyfikowania active kontrolera](#check-the-physical-device-to-identify-the-active-controller)

Każdy z tych procedur jest opisane w dalszej części.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Użyj portalu Azure, aby zidentyfikować kontroler active
W portalu Azure, przejdź do urządzenia, a następnie do **Monitor** > **kondycji sprzętu**i przewiń do **kontrolerów** sekcji. W tym miejscu można sprawdzić, który kontroler jest aktywny.

![Zidentyfikuj kontroler active w portalu Azure](./media/storsimple-controller-replacement/IC752072.png)

**Rysunek 6** portalu Azure pokazujący active kontrolera

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Zidentyfikuj kontroler active za pomocą programu Windows PowerShell dla urządzenia StorSimple
Gdy uzyskujesz dostęp do urządzenia za pośrednictwem konsoli szeregowej, zobaczy komunikat transparentu. Komunikat transparentu zawiera urządzenia podstawowe informacje, takie jak model, nazwę, wersję zainstalowanego oprogramowania i stan kontrolera, które uzyskują dostęp do. Na poniższej ilustracji przedstawiono przykład komunikat transparentu:

![Komunikat transparentu szeregowe](./media/storsimple-controller-replacement/IC741098.png)

**Rysunek 7** komunikat transparentu przedstawiający kontrolera 0 jako aktywny

Komunikat transparentu służy do ustalenia, czy są podłączone do kontrolera jest aktywnych lub pasywnych.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Sprawdź urządzenie fizyczne do identyfikowania active kontrolera
Aby zidentyfikować kontroler active na urządzeniu, zlokalizuj niebieski LED powyżej portu dane 5 tyłu obudowy podstawowego.

Jeśli migotania LED tego kontrolera jest aktywna, i inny kontroler jest w stanie wstrzymania. Skorzystaj z poniższych diagram i tabeli jako pomoc.

![Urządzenia IDE głównej obudowa z porty danych](./media/storsimple-controller-replacement/IC741055.png)

**Rysunek 8** obu głównej obudowa z portami danych i monitorowania LED

| Etykieta | Opis |
|:--- |:--- |
| 1-6 |DANE 0 – 5 porty sieciowe |
| 7 |Niebieski LED |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [wymiana składników sprzętowych StorSimple](storsimple-8000-hardware-component-replacement.md).

