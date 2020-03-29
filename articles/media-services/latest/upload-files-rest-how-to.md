---
title: Przekazywanie plików na konto usługi Azure Media Services w wersji 3 przy użyciu funkcji REST | Dokumenty firmy Microsoft
description: Dowiedz się, jak uzyskać zawartość multimedialną do usługi Media Services, tworząc i przekazując zasoby.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 38d46978e37ead59deb17a86f643df041452e497
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76705772"
---
# <a name="upload-files-into-a-media-services-v3-account-using-rest"></a>Przekazywanie plików na konto usługi Media Services w wersji 3 przy użyciu funkcji REST

W usłudze Media Services należy przekazać pliki cyfrowe do kontenera obiektów blob skojarzonego z zasobem. Encja [Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki podpisów kodowanych (oraz metadane dotyczące tych plików). Po przesłaniu plików do kontenera zasobu zawartość jest bezpiecznie przechowywana w chmurze w celu dalszego przetwarzania i przesyłania strumieniowego.

W tym artykule pokazano, jak przekazać plik lokalny przy użyciu REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, należy:

- Przejrzyj [koncepcję zasobu](assets-concept.md).
- [Skonfiguruj listonosz dla wywołań interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).
    
    Upewnij się, że postępuj zgodnie z ostatnim krokiem w temacie [Pobierz token usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Tworzenie zasobu

W tej sekcji pokazano, jak utworzyć nowy zasób.

1. Wybierz **pozycję Zasoby** -> **Utwórz lub zaktualizuj zasób**.
2. Kliknij pozycję **Wyślij**.

    ![Tworzenie zasobu](./media/upload-files/postman-create-asset.png)

Zobaczysz **odpowiedź** z informacjami o nowo utworzonym zasobie.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Uzyskaj adres URL sygnatury dostępu Współdzielonego z uprawnieniami do odczytu i zapisu 

W tej sekcji pokazano, jak uzyskać adres URL sygnatury dostępu Współdzielonego, który został wygenerowany dla utworzonego zasobu. Adres URL sygnatury dostępu Współdzielonego został utworzony z uprawnieniami do odczytu i zapisu i może służyć do przekazywania plików cyfrowych do kontenera zasobów.

1. Wybierz **pozycję Zasoby** -> **Wyświetl adresy URL zasobów**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/upload-files/postman-create-sas-locator.png)

Zobaczysz **odpowiedź** z informacjami o adresach URL zasobu. Skopiuj pierwszy adres URL i użyj go do przesłania pliku.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przekazywania

Użyj interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md) lub [sdk .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Następne kroki

[Samouczek: Kodowanie zdalnego pliku na podstawie adresu URL i przesyłanie strumieniowe wideo - REST](stream-files-tutorial-with-rest.md)
