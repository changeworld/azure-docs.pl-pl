---
title: Analizowanie multimediów za pomocą witryny Azure portal | Dokumentacja firmy Microsoft
description: W tym temacie omówiono sposób przetwarzania multimediów procesory multimediów usługi Media Analytics (MP) przy użyciu witryny Azure portal.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d3917f65d8be08d6355013393f6c6675ea6c7fc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61131819"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analiza multimediów za pomocą witryny Azure Portal 
> [!NOTE]
> Do ukończenia tego samouczka jest potrzebne konto platformy Azure. Aby uzyskać szczegółowe informacje, zobacz temat [Bezpłatna wersja próbna systemu Azure](https://azure.microsoft.com/pricing/free-trial/). 
> 
> 

## <a name="overview"></a>Omówienie
Analiza usługi multimediów Azure to kolekcja składników mowy i obrazu (w skali przedsiębiorstwa, zgodność, bezpieczeństwo i globalny zasięg), które ułatwiają organizacjom i przedsiębiorstwom uzyskiwanie przydatnych wyników analiz na podstawie posiadanych plików wideo. Aby uzyskać bardziej szczegółowe omówienie usługi Azure Media Services Analytics zobacz [to](media-services-analytics-overview.md) tematu. 

W tym temacie omówiono sposób przetwarzania multimediów procesory multimediów usługi Media Analytics (MP) przy użyciu witryny Azure portal. Pakiety administracyjne analiza multimediów tworzą pliki MP4 lub pliki w formacie JSON. Plik MP4 utworzony przez procesor multimediów można pobrać progresywnie. Plik JSON utworzony przez procesor multimediów można pobrać plik z magazynu obiektów blob platformy Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Wybierz zasób, który chcesz analizować
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz opcję **Elementy zawartości**.  
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Wybierz zasób, który chcesz analizować, a następnie naciśnij klawisz **analizy** przycisku.
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. W **przetwarzanie elementu zawartości multimediów za pomocą usługi Media Analytics** oknie Wybierz procesor. 
   
    W pozostałej części artykułu wyjaśnia, dlaczego i jak używać każdego procesora. 
5. Naciśnij klawisz **Utwórz** do ekranu startowego, a zadania.

## <a name="azure-media-indexer"></a>Azure Media Indexer
**Usługi Azure Media Indexer** procesor multimediów umożliwia Dodaj plików multimedialnych i zawartości multimedialnej możliwość wyszukiwania, a także wygenerować zamknięte śledzi podpisów. W tej części przedstawiono informacje na temat opcji, które określisz dla tego pakietu administracyjnego.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Język
Język naturalny rozpoznawany w pliku multimedialnego. Na przykład w językach angielskim i hiszpańskim. 

### <a name="captions"></a>podpisy
Możesz wybrać format etykiety, które zostaną wygenerowane z zawartością. Zadania można wygenerować plików z napisami w następujących formatach:  

* **SAMI**
* **TTML**
* **WebVTT**

Zamknięte podpisu (DW) plików w tych formatach można udostępnić pliki audio i wideo dla osób z wadami słuchu niepełnosprawności.

### <a name="aib-file"></a>Plik AIB
Wybierz tę opcję, jeśli chcesz wygenerować plik obiektu Blob indeksu Audio do użycia z niestandardowych IFilter serwera SQL. Aby uzyskać więcej informacji, zobacz [to](https://azure.microsoft.com/blog/using-aib-files-with-azure-media-indexer-and-sql-server/) blogu.

### <a name="keywords"></a>słowa kluczowe
Wybierz tę opcję, jeśli chcesz wygenerować plik XML słów kluczowych. Ten plik zawiera słowa kluczowe wyodrębnione z zawartości mówionej o częstotliwości i przesunięciu informacji.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa umożliwiająca zidentyfikowanie zadania. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwiająca zidentyfikowanie zawartości wyjściowej. 

### <a name="speed"></a>Szybkość
Określ szybkości, z którego ma zostać przyspieszenia wejściowego klipu wideo. Dane wyjściowe są stabilizowanych i czas, jaki upłynął dobór wejściowego pliku wideo.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa umożliwiająca zidentyfikowanie zadania. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwiająca zidentyfikowanie zawartości wyjściowej. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
**Wykrywanie twarzy multimediów Azure** procesor multimediów (MP) pozwala na count, śledzenie przepływu i nawet miernika uczestnictwa odbiorców i reagowanie na nie przy użyciu twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy umożliwia znalezienie i śledzenie ludzkich twarzy w filmie wideo. Wiele powierzchni może zostać wykryte i następnie być śledzone przechodzące wokół, za pomocą metadanych czasu i bieżącej lokalizacji, zwracane w pliku JSON. Podczas śledzenia podejmie oferowanie spójny identyfikator tego samego twarzy podczas, gdy osoba jest poruszanie się w na ekranie, nawet jeśli są zablokowane lub krótko pozostaw ramki.
  
  > [!NOTE]
  > Tej usługi nie wykonuje rozpoznawanie twarzy. Osoba, która pozostawia ramki lub staje się blokować dla zbyt długo otrzyma nowy identyfikator przypadku zwracają.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesor multimediów wykrywanie twarzy, które zwraca analizy na wiele atrybutów emocjonalnej twarzy wykryć, w tym szczęście, smutek, strach i gniew. 

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Tryb wykrywania
Jedną z następujących trybów może służyć przez procesor:

* Wykrywanie twarzy
* na wykrywanie emocji na twarzy
* Agreguj wykrywanie emocji

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa umożliwiająca zidentyfikowanie zadania. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwiająca zidentyfikowanie zawartości wyjściowej. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
**Wykrywanie ruchu multimediów Azure** procesor multimediów (MP) pozwala na efektywne identyfikują sekcje zainteresowania w wideo w przeciwnym razie długich i procesu. Wykrywanie ruchu można na statyczne aparatu nagrań z monitorowania identyfikują sekcje wideo, w której występuje ruchu. Generuje plik JSON zawierający metadane za pomocą sygnatur czasowych i otaczający region, w której wystąpiło zdarzenie.

Skierowany strumieniowych źródeł wideo zabezpieczeń, ta technologia jest w stanie kategoryzowanie ruchu do odpowiednie zdarzenia i fałszywych alarmów, takie jak cieni i zmian oświetlenia. Umożliwia generowanie alertów zabezpieczeń z kanałami informacyjnymi aparatu bez otrzymywania wiadomości-śmieci nieskończone zdarzeń nie ma znaczenia, będąc wyodrębnić chwil zainteresowania z wideo nadzoru bardzo długi.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
To procesor może pomóc w tworzenie podsumowań długich wideo, automatycznie wybierając interesujący fragmenty kodu z źródłowy plik wideo. Jest to przydatne, gdy chcesz zapewnić szybki przegląd czego można oczekiwać w długich wideo. Aby uzyskać szczegółowe informacje i przykłady, zobacz [miniatur wideo multimediów Azure Użyj do tworzenie podsumowań wideo](media-services-video-summarization.md)

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa umożliwiająca zidentyfikowanie zadania. [To](media-services-portal-check-job-progress.md) artykule opisano, jak można monitorować postęp zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa umożliwiająca zidentyfikowanie zawartości wyjściowej. 

## <a name="azure-media-content-moderator"></a>Azure Media Content Moderator
To procesor pomaga wykrywać potencjalne dorosłych i zawartości erotycznej w klipach wideo. Procesor automatycznie wykrywa zrzuty i klatki kluczowe w trakcie filmu wideo. Ocenia klatki kluczowe możliwości zawartości dla dorosłych lub erotycznej i sugeruje przeglądy oparte na domyślne progi. Aby uzyskać szczegółowe informacje i przykłady, zobacz [użycia usługi Azure Media pakietu Content Moderator do umiarkowanego filmów wideo](media-services-content-moderation.md)

![Umiarkowany filmów wideo](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Version 
Use "2.0".

### <a name="mode"></a>Tryb
Ignoruj wersja 2.0 `Mode` ustawienie.

## <a name="next-steps"></a>Kolejne kroki
Wyświetl usługi Media Services ścieżki szkoleniowe.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
