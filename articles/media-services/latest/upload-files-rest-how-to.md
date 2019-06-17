---
title: Przekazywanie plików na konto usługi Azure Media Services przy użyciu usługi REST | Dokumentacja firmy Microsoft
description: Dowiedz się, jak uzyskać zawartości multimedialnej do usługi Media Services, tworząc i przekazując zasoby.
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
ms.openlocfilehash: a241f66adecbab1d0b1462f379d3765d6c1de252
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61466803"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Przekazywanie plików na konto usługi Media Services przy użyciu usługi REST

W usłudze Media Services możesz przekazać pliki cyfrowe do kontenera obiektów blob, skojarzone z elementem zawartości. [Zasobów](https://docs.microsoft.com/rest/api/media/operations/asset) jednostki może zawierać wideo, audio, obrazy, kolekcje miniatur, tekst śledzi i napisów plików (i metadane dotyczące tych plików). Gdy pliki są przekazywane do kontenera elementu zawartości, zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

W tym artykule pokazano, jak przekazanie pliku lokalnego suing REST.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, musisz:

- Przegląd [koncepcji zasobów](assets-concept.md).
- [Konfigurowanie narzędzia Postman dla wywołania interfejsu API REST usługi Azure Media Services](media-rest-apis-with-postman.md).
    
    Upewnij się, że należy wykonać ostatni krok w temacie [pobrać tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Utworzenie elementu zawartości

W tej sekcji przedstawiono sposób tworzenia nowego elementu zawartości.

1. Wybierz **zasoby** -> **tworzenia lub aktualizacji zasobu**.
2. Kliknij pozycję **Wyślij**.

    ![Utworzenie elementu zawartości](./media/upload-files/postman-create-asset.png)

Zostanie wyświetlony **odpowiedzi** z użyciem informacji o nowo utworzonego zasobu.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Pobierz adres URL sygnatury dostępu Współdzielonego z uprawnieniami do odczytu i zapisu 

W tej sekcji pokazano, jak można pobrać adresu URL sygnatury dostępu Współdzielonego, który został wygenerowany dla utworzonego zasobu. Adres URL sygnatury dostępu Współdzielonego został utworzony z uprawnieniami do odczytu i zapisu i może służyć do przekazać pliki cyfrowe do kontenera zasobów.

1. Wybierz **zasoby** -> **listy adresów URL zasobów**.
2. Kliknij pozycję **Wyślij**.

    ![Przekazywanie pliku](./media/upload-files/postman-create-sas-locator.png)

Zostanie wyświetlony **odpowiedzi** z użyciem informacji o adresach URL zasobu. Skopiuj adres URL pierwszego i używać go do przekazania pliku.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Przekazywanie pliku do magazynu obiektów blob przy użyciu adresu URL przesyłania

Za pomocą interfejsów API usługi Azure Storage lub zestawów SDK (na przykład [interfejsu API REST magazynu](../../storage/common/storage-rest-api-auth.md), [zestawu JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md), lub [zestawu .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Kolejne kroki

[Samouczek: Kodowanie pliku zdalnego na podstawie adresu URL i przesyłanie strumieniowe wideo — REST](stream-files-tutorial-with-rest.md)
