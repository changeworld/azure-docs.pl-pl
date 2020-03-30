---
title: Zarządzanie kontami usługi Azure Media Services w wersji 3 | Dokumenty firmy Microsoft
description: Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimediów na platformie Azure, musisz utworzyć konto usługi Media Services. W tym artykule wyjaśniono, jak zarządzać kontami usługi Azure Media Services w wersji 3.
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
ms.openlocfilehash: 08579f7ba952bb4ebcba1595508612affb852528
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980385"
---
# <a name="manage-azure-media-services-v3-accounts"></a>Zarządzanie kontami usługi Azure Media Services w wersji 3

Aby rozpocząć zarządzanie, szyfrowanie, kodowanie, analizowanie i przesyłanie strumieniowe zawartości multimediów na platformie Azure, musisz utworzyć konto usługi Media Services. Podczas tworzenia konta usługi Media Services musisz podać nazwę zasobu konta usługi Azure Storage. Podane konto magazynu jest dołączane do konta usługi Media Services. Konto usług Media Services i wszystkie skojarzone konta magazynu muszą być w tej samej subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Konta magazynu](storage-account-concept.md).

## <a name="moving-a-media-services-account-between-subscriptions"></a>Przenoszenie konta usługi Media Services między subskrypcjami 

Jeśli musisz przenieść konto usługi Media Services do nowej subskrypcji, musisz najpierw przenieść całą grupę zasobów zawierającą konto usługi Media Services do nowej subskrypcji. Należy przenieść wszystkie dołączone zasoby: konta usługi Azure Storage, profile usługi Azure CDN itp. Aby uzyskać więcej informacji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md). Podobnie jak w przypadku wszystkich zasobów na platformie Azure, przeniesienie grupy zasobów może zająć trochę czasu.

> [!NOTE]
> Usługi Media Services w wersji 3 obsługują model wielokrotnego dzierżawy.

### <a name="considerations"></a>Zagadnienia do rozważenia

* Utwórz kopie zapasowe wszystkich danych na koncie przed migracją do innej subskrypcji.
* Musisz zatrzymać wszystkie punkty końcowe przesyłania strumieniowego i zasoby przesyłania strumieniowego na żywo. Użytkownicy nie będą mogli uzyskać dostępu do zawartości przez czas przenoszenia grupy zasobów. 

> [!IMPORTANT]
> Nie należy uruchamiać punktu końcowego przesyłania strumieniowego, dopóki przeniesienie nie zakończy się pomyślnie.

### <a name="troubleshoot"></a>Rozwiązywanie problemów 

Jeśli konto usługi Media Services lub skojarzone konto usługi Azure Storage zostaną "rozłączone" po przeniesieniu grupy zasobów, spróbuj obrócić klucze konta magazynu. Jeśli obracanie kluczy konta magazynu nie rozwiązuje stanu "rozłączone" konta usługi Media Services, należy zgłosić nowe żądanie pomocy technicznej z menu "Pomoc techniczna + rozwiązywanie problemów" na koncie usługi Media Services.  

## <a name="next-steps"></a>Następne kroki

[Tworzenie konta](create-account-cli-quickstart.md)
