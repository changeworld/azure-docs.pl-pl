---
title: Wyświetlanie stanu zadań Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wyświetlić stan zadania importu/eksportu i dysków używanych.
author: alkohli
manager: jeconnoc
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.openlocfilehash: 176cbf190b6442682a222eb4f24b6583fb87a46b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34660881"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Wyświetlanie stanu zadań Import/Eksport Azure

Ten artykuł zawiera informacje na temat wyświetlić stan dysku i zadania dla zadań Import/Eksport Azure. Usługa Import/Eksport Azure jest używany do bezpiecznego przesyłania dużych ilości danych obiektów blob Azure i usługi pliki Azure. Usługa jest również używana do eksportowania danych z magazynu obiektów Blob platformy Azure.  

## <a name="view-job-and-drive-status"></a>Wyświetlanie stanu zadania i dysku
Można śledzić stan import lub wyeksportować zadań z portalu Azure. Kliknij przycisk **importu/eksportu** kartę. Na stronie zostanie wyświetlona lista zadań.

![Widok stanu zadania](./media/storage-import-export-service/jobstate.png)


## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Zostanie wyświetlony jeden z następujących stanów zadania w zależności od tego, w którym dysk jest w procesie.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania jest ustawiony stan **tworzenie**. Gdy zadanie jest w **tworzenie** stanu usługi Import/Eksport przyjęto założenie, dyski nie zostały wysłane do centrum danych. Zadanie może pozostawać w tym stanie przez maksymalnie dwa tygodnie, po których zostanie on automatycznie usunięty przez usługę. |
| Wysyłka | Po wysyłasz do pakietu, należy zaktualizować informacje o śledzeniu w portalu Azure.  Spowoduje to włączenie zadania do **wysyłania** stanu. Zadanie pozostanie w **wysyłania** stanu przez maksymalnie dwa tygodnie. 
| Odebrane | Po otrzymaniu wszystkich dysków w centrum danych, stan zadania ma wartość **odebrane**. |
| Transferowanie | Po rozpoczęciu przetwarzania co najmniej jeden dysk ma ustawioną wartość Stan zadania **transferowanie**. Aby uzyskać więcej informacji, przejdź do [stany stacji](#view-drive-status). |
| Pakowanie | Po zakończeniu wszystkich dysków przetwarzania, zadanie jest umieszczany w **pakowania** stan do momentu dyski są wysyłane z powrotem. |
| Ukończony | Po wszystkich dysków są wysyłane do Ciebie, jeśli zadanie zostało ukończone bez błędów, następnie zadanie jest ustawiana na **Ukończono**. Zadanie jest automatycznie usuwane po 90 dniach w **Ukończono** stanu. |
| Zamknięte | Po wszystkich dysków są wysyłane do Ciebie, jeśli wystąpiły błędy podczas przetwarzania zadania, zadanie jest ustawiana na **zamknięte**. Zadanie jest automatycznie usuwane po 90 dniach w **zamknięte** stanu. |

## <a name="view-drive-status"></a>Wyświetl stan dysku

W poniższej tabeli opisano cyklu życia poszczególnych dyskach, ponieważ przechodzi ona za pomocą zadania importu lub eksportu. Bieżący stan każdego dysku w ramach zadania jest widoczny w portalu Azure.

W poniższej tabeli opisano każdy stan każdego dysku w ramach zadania mogą przechodzić przez.

| Stan stacji | Opis |
|:--- |:--- |
| Określona | Dla zadania importu po utworzeniu zadania w portalu Azure, stan początkowy dla dysku jest **określona**. Dla zadania eksportu, ponieważ dysk nie zostanie wskazany po utworzeniu zadania dysk początkowy stan jest **odebrane**. |
| Odebrane | Dysk przechodzi do **odebrane** stanu, gdy usługa Import/eksport został przetworzony dysków, które zostały odebrane od firmy wysyłania dla zadania importu. Dla zadania eksportu jest dysk początkowy stan **odebrane** stanu. |
| NeverReceived | Przenosi dysku **NeverReceived** stanu po odebraniu pakietu dla zadania, ale pakiet nie zawiera dysku. Dysk zostanie przesunięta w tym stanie została już dwa tygodnie, ponieważ usługa odebrała informacji dotyczących wysyłki, ale pakiet nie ma jeszcze dotarły w centrum danych. |
| Transferowanie | Przenosi dysku **transferowanie** stan, kiedy usługa zaczyna się na przesyłanie danych z dysku do magazynu Azure. |
| Ukończony | Przenosi dysku **Ukończono** stan, kiedy usługa pomyślnie przekazywane wszystkie dane bez błędów.
| CompletedMoreInfo | Przenosi dysku **CompletedMoreInfo** stanu, gdy usługa napotkał problemy podczas kopiowania danych z lub na dysku. Informacje mogą obejmować błędy, ostrzeżenia lub komunikaty informacyjne o zastępowaniu obiektów blob.
| ShippedBack | Przenosi dysku **ShippedBack** stan, gdy zostały wydane z centrum danych na powrót do adres zwrotny. |

Ten obraz z portalu Azure Wyświetla stan dysku przykładowe zadania:

![Wyświetl stan stacji](./media/storage-import-export-service/drivestate.png)

W poniższej tabeli opisano stanów awarii dysku i akcje wykonywane dla każdego stanu.

| Stan stacji | Wydarzenie | Rozdzielczość / następny krok |
|:--- |:--- |:--- |
| NeverReceived | Dysk, który jest oznaczony jako **NeverReceived** (ponieważ nie została odebrana jako część zadania wydania) dociera do innego wydania. | Zespół operacyjny przenosi stacji **odebrane**. |
| ND | Dysk, który nie jest częścią wszystkie zadania dociera do centrum danych w ramach innego zadania. | Dysk jest oznaczony jako dodatkowy dysk i jest zwracane po zakończeniu zadania skojarzone z oryginalnego pakietu. |

## <a name="time-to-process-job"></a>Czas procesu zadania
Ilość czasu, jaki zajmuje to przetwarzanie zadania importu/eksportu w zależności od szeregu czynników takich jak:

-  Czas dostawy
-  Obciążenia w centrum danych
-  Typ zadania i rozmiar kopiowanych danych
-  Liczba dysków w ramach zadania. 

Usługa Import/Eksport nie ma umowy dotyczącej poziomu usług, ale usługa dokłada starań, aby wykonać kopię w ciągu 7 do 10 dni po otrzymaniu dyski. Oprócz stanu opublikowane w portalu Azure interfejsów API REST można śledzić postęp zadania. Wykonano parametru w [listy zadań]() wywołania operacji interfejsu API udostępnia procent postępu kopiowania.


## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Transfer danych za pomocą narzędzia wiersza polecenia azcopy](storage-use-azcopy.md)
* [Przykładowe interfejsu API REST wyeksportować importu platformy Azure](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

