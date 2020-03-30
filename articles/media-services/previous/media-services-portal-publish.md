---
title: Publikowanie zawartości w witrynie Azure portal | Dokumenty firmy Microsoft
description: W tym samouczku otrzymasz od użytkownika kroki publikowania zawartości w witrynie Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 92c364eb-5a5f-4f4e-8816-b162c031bb40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: 5f242018abfb15cea1b76cbcaad00942ec25d78d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69015076"
---
# <a name="publish-content-in-the-azure-portal"></a>Publikowanie zawartości w witrynie Azure portal  
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-publish.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Reszta](media-services-rest-deliver-streaming-content.md)
> 
> 

## <a name="overview"></a>Omówienie
> [!NOTE]
> Do wykonania kroków tego samouczka potrzebne jest konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

Aby podać użytkownikowi adres URL, który może służyć do odtwarzania strumieniowego lub pobierania zawartości, najpierw musisz opublikować swój element zawartości przez utworzenie lokalizatora. Lokalizatory zapewniają dostęp do plików zasobów. Usługa Azure Media Services obsługuje dwa typy lokalizatorów: 

* **Lokalizatory przesyłania strumieniowego (OnDemandOrigin)**. Lokalizatory przesyłania strumieniowego służą do adaptacyjnego przesyłania strumieniowego. Przykłady adaptacyjnego przesyłania strumieniowego obejmują przesyłanie strumieniowe na żywo (HLS) firmy Apple HTTP, płynne przesyłanie strumieniowe i dynamiczne przesyłanie strumieniowe adaptacyjne za pośrednictwem protokołu HTTP (DASH, zwanego również MPEG-DASH). Aby utworzyć lokalizator przesyłania strumieniowego, element zawartości musi zawierać plik ism. Na przykład http://amstest.streaming.mediaservices.windows.net/61b3da1d-96c7-489e-bd21-c5f8a7494b03/scott.ism/manifest.
* **Lokalizatory progresywne (sygnatura dostępu współdzielonego)**. Lokalizatory progresywne służą do dostarczania wideo przy użyciu pobierania progresywnego.

Aby utworzyć adres URL przesyłania strumieniowego HLS, dołącz *(format=m3u8-aapl)* do adresu URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=m3u8-aapl)

Aby utworzyć adres URL przesyłania strumieniowego do odtwarzania elementu zawartości Smooth Streaming, użyj następującego formatu adresu URL:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest

Aby utworzyć adres URL przesyłania strumieniowego w protokole MPEG-DASH, dołącz do adresu URL ciąg *(format=mpd-time-csf)*:

    {streaming endpoint name-media services account name}/{locator ID}/{file name}.ism/Manifest(format=mpd-time-csf)

Adres URL sygnatury dostępu współdzielonego ma następujący format:

    {blob container name}/{asset name}/{file name}/{shared access signature}

Aby uzyskać więcej informacji, zobacz [omówienie dostarczania zawartości](media-services-deliver-content-overview.md).

> [!NOTE]
> Lokalizatory utworzone w witrynie Azure Portal przed marcem 2015 r. mają dwuletnią datę wygaśnięcia.  
> 
> 

Aby zaktualizować datę wygaśnięcia lokalizatora, można użyć [interfejsu API REST](https://docs.microsoft.com/rest/api/media/operations/locator#update_a_locator) lub interfejsu API platformy [.NET](https://go.microsoft.com/fwlink/?LinkID=533259). 

> [!NOTE]
> Po zaktualizowaniu daty wygaśnięcia lokalizatora sygnatury dostępu współdzielonego następuje zmiana adresu URL.

### <a name="to-use-the-portal-to-publish-an-asset"></a>Aby opublikować element zawartości za pomocą portalu
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz **pozycję Zasoby ustawień** > **Assets**. Wybierz element zawartości, który chcesz opublikować.
3. Wybierz przycisk **Publikuj**.
4. Wybierz typ lokalizatora.
5. Wybierz pozycję **Dodaj**.
   
    ![Publikowanie wideo](./media/media-services-portal-vod-get-started/media-services-publish1.png)

Adres URL jest dodawany do listy **Opublikowane adresy URL**.

## <a name="play-content-in-the-portal"></a>Odtwarzanie zawartości w portalu
Swoje wideo możesz przetestować w odtwarzaczu zawartości w witrynie Azure Portal.

Wybierz wideo, a następnie wybierz przycisk **Odtwórz**.

![Odtwarzanie wideo w witrynie Azure Portal](./media/media-services-portal-vod-get-started/media-services-play.png)

Zagadnienia do rozważenia:

* Upewnij się, że wideo zostało już opublikowane.
* Odtwarzacz multimedialny w witrynie Azure Portal odtwarza zawartość z domyślnego punktu końcowego przesyłania strumieniowego. Aby odtworzyć zawartość z punktu końcowego przesyłania strumieniowego innego niż domyślny, zaznacz i skopiuj adres URL, a następnie wklej go do innego odtwarzacza. Na przykład swoje wideo możesz przetestować w usłudze [Azure Media Player](https://aka.ms/azuremediaplayer).
* Punkt końcowy przesyłania strumieniowego, z którego przesyłane strumieniowo musi być uruchomiony.  

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

