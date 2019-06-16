---
title: Redagowanie twarze za pomocą usługi Azure Media Analytics wskazówki | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono instrukcje krok po kroku na temat uruchamiania redakcyjne pełnego przepływu pracy przy użyciu usługi Azure Media Services Explorer (AMSE) i Azure Media Redactor Visualizer (Narzędzia typu open source).
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
ms.author: rli; juliako;
ms.openlocfilehash: 3e4844c3174e41ca7f6f5667a2777aba11f70f11
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60875075"
---
# <a name="redact-faces-with-azure-media-analytics-walkthrough"></a>Redagowanie twarze z przewodnikiem analizy multimediów Azure

## <a name="overview"></a>Omówienie

**Usługa Azure Media Redactor** jest [analizy multimediów Azure](media-services-analytics-overview.md) procesor multimediów (MP), który oferuje pomocą redakcji twarzy skalowalny w chmurze. Pomocą redakcji twarzy umożliwia modyfikowanie wideo w celu rozmycie twarze wybranych osób. Można użyć usługa redakcji twarzy w publicznych scenariuszach bezpieczeństwa i mediów informacyjnych. Kilka minut materiał, który zawiera wiele powierzchni może zająć godzin redagowanie ręcznie, ale z tą usługą procesu redakcji twarzy wymaga tylko kilku prostych krokach. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/azure-media-redactor/) blogu.

Aby uzyskać szczegółowe informacje o **usługi Azure Media Redactor**, zobacz [Przegląd redakcji twarzy](media-services-face-redaction.md) tematu.

W tym temacie przedstawiono instrukcje krok po kroku na temat uruchamiania redakcyjne pełnego przepływu pracy przy użyciu usługi Azure Media Services Explorer (AMSE) i Azure Media Redactor Visualizer (Narzędzia typu open source).

Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/redaction-preview-available-globally) blogu.

## <a name="azure-media-services-explorer-workflow"></a>Usługa Azure Media Services Explorer przepływu pracy

Najprostszym sposobem, aby zacząć korzystać z usługi Redactor jest do użycia przy użyciu narzędzia AMSE typu open source w serwisie GitHub. Możesz uruchomić uproszczonego przepływu pracy za pośrednictwem **połączone** trybu, jeśli nie potrzebujesz dostępu do danych json adnotacji lub obrazów jpg twarzy.

### <a name="download-and-setup"></a>Pobrania i instalacji

1. Pobierz narzędzie AMSE z [tutaj](https://github.com/Azure/Azure-Media-Services-Explorer).
1. Zaloguj się do konta usługi Media Services za pomocą klucza usługi.

    Aby uzyskać informacje o kluczu i nazwie konta, przejdź do witryny [Azure Portal](https://portal.azure.com/) i wybierz swoje konto AMS. Następnie, wybierz pozycję Ustawienia > klucze. W oknie Zarządzanie kluczami widoczna jest nazwa konta oraz wyświetlane są klucze podstawowe i pomocnicze. Skopiuj wartości nazwy konta i klucza podstawowego.

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough001.png)

### <a name="first-pass--analyze-mode"></a>Najpierw przekazać — analizowanie tryb

1. Przekazywanie plików multimediów, za pośrednictwem zasobów -> przekazywania, lub za pomocą przeciągania i upuszczania. 
1. Kliknij prawym przyciskiem myszy i przetwarzanie pliku multimedialnego za pomocą usługi Media Analytics -> Azure Media Redactor -> Tryb analizy. 


![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough002.png)

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough003.png)

Dane wyjściowe będzie zawierać plik json adnotacje z twarzy danych lokalizacji, a także jpg każdej twarzy wykryte. 

![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough004.png)

### <a name="second-pass--redact-mode"></a>Po drugie przekazać — modyfikowanie tryb

1. Przekaż wideo oryginału w danych wyjściowych z pierwszym przebiegu i Ustaw jako zasób podstawowy. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough005.png)

2. (Opcjonalnie) Przekazywanie pliku "Dance_idlist.txt", który zawiera listę identyfikatorów, o których chcesz redagowanie rozdzielonych znakami nowego wiersza. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough006.png)

3. (Opcjonalnie) Wprowadź zmiany w pliku annotations.json, np. zwiększenie otaczający granice pola. 
4. Kliknij prawym przyciskiem myszy elementu zawartości wyjściowej z pierwszym przebiegu, wybierz Redactor i uruchomić z **Redact** trybu. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough007.png)

5. Pobierz lub udostępnianie zasobów zostały zredagowane pliku wyjściowego. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough008.png)

## <a name="azure-media-redactor-visualizer-open-source-tool"></a>Narzędzia typu open source w usłudze Azure Media Redactor wizualizatora

Open source [narzędzi wizualizatora](https://github.com/Microsoft/azure-media-redactor-visualizer) ma na celu deweloperów rozpoczynających się przy użyciu formatu adnotacje z analizy i przy użyciu danych wyjściowych.

Po klonowania repozytorium, aby można było uruchomić projekt, konieczne będzie pobrać narzędzia FFMPEG z ich [oficjalna witryna](https://ffmpeg.org/download.html).

Jeśli jesteś deweloperem próby przeprowadzenia analizy danych JSON w adnotacji, poszukaj wewnątrz Models.MetaData przykładów kodu.

### <a name="set-up-the-tool"></a>Konfigurowanie narzędzia

1.  Pobierz i skompiluj całego rozwiązania. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough009.png)

2.  Pobierz narzędzia FFMPEG z [tutaj](https://ffmpeg.org/download.html). Ten projekt został pierwotnie opracowana be1d324 wersji (2016-10-04) z łączenia statycznego. 
3.  Skopiuj ffmpeg.exe i ffprobe.exe, w tym samym folderze danych wyjściowych jako AzureMediaRedactor.exe. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough010.png)

4. Uruchom AzureMediaRedactor.exe. 

### <a name="use-the-tool"></a>Za pomocą narzędzia

1. Przetwarzanie wideo na Twoim koncie usługi Azure Media Services przy użyciu pakietu administracyjnego usługi Redactor w trybie analizy. 
2. Pobieranie oryginalnego pliku wideo i dane wyjściowe redakcyjne — analizowanie zadania. 
3. Uruchom aplikację wizualizatora, a następnie wybierz pliki powyżej. 

    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough011.png)

4. Wyświetl podgląd pliku. Zaznacz powierzchnie, które chcesz rozmycie za pośrednictwem paska bocznego po prawej stronie. 
    
    ![Redakcja twarzy](./media/media-services-redactor-walkthrough/media-services-redactor-walkthrough012.png)

5.  Pole tekstowe dolnej zostaną zaktualizowane o twarzy, identyfikatory. Utwórz plik o nazwie "idlist.txt" te identyfikatory jako listę rozdzielonych znakami nowego wiersza. 

    >[!NOTE]
    > Idlist.txt powinny być zapisane w formacie ANSI. Notatnik można użyć, aby zapisać w formacie ANSI.
    
6.  Przekaż ten plik do elementu zawartości wyjściowej z kroku 1. Przekazywanie oryginalnego filmu wideo do tego zasobu, a także, a następnie ustaw jako zasób podstawowy. 
7.  Uruchom zadanie redakcyjne tego zasobu z trybem "Redact" można pobrać końcowy zredagowanego wideo. 

## <a name="next-steps"></a>Kolejne kroki 

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Powiązane linki
[Przegląd Analityki usługi Azure Media Services](media-services-analytics-overview.md)

[Pokazy usługi Azure Media Analytics](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

[Ogłoszenie pomocą redakcji twarzy na potrzeby analizy multimediów Azure](https://azure.microsoft.com/blog/azure-media-redactor/)
