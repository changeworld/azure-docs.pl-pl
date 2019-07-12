---
title: Microsoft Azure Data Box Heavy — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy hybrydowego rozwiązania Azure Data Box, które umożliwia przenoszenie ogromnych ilości danych na platformę Azure
services: databox
documentationcenter: NA
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: overview
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 0f4657cdd71a104ca111f62a6e9757b5a33b46e8
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592300"
---
# <a name="what-is-azure-data-box-heavy"></a>Czym jest Azure Data Box Heavy?

Duże pole danych platformy Azure pozwala na wysyłanie setki terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Dane są przesyłane do platformy Azure przez wysyłanie duże pole danych urządzenia z pojemnością magazynu 1 PB, wypełnij ze swoimi danymi i wysłać do firmy Microsoft. Urządzenie ma rugged wielkości liter do ochrony i zabezpieczanie danych podczas przesyłania.

Gdy urządzenia są odbierane w centrum danych, skonfigurować go przy użyciu lokalnego Interfejsu w przeglądarce. Skopiuj dane z serwerów na urządzenie, a następnie wyślij urządzenie z powrotem do centrum danych platformy Azure. W centrum danych platformy Azure Twoje dane są przekazywane do kont usługi Azure Storage. Możesz śledzić cały proces end-to-end w witrynie Azure portal.


> [!IMPORTANT]
> - Aby zażądać urządzenia, zarejestruj się w [witryny Azure portal](https://portal.azure.com).


## <a name="use-cases"></a>Przypadki zastosowań

Duże pole danych jest najbardziej odpowiednia w przypadku ilości danych przez setki terabajtów, gdy łączność sieciowa jest za mała, aby przekazać dane do usługi Azure. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Heavy do transferu danych.

 - **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure.
     - Przenieś bibliotekę multimediów z taśmy w trybie offline na platformę Azure, aby utworzyć bibliotekę multimediów w trybie online.
     - Migruj swoje farma maszyn wirtualnych programu SQL server i aplikacji na platformie Azure.
     - Przenieś dane historyczne na platformę Azure do szczegółowej analizy i raportu za pomocą HDInsight.

 - **Początkowy transfer zbiorczy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą rozwiązania Data Box Heavy (inicjatora) następują transfery przyrostowe za pośrednictwem sieci.
     - Na przykład duże pole danych i jej partnerów rozwiązania tworzenia kopii zapasowych są używane do przenoszenia początkowa kopia zapasowa historycznych dużych na platformie Azure. Po zakończeniu tego procesu dane przyrostowe są przekazywane za pośrednictwem sieci do usługi Azure Storage.

 - **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

## <a name="benefits"></a>Korzyści

Duże pole danych jest przeznaczony do przenieść duże ilości danych na platformę Azure z małym wpływu w sieci. Oferuje ono następujące korzyści:

- **Szybkość** — duże pole danych korzysta z interfejsów sieciowych o wysokiej wydajności, 40 GB.

- **Zabezpieczenia** — duże pole danych zawiera wbudowane zabezpieczenia dla urządzenia, dane i usługi.
    - Urządzenie ma odporną na wstrząsy obudowę zabezpieczoną antywłamaniowymi śrubami i nalepkami umożliwiającymi wykrycie manipulacji.
    - Dane na urządzeniu są przez cały czas zabezpieczane za pomocą 256-bitowego szyfrowania AES.
    - Urządzenie można odblokować tylko przy użyciu hasła podanego w witrynie Azure Portal.
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure.
    - Po przekazaniu danych do platformy Azure dyski na urządzeniu są czyszczone, zgodnie ze standardami 800 88r1 National Institute of Standards and Technology (NIST).


## <a name="features-and-specifications"></a>Funkcje i specyfikacje

Duże pole dane urządzenie ma następujące funkcje w tej wersji.

| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | ~ 500 modułów równoważenia obciążenia. <br>Urządzenie o blokowaniu koła dla transportu|
| Wymiary                                              | Szerokość: 26 cala, wysokość: 28 cala długość: 48 cala |
| Miejsce w stojaku                                              | Nie może być montowane w stojaku|
| Wymagane przewody                                         | 4 zaangażowaniu 120 V / 10 A zasilania dołączony przewodów (NEMA 5 – 15) <br> Urządzenie obsługuje maksymalnie 240 power V i ma C-13 power zbiorników <br> Użyj kable sieciowe są zgodne z [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html)  |
| Zasilania                                                    | 4 wbudowanej dostaw jednostki (PSUs) udostępniane w węzłach urządzenia <br> zasilacz 1200 typowe power rysowania|
| Pojemność magazynu                                        | ~ 1-PB raw, 70 dyski 14 TB <br> 770 TB pojemności do wykorzystania|
| Liczba węzłów                                          | 2 węzły niezależnych na urządzenie (500 TB każdy) |
| Interfejsy sieciowe na węzeł                             | 4 interfejsów sieciowych w każdym węźle <br><br> MGMT, DATA3 <ul><li> 2 x 1 GbE interfejsów </li><li> MGMT służy do zarządzania i konfiguracji początkowej, można skonfigurować użytkownika </li><li> Data3 jest konfigurowanych przez użytkownika i dynamiczne Host Configuration Protocol (DHCP) domyślnie</li><li>Interfejsy sieciowe 1 GbE mogą być również skonfigurowany jako interfejsy 10 GbE</li></ul>Dane1, interfejsy danych dane2 <ul><li>Interfejsy GbE 2 x 40 </li><li> Użytkownika można ich konfigurować dla protokołu DHCP (ustawienie domyślne) lub statycznym</li></ul>|


## <a name="components"></a>Składniki

Rozwiązanie Data Box Heavy składa się z następujących składników:

* **Urządzenie Data Box Heavy** — urządzenie fizyczne z odporną na wstrząsy obudową, które przechowuje dane w bezpieczny sposób. To urządzenie ma 770 TB pojemności magazynu można używać.
    
* **Usługa Data Box** — rozszerzenie witryny Azure Portal, które pozwala na zarządzanie urządzeniem Data Box Heavy za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Usługa Data Box do administrowania urządzeniem duże pole danych. Zadania usługi obejmują tworzenie zamówień i zarządzanie nimi, wyświetlanie alertów i zarządzanie nimi oraz zarządzanie udziałami.  

* **Lokalny internetowy interfejs użytkownika** — internetowy interfejs użytkownika używany do konfigurowania urządzenia (dzięki czemu może ono nawiązać połączenie z siecią lokalną) oraz do rejestrowania urządzenia w usłudze Data Box. Lokalny internetowy interfejs użytkownika umożliwia również wyłączanie i ponowne uruchamianie urządzenia, wyświetlanie dzienników kopiowania oraz kontaktowanie się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.


## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli urządzenie jest dostępne, platforma Azure przygotowuje i wysyła urządzenie z identyfikatorem śledzenia przesyłki.

2. **Odbieranie** — gdy urządzenie zostanie dostarczone, podłącz je do sieci i zasilania przy użyciu wskazanych przewodów. Włącz urządzenie i nawiąż z nim połączenie. Skonfiguruj sieć urządzenia i zainstaluj udziały na komputerze-hoście, z którego chcesz skopiować dane.

3. **Kopiowanie danych** — skopiuj dane do udziałów urządzenia Data Box Heavy.

4. **Zwracanie** — przygotuj, wyłącz i odeślij urządzenie z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z urządzenia na platformę Azure. Dyski urządzenia są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu otrzymasz powiadomienie za pośrednictwem poczty e-mail na wszystkie zmiany stanu.

## <a name="region-availability"></a>Dostępność w danym regionie

Duże pole danych mogą przesyłać dane oparte na region, w którym wdrożono usługę, kraj/region, z którym urządzenie jest dostarczane i docelowego konta magazynu platformy Azure, w którym transferu danych.

- **Dostępność usługi** — w tej wersji rozwiązanie Data Box Heavy jest dostępne w następujących regionach:
    - Wszystkie regiony chmury publicznej w Stanach Zjednoczonych — Zachodnio-środkowe stany USA, Zachodnie stany USA 2, Zachodnie stany USA, Południowo-środkowe stany USA, Środkowe stany USA, Północno-środkowe stany USA, Wschodnie stany USA i Wschodnie stany USA 2.
    - Unia Europejska — Europa Zachodnia i Europa Północna.
    - Zjednoczone Królestwo — Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo.
    - Francja — Francja Środkowa i Francja Południowa.

- **Docelowe konta magazynu** — konta magazynu do przechowywania danych są dostępne we wszystkich regionach świadczenia usługi Azure, w których ta usługa jest dostępna.

Aby uzyskać najbardziej aktualne informacje na temat dostępności region dla duże pole danych, przejdź do [produkty Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all).

## <a name="sign-up"></a>Rejestrowanie

Wykonaj poniższe kroki, aby zasubskrybować duże pole danych:

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. Kliknij przycisk **+ Utwórz zasób** do utworzenia nowego zasobu. Wyszukaj **Azure Data Box**. Wybierz usługę **Azure Data Box**.
3. Kliknij przycisk **Utwórz**.
4. Wybierz subskrypcję, dla której chcesz użyć dla dużych pola danych. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Heavy. W opcji **Data Box Heavy** kliknij pozycję **Zarejestruj się**.
5. Odpowiedzi na pytania dotyczące danych częstotliwość transferu docelowej przedziału czasu, kraj/region, w miejscu zamieszkania użytkownika Usługa transferu danych, przepustowość sieci i danych. Przejrzyj zasady ochrony prywatności i warunki i zaznacz pole wyboru obok pozycji Firma Microsoft może używać Twojego adresu e-mail do kontaktu.

Po zarejestrowaniu, może zamówić łączność obejmującą duże pole danych.

    
