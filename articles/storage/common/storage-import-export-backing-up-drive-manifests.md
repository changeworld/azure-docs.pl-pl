---
title: Tworzenie kopii zapasowej manifestów dysków Azure Import/Export | Dokumentacja firmy Microsoft
description: Dowiedz się, jak Twoja manifestów dysków dla usługi Microsoft Azure Import/Export, automatycznie utworzona kopia zapasowa.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: ea223ea3ccd113014ceabff34cc4d0174abb1ddf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61483132"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Tworzenie kopii zapasowej dysku manifesty dla zadań Azure Import/Export

Manifestów dysków może być automatycznie do kopii zapasowej do obiektów blob, ustawiając `BackupDriveManifest` właściwości `true` w [umieścić zadania](/rest/api/storageimportexport/jobs) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs) operacji interfejsu API REST. Domyślnie manifestów dysków są nie kopię zapasową. Tworzenie kopii zapasowych dysków manifestu są przechowywane jako blokowe obiekty BLOB w kontenerze w ramach konta magazynu skojarzone z zadaniem. Domyślnie, nazwa kontenera jest `waimportexport`, ale można określić inną nazwę w `DiagnosticsPath` właściwości podczas wywoływania `Put Job` lub `Update Job Properties` operacji. Manifestu obiektów blob kopii zapasowej są nazwane w następującym formacie: `waies/jobname_driveid_timestamp_manifest.xml`.

 Możesz pobrać identyfikator URI dysku kopii zapasowej manifestów dla zadania, wywołując [pobrania zadania](/rest/api/storageimportexport/jobs) operacji. Obiekt blob identyfikatora URI jest zwracany w `ManifestUri` właściwości dla każdego dysku.

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
