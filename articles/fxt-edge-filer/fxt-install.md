---
title: 'Samouczek: Instalowanie urządzenia fizycznego usługi Azure FXT Edge Filer'
description: Jak rozpakować, stelaż i kabel fizyczny składnik urządzenia z hybrydowej pamięci podręcznej magazynu Microsoft Azure FXT Edge Filer
author: ekpgh
ms.author: rohogue
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 07/01/2019
ms.openlocfilehash: 7c77e25d0dcabc49f2e6672645c6bc41e8662ec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75551033"
---
# <a name="tutorial-install-azure-fxt-edge-filer"></a>Samouczek: Instalowanie narzędzia Azure FXT Edge Filer 

W tym samouczku opisano sposób instalowania węzła sprzętowego dla pamięci podręcznej hybrydowego magazynu usługi Azure FXT Edge Filer. Aby utworzyć klaster plików usługi Azure FXT Edge Filer, należy zainstalować co najmniej trzy węzły sprzętowe.

Procedura instalacji polega na rozpakowaniu i montażu w stelażu oraz podłączeniu ramienia zarządzania kablami (CMA) i przedniej ramki. Osobny samouczek wyjaśnia podłączanie kabli sieciowych i podłączanie zasilania. 

Instalacja węzła filera usługi Azure FXT Edge trwa około godziny. 

Ten samouczek zawiera następujące kroki konfiguracji: 

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Zamontuj urządzenie w stelażu
> * Montaż przedniej ramki (opcjonalnie)

## <a name="installation-prerequisites"></a>Wymagania wstępne instalacji 

Przed rozpoczęciem upewnij się, że centrum danych i stojak, z którego będziesz korzystać, mają następujące funkcje:

* Dostępne gniazdo 1U w stelażu, w którym zamierzasz zamontować urządzenie.
* Zasilacze prądu przemiennego i systemy chłodzenia, które spełniają potrzeby narzędzia Azure FXT Edge Filer. (Przeczytaj [specyfikacje zasilania i termiczne,](fxt-specs.md#power-and-thermal-specifications) aby ułatwić planowanie i do zmiany rozmiaru instalacji).  

  > [!NOTE] 
  > Aby w pełni wykorzystać dwa nadmiarowe zasilacze (PSU), należy używać jednostek dystrybucji mocy w dwóch różnych obwodach rozgałęzień podczas podłączania zasilania prądem przemiennym. Przeczytaj [o podłączeniu kabli zasilających,](fxt-network-power.md#connect-power-cables) aby uzyskać szczegółowe informacje.  

## <a name="unpack-the-hardware-node"></a>Rozpakuj węzeł sprzętowy 

Każdy węzeł usługi Azure FXT Edge Filer jest dostarczany w jednym polu. Wykonaj te czynności, aby rozpakować urządzenie.

1. Umieść pudełko na płaskiej, poziomej powierzchni.

2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.

3. Rozpakuj zawartość pudełka. Upewnij się, że zawiera następujące elementy:
   * Jedno urządzenie FXT z pojedynczą obudową
   * dwa przewody zasilania,
   * Jedna przednia ramka i klucz
   * Montaż jednego zestawu szyn
   * Jedno ramię zarządzania kablami (CMA)
   * Instrukcja instalacji CMA broszura
   * Instrukcja montażu stelaża broszura
   * Broszura z informacjami o bezpieczeństwie, środowisku i przepisach

Jeśli nie otrzymasz wszystkich wymienionych elementów, skontaktuj się z dostawcą urządzenia, aby uzyskać pomoc. 

Upewnij się, że urządzenie ma wystarczająco dużo czasu, aby osiągnąć taką samą temperaturę jak w pomieszczeniu przed zainstalowaniem lub włączeniem. Jeśli zauważysz kondensację na dowolnej części urządzenia, odczekaj co najmniej 24 godziny przed instalacją.

Następnym krokiem jest zamontowanie urządzenia na stojaku.

## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie Azure FXT Edge Filer musi być zainstalowane w standardowym 19-calowym stojaku. 

Hybrydowa pamięć podręczna magazynu usługi Azure FXT Edge Filer składa się z co najmniej trzech urządzeń usługi Azure FXT Edge Filer. Powtórz kroki instalacji stelaża dla każdego urządzenia, które jest częścią systemu. 

### <a name="rack-install-prerequisites"></a>Wymagania wstępne dotyczące instalacji stelaża

* Przed rozpoczęciem należy zapoznać się z instrukcjami dotyczącymi bezpieczeństwa zawartymi w broszurze z informacjami dotyczącymi bezpieczeństwa, środowiska i przepisów, która została dostarczona wraz z urządzeniem.

  > [!NOTE]
  > Zawsze używaj dwóch osób podczas podnoszenia węzła, w tym podczas instalowania go w stojaku lub wyjmowania go ze stojaka. 

* Określ rodzaj instalacji szyny używanej w stelażu sprzętu. 
  * W przypadku stojaków zatrzaskowych z otworami kwadratowymi lub okrągłymi należy postępować zgodnie z instrukcjami dotyczącymi szyny bez narzędzi.
  * W przypadku stojaków na otwory gwintowane postępuj zgodnie z instrukcjami dotyczącymi szyny narzędziowej. 
  
    W przypadku konfiguracji montażu szyny narzędziowej należy dostarczyć osiem śrub, typu 10-32, 12-24, M5 lub M6. Średnica wierzchołka śrub musi być mniejsza niż 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Identyfikacja zawartości zestawu szyn

Zlokalizuj komponenty do montażu zestawu szyn:

1. Dwa zespoły szyn przesuwnych A7 Dell ReadyRails II (1)
1. Dwa paski hakowe i zapętlone (2)

![Numerowany rysunek zawartości zestawu szyn](media/fxt-install/identify-rail-kit-contents-400.png)

### <a name="rail-assembly---tool-less-rails-square-hole-or-round-hole-racks"></a>Montaż szyny - szyny beznarzędziowe (kwadratowe stojaki na otwory lub okrągłe otwory)

W przypadku stojaków z kwadratowymi lub okrągłymi otworami należy wykonać tę procedurę, aby zmontować i zainstalować szyny. 

1. Umieść lewe i prawe elementy końca szyny oznaczone **przednim przodem** do wewnątrz. Umieść każdy element końcowy tak, aby mieścił się w otworach z przodu pionowych kołnierzy rack. (1)

2. Wyrównaj każdy element końcowy w dolnej i górnej części zęba w przestrzeni, którą chcesz zamontować. (2)

3. Włącz tylną część szyny, aż w pełni zasunie na pionowym kołnierzu stojaka, a zatrzask zatrzaśnie się na swoim miejscu. Powtórz te kroki, aby ustawić i umieścić przedni element na pionowym kołnierzu stojaka. (3)

> [!TIP]
> Aby usunąć szyny, pociągnij przycisk zwalniania zatrzasku na punkcie środkowym części końcowej (4) i odsuń każdą szynę.

![Schemat instalowania i usuwania szyn bez narzędzi, z ponumerowanymi krokami](media/fxt-install/installing-removing-tool-less-rails-400.png)

### <a name="rail-assembly---tooled-rails-threaded-hole-racks"></a>Montaż szyny - szyny narzędziowe (stojaki na otwory gwintowane)

W przypadku stojaków z otworami gwintowanymi wykonaj tę procedurę, aby zmontować i zainstalować szyny.

1. Wyjmij szpilki z przednich i tylnych wsporników montażowych za pomocą śrubokręta z płaskimi końcówkami. (1)
1. Pociągnij i obróć podzespoły zatrzasku szyny, aby usunąć je z wsporników montażowych. (2)
1. Przymocuj lewą i prawą szynę montażową do przednich pionowych kołnierzy rack za pomocą dwóch par śrub. (3)
1. Przesuń lewe i prawe tylne wsporniki do przodu do tylnych pionowych kołnierzy stelaża i przymocuj je za pomocą dwóch par śrub. (4)

![Schemat instalowania i usuwania szyny narzędziowej, z ponumerowanymi stopniami](media/fxt-install/installing-removing-tooled-rails-400.png)

### <a name="install-the-system-in-the-rack"></a>Zainstaluj system w stelażu

Wykonaj następujące kroki, aby zainstalować urządzenie Azure FXT Edge Filer w stojaku.

1. Wyciągnij wewnętrzne szyny przesuwne ze stojaka, aż zablokują się na swoim miejscu. (1)
1. Zlokalizuj odsunięcie tylnej szyny po obu stronach urządzenia i opuść je do tylnych rowów J na zespołach prowadnic. (2) 
1. Obróć urządzenie w dół, aż wszystkie postoje szyny zostaną osadzone w rowkach J. (3)
1. Wciśnij urządzenie do środka, aż dźwignie blokady zatrzasną się na swoim miejscu.
1. Naciśnij przyciski blokady zwalniania suwaka na obu szynach (4) i wsuń urządzenie do stelaża.

![Zainstaluj system na diagramie stojaka z ponumerowanymi krokami](media/fxt-install/installing-system-rack-400.png)

### <a name="remove-the-system-from-the-rack"></a>Wyjmij system ze stelaża

Aby wyjąć urządzenie ze stojaka, wykonaj tę procedurę. 

1. Zlokalizować dźwignie zamka po bokach szyn wewnętrznych (1).
2. Odblokuj każdą dźwignię, obracając ją do pozycji zwalniającej (2).
3. Chwyć mocno boki systemu i pociągnij go do przodu, aż szyna znajduje się z przodu rowków J. Podnieś system do góry i z dala od stelaża i umieść go na równej powierzchni (3).

![Ilustracja usuwania systemu ze stelaża z ponumerowanymi krokami](media/fxt-install/removing-system-rack-400.png)

### <a name="engage-the-slam-latch"></a>Zaangażuj zatrzask slam

1. Skierowana do przodu, zlokalizuj zatrzask (1) po obu stronach systemu.
2. Zatrzaski włączają się automatycznie, gdy system jest wciskany do stelaża. 

Aby zwolnić zatrzaski podczas wyjmowania systemu, pociągnij je do góry (2).

Opcjonalne śruby mocujące na twardo są dostarczane w celu zabezpieczenia systemu do stelaża w celu wysyłki lub w innych niestabilnych środowiskach. Znajdź śrubę pod każdym zatrzaskiem i dokręć je #2 śrubokrętem krzyżakowym (3).

![Ponumerowana ilustracja angażowania i zwalniania zatrzasku](media/fxt-install/engaging-releasing-slam-latch-400.png)

### <a name="install-the-cable-management-arm"></a>Zamontować ramię zarządzania kablami 

Opcjonalne ramię zarządzania kablami (CMA) jest dostarczane z FXT Edge Filer. Wydrukowane instrukcje instalacji znajdują się w opakowaniu. 

1. Rozpakuj i zidentyfikuj elementy zestawu ramienia do zarządzania kablami:
   * Taca CMA (1)
   * CMA (2)
   * Nylonowe opaski kablowe (3)
   * Uchwyty mocowania CMA (4)
   * Kabel wskaźnika stanu (5) 

   > [!TIP] 
   > Aby zabezpieczyć CMA do wysyłki w stojaku, zapętl krawat owija się wokół koszy i tacy i mocno je cinch. Zabezpieczenie cma w ten sposób będzie również zabezpieczyć system w niestabilnych środowiskach.

   ![Ilustracja części CMA](media/fxt-install/cma-kit-400.png)

2. Zainstaluj tacę CMA.

   Taca CMA zapewnia wsparcie i działa jako uchwyt dla CMA. 

   1. Wyrównaj i włącz każdą stronę zasobnika za pomocą uchwytów odbiornika na wewnętrznych krawędziach szyn. 
   1. Przesuń tacę do przodu, aż zatrzaśnie się na swoim miejscu. (1)
   1. Aby wyjąć tacę, ściśnij przyciski zwalniające zatrzask w kierunku środka i wyciągnij tacę z uchwytów odbiornika (2).

   ![Ilustracja instalacji zasobnika CMA](media/fxt-install/cma-tray-install-400.png)

3. Zainstaluj uchwyty mocowania CMA. 

   > [!NOTE]
   >
   > * Cma można przymocować do prawej lub lewej szyny montażowej, w zależności od tego, jak zamierzasz kierować kable z systemu. 
   > * Dla wygody zamontuj CMA z boku naprzeciwko zasilaczy (strona A). Jeśli jest zamontowany na boku B, cma musi być odłączony w celu usunięcia zewnętrznego źródła zasilania. 
   > * Przed wyjęciem zasilaczy należy zawsze wyjąć tacę. 

   ![Ilustracja instalacji wspornika CMA](media/fxt-install/cma-bracket-l-r-install-400.png)

   1. Wybierz odpowiedni uchwyt mocujący CMA dla strony, w której chcesz zamontować CMA (strona B lub bok A).
   1. Zamontować uchwyt mocujący CMA z odpowiednim oznaczeniem bocznym A lub bocznym B z tyłu szyny przesuwnej.
   1. Wyrównaj otwory na wsporniku ze szpilkami na szynie przesuwnej. Wciśnij wspornik w dół, aż zablokuje się na swoim miejscu. 

4. Zainstaluj cma.

   1. Z tyłu systemu zamontuj zatrzask z przodu CMA na najgłębszym wsporniku zespołu suwaka, aż zatrzask się zatrzasnie (1). 
   1. Zamontować drugi zatrzask na końcu zewnętrznego wspornika, aż zatrzask się zatrzasnie (2). 
   1. Aby usunąć CMA, odłącz oba zatrzaski, naciskając przyciski zwalniające CMA w górnej części wewnętrznej i zewnętrznej obudowy zatrzasku (3).

   ![Ilustracja głównej instalacji CMA](media/fxt-install/cma-install-400.png)

   Cma można obracać z dala od systemu dostępu i serwisu. Na zawiasach unieś CMA z dala od tacy, aby go odsunąć (1). Po odsunieniu go od tacy odsuń CMA od systemu (2).

   ![Ilustracja CMA obrócony otwarty do serwisu](media/fxt-install/cma-swing-over-tray-400.png)

## <a name="install-the-front-bezel-optional"></a>Montaż przedniej ramki (opcjonalnie)

W tej sekcji wyjaśniono, jak zainstalować i usunąć przednią ramkę (płytę czołową) dla sprzętu Azure FXT Edge Filer. 

Aby zainstalować przednią ramkę: 

1. Zlokalizuj i wyjmij klucz ramki, który znajduje się w opakowaniu ramki. 
1. Wyrównaj ramkę z przednią częścią obudowy i włóż kołki po prawej stronie ramki do otworów na kołnierzu z prawej strony stelaża. 
1. Dopasuj lewy koniec ramki do obudowy. Naciskaj ramkę, aż przycisk po lewej stronie zatrzaśnie się na swoim miejscu.
1. Zablokuj ramkę za pomocą klucza.

Aby usunąć przednią ramkę: 
1. Odblokuj ramkę za pomocą klucza ramki.
1. Naciśnij przycisk zwalniający po lewej stronie i odciągnij lewy koniec ramki od obudowy.
1. Odłącz prawy koniec i zdejmij ramkę.
   
   ![Obraz przedstawiający przycisk zwalniający po lewej stronie ramki i jak go usunąć, wyciągając go na zewnątrz z lewej strony](media/fxt-install/remove-bezel-edited-600.png)

## <a name="next-steps"></a>Następne kroki

Po rozpakowaniu i rozpakowaniu urządzenia należy kontynuować konfigurację, podłączając kable sieciowe i podłączając zasilanie sieciowe do narzędzia Azure FXT Edge Filer.

> [!div class="nextstepaction"]
> [Kabel portów sieciowych i zasilania](fxt-network-power.md)