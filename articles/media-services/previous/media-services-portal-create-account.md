---
title: Tworzenie konta usługi Azure Media Services za pomocą witryny Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki tworzenia konta usługi Azure Media Services za pomocą portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/03/2017
ms.author: juliako
ms.openlocfilehash: fcf02e39a305281501773db51383dff66c485493
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33780623"
---
# <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Tworzenie konta usługi Azure Media Services za pomocą witryny Azure Portal
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-create-account.md)
> * [REST](https://docs.microsoft.com/rest/api/media/mediaservice)
> 
> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Portal Azure umożliwia szybkie utworzenie konta usługi Azure Media Services (AMS). Korzystając z konta, możesz uzyskiwać dostęp do usługi Media Services, która umożliwia przechowywanie, szyfrowanie, kodowanie i przesyłanie strumieniowe zawartości oraz zarządzanie nią na platformie Azure. Podczas tworzenia konta usługi Media Services możesz również utworzyć skojarzone konto magazynu (lub użyć istniejącego konta) w tym samym regionie geograficznym, co konto usługi Media Services.

W tym artykule omówiono niektóre typowe pojęcia i pokazano, jak utworzyć konto usługi Media Services za pomocą portalu Azure.

> [!NOTE]
> Aby uzyskać informacje na temat dostępności funkcji usługi Azure Media Services w różnych regionach, zobacz temat opisujący [dostępność funkcji usługi AMS w centrach danych](scenarios-and-availability.md#availability).

## <a name="concepts"></a>Pojęcia
Uzyskiwanie dostępu do usługi Media Services wymaga dwóch skojarzonych kont:

* Konto usługi Media Services. Twoje konto umożliwia dostęp do opartej na chmurze usługi Media Services dostępnej na platformie Azure. Na koncie usługi Media Services nie jest przechowywana zawartość multimedialna. Zamiast tego na koncie są przechowywane metadane dotyczące zawartości multimedialnej oraz zadania przetwarzania multimediów. Podczas tworzenia konta można wybrać dostępny region usługi Media Services. Wybrany region określa centrum danych, w którym są przechowywane rekordy metadanych konta użytkownika.
  
* Konto usługi Azure Storage. Konta usługi Storage muszą znajdować się w tym samym regionie geograficznym co konto usługi Media Services. Podczas tworzenia konta usługi Media Services można wybrać istniejące konto magazynu w tym samym regionie lub można utworzyć nowe konto magazynu w tym samym regionie. Jeśli usuniesz konto usługi Media Services, obiekty blob na powiązanym koncie magazynu nie zostaną usunięte.

  > [!NOTE]
  > Usługa Media Services ogranicza konto magazynu podstawowego do konta **Magazyn ogólny** za pomocą tabel i kolejek. Aby uzyskać więcej informacji dotyczących typów magazynu, zobacz [About Azure storage accounts](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) (Informacje o kontach usługi Azure Storage).

## <a name="create-an-ams-account"></a>Tworzenie konta AMS
W tej sekcji opisano kroki w procesie tworzenia konta usługi AMS.

1. Zaloguj się w [portalu Azure](https://portal.azure.com/).
2. Kliknij pozycję **+Nowe** > **Sieci Web i mobilność** > **Media Services**.
   
    ![Tworzenie usługi Media Services](./media/media-services-create-account/media-services-new1.png)
3. Na stronie **TWORZENIE KONTA USŁUGI MEDIA SERVICES** wprowadź wymagane wartości.
   
    ![Tworzenie usługi Media Services](./media/media-services-create-account/media-services-new3.png)
   
   1. W polu **Nazwa konta** wprowadź nazwę nowego konta usługi AMS. Nazwa konta usługi Media Services składa się z małych liter i cyfr (bez spacji) i może zawierać od 3 do 24 znaków.
   2. W subskrypcji wybierz jedną z różnych subskrypcji Azure, do których masz dostęp.
   3. W polu **Grupa zasobów** wybierz nowy lub istniejący zasób.  Grupa zasobów jest kolekcją zasobów, które mają ten sam cykl życia, uprawnienia i zasady. Więcej informacji można znaleźć [tutaj](../../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. W polu **Lokalizacja** wybierz region geograficzny używany do przechowywania nośników i rekordów metadanych dla konta usługi Media Services. Ten region będzie służyć do przetwarzania i przesyłania strumieniowego multimediów. Na liście rozwijanej są wyświetlane tylko regiony dostępne w usłudze Media Services. 
   5. W polu **Konto magazynu** wybierz konto magazynu, aby udostępnić magazyn obiektów Blob dla zawartości multimedialnej z konta usługi Media Services. Można wybrać istniejące konto magazynu w tym samym regionie geograficznym co konto usługi Media Services albo utworzyć konto magazynu. Nowe konto magazynu jest tworzone w tym samym regionie. Reguły dotyczące nazw kont magazynów są takie same, jak w przypadku kont usługi Media Services.
      
       Więcej informacji o magazynie można znaleźć [tutaj](../../storage/common/storage-introduction.md).
   6. Wybierz opcję **Przypnij do pulpitu nawigacyjnego**, aby wyświetlić postęp wdrażania konta.
4. Kliknij opcję **Utwórz** w dolnej części formularza.
   
    Po pomyślnym utworzeniu konta zostanie załadowana strona przeglądu. W tabeli punktów końcowych przesyłania strumieniowego konto będzie mieć domyślny punkt końcowy przesyłania strumieniowego w stanie **Zatrzymany**. 

    >[!NOTE]
    >Po utworzeniu konta usługi AMS zostanie do niego dodany **domyślny** punkt końcowy przesyłania strumieniowego mający stan **Zatrzymany**. Aby rozpocząć przesyłanie strumieniowe zawartości oraz korzystać z dynamicznego tworzenia pakietów i szyfrowania dynamicznego, punkt końcowy przesyłania strumieniowego, z którego chcesz strumieniowo przesyłać zawartość, musi mieć stan **Uruchomiony**. 
   
## <a name="to-manage-your-ams-account"></a>Zarządzanie kontem usługi AMS

W celu zarządzania kontem usługi AMS (na przykład programowego łączenia się z interfejsem API usługi AMS, przekazywania plików wideo, kodowania zasobów, konfigurowania ochrony zawartości, monitorowania postępów zadań) wybierz opcję **Ustawienia** po lewej stronie portalu. W obszarze **Ustawienia** przejdź do jednego z dostępnych bloków (na przykład: **Dostęp do interfejsu API**, **Zasoby**, **Zadania**, **Ochrona zawartości**).


## <a name="next-steps"></a>Następne kroki

Teraz możesz przekazać pliki na konto usługi AMS. Więcej informacji znajduje się na stronie [Przekazywanie plików](media-services-portal-upload-files.md).

Jeśli planujesz uzyskiwać dostęp do interfejsu API usługi AMS programowo, zobacz temat [Dostęp do interfejsu API usługi Azure Media Services przy użyciu uwierzytelniania usługi Azure AD](media-services-use-aad-auth-to-access-ams-api.md).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

