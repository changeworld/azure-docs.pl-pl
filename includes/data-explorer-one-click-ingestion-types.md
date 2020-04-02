---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 30/03/2020
ms.author: orspodek
ms.openlocfilehash: 9ae820097f8515dc44c67d95366f96c241cb77a1
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80523053"
---
## <a name="select-an-ingestion-type"></a>Wybieranie typu pozyskiwania

W przypadku **typu połkwania**wybierz jedną z następujących opcji:
   * **z magazynu** — w polu **Łącze do magazynu** dodaj adres URL konta magazynu. Użyj [adresu URL sygnatury dostępu Współdzielonego](/azurevs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) obiektów Blob dla kont magazynu prywatnego.
   
      ![Połknienie jednym kliknięciem z magazynu](media/data-explorer-one-click-ingestion-types/from-storage-blob.png)

    * **z pliku** - wybierz **opcję Przeglądaj,** aby zlokalizować plik, lub przeciągnij plik w pole.
  
      ![Połknienie jednym kliknięciem z pliku](media/data-explorer-one-click-ingestion-types/from-file.png)

    * **z kontenera** — w polu **Łącze do magazynu** dodaj [adres URL sygnatury dostępu Współdzielonego](/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) kontenera i opcjonalnie wprowadź rozmiar próbki.

      ![Połknienie jednym kliknięciem z kontenera](media/data-explorer-one-click-ingestion-types/from-container.png)

  Pojawi się próbka danych. Jeśli chcesz, możesz go odfiltrować, aby wyświetlić tylko pliki, które zaczynają się kończyć określonymi znakami. Po dostosowaniu filtrów podgląd zostanie automatycznie zaktualizowany.
  
  Na przykład można filtrować wszystkie pliki, które zaczynają się od *danych* wyrazu i kończą na rozszerzeniu *csv.gz.*

  ![Filtr pozyskiwania jednym kliknięciem](media/data-explorer-one-click-ingestion-types/from-container-with-filter.png)
