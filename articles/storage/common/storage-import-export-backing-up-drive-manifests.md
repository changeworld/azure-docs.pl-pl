---
title: Tworzenie kopii zapasowej manifestów dysku Import/Eksport Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ma Twojej manifestów dysku dla kopii zapasowej automatycznie usługi Import/Eksport Microsoft Azure.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 594abd80-b834-4077-a474-d8a0f4b7928a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 33eb8e1eea8f8aa7b79ef3e54f2b1ed88dc794ae
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873667"
---
# <a name="backing-up-drive-manifests-for-azure-importexport-jobs"></a>Tworzenie kopii zapasowej dysku manifesty prac Import/Eksport Azure

Manifesty dysku mogą być automatycznie do kopii zapasowej do obiektów blob przez ustawienie `BackupDriveManifest` właściwości `true` w [zawiesić zadanie](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) lub [właściwości zadania aktualizacji](/rest/api/storageimportexport/jobs#Jobs_Update) operacje interfejsu API REST. Domyślnie manifestów dysku nie kopii zapasowej. Tworzenie kopii zapasowych manifestu dysku są przechowywane jako blokowych obiektów blob w kontenerze w ramach konta magazynu skojarzone z zadaniem. Domyślnie nazwa kontenera jest `waimportexport`, ale można określić inną nazwę w `DiagnosticsPath` właściwości podczas wywoływania metody `Put Job` lub `Update Job Properties` operacji. Kopia zapasowa manifestu obiektów blob są nazywane w następującym formacie: `waies/jobname_driveid_timestamp_manifest.xml`.

 Identyfikator URI manifesty kopii zapasowej dysku dla zadania można pobrać przez wywołanie metody [pobrania zadania](/rest/api/storageimportexport/jobs#Jobs_Get) operacji. Identyfikator URI jest zwracany w obiekcie blob `ManifestUri` właściwości dla każdego dysku.

## <a name="next-steps"></a>Następne kroki

* [Przy użyciu interfejsu API REST usługi Import/Eksport](storage-import-export-using-the-rest-api.md)
