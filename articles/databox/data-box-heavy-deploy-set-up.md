---
title: Samouczek dotyczący konfigurowania Azure Data Box mocno | Dokumentacja firmy Microsoft
description: Dowiedz się, jak okablowanie, a następnie nawiązać połączenie z duże pole danych platformy Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: alkohli
ms.openlocfilehash: d4cdc9b6c31c62080fed5a8528577fef72c01c3f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67271707"
---
# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy-preview"></a>Samouczek: Podłączanie kabli i nawiązać połączenie z usługi Azure Data pole duże (wersja zapoznawcza)


W tym samouczku opisano, jak okablowanie, łączenie i Włącz swoje duże pole danych platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kabel swoje duże pole danych
> * Nawiązać połączenie z Twojej duże pole danych

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Kolejność usługi Azure Data Box ciężkich](data-box-heavy-deploy-ordered.md).
2. Otrzymał swoje duże pole danych, a stan zamówienia w portalu jest **dostarczone**.
3. Po przejrzeniu [duże pole danych wytyczne bezpieczeństwa](data-box-safety.md).
4. Musi mieć dostęp do prostego lokacji w centrum danych dzięki odległości połączenie dostępnej sieci, która może pomieścić urządzenia przy użyciu ten system. To urządzenie nie może być zamontowane w stojaku.
5. W przypadku otrzymania cztery przewodów zasilania jest ona za pomocą urządzenia magazynującego.
6. Należy posiadać komputer hosta podłączony do sieci centrum danych. Twoje dane duże pole umożliwia skopiowanie danych z tego komputera. Należy uruchomić komputer-host [obsługiwany system operacyjny](data-box-heavy-system-requirements.md).
7. Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. 
8. Musisz mieć komputer przenośny za pomocą kabla RJ-45, aby nawiązać połączenie z lokalnego interfejsu użytkownika i skonfigurować urządzenie. Aby skonfigurować jeden raz każdego węzła urządzenia przy użyciu komputera przenośnego.
9. Wymagany jest jeden kabel 40-GB/s lub kabel 10 GB/s na każdy węzeł urządzenia.
    - Wybierz kable, które są zgodne z [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) interfejsu sieciowego.
    - Kabel 40-GB/s urządzenia koniec kabla musi być QSFP +.
    - Kabel 10 GB/s wymaga kabel SFP + podłączony do przełącznik 10 GB/s, na jednym końcu QSFP + SFP + karty (lub kartą QSA) dla elementu end podłączany do urządzenia.

## <a name="cable-your-device-for-power"></a>Podłączanie kabli do urządzenia zasilania

Wykonaj poniższe kroki, aby Podłączanie kabli do urządzenia.

1. Sprawdź, czy urządzenie nie ma śladów naruszenia ani innych widocznych uszkodzeń. Jeśli urządzenie jest naruszone lub poważnie uszkodzone, nie kontynuuj. [Skontaktuj się z Microsoft Support](data-box-disk-contact-microsoft-support.md) natychmiast ułatwią Ci ocenę, czy urządzenie jest w dobrym stanie, a jeśli użytkownicy chcą dostarczony zamiennika.
2. Zmienianie urządzenia w miejscu zainstalowania.

    ![Witryna instalacji urządzenia duże pole danych](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zablokuj tylnej kółka na urządzeniu, jak pokazano poniżej.

    ![Dane pole duże kółka urządzenie zablokowane](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Znajdź pokrętła, które uwalniają przód i Wstecz drzwi urządzenia. Odblokuj i przenieść drzwiami frontowymi, dopóki nie zostanie wyrównana po stronie urządzenia. Powtórz te czynności przy użyciu także drzwi Wstecz.
    Zarówno drzwi musi pozostać otwarte, gdy urządzenie jest operacyjnej, aby umożliwić optymalne powietrza przodu do tyłu przez urządzenie.

    ![Otwórz drzwi duże pole danych](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. Na pasku tyłu urządzenia powinny mieć cztery przewodów zasilania. Usuń wszystkie kable do zasobnika i umieść je specjalnie.

    ![Duże pole przewodów zasilania danych do zasobnika](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Następnym krokiem jest w celu identyfikowania różnych portów tyłu urządzenia. Istnieją dwa węzły urządzenia, **Węzeł1** i **Węzeł2**. Każdy węzeł ma cztery interfejsów sieciowych, **MGMT**, **dane1**, **dane2**, **DATA3**. **MGMT** służy do konfigurowania zarządzania podczas konfiguracji początkowej urządzenia. **Dane1**-**DATA3** portów danych. **MGMT** i **DATA3** porty są 1 GB/s, podczas gdy **dane1**, **dane2** może działać jako portów 40-GB/s lub 10 GB/s portów. W dolnej części węzły dwa urządzenia są cztery dostaw jednostki zasilania (PSUs), które są współużytkowane przez węzły dwa urządzenia. Jak twarzy to urządzenie **PSUs** są **PSU1**, **PSU2**, **PSU3**, i **PSU4** od lewej do prawej.

    ![Porty duże pole danych](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Połączyć wszystkie kable cztery zasilania do źródła zasilania urządzenia. Zielony diod LED Włącz i blink.
8. Użyj przycisków zasilania na płaszczyźnie frontonu, aby włączyć w węzłach urządzenia. Zachowaj przycisku zasilania wciśnięte przez kilka sekund, aż niebieski światła. Wszystkie diod LED zasilacze tyłu urządzenie teraz powinno być stałe. Operacyjne panelu przedniego urządzenia zawiera także diod LED błędów. W przypadku błędów, które świeci diod LED, oznacza to, wadliwe zasilania lub wentylatora lub problem z stacje dysków.  

    ![Duże pole danych — panel ops przedniej](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Kabel pierwszego węzła sieci

Na jednym z węzłów urządzenie wykonaj następujące kroki do kabla dla sieci.

1. Użyj kabla sieciowego CAT 6 RJ-45 (niebieskiego kabla na obrazie), aby podłączyć komputer hosta do portu zarządzania 1 GB/s.
2. Użyj kabla QSFP + (fiber lub Coopera) do łączenia się z co najmniej jeden interfejs sieciowy 40-GB/s (preferowane ponad 1 GB/s) dla danych. Jeśli używasz przełącznika 10 GB/s, za pomocą kabla SFP + QSFP + SFP + karty (karta QSA) do łączenia z 40 GB/s interfejsu sieciowego dla danych.

    ![Porty duże pole danych, rozpakowane](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > DANE 1 dane2 są przełączane i nie są zgodne, co jest wyświetlane w lokalnym internetowym interfejsie użytkownika.
    > Karta kabel 40 GB/s łączy po wstawieniu w sposób, jak pokazano poniżej.

    ![Duże pole adaptera kabel 40-GB/s danych](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Skonfigurować pierwszy węzeł

Wykonaj następujące kroki, aby skonfigurować urządzenia przy użyciu konfiguracji lokalnej, jak i portalu Azure.

1. Pobierz poświadczenia urządzenia z portalu. Przejdź do obszaru **Ogólne > Szczegóły urządzenia**. Skopiuj wartość z pola **Hasło urządzenia**. Te hasła są powiązane z określonej kolejności, w portalu. Odpowiadający dwa węzły w duże pole danych, zobaczysz dwa numery seryjne. Hasło administratora urządzenia dla obu węzłów jest taka sama.

    ![Poświadczenia urządzenia duże pole danych](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Podłącz stacji roboczej użytkownika do urządzenia za pośrednictwem kabla sieciowego CAT6 RJ-45.
3. Skonfiguruj kartę Ethernet na komputerze używasz do połączenia z urządzeniem przy użyciu statycznego adresu IP z `192.168.100.5` i podsieć `255.255.255.0`.

    ![Duże pole danych łączy się z lokalnego Interfejsu w przeglądarce](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Nawiązać połączenie z lokalnego Interfejsu w przeglądarce urządzenia pod adresem URL: `http://192.168.100.10`. Kliknij przycisk **zaawansowane** a następnie kliknij przycisk **przejść do 192.168.100.10 (niebezpieczne)** .
5. Zostanie wyświetlona strona **Logowanie** dla lokalnego internetowego interfejsu użytkownika.
    
    - Dopasowuje jeden węzeł numery seryjne na tej stronie zarówno interfejs użytkownika portalu, jak i lokalnego Interfejsu w przeglądarce. Zanotuj numer węzła do mapowania numeru seryjnego. Istnieją dwa węzły i dwa numery seryjne w portalu. To mapowanie pomaga zrozumieć, który węzeł odnosi się do numeru seryjnego, który.
    - Urządzenie jest w tym momencie zablokowane.
    - Podaj hasło administratora urządzenia, uzyskanych w poprzednim kroku, aby zalogować się do urządzenia. Kliknij pozycję **Zaloguj się**.

    ![Zaloguj się do pola danych — dużych lokalnego internetowego interfejsu użytkownika](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Na pulpicie nawigacyjnym upewnij się, że skonfigurowano interfejsów sieciowych. Istnieją cztery interfejsów sieciowych w węźle urządzenia, dwa 1 GB/s i dwa 40 GB/s. Jeden interfejs 1 GB/s jest interfejsem zarządzania i dlatego nie użytkownika. Pozostałe trzy interfejsy są przeznaczone dla danych i mogą być konfigurowane przez użytkownika.

- Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane.
- Jeśli nie włączono protokołu DHCP, przejdź do interfejsy sieciowe zestawu i Przypisz statyczne adresy IP, jeśli to konieczne.

    ![Węzeł pulpit nawigacyjny duże pole danych 1](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Skonfiguruj drugi węzeł

Wykonaj kroki opisane szczegółowo w [skonfigurować pierwszy węzeł](#configure-first-node) dla drugiego węzła urządzenia.

![Węzeł pulpit nawigacyjny duże pole danych 2](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po zakończeniu konfiguracji urządzenia możesz połączyć się z udziałami na urządzeniu i skopiować dane z komputera na urządzenie.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono Azure Data Box mocno tematy takie jak:

> [!div class="checklist"]
> * Kabel swoje duże pole danych
> * Nawiązać połączenie z Twojej duże pole danych

Przejdź do następnego samouczka, aby dowiedzieć się, jak skopiować dane na Twoje duże pole danych.

> [!div class="nextstepaction"]
> [Kopiowanie danych na urządzenie Azure Data Box](./data-box-heavy-deploy-copy-data.md)
