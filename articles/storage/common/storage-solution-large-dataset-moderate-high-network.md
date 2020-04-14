---
title: Opcje transferu danych platformy Azure dla dużych zestawów danych o umiarkowanej lub wysokiej przepustowości sieci| Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure do transferu danych, gdy masz umiarkowaną do wysokiej przepustowości sieci w danym środowisku i planujesz przenieść duże zestawy danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: cfe450972acda3affbea6cbe3d262bc4b1956dc9
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81262863"
---
# <a name="data-transfer-for-large-datasets-with-moderate-to-high-network-bandwidth"></a>Transfer dużych zestawów danych w przypadku umiarkowanej lub wysokiej przepustowości sieci
 
Ten artykuł zawiera omówienie rozwiązań transferu danych, gdy masz umiarkowaną do wysokiej przepustowości sieci w danym środowisku i planujesz przenieść duże zestawy danych. W tym artykule opisano również zalecane opcje transferu danych i odpowiednią macierz możliwości klucza dla tego scenariusza.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do [witryny Wybierz rozwiązanie do transferu danych platformy Azure](storage-choose-data-transfer-solution.md).

## <a name="scenario-description"></a>Opis scenariusza

Duże zestawy danych odnoszą się do rozmiarów danych w kolejności TBs do PBs. Umiarkowana do wysokiej przepustowości sieci odnosi się do 100 Mb/s do 10 Gb/s.

## <a name="recommended-options"></a>Zalecane opcje

Opcje zalecane w tym scenariuszu zależą od tego, czy masz umiarkowaną przepustowość sieci, czy wysoką przepustowość sieci.

### <a name="moderate-network-bandwidth-100-mbps---1-gbps"></a>Umiarkowana przepustowość sieci (100 Mb/s - 1 Gb/s)

Przy umiarkowanej przepustowości sieci należy prognozować czas przesyłania danych przez sieć.

Poniższa tabela służy do szacowania czasu i na tej podstawie wybierz między transferem offline lub za pośrednictwem transferu sieciowego. W tabeli przedstawiono przewidywany czas przesyłania danych sieciowych dla różnych dostępnych przepustowości sieci (przy założeniu 90% wykorzystania).  

![Transfer sieciowy lub transfer w trybie offline](media/storage-solution-large-dataset-low-network/storage-network-or-offline-transfer.png)

- Jeśli przewiduje się, że transfer sieciowy będzie zbyt wolny, należy użyć urządzenia fizycznego. Zalecane opcje w tym przypadku są urządzenia transferu w trybie offline z rodziny Azure Data Box lub Azure Import/Export przy użyciu własnych dysków.

    - **Rodzina usługi Azure Data Box do przesyłania w trybie offline** — używaj urządzeń z urządzeń dostarczonych przez firmę Microsoft data box, aby przenieść duże ilości danych na platformę Azure, gdy jesteś ograniczony przez czas, dostępność sieci lub koszty. Kopiowanie danych lokalnych przy użyciu narzędzi, takich jak Robocopy. W zależności od rozmiaru danych przeznaczonych do przesyłania można wybrać opcję Dysk pola danych, Pole danych lub Data Box Heavy.
    - **Importowanie/eksportowanie platformy** Azure — użyj usługi Importowanie/Eksportowanie platformy Azure przez wysłanie własnych dysków, aby bezpiecznie importować duże ilości danych do magazynu obiektów Blob platformy Azure i plików platformy Azure. Ta usługa może również służyć do przesyłania danych z magazynu obiektów Blob platformy Azure na dyski i wysyłać do witryn lokalnych.

- Jeśli przewiduje się, że transfer sieciowy będzie rozsądny, można użyć dowolnego z następujących narzędzi wyszczególnionych w [programie Wysoka przepustowość sieci](#high-network-bandwidth).


### <a name="high-network-bandwidth-1-gbps---100-gbps"></a>Wysoka przepustowość sieci (1 Gb/s - 100 Gb/s)

Jeśli dostępna przepustowość sieci jest wysoka, użyj jednego z następujących narzędzi.

- **AzCopy** — użyj tego narzędzia wiersza polecenia, aby łatwo kopiować dane do i z usługi Azure Obiektów Blob, plików i magazynu tabel o optymalnej wydajności. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
- **Interfejsy API/zestawów SDK rest usługi Azure Storage** — podczas tworzenia aplikacji można tworzyć aplikację za pomocą interfejsów API rest usługi Azure Storage i używać zestawów SDK platformy Azure oferowanych w wielu językach.
- **Rodzina usługi Azure Data Box dla transferów online** — data box edge i data box gateway to urządzenia sieciowe online, które mogą przenosić dane do platformy Azure i z nich. Użyj urządzenia fizycznego Data Box Edge, gdy istnieje jednoczesna potrzeba ciągłego pozyskiwania i wstępnego przetwarzania danych przed przesłaniem. Data Box Gateway to wirtualna wersja urządzenia z tymi samymi możliwościami transferu danych. W każdym przypadku transfer danych jest zarządzany przez urządzenie.
- **Usługa Azure Data Factory** — fabryka danych powinna służyć do skalowania w poziomie operacji transferu, a jeśli istnieje potrzeba aranżacji i możliwości monitorowania klasy korporacyjnej. Użyj usługi Data Factory, aby regularnie przesyłać pliki między kilkoma usługami platformy Azure, lokalnymi lub kombinacją tych dwóch usług. Za pomocą usługi Data Factory można tworzyć i planować przepływy pracy oparte na danych (nazywane potokami), które pozyskiwania danych z różnych magazynów danych i automatyzacji przenoszenia danych i przekształcania danych.

## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższych tabelach podsumowano różnice w możliwościach klucza dla zalecanych opcji.

### <a name="moderate-network-bandwidth"></a>Umiarkowana przepustowość sieci

Jeśli używasz transferu danych w trybie offline, użyj poniższej tabeli, aby zrozumieć różnice w możliwościach klucza.

|                                     |    Data Box Disk      |    Data Box                                      |    Data Box Heavy            |    Import/Export                       |
|-------------------------------------|---------------------------------|--------------------------------------------------|------------------------------------------|----------------------------------------|
|    Rozmiar danych                        |    Do 35 TB                 |    Do 80 kb/s na urządzenie                       |    Do 800 TB na urządzenie               |    Zmienna                            |
|    Typ danych                        |    Obiekty blob platformy Azure                  |    Obiekty blob platformy Azure<br>Azure Files                    |    Obiekty blob platformy Azure<br>Azure Files            |    Obiekty blob platformy Azure<br>Azure Files          |
|    Faktor                      |    5 SSD na zamówienie             |    1 X 50 funtów. urządzenie o rozmiarze pulpitu na zamówienie    |    1 X ~500 funtów. duże urządzenie na zamówienie    |    Do 10 dysków twardych/dysków SSD na zamówienie        |
|    Początkowy czas instalacji               |    Małe <br>(15 min.            |    Od niskiego do umiarkowanego <br> (<30 min)               |    Średni<br>(1-2 godziny)               |    Umiarkowane lub trudne<br>(zmienna) |
|    Wysyłanie danych na platformę Azure               |    Tak                          |    Tak                                           |    Tak                                   |    Tak                                 |
|    Eksportowanie danych z platformy Azure           |    Nie                           |    Nie                                            |    Nie                                    |    Tak                                 |
|    Szyfrowanie                       |    AES 128-bitowy                  |    AES 256-bitowy                                   |    AES 256-bitowy                           |    AES 128-bitowy                         |
|    Sprzęt                         |     Firma Microsoft dostarczyła          |    Firma Microsoft dostarczyła                            |    Firma Microsoft dostarczyła                    |    Dostarczony klient                   |
|    Interfejs sieciowy                |    USB 3.1/SATA                 |    RJ 45, SFP+                                   |    RJ45, QSFP+                           |    SATA II/SATA III                    |
|    Integracja z partnerami              |    Niektóre                         |    [Wysokiej](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                          |    [Wysokiej](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureExpressPod)                                  |    Niektóre                                |
|    Wysyłka                         |    Zarządzane przez firmę Microsoft            |    Zarządzane przez firmę Microsoft                             |    Zarządzane przez firmę Microsoft                     |    Zarządzane przez klienta                    |
| Używanie podczas przenoszenia danych         |W granicach handlu|W granicach handlu|W granicach handlu|Ponad granicami geograficznymi, np.|
|    Cennik                          |    [Cennik](https://azure.microsoft.com/pricing/details/databox/disk/)                    |   [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/)                                      |  [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/heavy/)                               |   [Cennik](https://azure.microsoft.com/pricing/details/storage-import-export/)                            |


Jeśli korzystasz z transferu danych online, użyj tabeli w poniższej sekcji, aby uzyskać wysoką przepustowość sieci.

### <a name="high-network-bandwidth"></a>Wysoka przepustowość sieci

|                                     |    Narzędzia AzCopy, <br>Azure PowerShell, <br>Interfejs wiersza polecenia platformy Azure             |    Interfejsy API REST, zestawY SDK usługi Azure Storage REST                   |    Brama pola danych lub krawędź pola danych          |    Azure Data Factory                                            |
|-------------------------------------|------------------------------------|----------------------------------------------|----------------------------------|-----------------------------------------------------------------------|
|    Typ danych                  |    Obiekty blob platformy Azure, pliki platformy Azure, tabele platformy Azure    |    Obiekty blob platformy Azure, pliki platformy Azure, tabele platformy Azure    |    Obiekty BLOB platformy Azure, pliki platformy Azure                           |   Obsługuje ponad 70 złączy danych do magazynów danych i formatów    |
|    Faktor                |    Narzędzia wiersza polecenia                        |    Interfejs programowy                    |    Firma Microsoft dostarcza wirtualną <br>lub urządzenie fizyczne     |    Usługa w witrynie Azure portal                                            |
|    Początkowa konfiguracja jednorazowa     |    Łatwe               |    Średni                       |    Łatwe (<30 minut) do umiarkowanego (1-2 godziny)            |    Szerokie                                                          |
|    Wstępne przetwarzanie danych              |    Nie                                        |    Nie                                        |    Tak (z obliczenia krawędzi)                               |    Tak                                                                |
|    Transfer z innych chmur       |    Nie                                        |    Nie                                        |    Nie                                                    |    Tak                                                                |
|    Typ użytkownika                        |    IT Pro lub deweloper                                       |    Deweloperskie                                       |    Informatyk                                                |    Informatyk                                                             |
|    Cennik                          |    Bezpłatne, obowiązują opłaty za wyjście z danych         |    Bezpłatne, obowiązują opłaty za wyjście z danych         |    [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                                               |    [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                            |

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak przesyłać dane za pomocą pliku Import/Eksport](/azure/storage/common/storage-import-export-data-to-blobs).
- Dowiedz się, jak

    - [Przesyłanie danych za pomocą dysku data box](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Przesyłanie danych za pomocą pola danych](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
- [Przesyłanie danych za pomocą programu AzCopy](/azure/storage/common/storage-use-azcopy-v10).
- Dowiedz się, jak:
    - [Przesyłanie danych za pomocą bramy pola danych](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Przekształć dane za pomocą usługi Data Box Edge przed wysłaniem na platformę Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Dowiedz się, jak przesyłać dane za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal).
- Przesyłanie danych za pomocą interfejsów API REST

    - [W .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
    - [W języku Java](https://docs.microsoft.com/java/api/overview/azure/storage)
