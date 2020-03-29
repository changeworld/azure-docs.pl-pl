---
title: Wybierz rozwiązanie platformy Azure do okresowego przesyłania danych| Dokumenty firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie platformy Azure do przesyłania danych podczas okresowego przesyłania danych.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 06/24/2019
ms.author: alkohli
ms.openlocfilehash: fb49802adf6242f445b700d06622d7e6aa336b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67357038"
---
# <a name="solutions-for-periodic-data-transfer"></a>Rozwiązania do okresowego transferu danych
 
Ten artykuł zawiera omówienie rozwiązań transferu danych podczas okresowego przesyłania danych. Okresowy transfer danych za pośrednictwem sieci można sklasyfikować jako powtarzający się w regularnych odstępach czasu lub ciągłego przenoszenia danych. W tym artykule opisano również zalecane opcje transferu danych i odpowiednią macierz możliwości klucza dla tego scenariusza.

Aby zapoznać się z omówieniem wszystkich dostępnych opcji transferu danych, przejdź do [witryny Wybierz rozwiązanie do transferu danych platformy Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Zalecane opcje

Zalecane opcje okresowego przesyłania danych dzielą się na dwie kategorie w zależności od tego, czy transfer jest powtarzany, czy ciągły.

- **Narzędzia skryptowe/programowe** — w przypadku transferu danych, który odbywa się w regularnych odstępach czasu, należy używać narzędzi skryptowych i programowych, takich jak interfejsy API AzCopy i Azure Storage REST. Narzędzia te są skierowane do specjalistów IT i programistów.

    - **AzCopy** — użyj tego narzędzia wiersza polecenia, aby łatwo kopiować dane do i z usługi Azure Obiektów Blob, plików i magazynu tabel o optymalnej wydajności. AzCopy obsługuje współbieżność i równoległość oraz możliwość wznowienia operacji kopiowania po przerwaniu.
    - **Interfejsy API/zestawów SDK rest usługi Azure Storage** — podczas tworzenia aplikacji można tworzyć aplikację za pomocą interfejsów API rest usługi Azure Storage i używać zestawów SDK platformy Azure oferowanych w wielu językach. Interfejsy API REST można również korzystać z biblioteki przenoszenia danych usługi Azure Storage zaprojektowane specjalnie do kopiowania danych o wysokiej wydajności do i z platformy Azure.

- **Narzędzia do ciągłego pozyskiwania danych** — w przypadku ciągłego, bieżącego pozyskiwania danych można wybrać jedno z urządzeń do przesyłania danych online data box lub usługi Azure Data Factory. Narzędzia te są ujeżdne przez specjalistów IT i mogą w przejrzysty sposób zautomatyzować przesyłanie danych.

    - **Usługa Azure Data Factory** — fabryka danych powinna służyć do skalowania w poziomie operacji transferu, a jeśli istnieje potrzeba aranżacji i możliwości monitorowania klasy korporacyjnej. Użyj usługi Azure Data Factory, aby skonfigurować potok w chmurze, który regularnie przesyła pliki między kilkoma usługami platformy Azure, lokalnie lub kombinacją tych dwóch. Usługa Azure Data Factory umożliwia organizowanie przepływów pracy opartych na danych, które pozyskiwania danych z różnych magazynów danych i automatyzacji przenoszenia danych i przekształcania danych.
    - **Rodzina usługi Azure Data Box dla transferów online** — data box edge i data box gateway to urządzenia sieciowe online, które mogą przenosić dane do platformy Azure i z nich. Data Box Edge używa obliczeń edge z obsługą sztucznej inteligencji (AI) do wstępnego przetwarzania danych przed przesłaniem. Data Box Gateway to wirtualna wersja urządzenia z tymi samymi możliwościami transferu danych.


## <a name="comparison-of-key-capabilities"></a>Porównanie kluczowych możliwości

W poniższej tabeli podsumowano różnice w kluczowych możliwościach.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Przesyłanie danych w sieci skryptowej/programowej

| Możliwości                  | Narzędzie AzCopy                                 | Interfejsy API REST usługi Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Faktor                 | Narzędzie wiersza polecenia firmy Microsoft       | Klienci rozwijają się przeciwko pamięci masowej <br> INTERFEJSY API REST przy użyciu bibliotek klienta platformy Azure |
| Początkowa konfiguracja jednorazowa     | Minimalny                                | Umiarkowany, zmienny wysiłek rozwojowy    |
| Format danych                 | Obiekty blob platformy Azure, pliki platformy Azure, tabele platformy Azure | Obiekty blob platformy Azure, pliki platformy Azure, tabele platformy Azure   |
| Wydajność                 | Już zoptymalizowane                      | Optymalizuj w miarę opracowywania                  |
| Cennik                     | Bezpłatne, obowiązują opłaty za wyjście z danych      | Bezpłatne, obowiązują opłaty za wyjście z danych        |

### <a name="continuous-data-ingestion-over-network"></a>Ciągłe pojmowanie danych przez sieć

| Funkcja                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Faktor                                   | Urządzenie wirtualne             | Urządzenie fizyczne          | Usługa w witrynie Azure portal, agent lokalnie                                                            |
| Sprzęt                                      | Twój hipernadzorcy            | Dostarczone przez firmę Microsoft    | Nie dotyczy                                                            |
| Początkowy nakład pracy instalatora                          | Niski (<30 minut.            | Umiarkowany (~kilka godzin) | Duże (~dni)                                                 |
| Format danych                                   | Obiekty BLOB platformy Azure, pliki platformy Azure   | Obiekty BLOB platformy Azure, pliki platformy Azure | [Obsługuje ponad 70 złączy danych do magazynów danych i formatów](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Wstępne przetwarzanie danych                           | Nie                         | Tak, za pomocą obliczeń edge    | Tak                                                           |
| Lokalna pamięć podręczna<br>(do przechowywania danych lokalnych)    | Tak                        | Tak                      | Nie                                                            |
| Transfer z innych chmur                    | Nie                         | Nie                       | Tak                                                           |
| Cennik                                       | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Następne kroki

- [Przesyłanie danych za pomocą programu AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Więcej informacji na temat transferu danych za pomocą interfejsów API storage REST](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).
- Dowiedz się, jak:
    - [Przesyłanie danych za pomocą bramy pola danych](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Przekształć dane za pomocą usługi Data Box Edge przed wysłaniem na platformę Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Dowiedz się, jak przesyłać dane za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
