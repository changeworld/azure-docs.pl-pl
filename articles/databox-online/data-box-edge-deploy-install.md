---
title: Samouczek dotyczący instalacji urządzenia fizycznego Azure Data Box Edge | Microsoft Docs
description: Drugi samouczek na temat instalacji usługi Azure Data Box Edge dotyczy rozpakowywania, umieszczania na stojaku i podłączanie kabli urządzenia fizycznego.
services: databox-edge-gateway
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox-edge-gateway
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/08/2018
ms.author: alkohli
ms.custom: ''
Customer intent: As an IT admin, I need to understand how to install Data Box Edge in datacenter so I can use it to transfer data to Azure.
ms.openlocfilehash: 17602abd9dab590c806c33779cf127f9a5305f64
ms.sourcegitcommit: 26cc9a1feb03a00d92da6f022d34940192ef2c42
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2018
ms.locfileid: "48833203"
---
# <a name="tutorial-install-azure-data-box-edge-preview"></a>Samouczek: instalowanie usługi Azure Data Box Edge (wersja zapoznawcza)

W tym samouczku opisano sposób instalowania urządzenia fizycznego Data Box Edge. Procedura instalacji obejmuje rozpakowywanie, montowanie na stojaku i podłączanie kabli urządzenia. 

Instalacja może potrwać około 2 godzin.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

> [!IMPORTANT]
> Usługa Data Box Edge jest dostępna w wersji zapoznawczej. Przed zamówieniem i wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Wymagania wstępne

Wymagania wstępne dotyczące instalacji urządzenia fizycznego są następujące.

### <a name="for-the-data-box-edge-resource"></a>Zasób usługi Data Box Edge

Przed rozpoczęciem upewnij się, że:

* Zostały wykonane wszystkie kroki samouczka [Przygotowywanie portalu pod kątem usługi Data Box Edge](data-box-edge-deploy-prep.md).
    * Utworzono zasób usługi Data Box Edge, aby wdrożyć urządzenie.
    * Wygenerowano klucz aktywacji w celu aktywowania urządzenia przy użyciu zasobu usługi Data Box Edge.

 
### <a name="for-the-data-box-edge-physical-device"></a>Urządzenie fizyczne Data Box Edge

Przed wdrożeniem urządzenia:

- Upewnij się, że urządzenie zostało bezpiecznie umieszczone na płaskiej, stabilnej i poziomej powierzchni roboczej (lub podobnej).
- Sprawdź, czy lokacja, w której chcesz zamontować urządzenie, ma:
    - standardowe zasilanie prądem przemiennym z niezależnego źródła lub
    - jednostkę dystrybucji zasilania na stojaku (PDU, rack power distribution unit) z zasilaczem UPS.
- Upewnij się, że na stojaku, na którym chcesz zamontować urządzenie, masz dostępne gniazdo 1U.

### <a name="for-the-network-in-the-datacenter"></a>Sieć w centrum danych

Przed rozpoczęciem:

- Zapoznaj się z wymaganiami dotyczącymi przygotowania sieci pod kątem wdrożenia usługi Data Box Edge i skonfiguruj sieć w centrum danych zgodnie z tymi wymaganiami. Aby uzyskać więcej informacji, zobacz [Wymagania dotyczące sieci dla usługi Data Box Edge](data-box-gateway-system-requirements.md#networking-requirements).
- Aby umożliwić optymalne działanie urządzenia, przepustowość połączenia internetowego musi wynosić co najmniej 20 Mb/s.


## <a name="unpack-the-device"></a>Rozpakowywanie urządzenia

To urządzenie jest dostarczane w jednym pudełku. Aby rozpakować urządzenie, wykonaj następujące czynności. 

1. Umieść pudełko na płaskiej, poziomej powierzchni.
2. Sprawdź, czy na pudełku i na piance opakowaniowej znajdują się naderwania, zarysowania, przecięcia, uszkodzenia od wody lub inne widoczne uszkodzenia. Jeśli pudełko lub opakowanie jest poważnie uszkodzone, nie należy go otwierać. Skontaktuj się z pomocą techniczną firmy Microsoft, która pomoże Ci ustalić, czy urządzenie znajduje się w dobrym stanie umożliwiającym prawidłowe działanie.
3. Rozpakuj zawartość pudełka. Po rozpakowaniu upewnij się, że masz:
    - jedno urządzenie Edge w pojedynczej obudowie,
    - dwa przewody zasilania,
    - jeden beznarzędziowy zestaw do montażu przesuwnego (zawiera dwie szyny boczne i sprzęt do montowania).
4. Jeśli nie otrzymasz elementów wymienionych powyżej, skontaktuj się z pomocą techniczną usługi Data Box Edge. Następnym krokiem jest zamontowanie urządzenia na stojaku.


## <a name="rack-the-device"></a>Montowanie urządzenia na stojaku

Urządzenie należy zainstalować na standardowym, 19-calowym stojaku. Aby zamontować urządzenie na standardowym, 19-calowym stojaku z przednimi i tylnymi słupkami, użyj poniższej procedury.

> [!IMPORTANT]
> Aby urządzenie Data Box Edge działało poprawnie, należy je zamontować na stojaku.


1. Pociągnij przednią blokadę, aby uwolnić wewnętrzną szynę z zestawu prowadnic. Zwolnij blokadę zaczepu i popchnij środkową szynę do środka, aby wysunąć szynę. Wewnętrzna i zewnętrzna szyna powinny być teraz rozdzielone.

    ![Montowanie szyn na stojaku](./media/data-box-edge-deploy-install/rack-mount-rail-1.png)

2. Teraz zamontuj zewnętrzne szyny na pionowych elementach regału stojaka. Aby ułatwić orientację, przód szyn został oznaczony etykietą „Front”, a koniec został przymocowany do przedniej części obudowy. 
    
    1. Znajdź bolce szyny z przodu i z tyłu zestawu prowadnic. Rozciągnij szynę, aby jej długość była równa odległości między pionowymi słupkami stojaka. Najpierw przyczep zewnętrzną szynę do tylnego słupka stojaka. Dopasuj tylny uchwyt montażowy tak, aby umieścić go w otworach montażowych na stojaku.   
    2. Naciśnij i przytrzymaj spust na tylnym uchwycie, aby odsłonić metalowe haki. Wyrównaj i włóż do otworów montażowych, a następnie zwolnij spust.
    3. Wyrównaj przedni uchwyt z otworami montażowymi.
    4. Przedni uchwyt powinien być teraz przymocowany do stojaka. Opcjonalnie można użyć śrub M5 X 10L, aby przykręcić szyny do słupków, jeśli to konieczne. 

    ![Montowanie szyn na stojaku](./media/data-box-edge-deploy-install/rack-mount-rail-2.png)

3. Aby przymocować szynę wewnętrzną do obudowy, upewnij się, że otwory w szynie wewnętrznej są wyrównane z bolcami mocującymi na boku obudowy. Upewnij się, że wierzchołki bolców mocujących wystają przez otwory w szynie wewnętrznej. Pociągnij szynę w kierunku przodu obudowy, dopóki szyna nie zostanie zablokowana na miejscu ze słyszalnym kliknięciem. Powtórz te czynności dla drugiej szyny wewnętrznej. Wepchnij obudowę z przymocowaną szyną wewnętrzną do prowadnic, aby ukończyć instalację na stojaku.

    ![Montowanie szyn na stojaku](./media/data-box-edge-deploy-install/rack-mount-rail-3.png)

## <a name="cable-the-device"></a>Podłączanie kabli urządzenia

Poniższe procedury opisują sposób podłączania kabli urządzenia Edge do zasilania i sieci.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem podłączania kabli urządzenia będą potrzebne następujące elementy:

- Urządzenie fizyczne Edge, rozpakowane i zamontowane na stojaku.
- Dwa kable zasilające. 
- Dwa kable sieciowe 1-GbE RJ-45 i cztery kable miedziane 25-GbE SFP+.
- Dostęp do dwóch jednostek dystrybucji zasilania (zalecane).

Urządzenie Edge jest wyposażone w 8 dysków SSD NVMe. Na przednim panelu znajdują się diody LED stanu i przyciski zasilania. Z tyłu urządzenia znajdują się nadmiarowe zasilacze. Urządzenie ma sześć interfejsów sieciowych: dwa interfejsy 1 Gb/s i cztery interfejsy 25 Gb/s. Jest także wyposażone w kontroler zarządzania płytą główną (BMC). Zidentyfikuj różne porty na tylnym panelu montażowym urządzenia.
 
  ![Płyta montażowa okablowanego urządzenia](./media/data-box-edge-deploy-install/backplane-cabled.png)
 
Wykonaj poniższe kroki, aby podłączyć kable urządzenia do sieci i zasilania.

1. Podłącz kable zasilające do poszczególnych zasilaczy w obudowie. Aby zapewnić wysoką dostępność, zainstaluj i podłącz oba zasilacze do różnych źródeł zasilania.
2. Podłącz kable zasilające do jednostek dystrybucji zasilania stojaka. Upewnij się, że dwa zasilacze korzystają z oddzielnych źródeł zasilania.
3. Połącz interfejs sieciowy 1 GbE PORT 1 z komputerem używanym do konfigurowania urządzenia fizycznego. PORT 1 jest dedykowanym interfejsem zarządzania.
4. Połącz interfejs sieciowy 1 GbE PORT 2 z siecią centrum danych/Internetem za pomocą kabli sieciowych RJ-45. 
5. Połącz cztery interfejsy sieciowe 25 GbE PORT 3, PORT 4, PORT 5 i PORT 6 z siecią centrum danych/Internetem za pomocą miedzianych kabli SFP+. 

> [!NOTE]
> - Co najmniej jeden sieciowy interfejs danych — PORT 2, PORT 3, PORT 4, PORT 5 lub PORT 6 musi być połączony z Internetem (łączność z platformą Azure). 
> - Zalecamy, aby do wysyłania danych na platformę Azure używać interfejsu sieciowego 25 GbE, takiego jak PORT 3, PORT 4, PORT 5 lub PORT 6. 
> - Urządzenie Edge powinno być połączone z siecią centrum danych, aby mogło pozyskiwać dane z serwerów źródła danych.  


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Data Box Edge, takie jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia
> * Montowanie urządzenia na stojaku
> * Podłączanie kabli urządzenia

Przejdź do następnego samouczka, aby dowiedzieć się, jak nawiązać połączenie z urządzeniem oraz je skonfigurować i aktywować.

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z urządzeniem Data Box Edge i konfigurowanie go](./data-box-edge-deploy-connect-setup-activate.md)


