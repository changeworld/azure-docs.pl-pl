---
title: Microsoft Azure Data Box Heavy — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy hybrydowego rozwiązania Azure Data Box, które umożliwia przenoszenie ogromnych ilości danych na platformę Azure
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: overview
ms.custom: ''
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 09/24/2018
ms.author: alkohli
ms.openlocfilehash: 0a5b7f93f9ac6cc5b1076881727a42fd5b95ff4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306193"
---
# <a name="what-is-azure-data-box-heavy-preview"></a>Czym jest Azure Data Box Heavy? (Wersja zapoznawcza)

Rozwiązanie hybrydowe Microsoft Azure Data Box umożliwia wysyłanie setek terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez przekazanie na własność za pośrednictwem frachtu urządzenia magazynującego o pojemności 1 PB. Urządzenie ma odporną na wstrząsy obudowę chroniącą i zabezpieczającą dane w czasie transportu.

Rozwiązanie Data Box Heavy jest obecnie w wersji zapoznawczej. Urządzenie można zamówić po zarejestrowaniu się w witrynie Azure Portal. Gdy urządzenie dotrze do Twojego centrum danych, możesz go skonfigurować przy użyciu lokalnego internetowego interfejsu użytkownika. Skopiuj dane z serwerów na urządzenie, a następnie wyślij urządzenie z powrotem do centrum danych platformy Azure. W centrum danych platformy Azure Twoje dane są automatycznie przekazywane z urządzenia na platformę Azure. Cały proces można śledzić przez usługę Data Box w witrynie Azure Portal.


> [!IMPORTANT]
> - Rozwiązanie Data Box Heavy jest dostępne w wersji zapoznawczej. Przed wdrożeniem tego rozwiązania zapoznaj się z [warunkami świadczenia usług Azure w wersji zapoznawczej](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> - Aby zamówić urządzenie, zarejestruj się w [portalu w wersji zapoznawczej](https://aka.ms/).
> - W okresie obowiązywania wersji zapoznawczej rozwiązanie Data Box Heavy może być wysyłane do klientów w Stanach Zjednoczonych i Unii Europejskiej. Aby uzyskać więcej informacji, przejdź do tematu [Dostępność regionalna](#region-availability).

## <a name="use-cases"></a>Przypadki zastosowań

Rozwiązanie Data Box Heavy doskonale nadaje się do przesyłania danych o rozmiarze przekraczającym 500 TB w scenariuszach z ograniczoną łącznością lub pozbawionych łączności z siecią. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. Poniżej przedstawiono różne scenariusze, w których można wykorzystać rozwiązanie Data Box Heavy do transferu danych.

 - **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure. 
     - Przenoszenie biblioteki multimediów przechowywanej offline na taśmach na platformę Azure w celu utworzenia biblioteki multimediów w trybie online.
     - Migrowanie farmy maszyn wirtualnych, programu SQL Server i aplikacji na platformę Azure.
     - Przenoszenie danych historycznych na platformę Azure na potrzeby szczegółowej analizy i raportowania za pomocą usługi HDInsight.

 - **Początkowy transfer zbiorczy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą rozwiązania Data Box Heavy (inicjatora) następują transfery przyrostowe za pośrednictwem sieci. 
     - Na przykład partnerzy rozwiązania tworzenia kopii zapasowych, tacy jak Commvault, i rozwiązanie Data Box Heavy są używane do przenoszenia dużych początkowych kopii zapasowych historycznych danych na platformę Azure. Po zakończeniu tego procesu dane przyrostowe są przekazywane za pośrednictwem sieci do usługi Azure Storage.

 - **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.      

## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Heavy jest przeznaczone do przenoszenia ogromnych ilości danych na platformę Azure przy niewielkim wpływie na sieć. Oferuje ono następujące korzyści:

- **Szybkość** — rozwiązanie Data Box Heavy korzysta z interfejsów sieciowych wysokiej wydajności o przepustowości 40 GB/s.

- **Bezpieczeństwo** — rozwiązanie Data Box Heavy ma wbudowane zabezpieczenia urządzenia, danych i usługi.
    - Urządzenie ma odporną na wstrząsy obudowę zabezpieczoną antywłamaniowymi śrubami i nalepkami umożliwiającymi wykrycie manipulacji. 
    - Dane na urządzeniu są przez cały czas zabezpieczane za pomocą 256-bitowego szyfrowania AES.
    - Urządzenie można odblokować tylko przy użyciu hasła podanego w witrynie Azure Portal.
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure.
    - Po przekazaniu danych na platformę Azure dyski urządzenia są czyszczone zgodnie ze standardami NIST 800-88r1.


<!--## Features and specifications

The Data Box Heavy device has the following features in this release.

| Specifications                                          | Description              |
|---------------------------------------------------------|--------------------------|
| Weight                                                  | < 50 lbs.                |
| Dimensions                                              | Device - Width: 309.0 mm Height: 430.4 mm Depth: 502.0 mm |            
| Rack space                                              | 7 U when placed in the rack on its side (cannot be rack-mounted)|
| Cables required                                         | 1 X power cable (included) <br> 2 RJ45 cables <br> 2 X SFP+ Twinax copper cables|
| Storage capacity                                        | 100 TB <br> 80 TB usable capacity after RAID 5 protection|
| Network interfaces                                      | 2 X 1 GbE interface - MGMT, DATA 3. <br> MGMT - for management, not user configurable, used for initial setup <br> DATA3 - for data, user configurable, and is dynamic by default <br> MGMT and DATA 3 can also work as 10 GbE <br> 2 X 10 GbE interface - DATA 1, DATA 2 <br> Both are for data, can be configured as dynamic (default) or static |
| Data transfer media                                     | RJ45, SFP+ copper 10 GbE Ethernet  |
| Security                                                | Rugged device casing with tamper-proof custom screws <br> Tamper-evident stickers placed at the bottom of the device|
| Data transfer rate                                      | Up to 80 TB in a day over 10 GbE network interface        |
| Management                                              | Local web UI - one-time initial setup and configuration <br> Azure portal - day-to-day device management        |-->

## <a name="components"></a>Składniki

Rozwiązanie Data Box Heavy składa się z następujących składników:

* **Urządzenie Data Box Heavy** — urządzenie fizyczne z odporną na wstrząsy obudową, które przechowuje dane w bezpieczny sposób. Urządzenie ma 800 TB pojemności magazynu do wykorzystania. 

    
* **Usługa Data Box** — rozszerzenie witryny Azure Portal, które pozwala na zarządzanie urządzeniem Data Box Heavy za pomocą internetowego interfejsu umożliwiającego uzyskiwanie dostępu z różnych lokalizacji geograficznych. Usługa Data Box umożliwia wykonywanie codziennych zadań administracyjnych na urządzeniu Data Box Heavy. Zadania usługi obejmują tworzenie zamówień i zarządzanie nimi, wyświetlanie alertów i zarządzanie nimi oraz zarządzanie udziałami.  

* **Lokalny internetowy interfejs użytkownika** — internetowy interfejs użytkownika używany do konfigurowania urządzenia (dzięki czemu może ono nawiązać połączenie z siecią lokalną) oraz do rejestrowania urządzenia w usłudze Data Box. Lokalny internetowy interfejs użytkownika umożliwia również wyłączanie i ponowne uruchamianie urządzenia, wyświetlanie dzienników kopiowania oraz kontaktowanie się z pomocą techniczną firmy Microsoft w celu zgłoszenia żądania obsługi.


## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli urządzenie jest dostępne, platforma Azure przygotowuje i wysyła urządzenie z identyfikatorem śledzenia przesyłki.

2. **Odbieranie** — gdy urządzenie zostanie dostarczone, podłącz je do sieci i zasilania przy użyciu wskazanych przewodów. Włącz urządzenie i nawiąż z nim połączenie. Skonfiguruj sieć urządzenia i zainstaluj udziały na komputerze-hoście, z którego chcesz skopiować dane.

3. **Kopiowanie danych** — skopiuj dane do udziałów urządzenia Data Box Heavy.

4. **Zwracanie** — przygotuj, wyłącz i odeślij urządzenie z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z urządzenia na platformę Azure. Dyski urządzenia są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu będziesz otrzymywać powiadomienia e-mail o wszystkich zmianach stanu. 

## <a name="region-availability"></a>Dostępność w danym regionie

Rozwiązanie Data Box Heavy umożliwia przesyłanie danych w zależności od regionu, w którym wdrożono usługę, kraju, do którego wysłano urządzenie, oraz docelowego konta usługi Azure Storage, do którego przekazano dane. 

- **Dostępność usługi** — w tej wersji rozwiązanie Data Box Heavy jest dostępne w następujących regionach:
    - Wszystkie regiony chmury publicznej w Stanach Zjednoczonych — Zachodnio-środkowe stany USA, Zachodnie stany USA 2, Zachodnie stany USA, Południowo-środkowe stany USA, Środkowe stany USA, Północno-środkowe stany USA, Wschodnie stany USA i Wschodnie stany USA 2.
    - Unia Europejska — Europa Zachodnia i Europa Północna.
    - Zjednoczone Królestwo — Południowe Zjednoczone Królestwo i Zachodnie Zjednoczone Królestwo.
    - Francja — Francja Środkowa i Francja Południowa.

- **Docelowe konta magazynu** — konta magazynu do przechowywania danych są dostępne we wszystkich regionach świadczenia usługi Azure, w których ta usługa jest dostępna. 

## <a name="sign-up"></a>Rejestrowanie

Rozwiązanie Data Box Heavy jest dostępne w wersji zapoznawczej po wcześniejszym zarejestrowaniu się. Wykonaj poniższe kroki, aby zarejestrować się na potrzeby rozwiązania Data Box Heavy:

1. Zaloguj się do witryny Azure Portal pod adresem https://aka.ms/azuredatabox.
2. Kliknij znak **+**, aby utworzyć nowy zasób. Wyszukaj **Azure Data Box**. Wybierz usługę **Azure Data Box**.

    <!--![The Data Box Heavy sign up 1]()-->

3. Kliknij pozycję **Utwórz**.

    <!--![The Data Box Heavy sign up 2]()-->

4. Wybierz subskrypcję, w ramach której chcesz używać rozwiązania Data Box Heavy w wersji zapoznawczej. Wybierz region, w którym chcesz wdrożyć zasób rozwiązania Data Box Heavy. W opcji **Data Box Heavy** kliknij pozycję **Zarejestruj się**.

   <!--![The Data Box Heavy sign up 3]()-->

5. Odpowiedz na pytania dotyczące kraju przechowywania danych, przedziału czasowego, docelowej usługi platformy Azure na potrzeby transferu danych, przepustowości sieci i częstotliwości transferu danych. Przejrzyj zasady ochrony prywatności i warunki i zaznacz pole wyboru obok pozycji Firma Microsoft może używać Twojego adresu e-mail do kontaktu.

    <!--![The Data Box Heavy sign up 4]()-->

Po utworzeniu konta i włączeniu opcji korzystania z wersji zapoznawczej można zamówić usługę Data Box Heavy.




