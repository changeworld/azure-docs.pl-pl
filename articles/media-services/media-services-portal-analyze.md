---
title: Analiza multimediów przy użyciu portalu Azure | Dokumentacja firmy Microsoft
description: W tym temacie omówiono sposób przetwarzania multimediów procesory multimediów usługi analiza multimediów (MP) przy użyciu portalu Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: juliako
ms.openlocfilehash: fee9f8d2204869cbe5cac7f446e8011305e92bfa
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2018
ms.locfileid: "29129094"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analiza multimediów za pomocą witryny Azure Portal
> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz artykuł [Bezpłatna wersja próbna platformy Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Przegląd
Azure Media Services Analytics to kolekcja składników mowy i obrazu (w skali przedsiębiorstwa, zgodności i zabezpieczeń globalne), które ułatwiają organizacjom i przedsiębiorstwom uzyskiwanie przydatnych wyników analiz na podstawie posiadanych plików wideo. Aby uzyskać bardziej szczegółowe omówienie usługi Azure Media Services Analytics zobacz [to](media-services-analytics-overview.md) tematu. 

W tym temacie omówiono sposób przetwarzania multimediów procesory multimediów usługi analiza multimediów (MP) przy użyciu portalu Azure. Pakiety MP analizy multimediów tworzą pliki MP4 lub pliki w formacie JSON. Jeśli plik MP4 utworzony przez procesor multimediów można pobrać progresywnie. Plik JSON utworzony przez procesor multimediów można pobrać z magazynu obiektów blob platformy Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Wybierz zasób, który chcesz przeanalizować
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz opcję **Elementy zawartości**.  
   .
    ![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Wybierz element zawartości, którą chcesz do analizowania i naciśnij klawisz **Analizuj** przycisku.
   
    ![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. W **procesu multimedialnej z analizy multimediów** okna, wybierz procesor. 
   
    Pozostała część artykuł zawiera informacje o przyczynie i sposobu użycia każdego procesora. 
5. Naciśnij klawisz **Utwórz** można uruchomić zadania.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Azure Media indeksatora** procesor multimediów pozwala na udostępnianie plików multimedialnych i treści wyszukiwanie, a także wygenerować zamkniętego śledzi podpisów. W tej sekcji przedstawiono niektóre szczegóły na temat opcji, które można określić dla tego pakietu administracyjnego.

![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Język
Języka naturalnego zostać rozpoznany w pliku multimedialnego. Na przykład w języku angielskim i hiszpańskim. 

### <a name="captions"></a>Podpisy
Można wybrać formatu podpisu, który zostanie wygenerowane z zawartości. Zadania indeksowania może generować pliki napisów w następujących formatach:  

* **SAMI**
* **TTML**
* **WebVTT**

Zamknięte podpis (DW) plików w tych formatach można udostępnić osoby niepełnosprawne przesłuchanie plików audio i wideo.

### <a name="aib-file"></a>Plik AIB
Wybierz tę opcję, jeśli chcesz wygenerować plik Audio indeksu Blob do użycia z niestandardowych IFilter serwera SQL. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogu.

### <a name="keywords"></a>Słowa kluczowe
Wybierz tę opcję, jeśli chcesz wygenerować plik XML słów kluczowych. Ten plik zawiera słowa kluczowe wyodrębnione z zawartości mowy, częstotliwości i przesunięcia informacje.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, które pozwala zidentyfikować zadanie. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwia identyfikowanie zawartości danych wyjściowych. 

## <a name="azure-media-hyperlapse"></a>Azure Media Hyperlapse
Azure Media Hyperlapse jest MP tworzącą smooth czas, jaki upłynął wideo z pierwszą osobą lub akcji aparatu zawartości.  Aby uzyskać więcej informacji, zobacz [ten](media-services-hyperlapse-content.md) temat. W tej sekcji przedstawiono niektóre szczegóły na temat opcji, które można określić dla tego pakietu administracyjnego.

![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze004.png)

### <a name="speed"></a>Szybkość
Określ szybkość, z którą ma zostać przyspieszenia wejściowy plik wideo. Dane wyjściowe są stabilnych i czas, jaki upłynął dobór wejściowego pliku wideo.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, które pozwala zidentyfikować zadanie. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwia identyfikowanie zawartości danych wyjściowych. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Azure Media krój detektora** procesor multimediów (MP) umożliwia count, śledzić przeniesień i nawet określić udział odbiorców i reakcji za pośrednictwem twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy na obrazie**
  
    Wykrywanie twarzy na obrazie znajduje i śledzi człowieka kroje w pliku wideo. Wiele powierzchni mogą być wykrywane i następnie śledzenia przechodzą wokół, czas i lokalizację metadanymi zwrócił w pliku JSON. Podczas śledzenia podejmie ma zostać przypisany do tej samej kroju spójny identyfikator, gdy osoba jest przenoszenia na ekranie, nawet jeśli są zablokowane lub pozostaw krótko ramki.
  
  > [!NOTE]
  > Tej usługi nie przeprowadza rozpoznawanie twarzy. Osoba, która pozostawia ramki lub staje się blokować dla zbyt długo będzie mógł skorzystać z nowym Identyfikatorem gdy zwracają.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesor multimediów wykrywania twarzy na obrazie, które zwraca analizy na wiele atrybutów emocjonalne kroje wykryte, w tym szczęście, sadness, obawy i gniew. 

![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Tryb wykrywania
Jeden z następujących trybów mogą posłużyć procesora:

* wykrywanie twarzy na obrazie
* na powierzchni emocji wykrywania
* wykrywanie emocji agregacji

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, które pozwala zidentyfikować zadanie. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwia identyfikowanie zawartości danych wyjściowych. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Czujnik ruchu Azure Media** procesor multimediów (MP) umożliwia wydajne zidentyfikować sekcje zawierają informacje przydatne w wideo w innym przypadku długich i procesu. Wykrywanie ruchu można w statycznej kamery sekcje wideo, gdzie występuje ruchu. Generuje plik JSON zawierający metadane z sygnatury czasowe i ograniczający regionu, w którym wystąpiło zdarzenie.

Docelowe do źródła wideo zabezpieczeń, ta technologia jest w stanie kategoryzację ruchu na odpowiednie zdarzenia i fałszywych alarmów, takie jak cieni i zmian oświetlenia. Umożliwia generowanie alertów zabezpieczeń z aparatu fotograficznego źródła danych bez otrzymywania wiadomości-śmieci nieskończone zdarzenia nie ma znaczenia, będąc wyodrębnić chwil płynących z bardzo długi nadzoru wideo.

![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Tego procesora mogą pomóc tworzyć podsumowania długich filmów wideo, wybierając automatycznie interesujące fragmenty kodu ze źródła wideo. Jest to przydatne, gdy chcesz zapewnić szybki przegląd czego można oczekiwać w długich wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [miniatur wideo multimediów Azure używana do tworzenie podsumowań wideo](media-services-video-summarization.md)

![Analizowanie plików wideo](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, które pozwala zidentyfikować zadanie. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwia identyfikowanie zawartości danych wyjściowych. 

## <a name="azure-media-content-moderator"></a>Azure Media moderatora zawartości
Tego procesora ułatwia wykrywanie potencjalnych zawartość dla dorosłych i luksusowych w wideo. Procesor automatycznie wykrywa zrzuty i kluczowych wideo. Wyników klatek kluczowych możliwości zawartości dla dorosłych lub luksusowych i sugeruje przeglądami na podstawie progów domyślne. Aby uzyskać szczegółowe informacje i przykłady, zobacz [użycia usługi Azure Media zawartości moderatora na średni wideo](media-services-content-moderation.md)

![Umiarkowany wideo](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Wersja 
Użyj "2.0".

### <a name="mode"></a>Tryb
Ignoruj wersja 2.0 `Mode` ustawienie.

## <a name="next-steps"></a>Kolejne kroki
Ścieżki szkoleniowe dotyczące usługi Media widoku.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
