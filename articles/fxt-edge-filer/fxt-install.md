---
title: Samouczek na temat instalowania urządzenia fizycznego filtr Edge FXT platformy Azure | Dokumentacja firmy Microsoft
description: Jak rozpakować, stojaku i Podłączanie kabli składnik urządzenia fizycznego pamięć podręczna programu Microsoft Azure FXT krawędzi filtr hybrydowego magazynu
services: ''
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: 45f4e12a06e012a06e0b2659d729fce3a191cd32
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450422"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Samouczek: Zainstaluj filtr Edge FXT platformy Azure 

W tym samouczku opisano sposób instalowania węzeł sprzęt na potrzeby pamięci podręcznej Azure FXT krawędzi filtr hybrydowego magazynu. Należy zainstalować co najmniej trzy węzły sprzętu, aby utworzyć klaster usługi Azure FXT krawędzi filtr.

Procedura instalacji obejmuje Rozpakowywanie i stojaka, instalowanie urządzenia i podłączenie kabla ramię zarządzania (CMA) oraz przedniej osłony. Oddzielne samouczku wyjaśniono, dołączający kable i Podłączanie zasilania. 

Trwa około godzinę instalacji węzła filtr Edge FXT platformy Azure. 

Ten samouczek obejmuje następujące kroki konfiguracji: 

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Instalowanie urządzenia w stojaku
> * Zainstaluj przedniej osłony (opcjonalnie)

## <a name="installation-prerequisites"></a>Wymagania wstępne instalacji 

Przed rozpoczęciem upewnij się, że centrum danych i stojaka, które będą używane następujące funkcje:

* 1U dostępne miejsce w stojaku gdzie zamierzasz zainstalować na urządzeniu.
* Zasilania Sieciowego i chłodzenie systemów spełniających potrzeby filtr Edge FXT platformy Azure. 

  Aby uzyskać pomoc, planowania i zmiany rozmiaru instalacji, przeczytaj [możliwości i specyfikacje termiczny](fxt-specs.md#power-and-thermal-specifications) informacje na temat oceny poziomu tytułowej i termiczny dane wyjściowe w typowych okolicznościach.  

  > [!NOTE] 
  > Do w pełni korzystać z dwóch jednostek dostaw nadmiarowe zasilanie (PSUs), należy użyć jednostki dystrybucji zasilania na dwa obwody inną gałąź podczas dołączania Przywrócenie zasilania sieciowego. Odczyt [Podłącz kable zasilania](fxt-network-power.md#connect-power-cables) Aby uzyskać szczegółowe informacje.  

## <a name="unpack-the-hardware-node"></a>Rozpakowywanie węzła sprzętu 

Każdy węzeł filtr Edge FXT Azure jest dostarczany w jednym polu. Wykonaj następujące kroki, aby rozpakować urządzenie.

1. Umieść pudełko na płaskiej, poziomej powierzchni.

2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.

3. Rozpakuj zawartość pudełka. Upewnij się, że zawiera on następujące elementy:
   * Jedno urządzenie FXT pojedynczego obudowy
   * dwa przewody zasilania,
   * Jedną ramką frontonu i klucz
   * Szyny jeden zestaw zestawu
   * Jeden kabel ramię zarządzania (CMA)
   * Broszury instrukcje instalacji CMA
   * Broszury instrukcje instalacji stojak
   * Broszury bezpieczeństwa, środowiska i informacje prawne

Jeśli nie masz wszystkie elementy na liście, skontaktuj się z dostawcą urządzenia pomocy technicznej. 

Upewnij się, że urządzenie ma wystarczająco dużo czasu, aby osiągnąć ten sam temperatury jako miejsca przed zainstalowaniem go, lub jego włączeniem. Jeśli zauważysz kondensacji na dowolną część urządzenia, poczekaj co najmniej 24 godziny przed rozpoczęciem instalacji.

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie Azure FXT krawędzi filtr musi być zainstalowane w standardowym stelażu 19-calowy. 

Pamięć podręczna magazynu dla hybrydowych filtr Edge FXT Azure składa się z trzech lub więcej urządzeń filtr Edge FXT platformy Azure. Powtórz kroki instalacji stojak dla każdego urządzenia, które jest częścią systemu. 

### <a name="rack-install-prerequisites"></a>Stojak instalowanie wstępnie wymaganego oprogramowania

* Przed rozpoczęciem zapoznaj się z instrukcjami bezpieczeństwa broszura bezpieczeństwa, środowiska i informacji prawnych, dostarczanej z Twoim urządzeniem.

  > [!NOTE]
  > Zawsze należy używać dwie osoby, podczas podnoszenia węźle, w tym przypadku zainstaluj go w stojaku, lub usuń go w stojaku. 

* Określ typ instalacji szyny używane z stelażu urządzenia. 
  * Dziura kwadratowym lub round przystawki stojakami znajduje się w instrukcji niewymagająca szyny.
  * Stojaki wątków dziura znajduje się w instrukcji tooled szyny. 
  
    Tooled szyny Instalowanie konfiguracji należy podać osiem śruby: typ 10 32, 12 – 24, M5 lub M6. Główny średnica śruby musi być mniejsza niż 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Zidentyfikować jej zawartość zestawu szyny

Znajdź składniki do zainstalowania zestawu kit szyny:

1. II ReadyRails Dell A7 w dwóch przedłużanie szyny zestawy (1)
1. Utworzenie punktu zaczepienia i pętli taśm (2)

![Rysowanie numerowane szyny zestaw treści](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Zestaw szyny - rails niewymagająca (dziura kwadratowy lub stojakami round dziura)

W przypadku stojaków za pomocą przystawki kwadratowym lub round luk w wykonaj tę procedurę, aby utworzyć i zainstalować szyny. 

1. Umieść elementy end szyny lewy i prawy etykietą **FRONTONU** skierowany do wewnątrz. Umieść każdy element end jej miejsca w otwory na przedniej stronie stopka stelażu pionowym. (1)

2. Wyrównuje każdej części zakończenia w dolnym i górnym otworów tor w miejscu, aby go zainstalować. (2)

3. Angażowanie zaplecza szyny dopóki nie jest w pełni stanowisk na listwie stelażu pionowym i kliknięć zatrzaśnięcia w miejscu. Powtórz te kroki, aby ustawić położenie i stanowisko element frontonu na listwie stelażu pionowym. (3)

> [!TIP]
> Aby usunąć rails, ściąganie zatrzaśnięcia przycisku wersji na element end punkt środkowy (4) i Wyjmij urządzenie każdego szyny.

![Diagram instalacji i usuwania narzędzi platformy rails, za pomocą kroków](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Zestaw szyny - tooled rails (Zhierarchizowany dziura stojakami)

W przypadku stojakami z wątków luki wykonaj tę procedurę, aby utworzyć i zainstalować szyny.

1. Usuń numery PIN z przodu i do tyłu wymiar z śrubokręta przebijaka. (1)
1. Ściąganie i Obróć podzespołów zatrzaśnięcia kolei, aby usunąć je z nawiasów instalowania. (2)
1. Dołączanie po lewej stronie i bezpośrednio instalowanie platformy rails, aby przy użyciu dwóch par śruby stopka frontonu stojak pionowy. (3)
1. Przesuń wstecz lewy i prawy nawiasy kwadratowe przekazywania względem stopka tylnej stojak pionowych i dołącz je przy użyciu dwóch par śruby. (4)

![Diagram instalacji i usuwania tooled rails za pomocą kroków](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Zainstaluj system w stojaku

Wykonaj następujące kroki, aby zainstalować urządzenia filtr Edge FXT platformy Azure w stojaku.

1. Ściągnij rails wewnętrzny slajdów ze stojaka, do momentu ich blokowanie w miejscu. (1)
1. Znajdź standoff tylnej szyny na każdej stronie urządzenia i zmniejszyć ich do tyłu gniazd "j" zestawów slajdów. (2) 
1. Obróć urządzenie w dół, aż wszystkie standoffs szyny są umieszczone w gnieździe "j". (3)
1. Wypchnij urządzenia do wewnątrz do momentu dźwigni blokady kliknij w miejscu.
1. Naciśnij przycisk blokady wersji slajdów na obie platformy rails (4) i przesuń urządzenia w stojaku.

![Zainstaluj system w schematu blokowego przy użyciu kroków](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Usuwanie systemu stojaku

Aby usunąć urządzenie z stojaku, wykonaj poniższą procedurę. 

1. Znajdź dźwigni blokadę na stronach rails wewnętrzny (1).
2. Uzyskaj dostęp do każdego suwaka obracając do jego wersji pozycji (2).
3. Mocno niejasny boki systemu, a następnie przeciągnij ją do przodu, aż standoffs szyny znajdują się na początku gniazd "j". Przenoszenie system w górę i w kierunku od stojaku i umieść go na poziomie powierzchni (3).

![Ilustracja usuwanie systemu z perspektywy regału sprzętowego, za pomocą kroków](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Angażowanie zatrzaśnięcia slam

1. Połączonego z przodu, zlokalizuj zatrzaśnięcia slam (1) po obu stronach systemu.
2. Zamków angażować się automatycznie, ponieważ system jest przesunięta w stojaku. 

Aby zwolnić zamków, podczas usuwania systemu, wyciągnij je w górę (2).

Opcjonalnie śruby twarde instalacji znajdują się do zabezpieczania systemu w stojaku, wydanie lub w innych środowiskach niestabilny. Znajdź śrubę w ramach każdego zatrzaśnięcia i zwiększyć ich z #2 krzyżakowy (3).

![Ilustracja interesujące i zwalniania zatrzaśnięcia slam numerowane](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Zainstaluj ramię zarządzania kabel 

Opcjonalny kabel ramię zarządzania (CMA) jest dostarczany z FXT filtr Edge. Drukowane instrukcje instalacji znajdują się w pakiecie. 

<!-- need current images in electronic form from Dell - these are scans -->

1. Rozpakowywanie i zidentyfikować składników zestawu ramię zarządzania kabel:
   * Na pasku CMA (1)
   * CMA (2)
   * Tie kabel nylonu zawija (3)
   * Nawiasy kwadratowe załącznika CMA (4)
   * Kabel wskaźnika stanu (5) 

   > [!TIP] 
   > Aby zabezpieczyć CMA do wydania w stojaku, pętli zawija tie wokół zarówno koszyki, jak i na pasku zadań i ich mocno cinch. Zabezpieczanie CMA w ten sposób spowoduje również zabezpieczeniu systemu w środowiskach niestabilny.

   ![Ilustracja części CMA](media/fxt-install/cma-parts-scan-400.png)

2. Zainstaluj CMA na pasku zadań.

   Na pasku CMA zapewnia obsługę i działa jako podtrzymującą dla CMA. 

   1. Wyrównaj i prezentować każdej stronie zasobnik w nawiasach kwadratowych odbiornika na wewnętrzne krawędzie szyny. 
   1. Wypchnij zasobnik do przodu do momentu kliknięcia go w miejscu. (1)
   1. Aby usunąć zasobnik, zmieścić przyciski wersji zatrzaśnięcia kierunku środka i ściąganie zasobnik poza nawiasy odbiorcy (2).

   ![Ilustracja CMA zasobnik instalacji](media/fxt-install/cma-tray-scan-400.png)

3. Zainstaluj CMA nawiasy załącznika. 

   > [!NOTE]
   >
   > * Możesz dołączyć CMA po prawej stronie lub w lewo, instalowania kolej, w zależności od tego, w jaki sposób planujesz kable trasy z systemu. 
   > * Dla wygody należy zainstalować CMA po stronie odwrotnej do źródła zasilania (po stronie A). Jeśli jest zainstalowany na stronie B, CMA muszą zostać przerwane, aby usunąć zewnętrzne zasilacz. 
   > * Zawsze usunąć zasobnik dostarcza zasilania. 

   ![Ilustracja CMA nawiasu instalacji](media/fxt-install/cma-brackets-scan-400.png)

   1. Wybierz odpowiednie nawiasu załącznika CMA dla strony, w którym chcesz zainstalować CMA (po stronie B lub po stronie A).
   1. Zainstaluj CMA nawiasu załącznika z odpowiedniego A strony lub oznaczenia po stronie B tyłu szyny slajdów.
   1. Wyrównaj luki w nawias z pinterest na szynie slajdów. Wypchnij nawias w dół, dopóki nie blokuje go w miejscu. 

4. Zainstaluj CMA.

   1. Tyłu systemu mieszczą się zatrzaśnięcia na frontonie CMA na najbardziej wewnętrzny nawias zestawu slajdów do czasu zatrzaśnięcia angażuje (1). 
   1. Dopasuj zatrzaśnięcia na końcu nawiasu prowadzące do czasu zatrzaśnięcia angażuje (2). 
   1. Aby usunąć CMA, należy odłączyć zarówno zamków, naciskając klawisz przyciski wersji CMA w górnej części obudowy zatrzaśnięcia wewnętrznych i zewnętrznych (3).

   ![Ilustracja głównej CMA instalacji](media/fxt-install/cma-install-scan-400.png)

   CMA można obracać od systemu pod kątem dostępu i usług. Na końcu zawiasowych przenoszenie CMA od zasobnik do wyjmij go (1) urządzenie. Po jej zakończeniu unseated do zasobnika typu swing CMA od systemu, (2).

   ![Ilustracja CMA obracać otwarte na potrzeby usługi](media/fxt-install/cma-open-scan-400.png)

## <a name="install-the-front-bezel-optional"></a>Zainstaluj przedniej osłony (opcjonalnie)

W tej sekcji opisano sposób instalowania i usuwania przedniej osłony (czołowej) dla sprzętu filtr Edge FXT platformy Azure. 

Aby zainstalować przedniej osłony: 

1. Zlokalizuj i Usuń klucz ramką, który jest dostarczany w pakiecie osłony. 
1. Wyrównaj ramką z przodu obudowy i Wstaw numerów PIN, po prawej stronie osłony do otworów na listwie instalacji stojak po prawej stronie węzła. 
1. Dopasuj lewy koniec ramką na obudowie. Naciśnij klawisz ramka, do momentu kliknięcia przycisków, które po lewej stronie w miejscu.
1. Zablokuj ramką przy użyciu klucza.

Aby usunąć przedniej osłony: 
1. Odblokuj ramką przy użyciu klucza osłony.
1. Naciśnij przycisk wersji po lewej stronie i ściąganie z lewego końca ramką od obudowy.
1. Odpięcie prawym końcu, a następnie usuń osłony.
   
   ![Obraz przedstawiający przycisk wersji po lewej stronie ramką i sposobach jego usuwania przez pobieranie na zewnątrz z lewej strony](media/fxt-install/remove-bezel-updated-600.png)

## <a name="next-steps"></a>Kolejne kroki

Po rozpakowane i urządzenia mają kontynuować instalację, dołączanie kable sieciowe i łączenie zasilacza filtr Edge FXT platformy Azure.

> [!div class="nextstepaction"]
> [Podłącz portów sieciowych i podać zasilania](fxt-network-power.md)