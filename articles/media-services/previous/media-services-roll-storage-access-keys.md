---
title: Aktualizowanie usługi Media Services po wprowadzeniu kluczy dostępu do magazynu | Dokumenty firmy Microsoft
description: W tym artykułach znajdziesz wskazówki dotyczące aktualizowania usługi Media Services po wprowadzeniu kluczy dostępu do magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: juliako
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 2a0d1c5af572c88dc11bed950b46706f0a2f081f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981960"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizowanie usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu 

Podczas tworzenia nowego konta usługi Azure Media Services (AMS) zostanie również poproszony o wybranie konta usługi Azure Storage używanego do przechowywania zawartości multimedialnej. Do konta usługi Media Services można dodać więcej niż jedno konta magazynu. W tym artykule pokazano, jak obracać klucze magazynu. Pokazuje również, jak dodać konta magazynu do konta multimedialnego. 

Aby wykonać akcje opisane w tym artykule, należy używać [interfejsów API usługi Azure Resource Manager](/rest/api/media/operations/azure-media-services-rest-api-reference) i programu [Powershell](https://docs.microsoft.com/powershell/module/az.media).  Aby uzyskać więcej informacji, zobacz [Jak zarządzać zasobami platformy Azure za pomocą programu PowerShell i Menedżera zasobów](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po utworzeniu nowego konta magazynu platforma Azure generuje dwa 512-bitowe klucze dostępu do magazynu, które są używane do uwierzytelniania dostępu do konta magazynu. Aby zapewnić większe bezpieczeństwo połączeń pamięci masowej, zaleca się okresowe ponowne generowanie i obracanie klucza dostępu do magazynu. Dwa klucze dostępu (podstawowy i pomocniczy) są dostarczane w celu umożliwienia obsługi połączeń z kontem magazynu przy użyciu jednego klucza dostępu podczas ponownego generowania innego klucza dostępu. Ta procedura jest również nazywana "kluczami dostępu stopniowym".

Usługa Media Services zależy od klucza magazynu dostarczonego do niego. W szczególności lokalizatory, które są używane do przesyłania strumieniowego lub pobierania zasobów zależą od określonego klucza dostępu do magazynu. Po utworzeniu konta usługi AMS, trwa zależność od klucza dostępu magazynu podstawowego domyślnie, ale jako użytkownik można zaktualizować klucz magazynu, który ma usługi AMS. Należy pamiętać, aby poinformować usługi Media Services, który klucz ma być używany, wykonując kroki opisane w tym artykule.  

>[!NOTE]
> Jeśli masz wiele kont magazynu, należy wykonać tę procedurę przy użyciu każdego konta magazynu. Kolejność obracania kluczy pamięci nie jest stała. Można najpierw obrócić klucz pomocniczy, a następnie klucz podstawowy lub odwrotnie.
>
> Przed wykonaniem kroków opisanych w tym artykule na koncie produkcyjnym należy przetestować je na koncie przedprodukcyjnym.
>

## <a name="steps-to-rotate-storage-keys"></a>Kroki obracania klawiszy pamięci masowej 
 
 1. Zmień klucz podstawowy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub [witryny Azure](https://portal.azure.com/) Portal.
 2. Wywołanie Sync-AzMediaServiceStorageKeys polecenie cmdlet z odpowiednimi params wymusić konto multimedialne, aby odebrać klucze konta magazynu
 
    W poniższym przykładzie pokazano, jak synchronizować klucze z kontami magazynu.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Poczekaj godzinę lub tak. Sprawdź, czy scenariusze przesyłania strumieniowego działają.
 4. Zmień klucz pomocniczy konta magazynu za pomocą polecenia cmdlet programu PowerShell lub witryny Azure Portal.
 5. Wywołanie Sync-AzMediaServiceStorageKeys powershell z odpowiednimi params wymusić konto multimedialne, aby odebrać nowe klucze konta magazynu. 
 6. Poczekaj godzinę lub tak. Sprawdź, czy scenariusze przesyłania strumieniowego działają.
 
### <a name="a-powershell-cmdlet-example"></a>Przykład polecenia cmdlet programu powershell 

W poniższym przykładzie pokazano, jak uzyskać konto magazynu i zsynchronizować go z kontem usługi AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Kroki, aby dodać konta magazynu do konta AMS

W poniższym artykule pokazano, jak dodać konta magazynu do konta usługi AMS: [Dołączanie wielu kont magazynu do konta usługi Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Podziękowania
Pragniemy podziękować następującym osobom, które przyczyniły się do stworzenia tego dokumentu: Cenk Dingiloglu, Milan Gada, Seva Titov.
