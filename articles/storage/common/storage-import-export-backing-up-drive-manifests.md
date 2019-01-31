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
ms.openlocfilehash: 61881508e18a2c7dbe1bc3be72d34423f862437a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2019
ms.locfileid: "55473395"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Tworzenie kopii zapasowej dysku manifesty dla zadań Azure Import/Export

Manifestów dysków może być automatycznie do kopii zapasowej do obiektów blob, ustawiając `BackupDriveManifest` właściwości `true` w [umieścić zadania](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operacji interfejsu API REST. Domyślnie manifestów dysków są nie kopię zapasową. Tworzenie kopii zapasowych dysków manifestu są przechowywane jako blokowe obiekty BLOB w kontenerze w ramach konta magazynu skojarzone z zadaniem. Domyślnie, nazwa kontenera jest `waimportexport`, ale można określić inną nazwę w `DiagnosticsPath` właściwości podczas wywoływania `Put Job` lub `Update Job Properties` operacji. Manifestu obiektów blob kopii zapasowej są nazwane w następującym formacie: `waies/jobname_driveid_timestamp_manifest.xml`.

 Możesz pobrać identyfikator URI dysku kopii zapasowej manifestów dla zadania, wywołując [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_Get) operacji. Obiekt blob identyfikatora URI jest zwracany w `ManifestUri` właściwości dla każdego dysku.

## <a name="next-steps"></a>Kolejne kroki

* [Przy użyciu interfejsu API REST usługi Import/Export](storage-import-export-using-the-rest-api.md)
