---
title: Przekazywanie plików na konto usługi Azure Media Services za pomocą rozwiązania Aspera | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki przekazywania plików na konto magazynu skojarzone z kontem usługi Media Services za pomocą usługi **Aspera Server On Demand** na platformie Azure.
services: media-services
documentationcenter: ''
author: johndeu
manager: femila
editor: ''
ms.assetid: 8812623a-b425-4a0f-9e05-0ee6c839b6f9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: juliako
ms.openlocfilehash: e522883da7fddad44741599107f2dbc4c99aace6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60827022"
---
# <a name="upload-files-into-a-media-services-account-using-the-aspera-server-on-demand-service-on-azure"></a>Przekazywanie plików na konto usługi Media Services przy użyciu usługi Aspera Server On Demand na platformie Azure 

## <a name="overview"></a>Omówienie

**Aspera** to oprogramowanie do szybkiego transferowania plików. Usługa **Aspera Server On Demand** dla platformy Azure umożliwia szybkie przekazywanie i pobieranie dużych plików bezpośrednio do i z magazynu obiektów blob platformy Azure. Aby uzyskać informacje na temat usługi **Aspera On Demand**, zobacz witrynę [Aspera Cloud](http://cloud.asperasoft.com/). 
  
Usługa **Aspera Server On Demand** dla platformy Azure jest dostępna do kupienia w witrynie [Azure Marketplace](https://azure.microsoft.com/marketplace/). Aby ukończyć zakup usługi **Aspera Server On Demand** dla platformy Azure, zaloguj się do witryny Azure Marketplace przy użycia swojego identyfikatora usługi Windows Live.

Ten samouczek przeprowadzi Cię przez kroki przekazywania plików na konto magazynu skojarzone z kontem usługi Media Services za pomocą usługi **Aspera Server On Demand** na platformie Azure. 

[W tym miejscu](https://github.com/Azure-Samples/media-services-dotnet-functions-integration/tree/master/103-aspera-ingest) można znaleźć przykład sposobu używania funkcji platformy Azure z usługami Aspera i Media Services.

>[!NOTE]
>Istnieje limit maksymalnego rozmiaru pliku przetwarzanego przez procesory multimediów usługi Azure Media Services. Zobacz [ten](media-services-quotas-and-limitations.md) artykuł, aby uzyskać szczegółowe informacje na temat ograniczeń rozmiarów plików.
>

## <a name="prerequisites"></a>Wymagania wstępne 

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Identyfikator usługi Windows Live
* [Konto platformy Azure](https://azure.microsoft.com). Aby uzyskać więcej informacji, zobacz [bezpłatnej wersji próbnej Azure](https://azure.microsoft.com/pricing/free-trial/). 
* [Konto usługi Azure Media Services](media-services-portal-create-account.md).

## <a name="purchase-aspera-on-demand-for-azure"></a>Kupowanie usługi Aspera On Demand dla platformy Azure

Po zalogowaniu się do witryny Azure Marketplace wykonaj te proste kroki, aby przeprowadzić zakup usługi Aspera On Demand dla platformy Azure.

1. Wyszukaj nazwę Aspera i wybierz pozycję „Server On Demand”.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera001.png)

2. Przejrzyj plany subskrypcji i kliknij pozycję „Zarejestruj się”.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera002.png)

3. Wprowadź szczegółowe informacje o subskrypcji Server on Demand.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera003.png)

4. Kliknij pozycję **Warstwa cenowa** i wybierz żądany miesięczny wolumin w panelu podrzędnym. W panelu **Szczegóły planu** wybierz pozycję **OK**. Następnie w panelu **Wybierz warstwę cenową** kliknij pozycję **Wybierz**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera004.png)

5. W panelu podrzędnym kliknij pozycję **Warunki prawne**, aby je wyświetlić i zaakceptować. Po przejrzeniu warunków prawnych kliknij pozycję **Kup**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera005.png)

6. Sfinalizuj zakup, klikając pozycję **Utwórz**.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera006.png)

7. Na pulpicie nawigacyjnym platformy Azure pojawi się informacja, że usługa jest aprowizowana.  Po zakończeniu aprowizacji nową subskrypcję będzie można znaleźć, wyszukując nazwę usługi w swoich zasobach. Po znalezieniu usługi kliknij ją dwukrotnie, aby uruchomić portal zarządzania usługą.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera007.png)

8. Uruchom portal zarządzania usługi Aspera. Po znalezieniu nowej usługi Aspera można uzyskać dostęp do portalu zarządzania, klikając usługę.  Zostanie uruchomiony nowy panel. Na tym nowym panelu kliknij **nazwę zasobu** nowej usługi.  Na poniższym zrzucie ekranu nazwa zasobu to „AsperaTransferDemo”. Po kliknięciu nazwy zasobu zostanie uruchomiony kolejny panel. W tym nowo uruchomionym panelu zobaczysz link „Zarządzaj”. Kliknij link „Zarządzaj”, aby uruchomić portal zarządzania usługi Aspera.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera008.png)

9. Po kliknięciu linku zarządzania zostanie otwarta strona rejestracji. Rejestracja jest wymagana, aby uzyskać dostęp do usługi.

   ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera009.png)

10. W tym momencie powinien już być możliwy dostęp do portalu zarządzania usługą Aspera, w którym można tworzyć klucze dostępu, pobierać klientów i licencje usługi Aspera, wyświetlać użycie i dowiedzieć się więcej na temat interfejsów API.

    Następujący zrzut ekranu przedstawia tworzenie dostępu. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera010.png)

    Poniższy zrzut ekranu przedstawia interfejsy raportowania użycia w portalu. 

    ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera011.png)

## <a name="upload-files-with-aspera"></a>Przekazywanie plików za pomocą usługi Aspera

1. Pobierz i zainstaluj oprogramowanie klienckie Aspera:
    
    * [Wtyczka przeglądarki](https://downloads.asperasoft.com/connect2/)
    * [Klient wzbogacony](https://downloads.asperasoft.com/en/downloads/2)

2. Przeprowadź pierwszy transfer. Aby móc używać klienta Aspera do transferowania danych za pomocą usługi transferowania Aspera, musisz ukończyć następujące czynności: 

   1. Utwórz klucz dostępu za pomocą portalu usługi Aspera.  
   2. Pobierz i zainstaluj klienta usługi Aspera (oprogramowanie można znaleźć w portalu usługi Aspera) oraz uzyskaj dla niego licencję.  

      >[!NOTE]
      >Przeczytaj podręcznik klienta usługi Aspera w celu uzyskania informacji o konfiguracji.
    
   3. Pobierz niektóre informacje o swoim koncie magazynu skojarzonym z kontem usługi Azure Media za pomocą witryny [Azure Portal](https://portal.azure.com/). Konkretnie potrzebne będą nazwa i klucz oraz nazwa kontenera obiektu blob magazynu, w którym chcesz umieścić swoją zawartość. 

       * Aby uzyskać informacje o magazynie z portalu: znajdź swoje konto magazynu, kliknij pozycję Klucze dostępu i skopiuj nazwę oraz klucz konta.
       * Aby uzyskać nazwę kontenera: znajdź swoje konto magazynu, wybierz opcję **Obiekty blob** i wybierz nazwę kontenera, do którego chcesz przekazywać zawartość. 

      Poniżej znajduje się zrzut ekranu **Menedżer połączeń** klienta usługi Aspera, w którym jako typ magazynu należy określić wartość „Azure”, a także podać poświadczenia i wskazać kontener obiektów blob.

      ![Aspera](./media/media-services-upload-files-with-aspera/media-services-upload-files-with-aspera012.png)

## <a name="resources"></a>Zasoby

W niniejszym artykule zostały wymienione poniższe zasoby. 

* [Nawiązywanie połączenia z wtyczką przeglądarki](https://downloads.asperasoft.com/connect2/)
* [Przewodnik po nawiązywaniu połączeń](https://downloads.asperasoft.com/en/documentation/8)
* [Klient usługi Aspera](https://downloads.asperasoft.com/en/downloads/2)
* [Podręcznik klienta](https://downloads.asperasoft.com/en/documentation/2)

## <a name="next-steps"></a>Kolejne kroki

Możesz teraz [kopiować obiekty blob z konta magazynu na konto usługi AMS](media-services-copying-existing-blob.md#copy-blobs-from-a-storage-account-into-an-ams-account).

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

