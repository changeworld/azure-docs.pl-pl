---
title: Diagnostyka i odzyskiwanie po błędach zadań usługi Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć pełne rejestrowanie dla zadań usługi Microsoft Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 306b94fbe23e0ae92dcd59f7a87b7bb58ef7c3b6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61478814"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostyka i odzyskiwanie po błędach zadań usługi Azure Import/Export
Dla każdego dysku, przetwarzania usługa Azure Import/Export tworzy dziennik błędów w skojarzonego konta magazynu. Można również włączyć pełne rejestrowanie, ustawiając `LogLevel` właściwości `Verbose` podczas wywoływania [umieścić zadania](/rest/api/storageimportexport/jobs) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs) operacji.

 Domyślnie dzienniki są zapisywane w kontenerze o nazwie `waimportexport`. Można określić inną nazwę, ustawiając `DiagnosticsPath` właściwości podczas wywoływania `Put Job` lub `Update Job Properties` operacji. Dzienniki są przechowywane jako obiekty BLOB typu block, następująca Konwencja nazewnictwa: `waies/jobname_driveid_timestamp_logtype.xml`.

 Można pobrać identyfikatora URI dzienniki zadania, wywołując [pobrania zadania](/rest/api/storageimportexport/jobs) operacji. Identyfikator URI, dla pełnego dziennika jest zwracany w `VerboseLogUri` właściwości dla każdego dysku, gdy identyfikator URI dla dziennika błędów jest zwracany w `ErrorLogUri` właściwości.

Dane rejestrowania można użyć, aby zidentyfikować następujące problemy.

## <a name="drive-errors"></a>Błędy dysku

Następujące elementy są klasyfikowane jako błędy dysku:

-   Błędy podczas uzyskiwania dostępu do lub odczytywania pliku manifestu

-   Nieprawidłowe klucze funkcji BitLocker

-   Błędy odczytu/zapisu dysku

## <a name="blob-errors"></a>Błędy obiektów blob

Następujące elementy są klasyfikowane jako błędy obiektów blob:

-   Nazwy lub nieprawidłowa / sprzeczna obiektów blob

-   Brakujące pliki

-   Nie znaleziono obiektu blob

-   Skrócona plików (pliki na dysku jest mniejszy niż określona w manifeście)

-   Uszkodzony plik zawartości (w przypadku zadań importu wykryte za pomocą sumy kontrolnej MD5)

-   Pliki metadanych i właściwości uszkodzony obiekt blob (wykryte za pomocą sumy kontrolnej MD5)

-   Nieprawidłowy schemat dla właściwości obiektu blob i/lub pliki metadanych

Można wykluczyć sytuacji, gdzie niektóre części zadania importu lub eksportu nie zakończą się pomyślnie, podczas ogólne zadania nadal. W takim przypadku możesz przekazać lub pobrać brakujące fragmenty danych za pośrednictwem sieci, lub możesz utworzyć nowe zadanie do transferu danych. Zobacz [odwołanie do usługi Azure Import/Export narzędzie](storage-import-export-tool-how-to-v1.md) dowiesz się, jak naprawić danych za pośrednictwem sieci.

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
