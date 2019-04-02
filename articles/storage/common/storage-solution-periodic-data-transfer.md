---
title: Wybierz rozwiązanie na platformie Azure do transferu danych okresowe | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wybrać rozwiązanie na platformie Azure do transferu danych podczas transferu danych okresowo.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 8f106674c1b1ec90477c7c030dc55085fcf10656
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801985"
---
# <a name="solutions-for-periodic-data-transfer"></a>Rozwiązania do transferu danych okresowe
 
Ten artykuł zawiera omówienie transferu danych rozwiązania są okresowo przesyłania danych. Transfer danych okresowe za pośrednictwem sieci można sklasyfikować jako cykliczne w regularnych odstępach czasu lub przenoszenia ciągłymi danymi. Opisano również opcje transferu danych zalecany i macierzy odpowiedniego kluczowe możliwości, w tym scenariuszu.

Aby dowiedzieć się z omówieniem wszystkich opcji transferu danych o dostępności, przejdź do [wybierz rozwiązanie transferu danych systemu Azure](storage-choose-data-transfer-solution.md).

## <a name="recommended-options"></a>Zalecane opcje

Zalecane opcje do transferu danych okresowe można podzielić na dwie kategorie w zależności od tego, czy transferu ciągłej lub cykliczne.

- **Narzędzia inicjowanych przez skrypty programowe** — w przypadku transferu danych w regularnych odstępach czasu, użyj inicjowanych przez skrypty i programowe narzędzi takich jak narzędzia AzCopy i usługi Azure Storage interfejsów API REST. Te narzędzia są przeznaczone do informatyków i deweloperów.

    - **Narzędzie AzCopy** — łatwo skopiować danych do i z obiektów blob platformy Azure, plików, za pomocą tego narzędzia wiersza polecenia i Table storage z optymalną wydajnością. Narzędzie AzCopy obsługuje współbieżność i równoległości i możliwość wznowienia operacji kopiowania, w przypadku przerwania.
    - **Azure Storage REST API/zestawów SDK** — podczas tworzenia aplikacji, możesz opracowywać aplikacje przed interfejsów API REST usługi Azure Storage i korzystanie z zestawów SDK platformy Azure, oferowane w wielu językach. Biblioteka przenoszenia danych magazynu platformy Azure przeznaczone dla o wysokiej wydajności kopiowanie danych do i z platformy Azure także mogą korzystać z interfejsów API REST.

- **Narzędzia do wprowadzania danych ciągłe** — do pozyskiwania danych ciągłe, bieżące, można wybrać jedną z urządzenia do urządzenia Data Box przesyłania online lub usługi Azure Data Factory. Te narzędzia są konfigurowane przez specjalistów IT i sposób niewidoczny dla użytkownika mogą zautomatyzować transferu danych.

    - **Usługa Azure Data Factory** — Data Factory powinien być używany do skalowania w poziomie operację transferu, i jeśli istnieje potrzeba do aranżacji i enterprise klasy funkcji monitorowania. Azure Data Factory służy do konfigurowania potoku chmury, które regularnie transferu plików między kilka usług platformy Azure, lokalnie lub kombinacji obu. Platformy Azure Data Factory umożliwia organizowanie opartych na danych przepływy pracy, które pozyskiwać dane z różnych magazynów danych i automatyzowania przenoszenia i przekształcania danych.
    - **Azure Data Box rodziny transfery online** -krawędź pola danych i bramy pola danych są urządzenia sieciowe online, które można przenieść dane do i z platformy Azure. Krawędź pola danych za pomocą sztucznej inteligencji (AI) — włączone obliczeń brzegowych wstępnie przetworzyć dane przed przekazywania. Brama pole danych jest wersja wirtualna urządzenia przy użyciu tych samych możliwości transferu danych.


## <a name="comparison-of-key-capabilities"></a>Porównanie możliwości klucza

W poniższej tabeli podsumowano różnice w kluczowych funkcji.

### <a name="scriptedprogrammatic-network-data-transfer"></a>Transfer danych sieci inicjowanych przez skrypty programistyczne

| Możliwości                  | Narzędzie AzCopy                                 | Interfejsy API REST usługi Azure Storage       |
|-----------------------------|----------------------------------------|-------------------------------|
| Współczynnik postaci                 | Narzędzie wiersza polecenia od firmy Microsoft       | Klientom opracowywanie względem magazynu <br> Interfejsy API REST przy użyciu biblioteki klienckie systemu Azure |
| Początkowa jednorazowej konfiguracji     | Minimalny                                | Średni nakład zmiennej    |
| Format danych                 | Obiekty BLOB platformy Azure, usługa Azure Files, tabele platformy Azure | Obiekty BLOB platformy Azure, usługa Azure Files, tabele platformy Azure   |
| Wydajność                 | Już zoptymalizowany                      | Optymalizowanie podczas opracowywania                  |
| Cennik                     | Bezpłatne, danych opłaty za ruch wychodzący      | Bezpłatne, danych opłaty za ruch wychodzący        |

### <a name="continuous-data-ingestion-over-network"></a>Pozyskiwanie danych ciągłe za pośrednictwem sieci

| Cecha                                       | Data Box Gateway | Data Box Edge   | Azure Data Factory        |
|----------------------------------|-----------------------------------------|--------------------------|---------------------------|
| Współczynnik postaci                                   | Urządzenie wirtualne             | Urządzenie fizyczne          | Usługi w witrynie Azure portal, lokalny agent                                                            |
| Sprzęt                                      | Z funkcji hypervisor            | Dostarczony przez firmę Microsoft    | Nie dotyczy                                                            |
| Nakład pracy początkowej konfiguracji                          | Niski (< 30 min.)            | Średni (~ Połącz godz.) | Duży (~ dni)                                                 |
| Format danych                                   | Obiekty BLOB platformy Azure, usługa Azure Files   | Obiekty BLOB platformy Azure, usługa Azure Files | [Obsługuje 70 łączników danych dla magazynów danych i formatów](https://docs.microsoft.com/azure/data-factory/copy-activity-overview#supported-data-stores-and-formats)|
| Przetwarzanie wstępne danych                           | Nie                         | Tak, za pośrednictwem obliczeń brzegowych    | Yes                                                           |
| Lokalna pamięć podręczna<br>(na potrzeby przechowywania danych lokalnych)    | Yes                        | Yes                      | Nie                                                            |
| Transfer z innych chmur                    | Nie                         | Nie                       | Yes                                                           |
| Cennik                                       | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/gateway/)                    | [Cennik](https://azure.microsoft.com/pricing/details/storage/databox/edge/)                  | [Cennik](https://azure.microsoft.com/pricing/details/data-factory/)                                                       |

## <a name="next-steps"></a>Kolejne kroki

- [Transferowanie danych za pomocą narzędzia AzCopy](/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json).
- [Więcej informacji na temat danych transferu za pomocą interfejsów API REST magazynu](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
- Zrozumieć, jak:
    - [Transferowanie danych za pomocą bramy pola danych](https://docs.microsoft.com/azure/databox-online/data-box-gateway-deploy-add-shares).
    - [Przekształcanie danych za pomocą krawędź pola danych przed wysłaniem do usługi Azure](https://docs.microsoft.com/azure/databox-online/data-box-edge-deploy-configure-compute).
- [Dowiedz się, jak transfer danych za pomocą usługi Azure Data Factory](https://docs.microsoft.com/azure/data-factory/tutorial-bulk-copy-portal).
