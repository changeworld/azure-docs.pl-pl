---
title: Redagowanie krojów z przewodnikiem analizy multimediów Azure | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono instrukcje krok po kroku na temat uruchamiania redakcyjne pełnego przepływu pracy przy użyciu usługi Azure Media Services Explorer (AMSE) i Azure Media Redactor Visualizer (narzędzie typu open source).
services: media-services
documentationcenter: ''
author: Lichard
manager: cfowler
editor: ''
ms.assetid: d6fa21b8-d80a-41b7-80c1-ff1761bc68f2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 09/03/2017
ms.author: rli; juliako;
ms.openlocfilehash: d7a6abaeff0e939ed9e299e16e2fe59b65127aba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redagowanie krojów z przewodnikiem analizy multimediów Azure

## <a name="overview"></a>Przegląd

**Azure Media Redactor** jest [analizy multimediów Azure](media-services-analytics-overview.md) procesor multimediów (MP) oferuje skalowalne krój redakcyjne w chmurze. Redakcyjne krój umożliwia modyfikowanie wideo do rozmycia kroje wybrane osoby. Można korzystać z usługi redakcyjne krój w publicznych scenariusze bezpieczeństwa i nośnika wiadomości. Kilka minut najmniejszym zawiera wiele kroje może zająć godzin redagowanie ręcznie, ale z tą usługą krój redakcyjne wymagany wykonanie kilku prostych krokach. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Szczegółowe informacje o **Azure Media Redactor**, zobacz [omówienie redakcyjne krój](media-services-face-redaction.md) tematu.

W tym temacie przedstawiono instrukcje krok po kroku na temat uruchamiania redakcyjne pełnego przepływu pracy przy użyciu usługi Azure Media Services Explorer (AMSE) i Azure Media Redactor Visualizer (narzędzie typu open source).

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Azure Media Services Explorer przepływu pracy

Najłatwiejszym sposobem na szybkie wprowadzenie Redactor jest narzędzie AMSE typu open source w witrynie github. Możesz uruchomić uproszczony przepływu pracy za pośrednictwem **łączyć** tryb, jeśli nie jest potrzebny dostęp do json adnotacji ani krój obrazów jpg.

### <a name="download-and-setup"></a>Pobierania i instalacji

1. Pobierz narzędzie AMSE z [tutaj](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Zaloguj się do konta usługi Media Services przy użyciu klucza usługi.

    Aby uzyskać informacje o kluczu i nazwie konta, przejdź do witryny [Azure Portal](https://portal.azure.com/) i wybierz swoje konto AMS. Wybierz ustawienia > klucze. W oknie Zarządzanie kluczami widoczna jest nazwa konta oraz wyświetlane są klucze podstawowe i pomocnicze. Skopiuj wartości nazwy konta i klucza podstawowego.

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Najpierw przekazać — analizowanie tryb

1. Przekazywanie pliku multimediów za pośrednictwem zasobów –> przekazywania, lub za pośrednictwem operacji przeciągania i upuszczania. 
1. Kliknij prawym przyciskiem myszy i przetwarzanie pliku nośnika, przy użyciu analizy multimediów Azure Media Redactor –> –> Tryb analizy. 


![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Dane wyjściowe będzie zawierać plik json adnotacje z danych lokalizacji krój, a także jpg każdego wykrytego powierzchni. 

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Drugi przekazać — redagowanie tryb

1. Wyślij oryginalnej zawartości wideo dane wyjściowe z pierwszego przejścia i Ustaw jako podstawowy zasobów. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcjonalnie) Przekaż plik "Dance_idlist.txt", który zawiera listę identyfikatorów chcesz redagowanie rozdzielone znakami nowego wiersza. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcjonalnie) Wprowadź zmiany w pliku annotations.json, takich jak zwiększenie ograniczający granice pola. 
4. Kliknij prawym przyciskiem myszy elementu zawartości wyjściowej z pierwszego przejścia, wybierz Redactor i uruchom z **Redact** tryb. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Pobieranie i udostępnianie zasobów zredagowanym pliku wyjściowego. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Azure Media Redactor wizualizatora typu open source narzędzia

Typu open source [narzędzie wizualizatora](https://github.com/Microsoft/azure-media-redactor-visualizer) ułatwia deweloperom uruchamiana z formatem adnotacje z analizy i przy użyciu danych wyjściowych.

Po można sklonować repozytorium, aby można było uruchomić projekt, musisz pobrać FFMPEG z ich [oficjalna witryna](https://ffmpeg.org/download.html).

Jeśli jesteś deweloperem próby przeprowadzenia analizy danych JSON adnotacji, poszukaj wewnątrz Models.MetaData przykłady kodu przykładowej.

### <a name="set-up-the-tool"></a>Konfigurowanie narzędzia

1.  Pobierz i skompiluj całe rozwiązanie. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Pobierz FFMPEG z [tutaj](https://ffmpeg.org/download.html). Ten projekt pierwotnie został opracowany z wersji be1d324 (2016-10-04) z statycznego łączenia. 
3.  Skopiuj ffmpeg.exe i ffprobe.exe do tego samego folderu wyjściowego jako AzureMediaRedactor.exe. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Uruchom AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Użyj narzędzia

1. Przetwarzanie wideo na koncie usługi Azure Media Services z Redactor MP na tryb analizy. 
2. Pobierz zarówno oryginalny plik wideo, jak i dane wyjściowe redakcyjne — analizowanie zadania. 
3. Uruchom aplikację wizualizatora i wybierz pliki powyżej. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Wyświetl podgląd pliku. Wybierz kroje, które chcesz rozmycia za pomocą paska bocznego po prawej stronie. 
    
    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Pole tekstowe dolnej zaktualizuje kroju identyfikatorów. Utwórz plik o nazwie "idlist.txt" te identyfikatory jako listę rozdzielone znakami nowego wiersza. 

    >[!NOTE]
    > Idlist.txt powinny być zapisane w formacie ANSI. Notatnik umożliwia zapisywanie w formacie ANSI.
    
6.  Przekazywanie tego pliku do elementu zawartości wyjściowej z kroku 1. Przekazywanie oryginalnego wideo do trwałego również i Ustaw jako podstawowy zasobów. 
7.  Uruchom zadanie redakcyjne ten zasób w trybie "Redact" można uzyskać końcowa zredagowanego wideo. 

## <a name="next-steps"></a>Kolejne kroki 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd analiz usługi Azure Media Services](media-services-analytics-overview.md)

[W trakcie analizy multimediów Azure](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Anonsowanie redakcyjne krój na potrzeby analizy multimediów Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
