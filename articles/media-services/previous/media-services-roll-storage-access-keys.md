---
title: Aktualizacja usługi Media Services po wycofanie kluczy dostępu do magazynu | Dokumentacja firmy Microsoft
description: W tym artykule umożliwiają wskazówki na temat aktualizowania usługi Media Services po wycofanie kluczy dostępu do magazynu.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: ebaa679c43bd577d4f37feb7f3a025086490e25f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>Aktualizacja usługi Media Services po wycofanie kluczy dostępu do magazynu

Podczas tworzenia nowego konta usługi Azure Media Services (AMS) również należy wybrać konto magazynu Azure używanego do przechowywania zawartości nośnika. Można dodać więcej niż jedno konto magazynu do konta usługi Media Services. W tym temacie przedstawiono sposób Obróć magazynu kluczy. Zawiera on również sposób dodawania konta magazynu do konta usługi media. 

Aby wykonać czynności opisane w tym temacie, należy używać [ARM interfejsów API](https://docs.microsoft.com/rest/api/media/mediaservice) i [Powershell](https://docs.microsoft.com/powershell/resourcemanager/azurerm.media/v0.3.2/azurerm.media).  Aby uzyskać więcej informacji, zobacz [sposobu zarządzania zasobami Azure za pomocą programu PowerShell i Menedżera zasobów](../../azure-resource-manager/powershell-azure-resource-manager.md).

## <a name="overview"></a>Przegląd

Po utworzeniu nowego konta magazynu Azure generuje dwa klucze dostępu do magazynu 512-bitowe, które są używane do uwierzytelniania dostępu do konta magazynu. Do zabezpieczania połączenia z magazynem, zalecane jest okresowe ponowne wygenerowanie i obracania klucz dostępu do magazynu. Dwa klucze dostępu (podstawowych i pomocniczych) znajdują się w celu umożliwienia można obsługiwać połączenia z kontem magazynu za pomocą jednego klucza dostępu, jednocześnie ponownie generując drugi klucz dostępu. Ta procedura jest również nazywany "stopniowego klucze dostępu".

Podany klucz magazynu zależy od usługi Media Services. W szczególności lokalizatorów, które są używane do przesyłania strumieniowego lub pobierania zasobów są zależne od klucz dostępu do określonego magazynu. Po utworzeniu konta usługi AMS ma zależność na klucz dostępu do magazynu głównej domyślnie, ale jako użytkownik może zaktualizować klucza magazynu, który ma AMS. Należy się upewnić umożliwić Media Services wiedzieć, który klucz do użycia, wykonując kroki opisane w tym temacie.  

>[!NOTE]
> Jeśli masz wiele kont magazynu, można zastosować tę procedurę za pomocą każdego konta magazynu. Kolejność, w którym obracania magazynu kluczy nie jest stały. Można obracać dodatkowej pierwszy klucza, a następnie podstawowy klucz lub na odwrót.
>
> Przed wykonaniem kroków opisanych w tym temacie na koncie produkcji, upewnij się, że testowane na koncie produkcji wstępnej.
>

## <a name="steps-to-rotate-storage-keys"></a>Kroki w celu obracania magazynu kluczy 
 
 1. Należy zmienić wartość klucza podstawowego konta magazynu za pomocą polecenia cmdlet programu powershell lub [Azure](https://portal.azure.com/) portalu.
 2. Wywołaj polecenie cmdlet AzureRmMediaServiceStorageKeys synchronizacji z odpowiednie parametry, aby wymusić wykrycie klucze konta magazynu konta usługi media
 
    Poniższy przykład pokazuje, jak można zsynchronizować klucze kont magazynu.
  
         Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Zaczekaj godzinę. Sprawdź, czy działają scenariusze przesyłania strumieniowego.
 4. Zmiana klucza pomocniczego konta magazynu za pomocą polecenia cmdlet programu powershell lub portalu Azure.
 5. Wywołanie synchronizacji AzureRmMediaServiceStorageKeys powershell za pomocą odpowiednie parametry, aby wymusić konta usługi media do odebrania nowych kluczy kont magazynu. 
 6. Zaczekaj godzinę. Sprawdź, czy działają scenariusze przesyłania strumieniowego.
 
### <a name="a-powershell-cmdlet-example"></a>Przykład polecenia cmdlet programu powershell 

W poniższym przykładzie pokazano, jak pobrać konta magazynu i synchronizacji z konta usługi AMS.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzureRmMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Kroki, aby dodać konta magazynu do konta usługi AMS

Poniższy temat opisuje sposób dodawania konta magazynu do konta usługi AMS: [dołączyć wiele kont magazynu do konta usługi Media Services](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Potwierdzenia
Chcemy potwierdzić następujących osób, które przyczyniły się do tworzenia tego dokumentu: Seva Titov Cenk Dingiloglu, Gada Mediolan.
