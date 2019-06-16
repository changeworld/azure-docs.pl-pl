---
title: Opcje dla dużych zestawów danych, średnia do Wysoka przepustowość sieci transferu danych systemu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie na platformie Azure do transferu danych, gdy średnia wysokiego przepustowość sieci w danym środowisku, a planowane transferowania dużych zestawów danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8dd55032c933cdc31b848addfdac991550376dcf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60729238"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transfer danych dla dużych zestawów danych przy użyciu umiarkowany Wysoka przepustowość sieci
 
Ten artykuł zawiera omówienie transferu danych rozwiązań po średnia wysokiego przepustowość sieci w Twoim środowisku i planujesz przeniesienie dużych zestawów danych. Opisano również opcje transferu danych zalecany i macierzy odpowiedniego kluczowe możliwości, w tym scenariuszu.

Aby dowiedzieć się z omówieniem wszystkich opcji transferu danych o dostępności, przejdź do [wybierz rozwiązanie transferu danych systemu Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Duże zestawy danych dotyczą ilości danych, zgodnie z kolejnością TB do PBs. Umiarkowany do wysokiej przepustowości odnosi się do 100 MB/s do 10 GB/s.

## <a name="recommended-options"></a>Zalecane opcje

Opcje zalecanych w tym scenariuszu zależą od tego, czy masz Średnia przepustowość sieci lub Wysoka przepustowość sieci.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Średnia przepustowość sieci (100 MB/s - 1 GB/s)

Średnia przepustowość sieci potrzebne do projektu podczas przesyłania danych za pośrednictwem sieci.

Skorzystaj z poniższej tabeli do oszacowania czasu i oparte na tym, wybierz między transferu w trybie offline lub za pośrednictwem transferu sieciowego. W tabeli przedstawiono przewidywany czas przesyłania danych z sieci dla różnych przepustowości dostępnej sieci (przy założeniu 90% wykorzystania).  

![Przekazanie sieci lub w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Jeśli transfer sieciowy jest przewidywane jest zbyt wolny, należy użyć urządzenia fizycznego. Zalecane opcje są w tym przypadku urządzenia w trybie offline transfer z rodziny Azure Data Box lub Azure Import/Export, za pomocą własnych dysków.

    - **Azure Data Box rodziny transferów w trybie offline** — przenoszenie dużych ilości danych na platformę Azure, gdy są ograniczone przez czas, dostępność sieci lub koszty przy użyciu urządzeń z urządzeń, urządzenia Data Box dostarczany przez firmę Microsoft. Kopiowanie danych lokalnych za pomocą narzędzi, takich jak Robocopy. W zależności od rozmiaru danych, przeznaczonych do przeniesienia możesz wybrać z dysku Data Box, urządzenia Data Box lub duże pole danych.
    - **Usługa Azure Import/Export** — usługi użycia usługi Azure Import/Export za wysyłanie własnych dysków twardych do bezpiecznego importowania dużych ilości danych do usługi Azure Blob storage i Azure Files. Ta usługa może również przenoszenia danych z usługi Azure Blob storage do stacji dysków do wysłania do lokacji lokalnej.

- Jeśli transfer sieciowy jest rzutowany za odpowiednie, a następnie możesz użyć dowolnej z następujących narzędzi, które są szczegółowo opisane w [Wysoka przepustowość sieci](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Wysoka przepustowość sieci (1 GB/s - 100 GB/s)

Jeśli dostępna przepustowość sieci jest wysokie, użyj jednej z następujących narzędzi.

- **Narzędzie AzCopy** — łatwo skopiować danych do i z obiektów blob platformy Azure, plików, za pomocą tego narzędzia wiersza polecenia i Table storage z optymalną wydajnością. Narzędzie AzCopy obsługuje współbieżność i równoległości i możliwość wznowienia operacji kopiowania, w przypadku przerwania.
- **Azure Storage REST API/zestawów SDK** — podczas tworzenia aplikacji, możesz opracowywać aplikacje przed interfejsów API REST usługi Azure Storage i korzystanie z zestawów SDK platformy Azure, oferowane w wielu językach.
- **Azure Data Box rodziny transfery online** — krawędź pola danych i bramy pola danych są urządzenia sieciowe online, które można przenieść dane do i z platformy Azure. Użyj urządzenia fizycznego, w przypadku jednoczesnego trzeba ciągłe wprowadzanie i wstępne przetwarzanie danych przed przekazać krawędź pola danych. Brama pole danych jest wersja wirtualna urządzenia przy użyciu tych samych możliwości transferu danych. W każdym przypadku transfer danych jest zarządzane przez urządzenie.
- **Usługa Azure Data Factory** — Data Factory powinien być używany do skalowania w poziomie operację transferu, i jeśli istnieje potrzeba do aranżacji i enterprise klasy funkcji monitorowania. Używaj fabryki danych regularnie transferowania plików między kilka usług platformy Azure, lokalnie lub kombinacji obu. Usługa Data Factory można tworzyć i zaplanować opartych na danych przepływy pracy (nazywane potokami), które pozyskiwać dane z różnych magazynów danych i automatyzowania przenoszenia i przekształcania danych.

## <a name="comparison-of-key-capabilities"></a>Porównanie możliwości klucza

W poniższej tabeli podsumowano różnice w najważniejsze funkcje umożliwiające zalecane opcje.

### <a name="moderate-network-bandwidth"></a>Średnia przepustowość sieci

Jeśli przy użyciu transferu danych w trybie offline, należy użyć poniższej tabeli, aby zrozumieć różnice w kluczowych funkcji.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Rozmiar danych                        |    Maksymalnie 35 TB                 |    Do 80 TB na każdym urządzeniu                       |    Maksymalnie 800 TB na każdym urządzeniu               |    Zmienna                            |
|    Typ danych                        |    Azure Blobs                  |    Azure Blobs<br>Azure Files                    |    Azure Blobs<br>Azure Files            |    Azure Blobs<br>Azure Files          |
|    Współczynnik postaci                      |    5 dysków SSD na zamówienie zlecenie             |    1 x 50-modułów równoważenia obciążenia. rozmiar pulpitu urządzenia na zamówienie zlecenie    |    Kg — 1 X ~ 500. duże urządzenia na zamówienie zlecenie    |    Do 10 dysków twardych/SSD na zamówienie zlecenie        |
|    Czas początkowej konfiguracji               |    Małe <br>(15 min)            |    Działania niskiej lub średniej <br> (< 30 minut)               |    Średnia<br>(1 – 2 godziny)               |    Moderowanie trudne do<br>(zmienna) |
|    Wysyłanie danych do platformy Azure               |    Tak                          |    Yes                                           |    Yes                                   |    Yes                                 |
|    Eksportowanie danych z platformy Azure           |    Nie                           |    Nie                                            |    Nie                                    |    Tak                                 |
|    Szyfrowanie                       |    AES 128-bitowy                  |    AES 256-bitowy                                   |    AES 256-bitowy                           |    AES 128-bitowy                         |
|    Sprzęt                         |     Podany firmy Microsoft          |    Podany firmy Microsoft                            |    Podany firmy Microsoft                    |    Dostarcza użytkownik                   |
|    Interfejs sieciowy                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integracja z partnerami              |    Niektóre                         |    [Wysoka](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                          |    [Wysoka](https://azuremarketplace.microsoft.com/campaigns/databox/azure-data-box)                                  |    Niektóre                                |
|    Wysyłki                         |    Zarządzany przez firmę Microsoft            |    Zarządzany przez firmę Microsoft                             |    Zarządzany przez firmę Microsoft                     |    Zarządzaną przez klienta                    |
| Stosowane, gdy dane są przenoszone         |W granicach handlowych|W granicach handlowych|W granicach handlowych|W granicach geograficznych, np. nam UE|
|    Cennik                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Jeśli przy użyciu transferu danych w trybie online, na użytek tabeli w poniższej sekcji Wysoka przepustowość sieci.

### <a name="high-network-bandwidth"></a>Wysoka przepustowość sieci

|                                     |    Narzędzia AzCopy <br>Azure PowerShell, <br>Interfejs wiersza polecenia platformy Azure             |    Interfejsy API REST, zestawy SDK usługi Azure Storage                   |    Brama pola danych lub krawędź pola danych          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Typ danych                  |    Obiekty BLOB platformy Azure, usługa Azure Files, tabele platformy Azure    |    Obiekty BLOB platformy Azure, usługa Azure Files, tabele platformy Azure    |    Obiekty BLOB platformy Azure, usługa Azure Files                           |   Obsługuje 70 łączników danych dla magazynów danych i formatów    |
|    Współczynnik postaci                |    Narzędzia wiersza polecenia                        |    Interfejs programistyczny                    |    Firma Microsoft dostarcza wirtualnej <br>lub urządzenia fizycznego     |    Usługi w witrynie Azure portal                                            |
|    Początkowa jednorazowej konfiguracji     |    Łatwość               |    Średnia                       |    Proste (< 30 minut) na Średni (1 – 2 godziny)            |    Rozbudowane                                                          |
|    Przetwarzanie wstępne danych              |    Nie                                        |    Nie                                        |    Tak (składnik obliczeniowy za pomocą przeglądarki Edge)                               |    Tak                                                                |
|    Transfer z innych chmur       |    Nie                                        |    Nie                                        |    Nie                                                    |    Tak                                                                |
|    Typ użytkownika                        |    IT Pro lub deweloperów                                       |    Deweloperskie                                       |    Specjaliści IT                                                |    Specjaliści IT                                                             |
|    Cennik                          |    Bezpłatne, danych opłaty za ruch wychodzący         |    Bezpłatne, danych opłaty za ruch wychodzący         |    [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się, jak transfer danych za pomocą Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Zrozumienie sposobu

    - [Transferowanie danych za pomocą dysku Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transferowanie danych za pomocą urządzenia Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Transferowanie danych za pomocą narzędzia AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Zrozumieć, jak:
    - [Transferowanie danych za pomocą bramy pola danych](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Przekształcanie danych za pomocą krawędź pola danych przed wysłaniem do usługi Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Dowiedz się, jak transfer danych za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Przesyłanie danych za pomocą interfejsów API REST

    - [Na platformie .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [W języku Java](https://docs.microsoft.com/java/api/overview/azure/storage/client)
