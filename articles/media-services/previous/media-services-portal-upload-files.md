---
title: Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal | Microsoft Docs
description: Ten samouczek przeprowadzi Cię przez kroki przekazywania plików na konto usługi Media Services w witrynie Azure Portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 3ad3dcea-95be-4711-9aae-a455a32434f6
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: 36e1f797263e367a73fde140d979243f96e83948
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33780555"
---
# <a name="upload-files-to-a-media-services-account-in-the-azure-portal"></a>Przekazywanie plików na konto usługi Media Services w witrynie Azure Portal
> [!div class="op_single_selector"]
> * [Portal](media-services-portal-upload-files.md)
> * [.NET](media-services-dotnet-upload-files.md)
> * [REST](media-services-rest-upload-files.md)
> 
> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 

Za pomocą usługi Azure Media Services można przekazać pliki cyfrowe do elementu zawartości. Element zawartości może zawierać wideo, audio, obrazy, kolekcje miniatur, ścieżki tekstowe i pliki transkrypcji (oraz metadane dla tych plików). Po przekazaniu plików zawartość jest bezpiecznie przechowywana w chmurze na potrzeby dalszego przetwarzania i przesyłania strumieniowego.

> [!NOTE]
> W usłudze Media Services obowiązuje limit maksymalnego rozmiaru przetwarzanych plików. Aby uzyskać szczegółowe informacje na temat limitów rozmiarów plików, zobacz [Media Services quotas and limitations](media-services-quotas-and-limitations.md) (Limity przydziału i ograniczenia w usłudze Media Services).
>

## <a name="upload-files"></a>Przekazywanie plików
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. Wybierz kolejno pozycje **Ustawienia** > **Elementy zawartości**. Następnie wybierz przycisk **Przekaż**.
   
    ![Przekazywanie plików](./media/media-services-portal-vod-get-started/media-services-upload.png)
   
    Zostanie wyświetlone okno **Przekaż element zawartości wideo**.
   
   > [!NOTE]
   > W usłudze Media Services nie ma limitu rozmiaru pliku dla przekazywanych wideo.
 
3. Na swoim komputerze przejdź do wideo, które chcesz przekazać. Wybierz wideo, a następnie wybierz przycisk **OK**.  
   
    Rozpocznie się przekazywanie. Postęp będzie widoczny pod nazwą pliku.  

Po zakończeniu przekazywania nowy element zawartości będzie widoczny w okienku **Elementy zawartości**. 

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zakodować przekazane elementy zawartości](media-services-portal-encode.md).

* Możesz także używać usługi Azure Functions do wyzwalania zadania kodowania, gdy plik trafi do skonfigurowanego kontenera. Aby uzyskać więcej informacji, zobacz przykład w temacie [Media Services: Integrating Azure Media Services with Azure Functions and Logic Apps](https://azure.microsoft.com/resources/samples/media-services-dotnet-functions-integration/) (Media Services: Integrowanie usługi Azure Media Services z usługami Azure Functions i Logic Apps).


