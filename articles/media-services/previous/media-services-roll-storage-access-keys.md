---
title: Aktualizowanie usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu | Dokumentacja firmy Microsoft
description: W tym artykule umożliwiają wskazówki na temat aktualizacji usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu.
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
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: c688169dc21304f234aead7196f377a3fa5fd633
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60407326"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizowanie usługi Media Services po stopniowym uaktualnieniu kluczy dostępu do magazynu 

Podczas tworzenia nowego konta usługi Azure Media Services (AMS), możesz również monit o wybranie konta usługi Azure Storage, który jest używany do przechowywania zawartości multimedialnej. Można dodać więcej niż jednego konta magazynu, do konta usługi Media Services. W tym artykule przedstawiono sposób rotacja kluczy magazynu. Prezentuje również sposób dodawania konta magazynu do konta usługi media. 

Aby wykonać czynności opisane w tym artykule, należy używać [interfejsów API usługi Azure Resource Manager](/rest/api/media/operations/azure-media-services-rest-api-reference) i [Powershell](https://docs.microsoft.com/powershell/module/az.media).  Aby uzyskać więcej informacji, zobacz [jak zarządzać zasobami platformy Azure przy użyciu programu PowerShell i Menedżera zasobów](../../azure-resource-manager/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie

Po utworzeniu nowego konta magazynu platformy Azure generuje dwa klucze dostępu do magazynu 512-bitowe, które są używane do uwierzytelniania dostępu do konta magazynu. Aby zabezpieczyć połączenia z magazynem więcej, zalecane jest okresowe ponowne wygenerowanie i Obróć klucz dostępu do magazynu. Dwa klucze dostępu (podstawowych i pomocniczych) znajdują się w celu umożliwia utrzymanie połączeń przy użyciu jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu konta magazynu. Ta procedura jest również nazywany "przerzucić klucze dostępu".

Podany klucz magazynu zależy od usługi Media Services. W szczególności lokalizatory, które są używane do przesyłania strumieniowego lub pobierania zasobów są zależne od klucza dostępu do określonego magazynu. Po utworzeniu konta usługi AMS zajmuje zależność magazynu podstawowego klucza dostępu domyślnie, ale jako użytkownik może zaktualizować klucza magazynu, który ma usługi AMS. Upewnij się umożliwić usługi Media Services wiedzieć, którego klucza należy używać, wykonując kroki opisane w tym artykule.  

>[!NOTE]
> Jeśli masz wiele kont magazynu, może wykonać tę procedurę, z każdym kontem magazynu. Kolejność, w którym rotacja kluczy magazynu nie jest określony. Można też obrócić dodatkowej pierwszy klucza, a następnie podstawowego klucza lub na odwrót.
>
> Przed wykonaniem kroków opisanych w tym artykule na koncie produkcyjnym, upewnij się przetestować je na koncie produkcji wstępnej.
>

## <a name="steps-to-rotate-storage-keys"></a>Kroki, aby rotacja kluczy magazynu 
 
 1. Zmiana klucza podstawowego konta magazynu za pomocą polecenia cmdlet programu powershell lub [Azure](https://portal.azure.com/) portalu.
 2. Wywołaj polecenie cmdlet AzMediaServiceStorageKeys synchronizacji z odpowiednie parametry do wymuszenia konta multimediów, aby pobrać klucze konta magazynu
 
    Poniższy przykład pokazuje, jak można zsynchronizować kluczy konta magazynu.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Poczekaj godziny. Sprawdź, czy pracujesz scenariusze przesyłania strumieniowego.
 4. Zmiana klucza pomocniczego konta magazynu za pomocą polecenia cmdlet programu powershell lub witryny Azure portal.
 5. Wywołanie synchronizacji AzMediaServiceStorageKeys powershell za pomocą odpowiednie parametry do wymuszenia konta usługi media do pobrania nowych kluczy konta magazynu. 
 6. Poczekaj godziny. Sprawdź, czy pracujesz scenariusze przesyłania strumieniowego.
 
### <a name="a-powershell-cmdlet-example"></a>Przykład polecenia cmdlet programu powershell 

Poniższy przykład pokazuje, jak pobrać konta magazynu, a następnie zsynchronizować ją z kontem usługi AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Kroki, aby dodać konta usługi storage z kontem usługi AMS

Następujący artykuł pokazuje, jak dodać konta magazynu do konta usługi AMS: [Dołączanie wielu kont magazynu do konta usługi Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potwierdzenia
Chcielibyśmy potwierdzić poniższe osób, które przyczyniły się do tworzenia tego dokumentu: Seva Titov Cenk Dingiloglu, Gada Mediolan.
