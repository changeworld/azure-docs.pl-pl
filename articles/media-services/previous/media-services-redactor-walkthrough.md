---
title: Redagowanie twarzy za pomocą przewodnika usługi Azure Media Analytics | Dokumenty firmy Microsoft
description: W tym temacie przedstawiono instrukcje krok po kroku dotyczące uruchamiania pełnego przepływu pracy redakcyjnego przy użyciu usługi Azure Media Services Explorer (AMSE) i narzędzia Azure Media Redactor Visualizer (narzędzie open source).
services: media-services
documentationcenter: ''
author: Lichard
manager: femila
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2019
ms.author: ril
ms.reviewer: juliako
ms.openlocfilehash: a8db8de6ef062dcf757f3d264379677d6550ea3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69997673"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redagowanie twarzy za pomocą przewodnika usługi Azure Media Analytics

## <a name="overview"></a>Omówienie

**Usługa Azure Media Redactor** to procesor multimediów [usługi Azure Media Analytics](media-services-analytics-overview.md) (MP), który oferuje skalowalną redakcję twarzy w chmurze. Funkcja przeredagowania twarzy umożliwia modyfikowanie filmu w celu rozmycia twarzy wybranych osób. Możesz użyć usługi redagowania twarzy w scenariuszach bezpieczeństwa publicznego i mediów informacyjnych. Kilka minut materiału, który zawiera wiele twarzy może potrwać wiele godzin, aby redagować ręcznie, ale z tej usługi proces redakcyjny twarzy będzie wymagać tylko kilku prostych kroków. Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/azure-media-redactor/) blog.

Aby uzyskać szczegółowe informacje na temat **usługi Azure Media Redactor,** zobacz [omówienie redakcji twarzy.](media-services-face-redaction.md)

W tym temacie przedstawiono instrukcje krok po kroku dotyczące uruchamiania pełnego przepływu pracy redakcyjnego przy użyciu usługi Azure Media Services Explorer (AMSE) i narzędzia Azure Media Redactor Visualizer (narzędzie open source).

Aby uzyskać więcej informacji, zobacz [ten](https://azure.microsoft.com/blog/redaction-preview-available-globally) blog.

## <a name="azure-media-services-explorer-workflow"></a>Przepływ pracy Eksploratora usług Azure Media Services

Najprostszym sposobem rozpoczęcia pracy z Redactor jest użycie narzędzia AMSE open source w usłudze GitHub. Jeśli nie potrzebujesz dostępu do adnotacji json lub obrazów jpg twarzy, można uruchomić uproszczony przepływ pracy w trybie **kombinacyjnym.**

### <a name="download-and-setup"></a>Pobieranie i konfiguracja

1. Pobierz narzędzie AMSE dla AMS v2 [stąd](https://aka.ms/amseforv2).
1. Zaloguj się do konta usługi Media Services przy użyciu klucza usługi.

    Aby uzyskać informacje o kluczu i nazwie konta, przejdź do witryny [Azure Portal](https://portal.azure.com/) i wybierz swoje konto AMS. Następnie wybierz pozycję Ustawienia > klawiszy. W oknie Zarządzanie kluczami widoczna jest nazwa konta oraz wyświetlane są klucze podstawowe i pomocnicze. Skopiuj wartości nazwy konta i klucza podstawowego.

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Pierwszy przebieg – tryb analizy

1. Prześlij plik multimedialny za pośrednictwem zasobu > przesyłania lub przeciągania i upuszczania. 
1. Kliknij prawym przyciskiem myszy i przetwórz plik multimedialny przy użyciu usługi Media Analytics — > trybie Redactor azure media – > analizy. 


![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Dane wyjściowe będą zawierać plik json adnotacji z danymi lokalizacji twarzy, a także jpg każdej wykrytej twarzy. 

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Drugie przejście – tryb redact

1. Prześlij oryginalny zasób wideo do danych wyjściowych z pierwszego przebiegu i ustaw jako zasób podstawowy. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcjonalnie) Prześlij plik "Dance_idlist.txt", który zawiera listę rozdzielanych linii nowego do nich, które chcesz redagować. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcjonalnie) Wprowadzanie wszelkich zmian w pliku adnotations.json, takich jak zwiększenie granic obwiedni. 
4. Kliknij prawym przyciskiem myszy zasób wyjściowy z pierwszego przebiegu, wybierz redactor i uruchom w trybie **Redact.** 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Pobierz lub udostępnij ostateczny zredagowany zasób wyjściowy. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Narzędzie open source Azure Media Redactor Visualizer

[Narzędzie wizualizatora](https://github.com/Microsoft/azure-media-redactor-visualizer) open source ma na celu pomóc deweloperom, począwszy od formatu adnotacji z analizowania i przy użyciu danych wyjściowych.

Po sklonowanie repozytorium, w celu uruchomienia projektu, trzeba będzie pobrać FFMPEG z ich [oficjalnej strony](https://ffmpeg.org/download.html).

Jeśli jesteś deweloperem, który próbuje przeanalizować dane adnotacji JSON, zajrzyj do modeli.MetaData, aby uzyskać przykładowe przykłady kodu.

### <a name="set-up-the-tool"></a>Konfigurowanie narzędzia

1.  Pobierz i zbuduj całe rozwiązanie. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Pobierz FFMPEG [stąd](https://ffmpeg.org/download.html). Projekt został pierwotnie opracowany z wersją be1d324 (2016-10-04) z łączem statycznym. 
3.  Skopiuj pliki ffmpeg.exe i ffprobe.exe do tego samego folderu wyjściowego co usługa AzureMediaRedactor.exe. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Uruchom plik AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Użyj narzędzia

1. Przetwórz wideo na koncie usługi Azure Media Services w trybie analizy redactor mp. 
2. Pobierz zarówno oryginalny plik wideo, jak i dane wyjściowe zadania redakcyjnego — analizowanie. 
3. Uruchom aplikację wizualizatora i wybierz pliki powyżej. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Wyświetl podgląd pliku. Wybierz, które ściany chcesz rozmyć za pomocą paska bocznego po prawej stronie. 
    
    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Dolne pole tekstowe zostanie zaktualizowane o identyfikatory twarzy. Utwórz plik o nazwie "idlist.txt" z tymi identyfikatorami jako listą rozdzielanych linii. 

    >[!NOTE]
    > Idlist.txt powinny być zapisywane w ANSI. Możesz użyć notatnika, aby zapisać w ANSI.
    
6.  Przekaż ten plik do zasobu wyjściowego z kroku 1. Prześlij oryginalny film do tego zasobu i ustaw jako główny zasób. 
7.  Uruchom zadanie redakcyjne dla tego zasobu w trybie "Redact", aby uzyskać ostateczny zredagowany film. 

## <a name="next-steps"></a>Następne kroki 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Omówienie usługi Azure Media Services Analytics](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Ogłaszanie redagowania twarzy w usłudze Azure Media Analytics](https://azure.microsoft.com/blog/azure-media-redactor/)
