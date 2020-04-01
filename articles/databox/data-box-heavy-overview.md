---
title: Microsoft Azure Data Box Heavy — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy hybrydowego rozwiązania Azure Data Box, które umożliwia przenoszenie ogromnych ilości danych na platformę Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 08/28/2019
ms.author: alkohli
ms.openlocfilehash: 2f1f01a8cfa25c222848e7cc5c86dc3532eba348
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437778"
---
# <a name="what-is-azure-data-box-heavy"></a>Czym jest Azure Data Box Heavy?

Usługa Azure Data Box Heavy umożliwia szybkie, niedrogie i niezawodne wysyłanie setek terabajtów danych na platformę Azure. Dane są przesyłane na platformę Azure przez wysłanie urządzenia Data Box Heavy o pojemności 1 PB, które wypełniasz danymi i odsyłasz do firmy Microsoft. Urządzenie ma wytrzymałą obudowę, która chroni i zabezpiecza dane podczas przesyłania.

Po odebraniu urządzenia w centrum danych należy skonfigurować je przy użyciu lokalnego interfejsu użytkownika sieci Web. Skopiuj dane z serwerów na urządzenie, a następnie wyślij urządzenie z powrotem do centrum danych platformy Azure. W centrum danych platformy Azure dane są przekazywane do kont usługi Azure Storage. Możesz śledzić cały proces end-to-end w witrynie Azure portal.


> [!IMPORTANT]
> - Aby zażądać urządzenia, zarejestruj się w [witrynie Azure portal](https://portal.azure.com).


## <a name="use-cases"></a>Przypadki zastosowań

Data Box Heavy najlepiej nadaje się do rozmiarów danych w setkach terabajtów, gdzie łączność sieciowa jest niewystarczająca do przekazania danych na platformę Azure. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Heavy do transferu danych.

 - **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure.
     - Przenieś bibliotekę multimediów z taśm offline na platformę Azure, aby utworzyć bibliotekę multimediów online.
     - Migrowanie farmy maszyn wirtualnych, serwera SQL i aplikacji na platformę Azure.
     - Przenoszenie danych historycznych na platformę Azure w celu dogłębnej analizy i raportowania przy użyciu usługi HDInsight.

 - **Początkowy transfer zbiorczy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą rozwiązania Data Box Heavy (inicjatora) następują transfery przyrostowe za pośrednictwem sieci.
     - Na przykład Data Box Heavy i partner rozwiązań do tworzenia kopii zapasowych są używane do przenoszenia początkowej dużej historycznej kopii zapasowej na platformę Azure. Po zakończeniu tego procesu dane przyrostowe są przekazywane za pośrednictwem sieci do usługi Azure Storage.

 - **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

## <a name="benefits"></a>Korzyści

Data Box Heavy został zaprojektowany, aby przenieść ogromne ilości danych na platformę Azure z niewielkim lub żadnym wpływem na sieć. Oferuje ono następujące korzyści:

- **Prędkość** — Data Box Heavy wykorzystuje wydajne interfejsy sieciowe 40 Gb/s.

- **Bezpieczeństwo** — Data Box Heavy ma wbudowane zabezpieczenia urządzenia, danych i usługi.
    - Urządzenie ma odporną na wstrząsy obudowę zabezpieczoną antywłamaniowymi śrubami i nalepkami umożliwiającymi wykrycie manipulacji.
    - Dane na urządzeniu są przez cały czas zabezpieczane za pomocą 256-bitowego szyfrowania AES.
    - Urządzenie można odblokować tylko przy użyciu hasła podanego w witrynie Azure Portal.
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure.
    - Po przekazaniu danych na platformę Azure dyski na urządzeniu są czyszczone, zgodnie ze standardami National Institute of Standards and Technology (NIST) 800-88r1.


## <a name="features-and-specifications"></a>Funkcje i specyfikacje

Urządzenie Data Box Heavy ma następujące funkcje w tej wersji.

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | ~ 500 funtów. <br>Urządzenie na kołach blokujących do transportu|
| Wymiary                                              | Szerokość: 26 cali Wysokość: 28 cali Długość: 48 cali |
| Miejsce w stojaku                                              | Nie można zamontować w stelażu|
| Wymagane przewody                                         | 4 zbrojone przewody zasilające 120 V / 10 A (NEMA 5-15) w zestawie <br> Urządzenie obsługuje moc do 240 V i ma gniazda zasilania C-13 <br> Używanie kabli sieciowych zgodnych z [mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Zasilanie                                                    | 4 wbudowane zasilacze (PSU) współdzielone w obu węzłach urządzenia <br> Typowy pochw, pochyłej mocy o mocy 1200 W|
| Pojemność magazynu                                        | ~ 1-PB surowy, 70 dysków po 14 TB każdy <br> Pojemność użytkowa 770 TB|
| Liczba węzłów                                          | 2 niezależne węzły na urządzenie (po 500 TB) |
| Interfejsy sieciowe na węzeł                             | 4 interfejsy sieciowe na węzeł <br><br> MGMT, DATA3 <ul><li> 2 interfejsy X 1-GbE </li><li> MGMT jest przeznaczony do zarządzania i wstępnej konfiguracji, a nie do konfiguracji użytkownika </li><li> DATA3 jest domyślnie konfigurowanym przez użytkownika i dynamicznym protokołem konfiguracji hosta (DHCP)</li></ul>INTERFEJSY DANYCH DATA1, DATA2 <ul><li>2 interfejsy X 40 GbE </li><li> Możliwość skonfigurowania przez użytkownika dla usługi DHCP (domyślna) lub statyczna</li></ul>|


## <a name="components"></a>Składniki

Rozwiązanie Data Box Heavy składa się z następujących składników:

* **Urządzenie Data Box Heavy** — urządzenie fizyczne z odporną na wstrząsy obudową, które przechowuje dane w bezpieczny sposób. Pojemność pamięci masowej tego urządzenia wynosi 770 TB.
    
* **Usługa Data Box** — rozszerzenie witryny Azure Portal, które pozwala na zarządzanie urządzeniem Data Box Heavy za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Użyj usługi Data Box do administrowania urządzeniem Data Box Heavy. Zadania usługi obejmują tworzenie zamówień i zarządzanie nimi, wyświetlanie alertów i zarządzanie nimi oraz zarządzanie udziałami.  

* **Lokalny internetowy interfejs użytkownika** — internetowy interfejs użytkownika używany do konfigurowania urządzenia (dzięki czemu może ono nawiązać połączenie z siecią lokalną) oraz do rejestrowania urządzenia w usłudze Data Box. Lokalny internetowy interfejs użytkownika umożliwia również wyłączanie i ponowne uruchamianie urządzenia, wyświetlanie dzienników kopiowania oraz kontaktowanie się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.


## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli urządzenie jest dostępne, platforma Azure przygotowuje i wysyła urządzenie z identyfikatorem śledzenia przesyłki.

2. **Odbieranie** — gdy urządzenie zostanie dostarczone, podłącz je do sieci i zasilania przy użyciu wskazanych przewodów. Włącz urządzenie i nawiąż z nim połączenie. Skonfiguruj sieć urządzenia i zainstaluj udziały na komputerze-hoście, z którego chcesz skopiować dane.

3. **Kopiowanie danych** — skopiuj dane do udziałów urządzenia Data Box Heavy.

4. **Zwracanie** — przygotuj, wyłącz i odeślij urządzenie z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z urządzenia na platformę Azure. Dyski urządzenia są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu otrzymasz powiadomienie e-mailem o wszystkich zmianach statusu.

## <a name="region-availability"></a>Dostępność w danym regionie

Data Box Heavy można przesyłać dane na podstawie regionu, w którym usługa jest wdrażana, kraju/regionu, do którego urządzenie jest wysyłane, a docelowe konto magazynu platformy Azure, w którym są przesyłane dane.

- **Dostępność usługi** — w tej wersji rozwiązanie Data Box Heavy jest dostępne w następujących regionach:
    - Wszystkie regiony chmury publicznej w Stanach Zjednoczonych — Zachodnio-środkowe stany USA, Zachodnie stany USA 2, Zachodnie stany USA, Południowo-środkowe stany USA, Środkowe stany USA, Północno-środkowe stany USA, Wschodnie stany USA i Wschodnie stany USA 2.
    - Unia Europejska — Europa Zachodnia i Europa Północna.
    - Zjednoczone Królestwo — Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo.
    - Francja — Francja Środkowa i Francja Południowa.

- **Docelowe konta magazynu** — konta magazynu do przechowywania danych są dostępne we wszystkich regionach świadczenia usługi Azure, w których ta usługa jest dostępna.

Aby uzyskać najbardziej aktualne informacje na temat dostępności regionu dla danych Box Heavy, przejdź do [produktów platformy Azure według regionów.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all)

## <a name="sign-up"></a>Rejestrowanie

Aby zarejestrować się w u danych Data Box Heavy, należy wykonać następujące kroki:

1. [Zaloguj się do witryny Azure portal](https://portal.azure.com).
2. Kliknij **przycisk + Utwórz zasób,** aby utworzyć nowy zasób. Wyszukaj **Azure Data Box**. Wybierz usługę **Azure Data Box**.
3. Kliknij przycisk **Utwórz**.
4. Wybierz subskrypcję, której chcesz użyć dla usługi Data Box Heavy. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Heavy. W opcji **Data Box Heavy** kliknij pozycję **Zarejestruj się**.
5. Odpowiedz na pytania dotyczące kraju/regionu rezydencji danych, przedziału czasowego, docelowej usługi platformy Azure dla transferu danych, przepustowości sieci i częstotliwości transferu danych. Przejrzyj zasady ochrony prywatności i warunki i zaznacz pole wyboru obok pozycji Firma Microsoft może używać Twojego adresu e-mail do kontaktu.

Po zarejestrowaniu się możesz zamówić plik Data Box Heavy.

    
