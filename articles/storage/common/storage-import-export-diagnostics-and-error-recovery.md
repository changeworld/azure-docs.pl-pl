---
title: Diagnostyka i błąd odzyskiwania dla zadań Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak włączyć pełne rejestrowanie dla zadania usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 096cc795-9af6-4335-9fe8-fffa9f239a17
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 0068aae9d6780aa41a070db0eb191d0d5a165d21
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873639"
---
# <a name="diagnostics-and-error-recovery-for-azure-importexport-jobs"></a>Diagnostyka i błąd odzyskiwania dla zadań Import/Eksport Azure
Dla każdego dysku przetwarzane usługi Import/Eksport Azure tworzy dziennik błędów w skojarzonego konta magazynu. Można również włączyć pełne rejestrowanie, ustawiając `LogLevel` właściwości `Verbose` podczas wywoływania metody [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operacji.

 Domyślnie dzienniki są zapisywane do kontenera o nazwie `waimportexport`. Można określić inną nazwę, ustawiając `DiagnosticsPath` właściwości podczas wywoływania metody `Put Job` lub `Update Job Properties` operacji. Dzienniki są przechowywane jako blokowych obiektów blob z następującą konwencją nazewnictwa: `waies/jobname_driveid_timestamp_logtype.xml`.

 Można pobrać identyfikatora URI dzienników zadania przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_Get) operacji. Identyfikator URI dla pełnego dziennika jest zwracany w `VerboseLogUri` właściwości dla każdego dysku, podczas gdy identyfikator URI dla dziennik błędów, jest zwracany w `ErrorLogUri` właściwości.

Dane rejestrowania służy do identyfikowania następujące problemy.

## <a name="drive-errors"></a>Błędy dysku

Następujące elementy sklasyfikowanych jako błędy dysku:

-   Błędy podczas uzyskiwania dostępu do lub odczytywania pliku manifestu

-   Niepoprawne klucze funkcji BitLocker

-   Błędy odczytu/zapisu dysków

## <a name="blob-errors"></a>Błędy obiektów blob

Następujące elementy sklasyfikowanych jako błędy obiektów blob:

-   Nazwy lub nieprawidłowa / sprzeczna obiektów blob

-   Brakujące pliki

-   Nie znaleziono obiektu blob

-   Skrócona plików (pliki na dysku są mniejsze niż określona w manifeście)

-   Uszkodzony plik zawartości (dla zadania importu wykrył z sumy kontrolnej MD5)

-   Pliki metadanych i właściwości uszkodzony obiekt blob (wykrył z sumy kontrolnej MD5)

-   Niepoprawny schemat dla właściwości obiektów blob i/lub pliki metadanych

Może to być przypadki, w którym niektórych części zadania importu lub eksportu nie zostało prawidłowo wykonane, ogólną zadanie nadal zakończy pracę. W takim przypadku możesz przekazać lub pobrać brakujące fragmenty danych za pośrednictwem sieci, lub można utworzyć nowe zadanie do transferu danych. Zobacz [odwołania narzędzie importu/eksportu Azure](storage-import-export-tool-how-to-v1.md) informacje na temat naprawy danych za pośrednictwem sieci.

## <a name="next-steps"></a>Następne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
