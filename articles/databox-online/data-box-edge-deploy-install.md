---
title: Samouczek dotyczący instalacji urządzenia fizycznego Azure Data Box Edge | Microsoft Docs
description: Drugi samouczek na temat instalacji usługi Azure Data Box Edge dotyczy rozpakowywania, umieszczania na stojaku i podłączania kabli urządzenia fizycznego.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: a1357e92b868f85556fc4d665eb475abd095fece
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2019
ms.locfileid: "58399997"
---
# <a name="tutorial-install-azure-data-box-edge"></a>Samouczek: Instalowanie usługi Azure Data Box krawędzi

W tym samouczku opisano sposób instalowania urządzenia fizycznego Data Box Edge. Procedura instalacji obejmuje rozpakowywanie, montowanie na stojaku i podłączanie kabli urządzenia. 

Instalacja może potrwać około dwóch godzin.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące:

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Wszystkie kroki Ukończono [przygotowanie do wdrożenia usługi Azure Data Box Edge](data-box-edge-deploy-prep.md).
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
    - jedno urządzenie Edge w pojedynczej obudowie,
    - dwa przewody zasilania,
    - Szyny jeden zestaw zestawu
    - Broszury bezpieczeństwa, środowiska i informacje prawne

Jeśli nie otrzymasz wszystkich elementów wymienionych tutaj, skontaktuj się z pomocą techniczną usługi Data Box Edge. Następnym krokiem jest zamontowanie urządzenia na stojaku.


## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie należy zainstalować na standardowym, 19-calowym stojaku. Użyj poniższej procedury w stojaku instalacji urządzenia w stojaku 19-calowy standardowy.

> [!IMPORTANT]
> Aby urządzenie Data Box Edge działało poprawnie, należy je zamontować na stojaku.


### <a name="prerequisites"></a>Wymagania wstępne

- Przed rozpoczęciem przeczytaj instrukcje dotyczące bezpieczeństwa w książeczki bezpieczeństwa, środowiska i informacji prawnych. Niniejszy podręcznik zostało wysłane do urządzenia.
- Rozpoczęta szyny w wyznaczonym miejscu, znajdujący się najbliżej dolnej części obudowy stojaka.
- Tooled szyny konfiguracji instalowania, musisz podać osiem śruby: #10-32 "," #12 – 24 "," #M5 "lub" #M6. Główny średnica śruby musi być mniejsza niż 10 mm (0,4").

### <a name="identify-the-rail-kit-contents"></a>Zidentyfikować jej zawartość zestawu szyny

Znajdź składniki do zainstalowania zestawu kit szyny:
1. II ReadyRails Dell A7 w dwóch przedłużanie zestawy szyny
2. Dwa taśm hook i pętli

![Identyfikacji zawartości zestawu szyny](./media/data-box-edge-deploy-install/identify-rail-kit-contents.png)

### <a name="install-and-remove-tool-less-rails-square-hole-or-round-hole-racks"></a>Instalowanie i usuwanie narzędzi rails (dziura kwadratowy lub stojakami round dziura)

1. Umieść elementy end szyny lewy i prawy etykietą **FRONT** skierowany do wewnątrz i poznaniu każdego z nich zakończenia stanowisko w otwory na przedniej stronie stopka stelażu pionowym.
2. Wyrównaj do każdego elementu end otworów dołu i od góry żądaną U miejsca do magazynowania.
3. Angażowanie zaplecza szyny dopóki nie jest w pełni stanowisk na listwie stelażu pionowym i kliknięć zatrzaśnięcia w miejscu. Powtórz te kroki, aby ustawić położenie i stanowisko element frontonu na listwie stelażu pionowym.
4. Aby usunąć rails, ściągnięcia zatrzaśnięcia przycisku wydania w środkowym element end i Wyjmij urządzenie każdego szyny.

![Instalowanie i usuwanie narzędzi platformy rails](./media/data-box-edge-deploy-install/installing-removing-tool-less-rails.png)

### <a name="install-and-remove-tooled-rails-threaded-hole-racks"></a>Instalowanie i usuwanie tooled rails (Zhierarchizowany dziura stojakami)

1. Usuń numery PIN z przodu i do tyłu instalowania nawiasy kwadratowe przy użyciu śrubokręta przebijaka.
2. Ściąganie i Obróć podzespołów zatrzaśnięcia kolei, aby usunąć je z nawiasów instalowania.
3. Dołączanie po lewej stronie i bezpośrednio instalowanie platformy rails, aby przy użyciu dwóch par śruby stopka frontonu stojak pionowy.
4. Przesuń wstecz lewy i prawy nawiasy kwadratowe przekazywania względem stopka tylnej stojak pionowych i dołącz je przy użyciu dwóch par śruby.

![Instalowanie i usuwanie tooled rails](./media/data-box-edge-deploy-install/installing-removing-tooled-rails.png)

### <a name="install-the-system-in-a-rack"></a>Zainstaluj system w stojaku

1. Ściągnij rails wewnętrzny slajdów ze stojaka, do momentu ich blokowanie w miejscu.
2. Znajdź standoff tylnej szyny na każdej stronie systemu i zmniejszyć ich do tyłu gniazd "j" zestawów slajdów. Obróć system w dół, aż wszystkie standoffs szyny są umieszczone w gnieździe "j".
3. Wypychanie systemu do wewnątrz do momentu dźwigni blokady kliknij w miejscu.
4. Naciśnij przycisk blokady wersji slajdów zarówno platformy rails, jak i slajdów system w stojaku.

![Zainstaluj system w stojaku](./media/data-box-edge-deploy-install/installing-system-rack.png)

### <a name="remove-the-system-from-the-rack"></a>Usuwanie systemu stojaku

1. Znajdź dźwigni blokadę na stronach wewnętrzny platformy rails.
2. Uzyskaj dostęp do każdego suwaka obracając do pozycji jego wersji.
3. Mocno niejasny boki systemu, a następnie przeciągnij ją do przodu, aż standoffs szyny znajdują się na początku gniazd "j". Przenoszenie system w górę i w kierunku od stojaku i umieść go na poziomie powierzchni.

![Usuń system z stojaku](./media/data-box-edge-deploy-install/removing-system-rack.png)

### <a name="engage-and-release-the-slam-latch"></a>Angażowanie i zwolnij zatrzaśnięcia slam

UWAGA: Systemy nie wyposażone zamków slam należy zabezpieczyć systemu przy użyciu śrub, zgodnie z opisem w kroku 3 tej procedury.

1. Połączonego z przodu, zlokalizuj zatrzaśnięcia slam po obu stronach systemu.
2. Zamków angażować się automatycznie jako system jest równoznaczne z wypchnięciem w stojaku i są wydawane przez pobieranie na zatrzaśnięcia.
3. Do zabezpieczenia systemu do wydania w stojaku lub w innych środowiskach niestabilny, zlokalizuj gwintowanym twarde instalacji w ramach każdego zatrzaśnięcia i podwyższenie poziomu każdego gwintowanym z #2 krzyżakowy.

![Angażowanie i zwolnij slam zatrzaśnięcie](./media/data-box-edge-deploy-install/engaging-releasing-slam-latch.png)

### <a name="route-the-cables"></a>Kable

> [!NOTE]
>  Jeśli opcjonalny ramię zarządzania kablowe (CMA) nie kolejność, użyj dwóch punktów zaczepienia i pętli taśm podane w zestawie szyny kierować kable tyłu systemu.

1. Znajdź zewnętrzne nawiasy CMA na stronach posługiwanie się nimi zarówno kołnierze stojaka.
2. Pakietu kable delikatnie, pobierając je wyczyść łączników systemu do lewej i prawej stronie.
3. Wątek punktu zaczepienia i pętla taśm za pomocą tooled gniazda na zewnętrzne nawiasy CMA po każdej stronie systemu, aby zabezpieczyć pakiety kabel.


![Kable](./media/data-box-edge-deploy-install/routing-cables.png)

## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

Poniższe procedury opisują sposób podłączania kabli urządzenia Edge do zasilania i sieci.

Aby można było rozpocząć podłączanie kabli urządzenia, potrzebne są następujące elementy:

- Urządzenie fizyczne Edge, rozpakowane i zamontowane na stojaku.
- Dwa kable zasilające. 
- Co najmniej jeden kabel sieciowy 1-GbE RJ-45 służący do łączenia z interfejsem zarządzania. Istnieją dwa interfejsy sieciowe 1-GbE: jeden do zarządzania i drugi stanowiący interfejs danych w urządzeniu.
- Jeden miedziany kabel 25-GbE SFP+ dla każdego interfejsu sieciowego danych do skonfigurowania. Co najmniej jeden sieciowy interfejs danych — PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 — musi być połączony z Internetem (umożliwiając łączność z platformą Azure).  
- Dostęp do dwóch jednostek dystrybucji zasilania (zalecane).

> [!NOTE]
> - Jeśli łączysz tylko jeden interfejs sieciowy danych zaleca się korzystanie z interfejsu sieciowych/25 10 GbE, takie jak PORT 3, 4 portu, portu 5 lub 6 portu do wysyłania danych do platformy Azure. 
> - Aby uzyskiwać najlepszą wydajność i obsługiwać duże ilości danych, rozważ połączenie wszystkich portów danych.
> - Urządzenie Edge powinno być połączone z siecią centrum danych, aby mogło pozyskiwać dane z serwerów źródła danych.

Urządzenie Edge jest wyposażone w 8 dysków SSD NVMe. Na przednim panelu znajdują się diody LED stanu i przyciski zasilania. Z tyłu urządzenia znajdują się nadmiarowe zasilacze. Urządzenie ma sześć interfejsów sieciowych:

- Dwa interfejsy 1 GB/s
- Cztery 25 GB interfejsy, które może również służyć jako interfejsy 10 GB/s.
- Kontroler zarządzania płytą główną (BMC). 

Zidentyfikuj różne porty na tylnym panelu montażowym urządzenia.
 
  ![Płyta montażowa okablowanego urządzenia](./media/data-box-edge-deploy-install/backplane-cabled.png)

Urządzenie ma dwie karty sieciowe, odpowiadający 6 portów: 

 - QLogic FastLinQ 41264
 - QLogic FastLinQ 41262

Aby uzyskać pełną listę obsługiwanych kable, przełączniki i należy w tych kartach sieciowych, przejdź do [Cavium FastlinQ 41000 serii współdziałanie macierzy](https://www.marvell.com/documents/xalflardzafh32cfvi0z/).
 
Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Podłącz kable zasilające do poszczególnych zasilaczy w obudowie. Aby zapewnić wysoką dostępność, zainstaluj i podłącz oba zasilacze do różnych źródeł zasilania.

2. Podłącz kable zasilające do jednostek dystrybucji zasilania stojaka. Upewnij się, że dwa zasilacze korzystają z oddzielnych źródeł zasilania.

3. Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.

4. Co najmniej jeden PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 łączący z Internetem/siecią centrum danych. W przypadku łączenia za pomocą portu PORT 2 użyj kabla sieciowego RJ-45. W przypadku interfejsów sieciowych 10/25 GbE Użyj kable SFP + miedzianego.  

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem oraz je skonfigurować i aktywować.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z urządzeniem Data Box Edge i konfigurowanie go](./data-box-edge-deploy-connect-setup-activate.md)


