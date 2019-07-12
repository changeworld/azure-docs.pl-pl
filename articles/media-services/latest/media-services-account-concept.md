---
title: Zarządzanie kontami v3 usługi Azure Media Services | Dokumentacja firmy Microsoft
description: Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. W tym artykule opisano sposób zarządzania usługi Azure Media Services v3 kont.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: fa9720c2c29af184016d2903e60520e701b4cf79
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670684"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Zarządzanie kontami v3 usługi Azure Media Services

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimedialnej na platformie Azure, musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [kont magazynu](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Przenoszenie konta usługi Media Services między subskrypcjami 

Jeśli musisz przenieść konto usługi Media Services do nowej subskrypcji, musisz najpierw przenieść całą grupę zasobów zawierającą konto usługi Media Services do nowej subskrypcji. Musisz przenieść wszystkie dołączone zasoby: Konta usługi Azure Storage, profile Azure CDN, itp. Aby uzyskać więcej informacji, zobacz [Move resources to new resource group or subscription](../../azure-resource-manager/resource-group-move-resources.md) (Przenoszenie zasobów do nowej grupy lub subskrypcji). Podobnie jak w przypadku wszelkich zasobów na platformie Azure, przenosi grupy zasobów może potrwać trochę czasu.

> [!NOTE]
> Usługa Media Services v3 obsługuje model obsługi wielu dzierżawców.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Tworzenie kopii zapasowych wszystkich danych na swoim koncie, przed migracją do innej subskrypcji.
* Należy zatrzymać wszystkie punkty końcowe przesyłania strumieniowego i przesyłania strumieniowego zasobów na żywo. Usługi Użytkownicy nie będą mogli korzystać z zawartości w czasie trwania operacji przenoszenia grupy zasobów. 

> [!IMPORTANT]
> Punkt końcowy przesyłania strumieniowego nie należy uruchamiać, aż przeniesienie zakończy się pomyślnie.

### <a name="troubleshoot"></a>Rozwiązywanie problemów 

Jeśli konto usługi Media Services i skojarzone konto usługi Azure Storage "rozłączają" po przeniesienie grupy zasobów, spróbuj rotacji kluczy konta magazynu. Jeśli rotacji kluczy konta magazynu nie rozwiąże "odłączonego" stan konta usługi Media Services, zgłoś żądanie pomocy technicznej z "Pomoc techniczna i rozwiązywanie problemów" menu w ramach konta usługi Media Services.  

## <a name="next-steps"></a>Następne kroki

[Create an account](create-account-cli-quickstart.md) (Tworzenie konta)
