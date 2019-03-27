---
title: Microsoft Azure Data Box Disk — omówienie | Microsoft Docs — dane
description: Ten artykuł dotyczy usługi Azure Data Box Disk, rozwiązania w chmurze umożliwiającego przenoszenie dużych ilości danych na platformę Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 01/09/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand what Data Box Disk is and how it works so I can use it to import on-premises data into Azure.
ms.openlocfilehash: 96ff843ada9d9043d2c10f479d488617ae56da41
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58497724"
---
# <a name="what-is-azure-data-box-disk"></a>Co to jest usługa Azure Data Box Disk?

Rozwiązanie Microsoft Azure Data Box Disk umożliwia wysyłanie terabajtów danych lokalnych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie od 1 do 5 dysków półprzewodnikowych (SSD). Te szyfrowane dyski o pojemności 8 TB są wysyłane do centrum danych za pośrednictwem przewoźnika regionalnego. 

Za pomocą usługi Data Box w witrynie Azure Portal możesz szybko konfigurować, łączyć i odblokowywać dyski. Skopiuj dane na dyski i odeślij dyski z powrotem do platformy Azure. W centrum danych platformy Azure dane są automatycznie przekazywane z dysków do chmury przez szybki, prywatny link przekazywania do sieci.

## <a name="use-cases"></a>Przypadki zastosowań

Usługa Data Box Disk umożliwia przenoszenie terabajtów danych w scenariuszach bez łączności lub z ograniczoną łącznością z siecią. Przenoszenie danych może być jednorazowe lub okresowe. Może być to również początkowy transfer danych zbiorczych, po którym następują transfery okresowe. 

- **Migracja jednorazowa** — przenoszenie dużej ilości danych lokalnych na platformę Azure. Może być to na przykład przenoszenie danych z taśm w trybie offline do danych archiwalnych w chłodnym magazynie platformy Azure.
- **Transfer przyrostowy** — po zakończeniu początkowego transferu zbiorczego przeprowadzonego za pomocą urządzenia Data Box Disk (inicjatora) następują transfery przyrostowe za pośrednictwem sieci. Na przykład rozwiązania Commvault i Data Box Disk są używane do przenoszenia kopii zapasowych na platformę Azure. Po tej migracji następuje kopiowanie danych przyrostowych za pośrednictwem sieci do usługi Azure Storage. 
- **Okresowe operacje przekazywania** — gdy duże ilości danych są generowane okresowo i trzeba je przenosić na platformę Azure. Może to dotyczyć na przykład scenariusza eksploracji zasobów energii, w którym jest generowana zawartość wideo przeznaczona dla platform wiertniczych i elektrowni wiatrowych.

## <a name="the-workflow"></a>Przepływ pracy

Typowy przepływ obejmuje następujące kroki:

1. **Zamawianie** — utwórz zamówienie w witrynie Azure Portal oraz podaj informacje wysyłkowe i docelowe konto magazynu platformy Azure dla danych. Jeśli dyski są dostępne, platforma Azure szyfruje, przygotuje i wysyła dyski z identyfikatorem śledzenia wysyłki.

2. **Odbieranie** — rozpakowywanie i łączenie dostarczonych dysków z komputerem zawierającym dane do skopiowania. Odblokuj dyski.
    
3. **Kopiowanie danych** — przeciągnij i upuść, aby skopiować dane na dyskach.

4. **Zwracanie** — przygotuj i wyślij dyski z powrotem do centrum danych platformy Azure.

5. **Przekazywanie** — dane są automatycznie kopiowane z dysków na platformę Azure. Dyski są bezpiecznie wymazywane zgodnie z wytycznymi Narodowego Instytutu Standaryzacji i Technologii (NIST, National Institute of Standards and Technology).

W trakcie tego procesu będziesz otrzymywać powiadomienia e-mail o wszystkich zmianach stanu. Aby uzyskać więcej informacji na temat szczegółowego przepływu, przejdź do tematu [Deploy Data Box Disks in Azure portal (Wdrażanie dysków usługi Data Box Disk w witrynie Azure Portal)](data-box-disk-quickstart-portal.md).


## <a name="benefits"></a>Korzyści

Rozwiązanie Data Box Disk jest przeznaczone do przenoszenia dużych ilości danych na platformę Azure bez wpływu na sieć. Oferuje ono następujące korzyści:

- **Szybkość** — urządzenie Data Box Disk używa połączenia USB 3.0 i umożliwia przenoszenie do 35 TB danych na platformę Azure w czasie krótszym niż tydzień.   

- **Łatwość użycia** — urządzenie Data Box to rozwiązanie proste do użycia.

    - Dyski używają połączeń USB, a czas ich instalacji jest minimalny.
    - Dyski mają niewielkie rozmiary, co ułatwia ich obsługę.
    - Dyski nie mają żadnych wymagań dotyczących zasilania zewnętrznego.
    - Dyski mogą być używane z serwerem centrum danych, komputerem stacjonarnym lub laptopem.
    - Rozwiązanie umożliwia kompleksowe śledzenie w witrynie Azure Portal.    

- **Zabezpieczanie** — usługa Data Box Disk ma wbudowane zabezpieczenia dysków, danych i usługi. 
    - Dyski są odporne na modyfikacje i obsługują możliwość bezpiecznej aktualizacji. 
    - Dane na dyskach są przez cały czas zabezpieczane za pomocą 128-bitowego szyfrowania AES. 
    - Dyski można odblokować tylko przy użyciu klucza podanego w witrynie Azure Portal. 
    - Usługa jest chroniona przy użyciu funkcji zabezpieczeń platformy Azure. 
    - Po przekazaniu danych do platformy Azure dyski są czyszczone zgodnie ze standardami NIST 800-88r1.  
    
Aby uzyskać więcej informacji, przejdź do tematu [Zabezpieczenia i ochrona danych w usłudze Azure Data Box Disk](data-box-disk-security.md).


## <a name="features-and-specifications"></a>Funkcje i specyfikacje


| Specyfikacje                                          | Opis              |
|---------------------------------------------------------|--------------------------|
| Waga                                                  | < 2 funty na opakowanie. Maksymalnie 5 dysków w opakowaniu                |
| Wymiary                                              | Dysk — SSD, 2,5 cala |            
| Kable                                                  | 1 kabel USB 3.1 na dysk|
| Pojemność magazynu na zamówienie                              | 40 TB (do wykorzystania ok. 35 TB)|
| Pojemność magazynu na dysku                                   | 8 TB (do wykorzystania ok. 7 TB)|
| Interfejs danych                                          | USB   |
| Bezpieczeństwo                                                | Wstępne szyfrowanie za pomocą funkcji BitLocker i zabezpieczone aktualizacje <br> Dyski chronione za pomocą klucza dostępu <br> Dane są zaszyfrowane przez cały czas  |
| Szybkość transferu danych                                      | Do 430 MB/s w zależności od rozmiaru pliku      |
|Zarządzanie                                               | Azure Portal |


## <a name="region-availability"></a>Dostępność w danym regionie

Obecnie usługa Data Box Disk może przesyłać dane do następujących regionów świadczenia usługi Azure:


|Region platformy Azure  |Region platformy Azure  |
|---------|---------|
|Środkowo-zachodnie stany USA     |Kanada Środkowa       |        
|Zachodnie stany USA 2     |Kanada Wschodnia         |     
|Zachodnie stany USA     | Europa Zachodnia        |      
|Środkowo-południowe stany USA   |Europa Północna     |         
|Środkowe stany USA     |Australia Wschodnia|
|Środkowo-północne stany USA  |Australia Południowo-Wschodnia   |
|Wschodnie stany USA      |Australia Środkowa |
|Wschodnie stany USA 2     |Australia Środkowa 2|
|Japonia Wschodnia     |Korea Środkowa |


## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, przejdź do [strony cennika](https://azure.microsoft.com/pricing/details/databox/disk/).

## <a name="next-steps"></a>Kolejne kroki

- Zapoznaj się z [wymaganiami rozwiązania Data Box Disk](data-box-disk-system-requirements.md).
- Zapoznaj się z [limitami usługi Data Box Disk](data-box-disk-limits.md).
- Szybko wdróż usługę [Azure Data Box Disk](data-box-disk-quickstart-portal.md) w witrynie Azure Portal.
