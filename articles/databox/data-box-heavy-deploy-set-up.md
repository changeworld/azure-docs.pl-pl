---
title: Samouczek dotyczący konfigurowania Azure Data Box Heavy | Microsoft Docs
description: Dowiedz się, jak podłączyć i połączyć Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: f5473ae7bd9ac1615d59d18c996e01902e1adbd6
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241413"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Samouczek: Kabel i połączenie z Azure Data Box Heavy

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Wprowadzenie do Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

W tym samouczku opisano sposób kabli, łączenia i włączania Azure Data Box Heavy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kabel Data Box Heavy
> * Nawiązywanie połączenia z Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Ten przewodnik zawiera instrukcje dotyczące sprawdzania wymagań wstępnych, kabli i połączenia urządzenia, kopiowania danych, przekazywania na platformę Azure, a następnie weryfikowania przekazanych danych.

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Azure Data Box Heavy](data-box-heavy-deploy-ordered.md)kolejności.
2. Otrzymano Data Box Heavy i stan zamówienia w portalu jest **dostarczany**.
3. Zawarto przegląd [wytycznych dotyczących bezpieczeństwa Data Box Heavy](data-box-safety.md).
4. Użytkownik musi mieć dostęp do płaskiej lokacji w centrum danych z bliskością dostępnego połączenia sieciowego, które może obsłużyć urządzenie z tym ustawieniem. Nie można zainstalować tego urządzenia w stojaku.
5. Uzyskano cztery uziemione przewody do użycia z urządzeniem magazynującym.
6. Należy posiadać komputer hosta podłączony do sieci centrum danych. Data Box Heavy skopiuje dane z tego komputera. Na komputerze hosta musi być uruchomiony [obsługiwany system operacyjny](data-box-heavy-system-requirements.md).
7. Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. 
8. Aby połączyć się z lokalnym interfejsem użytkownika i skonfigurować urządzenie, należy mieć komputer przenośny z kablem RJ-45. Aby skonfigurować każdy węzeł urządzenia, użyj laptopa.
9. Potrzebny jest kabel 1 40-GB/s lub kabel 10 GB/s dla każdego węzła urządzenia.
    - Wybierz kable, które są zgodne z interfejsem sieciowym [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) .
    - W przypadku kabla 40-GB/s urządzenie musi mieć wartość QSFP +.
    - W przypadku kabla 10 GB/s potrzebny jest kabel SFP +, który jest podłączany do przełącznika 10 GB/s na jednym końcu, przy użyciu karty QSFP + do SFP + adapter (lub karty QSA) dla punktu końcowego, który jest podłączony do urządzenia.

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Podłączanie kabla do urządzenia

Aby nawiązać połączenie z urządzeniem, wykonaj następujące czynności.

1. Sprawdź, czy urządzenie nie ma śladów naruszenia ani innych widocznych uszkodzeń. Jeśli urządzenie jest naruszone lub poważnie uszkodzone, nie kontynuuj. [Skontaktuj się z firmą pomoc techniczna firmy Microsoft](data-box-disk-contact-microsoft-support.md) natychmiast, aby ocenić, czy urządzenie jest w dobrym stanie, a jeśli zachodzi konieczność przeprowadzenia zamiany.
2. Przenieś urządzenie do lokacji instalacji.

    ![Lokacja instalacji urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zablokuj na urządzeniu urządzenia, jak pokazano poniżej.

    ![Zablokowane rzuty urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Znajdź pokrętła, które blokują przód i tylne drzwi urządzenia. Odblokuj i Przenieś tylne drzwi do momentu, gdy zostanie opróżnione po stronie urządzenia. Powtórz to również z tyłu.
    Drzwi muszą pozostać otwarte, gdy urządzenie jest w stanie zapewnić optymalny przepływ powietrza z przodu do tyłu przez urządzenie.

    ![Otwarte drzwi Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Zasobnik z tyłu urządzenia powinien mieć cztery kable zasilające. Usuń wszystkie kable z tacy i umieść je poza nimi.

    ![Data Box Heavy przewody zasilające na pasku zadań](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Następnym krokiem jest zidentyfikowanie różnych portów w tylnej części urządzenia. Istnieją dwa węzły urządzeń — **Węzeł1** i **Węzeł2**. Każdy węzeł ma cztery interfejsy sieciowe, **Zarządzanie**, **dane1**, **Dane2**, **DATA3**. Zarządzanie **służy do** konfigurowania zarządzania podczas początkowej konfiguracji urządzenia. Dane1-**DATA3** są portami danych. Porty **zarządzania** i **DATA3** są 1 GB/s, a w przypadku, gdy **dane1**, **Dane2** może współpracować z portami 40 GB lub 10 GB/s. W dolnej części dwóch węzłów urządzenia są cztery jednostki zasilacza (PSUs), które są współużytkowane przez dwa węzły urządzeń. W przypadku tego urządzenia **PSUs** są **PSU1**, **PSU2**, **PSU3**i **PSU4** od lewej do prawej.

    ![Porty Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Połącz wszystkie cztery kable zasilające z zasilaczami urządzeń. Zielone diody LED są włączane i migane.
8. Aby włączyć węzły urządzeń, użyj przycisków zasilanych na płaszczyźnie czołowej. Pozostaw przycisk potęgi w ciągu kilku sekund do momentu, aż niebieskie sygnalizatory są dostępne. Wszystkie zielone diody LED dla zasilaczy z tyłu urządzenia powinny teraz być trwałe. Przedni panel operacyjny urządzenia zawiera również diody LED błędów. Gdy dojdzie do awarii, wskazuje ona wadliwą PSUę lub wentylator lub problem z dyskami.  

    ![Data Box Heavy panelu Ops](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Pierwszy węzeł kabla dla sieci

Na jednym z węzłów urządzenia wykonaj następujące kroki, aby podłączyć kabel do sieci.

1. Aby podłączyć komputer hosta do portu zarządzania 1 GB/s, użyj kabla sieciowego CAT 6 RJ-45 (niebieski kabel na zdjęciu).
2. Użyj kabla QSFP + (włókna światłowodowego lub miedzianego), aby połączyć co najmniej 1 40 GB/s (preferowany przez 1 GB/s) interfejs sieciowy dla danych. W przypadku korzystania z przełącznika 10 GB/s należy użyć kabla SFP + z kartą QSFP + do SFP + (karta QSA), aby połączyć interfejs sieciowy 40 GB/s dla danych.

    ![Okablowanie Data Box Heavy portów](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DANE 1 i DANE2 są przełączane i nie pasują do elementów wyświetlanych w lokalnym interfejsie użytkownika sieci Web.
    > Karta kabla 40 GB/s łączy się po wstawieniu, jak pokazano poniżej.

    ![Karta Data Box Heavy 40-GB kablowa](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Skonfiguruj pierwszy węzeł

Wykonaj następujące kroki, aby skonfigurować urządzenie przy użyciu konfiguracji lokalnej i Azure Portal.

1. Pobierz poświadczenia urządzenia z portalu. Przejdź do obszaru **Ogólne > Szczegóły urządzenia**. Skopiuj wartość z pola **Hasło urządzenia**. Te hasła są powiązane z konkretną kolejnością w portalu. Dla dwóch węzłów w Data Box Heavy są wyświetlane dwa numery seryjne urządzeń. Hasło administratora urządzenia dla obu węzłów jest takie samo.

    ![Data Box Heavy poświadczenia urządzenia](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Podłącz stację roboczą klienta do urządzenia za pośrednictwem kabla sieciowego CAT6 RJ-45.
3. Skonfiguruj kartę Ethernet na komputerze używanym do łączenia się z urządzeniem przy użyciu statycznego adresu `192.168.100.5` IP i podsieci. `255.255.255.0`

    ![Data Box Heavy nawiązuje połączenie z lokalnym interfejsem użytkownika sieci Web](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Połącz się z lokalnym interfejsem użytkownika sieci Web urządzenia pod następującym adresem URL `http://192.168.100.10`:. Kliknij pozycję **Zaawansowane** , a następnie kliknij pozycję przechodzenie **do 192.168.100.10 (niebezpieczna)** .
5. Zostanie wyświetlona strona **Logowanie** dla lokalnego internetowego interfejsu użytkownika.
    
    - Jeden z numerów seryjnych węzłów na tej stronie jest zgodny zarówno w interfejsie użytkownika portalu, jak i w lokalnym interfejsie użytkownika sieci Web. Zanotuj numer węzła do mapowania numeru seryjnego. W portalu znajdują się dwa węzły i dwa numery seryjne urządzeń. To mapowanie pomaga zrozumieć, który węzeł odpowiada numerowi seryjnemu.
    - Urządzenie jest w tym momencie zablokowane.
    - Podaj hasło administratora urządzenia uzyskane w poprzednim kroku, aby zalogować się do urządzenia. Kliknij pozycję **Zaloguj się**.

    ![Zaloguj się do Data Box Heavy lokalnego interfejsu użytkownika sieci Web](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Upewnij się, że interfejsy sieciowe zostały skonfigurowane na pulpicie nawigacyjnym. W węźle urządzenia znajdują się cztery interfejsy sieciowe, dwie 1 GB/s i 2 40 GB/s. Jeden z interfejsów 1 GB/s jest interfejsem zarządzania i dlatego nie można go konfigurować. Pozostałe trzy interfejsy sieciowe są przeznaczone dla danych i mogą być konfigurowane przez użytkownika.

- Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane.
- Jeśli usługa DHCP nie jest włączona, przejdź do pozycji Ustaw interfejsy sieciowe i Przypisz statyczne adresy IP, jeśli to konieczne.

    ![Pulpit nawigacyjny Data Box Heavy 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Skonfiguruj drugi węzeł

Wykonaj kroki opisane w sekcji [Konfigurowanie pierwszego węzła](#configure-first-node) dla drugiego węzła urządzenia.

![Węzeł pulpitu nawigacyjnego Data Box Heavy 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po zakończeniu konfiguracji urządzenia możesz połączyć się z udziałami na urządzeniu i skopiować dane z komputera na urządzenie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono informacje dotyczące Azure Data Box Heavy tematów, takich jak:

> [!div class="checklist"]
> * Kabel Data Box Heavy
> * Nawiązywanie połączenia z Data Box Heavy

Przejdź do następnego samouczka, aby dowiedzieć się, jak kopiować dane na Data Box Heavy.

> [!div class="nextstepaction"]
> [Kopiowanie danych na urządzenie Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
