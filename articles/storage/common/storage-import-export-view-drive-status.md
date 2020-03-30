---
title: Wyświetl stan zadań importowania/eksportowania platformy Azure | Dokumenty firmy Microsoft
description: Dowiedz się, jak wyświetlić stan zadań importu/eksportu i używanych dysków.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 05/17/2018
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 222c893f06d9adf77f8a8124af18bc03c5d20bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72821431"
---
# <a name="view-the-status-of-azure-importexport-jobs"></a>Wyświetlanie stanu zadań importu/eksportu platformy Azure

Ten artykuł zawiera informacje dotyczące sposobu wyświetlania dysku i stanu zadania dla zadań importowania/eksportowania platformy Azure. Usługa Azure Import/Export jest używana do bezpiecznego przesyłania dużych ilości danych do obiektów blob platformy Azure i plików platformy Azure. Usługa jest również używana do eksportowania danych z magazynu obiektów Blob platformy Azure.  

## <a name="view-job-and-drive-status"></a>Wyświetlanie zadania i stanu dysku
Możesz śledzić stan zadań importowania lub eksportowania z witryny Azure portal. Kliknij kartę **Importuj/Eksportuj.** Na stronie zostanie wyświetlona lista zadań.

![Wyświetl stan zadania](./media/storage-import-export-service/jobstate.png)

## <a name="view-job-status"></a>Wyświetlanie stanu zadania

Zostanie wyświetlony jeden z następujących stanów zadania w zależności od tego, gdzie dysk jest w trakcie.

| Stan zadania | Opis |
|:--- |:--- |
| Tworzenie | Po utworzeniu zadania jego stan jest ustawiony na **Tworzenie**. Gdy zadanie jest w stanie **Tworzenia,** usługa Import/Export zakłada, że dyski nie zostały wysłane do centrum danych. Zadanie może pozostać w tym stanie przez okres do dwóch tygodni, po czym jest automatycznie usuwane przez usługę. |
| Wysyłka | Po wysłaniu pakietu należy zaktualizować informacje o śledzeniu w witrynie Azure portal.  To zamienia zadanie w stanie **wysyłki.** Zadanie pozostaje w stanie **Wysyłka** przez okres do dwóch tygodni. 
| Odebrano | Po odebraniu wszystkich dysków w centrum danych stan zadania jest ustawiony na **Odebrane**. |
| Przesyłanie | Po rozpoczęciu przetwarzania co najmniej jednego dysku stan zadania jest ustawiony na **Przeniesienie**. Aby uzyskać więcej informacji, przejdź do [drive states](#view-drive-status). |
| Tworzenie pakietów | Po zakończeniu przetwarzania wszystkich dysków zadanie jest umieszczane w stanie **pakowania,** dopóki dyski nie zostaną wysłane z powrotem do Ciebie. |
| Zakończone | Po wszystkich dysków są wysyłane z powrotem do Ciebie, jeśli zadanie zostało zakończone bez błędów, a następnie zadanie jest **ustawiona**na Zakończone . Zadanie jest automatycznie usuwane po 90 dniach w stanie **Zakończone.** |
| Zamknięte | Po wszystkich dysków są wysyłane z powrotem do Ciebie, jeśli wystąpiły błędy podczas przetwarzania zadania, zadanie jest ustawione na **Zamknięte**. Zadanie jest automatycznie usuwane po 90 dniach w stanie **Zamknięte.** |

## <a name="view-drive-status"></a>Wyświetlanie stanu dysku

W poniższej tabeli opisano cykl życia pojedynczego dysku przechodzącego przez zadanie importu lub eksportu. Bieżący stan każdego dysku w zadaniu jest widoczny w witrynie Azure portal.

W poniższej tabeli opisano każdy stan, który każdy dysk w zadaniu może przejść.

| Stan dysku | Opis |
|:--- |:--- |
| Określony | W przypadku zadania importu, gdy zadanie jest tworzone z witryny Azure portal, stan początkowy dysku jest **określony**. W przypadku zadania eksportu, ponieważ podczas tworzenia zadania nie określono dysku, stan początkowego dysku to **Odebrano**. |
| Odebrano | Dysk przechodzi do stanu **Odebrane,** gdy usługa Import/Eksport przetworzyła dyski odebrane od firmy spedycyjnej dla zadania importu. W przypadku zadania eksportu początkowy stan dysku jest stanem **Odebrane.** |
| Nigdy Nieuwzdrzona | Dysk przenosi się do **NeverReceived** stanu, gdy pakiet dla zadania nadejdzie, ale pakiet nie zawiera dysku. Dysk również przenosi się do tego stanu, jeśli minęły dwa tygodnie od odebraniem informacji o wysyłce, ale paczka nie dotarła jeszcze do centrum danych. |
| Przesyłanie | Dysk przenosi się do stanu **przenoszenia,** gdy usługa rozpoczyna transfer danych z dysku do usługi Azure Storage. |
| Zakończone | Dysk przenosi się do stanu **Zakończone,** gdy usługa pomyślnie przesłała wszystkie dane bez błędów.
| CompletedMoreInfo | Dysk przenosi się do **stanu CompletedMoreInfo,** gdy usługa napotkała pewne problemy podczas kopiowania danych z lub do dysku. Informacje mogą zawierać błędy, ostrzeżenia lub komunikaty informacyjne dotyczące zastępowania obiektów blob.
| Odesłanie wysłane | Dysk przenosi się do stanu **ShippedBack,** gdy został wysłany z centrum danych z powrotem na adres zwrotny. |

Ten obraz z witryny Azure Portal wyświetla stan dysku przykładowego zadania:

![Wyświetl stan dysku](./media/storage-import-export-service/drivestate.png)

W poniższej tabeli opisano stany awarii dysku i akcje podjęte dla każdego stanu.

| Stan dysku | Wydarzenie | Rozdzielczość / Następny krok |
|:--- |:--- |:--- |
| Nigdy Nieuwzdrzona | Dysk oznaczony jako **NeverReceived** (ponieważ nie został odebrany jako część przesyłki zadania) dociera do innej przesyłki. | Zespół operacyjny przenosi dysk do **Odebrane**. |
| Nie dotyczy | Dysk, który nie jest częścią żadnego zadania dociera do centrum danych w ramach innego zadania. | Dysk jest oznaczony jako dodatkowy dysk i jest zwracany po zakończeniu zadania skojarzonego z oryginalnym pakietem. |

## <a name="time-to-process-job"></a>Czas na przetworzenie zadania
Czas przetwarzania zadania importu/eksportu zależy od wielu czynników, takich jak:

-  Czas wysyłki
-  Ładowanie w centrum danych
-  Typ zadania i rozmiar kopiowanych danych
-  Liczba dysków w zadaniu. 

Usługa importu/eksportu nie ma umowy SLA, ale usługa stara się wykonać kopię w ciągu 7 do 10 dni po odebraniu dysków. Oprócz stanu opublikowanego w witrynie Azure Portal interfejsów API REST można śledzić postęp zadania. Parametr procentu wykonania w [wywołaniu](/previous-versions/azure/dn529083(v=azure.100)) interfejsu API operacji Listy zadań zapewnia procentowy postęp kopiowania.


## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia WAImportExport](storage-import-export-tool-how-to.md)
* [Przesyłanie danych za pomocą narzędzia wiersza polecenia AzCopy](storage-use-azcopy.md)
* [Przykład interfejsu API REST eksportujący narzędzie Azure Export](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/)
