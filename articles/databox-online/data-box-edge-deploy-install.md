---
title: Samouczek do zainstalowania - Rozpakować, stojak, kabel Azure Data Box Edge urządzenia fizycznego | Dokumenty firmy Microsoft
description: Drugi samouczek na temat instalacji usługi Azure Data Box Edge dotyczy rozpakowywania, umieszczania na stojaku i podłączania kabli urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/17/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: fe74db34e62a80935954c6cfc2e591d49a84b0b7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76263952"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Samouczek: Instalowanie usługi Azure Data Box Edge

W tym samouczku opisano sposób instalowania urządzenia fizycznego Data Box Edge. Procedura instalacji obejmuje rozpakowywanie, montowanie na stojaku i podłączanie kabli urządzenia. 

Instalacja może potrwać około dwóch godzin.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące:

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Wykonaliśmy wszystkie kroki w [obszarze Przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).
    * Utworzono zasób usługi Data Box Edge, aby wdrożyć urządzenie.
    * Wygenerowano klucz aktywacji w celu aktywowania urządzenia przy użyciu zasobu usługi Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Urządzenie fizyczne Data Box Edge

Przed wdrożeniem urządzenia:

- Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej.
- Sprawdź, czy lokacja, w której chcesz zamontować urządzenie, ma:
    - standardowe zasilanie prądem przemiennym z niezależnego źródła

        — Lub —
    - jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS
    - Dostępne gniazdo 1U w stojaku, na którym zamierzasz zainstalować urządzenie

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi przygotowania sieci pod kątem wdrożenia usługi Data Box Edge i skonfiguruj sieć w centrum danych zgodnie z tymi wymaganiami. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące sieci dla usługi Data Box Edge](data-box-edge-system-requirements.md#networking-port-requirements).

- Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.


## <a name="unpack-the-device"></a>Rozpakowywanie urządzenia

To urządzenie jest dostarczane w jednym pudełku. Aby rozpakować urządzenie, wykonaj następujące czynności. 

1. Umieść pudełko na płaskiej, poziomej powierzchni.
2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.
3. Rozpakuj zawartość pudełka. Po rozpakowaniu upewnij się, że masz:
    - Jedna obudowa Data Box Edge
    - dwa przewody zasilania,
    - Montaż jednego zestawu szyn
    - Broszura z informacjami o bezpieczeństwie, środowisku i przepisach

Jeśli nie otrzymasz wszystkich elementów wymienionych tutaj, skontaktuj się z pomocą techniczną usługi Data Box Edge. Następnym krokiem jest zamontowanie urządzenia na stojaku.


## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie należy zainstalować na standardowym, 19-calowym stojaku. Poniższa procedura umożliwia zamontowanie urządzenia w stojaku na standardowym 19-calowym stelażu.

> [!IMPORTANT]
> Aby urządzenie Data Box Edge działało poprawnie, należy je zamontować na stojaku.


### <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem zapoznaj się z instrukcjami dotyczącymi bezpieczeństwa zawartymi w broszurze z informacjami dotyczącymi bezpieczeństwa, środowiska i przepisów. Ta broszura została dostarczona z urządzeniem.
- Rozpocznij instalowanie szyn w przydzielonej przestrzeni, która znajduje się najbliżej dna obudowy stelaża.
- W przypadku konfiguracji montażu szyny narzędziowej:
    -  Musisz dostarczyć osiem śrub: #10-32, #12-24, #M5 lub #M6. Średnica wierzchołka śrub musi być mniejsza niż 10 mm (0,4").
    -  Potrzebujesz płaskiego śrubokręta.

### <a name="identify-the-rail-kit-contents"></a>Identyfikacja zawartości zestawu szyn

Zlokalizuj komponenty do montażu zestawu szyn:
1. Dwa zespoły szyn przesuwnych A7 Dell ReadyRails II
2. Dwa paski na rzep i pętlę

    ![Identyfikacja zawartości zestawu szyn](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Montaż i usuwanie szyn beznarzędziowych (kwadratowe stojaki na otwory lub okrągłe otwory)

> [!TIP]
> Ta opcja jest beznarzędziowa, ponieważ nie wymaga narzędzi do instalowania i zdejmowania szyn do niewątczonych kwadratowych lub okrągłych otworów w stojakach.

1. Umieść lewe i prawe elementy końca szyny oznaczone **przednim** przodem do wewnątrz i ustaw każdy element końcowy, aby umieścić w otworach z przodu pionowych kołnierzy rack.
2. Wyrównaj każdy element końcowy w dolnej i górnej części otworów żądanych przestrzeni U.
3. Włącz tylną część szyny, aż w pełni zasunie na pionowym kołnierzu stojaka, a zatrzask zatrzaśnie się na swoim miejscu. Powtórz te kroki, aby ustawić i umieścić przedni element na pionowym kołnierzu stojaka.
4. Aby usunąć szyny, pociągnij przycisk zwalniania zatrzasku na końcu części środkowej i odsuń każdą szynę.

    ![Instalowanie i usuwanie szyn bez narzędzi](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Montaż i usuwanie szyn narzędziowych (stojaki na otwory gwintowane)

> [!TIP]
> Ta opcja jest oprzyrządowana, ponieważ wymaga narzędzia _(śrubokręt płasko-przechylony)_ do zainstalowania i wyjęcia szyn do gwintowanych okrągłych otworów w stojakach.

1. Zdejmij kołki z przednich i tylnych wsporników montażowych za pomocą śrubokręta z płaskimi końcówkami.
2. Pociągnij i obróć podzespoły zatrzasku szyny, aby usunąć je z wsporników montażowych.
3. Przymocuj lewą i prawą szynę montażową do przednich pionowych kołnierzy rack za pomocą dwóch par śrub.
4. Przesuń lewe i prawe tylne wsporniki do przodu do tylnych pionowych kołnierzy stelaża i przymocuj je za pomocą dwóch par śrub.

    ![Instalowanie i usuwanie szyny narzędziowe](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Zainstaluj system w stelażu

1. Wyciągnij wewnętrzne szyny przesuwne ze stojaka, aż zablokują się na swoim miejscu.
2. Zlokalizuj odsunięcie tylnej szyny po obu stronach systemu i opuść je do tylnych rowów J na zespołach prowadnic. Obróć system w dół, aż wszystkie postoje szyny będą osadzone w rowkach J.
3. Wciśnij system do wewnątrz, aż dźwignie blokady zatrzasną się na swoim miejscu.
4. Naciśnij przyciski blokady zwalniania suwaka na obu szynach i wsuń system do stelaża.

    ![Instalacja systemu w stelażu](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Wyjmij system ze stelaża

1. Zlokalizować dźwignie zamka po bokach szyn wewnętrznych.
2. Odblokuj każdą dźwignię, obracając ją do pozycji zwalniającej.
3. Chwyć mocno boki systemu i pociągnij go do przodu, aż szyna znajduje się z przodu rowków J. Podnieś system do góry i z dala od stelaża i umieść go na równej powierzchni.

    ![Usuń system ze stelaża](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Zaangażuj i zwolnij zatrzask

> [!NOTE]
> W przypadku systemów niewymiernych w zatrzaski zatrzaskowe należy zabezpieczyć system za pomocą śrub, jak opisano w kroku 3 tej procedury.

1. Skierowana do przodu, zlokalizować zatrzask slam po obu stronach systemu.
2. Zatrzaski włączają się automatycznie, gdy system jest wciskany do stelaża i zwalniany przez podciągnięcie się na zatrzaskach.
3. Aby zabezpieczyć system do wysyłki w stojaku lub w innych niestabilnych środowiskach, zlokalizuj śrubę mocowania twardego pod każdym zatrzaskiem i dokręć każdą śrubę #2 śrubokrętem krzyżakowym.

    ![Włączanie i zwalnianie zatrzasku](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)


## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

Posyłaj kable, a następnie przekadź urządzenie. W poniższych procedurach wyjaśniono, jak podłączyć urządzenie Data Box Edge do zasilania i sieci.

Aby można było rozpocząć podłączanie kabli urządzenia, potrzebne są następujące elementy:

- Urządzenie fizyczne Data Box Edge, rozpakowane i zamontowane w stojaku.
- Dwa kable zasilające.
- Co najmniej jeden kabel sieciowy 1-GbE RJ-45 służący do łączenia z interfejsem zarządzania. Istnieją dwa interfejsy sieciowe 1-GbE: jeden do zarządzania i drugi stanowiący interfejs danych w urządzeniu.
- Jeden miedziany kabel 25-GbE SFP+ dla każdego interfejsu sieciowego danych do skonfigurowania. Co najmniej jeden sieciowy interfejs danych — PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 — musi być połączony z Internetem (umożliwiając łączność z platformą Azure).  
- Dostęp do dwóch jednostek dystrybucji zasilania (zalecane).

> [!NOTE]
> - Jeśli łączysz tylko jeden interfejs sieci danych, zaleca się użycie interfejsu sieciowego 25/10 GbE, takiego jak PORT 3, PORT 4, PORT 5 lub PORT 6 do wysyłania danych na platformę Azure. 
> - Aby uzyskiwać najlepszą wydajność i obsługiwać duże ilości danych, rozważ połączenie wszystkich portów danych.
> - Urządzenie Data Box Edge powinno być podłączone do sieci centrum danych, aby można było pozyskiwać dane z serwerów źródła danych.

Na urządzeniu Data Box Edge:

- Przedni panel ma dyski i przycisk zasilania.

    - Z przodu urządzenia znajduje się 10 gniazd dysków.
    - Gniazdo 0 ma dysk SATA o 240 GB używany jako dysk systemu operacyjnego. Gniazdo 1 jest puste, a gniazda od 2 do 9 to dyski SSD NVMe używane jako dyski z danymi.
- Tylna płaszczyzna zawiera nadmiarowe zasilacze (PSU).
- Tylna płaszczyzna ma sześć interfejsów sieciowych:

    - Dwa interfejsy 1 Gb/s.
    - Cztery interfejsy 25 Gb/s, które mogą również służyć jako interfejsy 10 Gb/s.
    - Kontroler zarządzania płytą główną (BMC).

- Tylna płaszczyzna ma dwie karty sieciowe odpowiadające 6 portom:

    - QLogic FastLinQ 41264
    - QLogic FastLinQ 41262

Aby uzyskać pełną listę obsługiwanych kabli, przełączników i nadajników nadawczo-odbiorczych dla tych kart sieciowych, przejdź do [cavium FastlinQ 41000 Series Interoperability Matrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Zidentyfikuj różne porty na tylnej płaszczyźnie urządzenia.

    ![Tylna płaszczyzna urządzenia kablowego](./media/data-box-edge-deploy-install/backplane-cabled.png)

2. Znajdź gniazda dysków i przycisk zasilania z przodu urządzenia.

    ![Płaszczyzna przednia urządzenia](./media/data-box-edge-deploy-install/device-front-plane-labeled-1.png)

3. Podłącz kable zasilające do poszczególnych zasilaczy w obudowie. Aby zapewnić wysoką dostępność, zainstaluj i podłącz oba zasilacze do różnych źródeł zasilania.
4. Podłącz kable zasilające do jednostek dystrybucji zasilania stojaka. Upewnij się, że dwa zasilacze korzystają z oddzielnych źródeł zasilania.
5. Naciśnij przycisk zasilania, aby włączyć urządzenie.
6. Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.
7. Co najmniej jeden PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 łączący z Internetem/siecią centrum danych.

    - W przypadku łączenia za pomocą portu PORT 2 użyj kabla sieciowego RJ-45.
    - W przypadku interfejsów sieciowych 10/25 GbE należy używać miedzianych kabli SFP+.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem oraz je skonfigurować i aktywować.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z urządzeniem Data Box Edge i konfigurowanie go](./data-box-edge-deploy-connect-setup-activate.md)
