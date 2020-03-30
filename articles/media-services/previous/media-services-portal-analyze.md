---
title: Analizowanie multimediów za pomocą portalu Azure | Dokumenty firmy Microsoft
description: W tym temacie omówiono sposób przetwarzania multimediów za pomocą procesorów multimediów usługi Media Analytics przy użyciu witryny Azure portal.
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
ms.openlocfilehash: 0e49e90209c7337081458b7c214d27b37d3b4da1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74462617"
---
# <a name="analyze-your-media-using-the-azure-portal"></a>Analizowanie multimediów za pomocą portalu Azure 

> [!IMPORTANT]
> Przejrzyj [plany emerytalne](media-services-analytics-overview.md#retirement-plans) niektórych procesorów multimediów.

## <a name="overview"></a>Omówienie
Usługa Azure Media Services Analytics to zbiór składników mowy i wizji (w skali przedsiębiorstwa, zgodności, zabezpieczeń i globalnym zasięgu), które ułatwiają organizacjom i przedsiębiorstwom uzyskiwanie użytecznych informacji z plików wideo. Aby uzyskać bardziej szczegółowe omówienie usługi Azure Media Services Analytics, zobacz [ten](media-services-analytics-overview.md) temat. 

W tym temacie omówiono sposób przetwarzania multimediów za pomocą procesorów multimediów usługi Media Analytics przy użyciu witryny Azure portal. Parlamentarzyści media analytics tworzą pliki MP4 lub JSON. Jeśli procesor multimediów wyprodukował plik MP4, plik jest stopniowo pobierany. Jeśli procesor multimediów wyprodukował plik JSON, można pobrać plik z magazynu obiektów blob platformy Azure. 

## <a name="choose-an-asset-that-you-want-to-analyze"></a>Wybieranie zasobu, który chcesz analizować
1. W witrynie [Azure Portal](https://portal.azure.com/) wybierz swoje konto usługi Azure Media Services.
2. W oknie **Ustawienia** wybierz opcję **Elementy zawartości**.  
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze001.png)
3. Wybierz zasób, który chcesz przeanalizować, i naciśnij przycisk **Analizuj.**
   
    ![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze002.png)
4. W oknie **Process media asset with Media Analytics** wybierz procesor. 
   
    W dalszej części artykułu wyjaśniono, dlaczego i jak korzystać z każdego procesora. 
5. Naciśnij **klawisz Utwórz,** aby rozpocząć zadanie.

## <a name="azure-media-indexer"></a>Azure Media Indexer
Procesor multimediów **indeksu mediów azure** umożliwia wyszukiwanie plików multimedialnych i zawartości, a także generowanie utworów z napisami. W tej sekcji przedstawiono kilka szczegółów dotyczących opcji określonych dla tego mp.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze003.png)

### <a name="language"></a>Język
Język naturalny, który ma być rozpoznawany w pliku multimedialnym. Na przykład angielski lub hiszpański. 

### <a name="captions"></a>Transkrypcje
Możesz wybrać format podpisu, który zostanie wygenerowany na podstawie zawartości. Zadanie indeksowania może generować pliki podpisów kodowych w następujących formatach:  
 
* **TTML (ttml)**
* **WebVTT (webvtt)**

Pliki z podpisami kodowanymi (CC) w tych formatach mogą być używane do udostępnienia plików audio i wideo osobom z niepełnosprawnością słuchu.

### <a name="keywords"></a>Słowa kluczowe
Wybierz tę opcję, jeśli chcesz wygenerować słowo kluczowe plik XML. Ten plik zawiera słowa kluczowe wyodrębnione z zawartości mowy, z częstotliwością i informacjami o przesunięciu.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która pozwala zidentyfikować zadanie. [W tym](media-services-portal-check-job-progress.md) artykule opisano sposób monitorowania postępu zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która umożliwia identyfikację zawartości wyjściowej. 

### <a name="speed"></a>Szybkość
Określ szybkość, z jaką ma być przyspieszony wejściowy film wideo. Wyjście jest stabilizowane i poklatkowe odwzorowanie wideo wejściowego.

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która pozwala zidentyfikować zadanie. [W tym](media-services-portal-check-job-progress.md) artykule opisano sposób monitorowania postępu zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która umożliwia identyfikację zawartości wyjściowej. 

## <a name="azure-media-face-detector"></a>Azure Media Face Detector
Procesor **multimediów Azure Media Face Detector** (MP) umożliwia liczenie, śledzenie ruchów, a nawet mierzenie udziału odbiorców i reakcji za pomocą mimiki twarzy. Ta usługa zawiera dwie funkcje: 

* **Wykrywanie twarzy**
  
    Wykrywanie twarzy znajduje i śledzi ludzkie twarze w filmie. Wiele twarzy można wykryć, a następnie być śledzone podczas ich poruszania się, z metadanych czasu i lokalizacji zwracanych w pliku JSON. Podczas śledzenia, będzie próbował dać spójny identyfikator do tej samej twarzy, gdy osoba porusza się na ekranie, nawet jeśli są one zablokowane lub na krótko opuścić ramkę.
  
  > [!NOTE]
  > Ta usługi nie wykonuje rozpoznawania twarzy. Osoba, która opuści ramkę lub zostanie zasłana zbyt długo, otrzyma nowy identyfikator po powrocie.
  > 
  > 
* **Wykrywanie emocji**
  
    Wykrywanie emocji jest opcjonalnym składnikiem procesora mediów wykrywania twarzy, który zwraca analizę wielu atrybutów emocjonalnych z wykrytych twarzy, w tym szczęścia, smutku, strachu, gniewu i innych. 

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze005.png)

### <a name="detection-mode"></a>Tryb wykrywania
Procesor może używać jednego z następujących trybów:

* wykrywanie twarzy
* wykrywanie emocji twarzy
* agregowanie wykrywania emocji

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która pozwala zidentyfikować zadanie. [W tym](media-services-portal-check-job-progress.md) artykule opisano sposób monitorowania postępu zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która umożliwia identyfikację zawartości wyjściowej. 

## <a name="azure-media-motion-detector"></a>Azure Media Motion Detector
Procesor **multimediów Azure Media Motion Detector** (MP) umożliwia efektywne identyfikowanie sekcji zainteresowań w ramach długiego i niezdarnego wideo. Wykrywanie ruchu może być używane na statycznych nagraniach z kamery do identyfikacji sekcji wideo, w których występuje ruch. Generuje plik JSON zawierający metadane z sygnaturami czasowymi i region ograniczający, w którym wystąpiło zdarzenie.

Technologia ta, ukierunkowana na kanały wideo związane z bezpieczeństwem, jest w stanie kategoryzować ruch w odpowiednich zdarzeniach i fałszywych alarmach, takich jak cienie i zmiany oświetlenia. Pozwala to na generowanie alertów bezpieczeństwa z kanałów z kamer bez spamowania niekończącymi się nieistotnymi wydarzeniami, jednocześnie będąc w stanie wyodrębnić interesujące momenty z bardzo długich filmów z monitoringu.

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze006.png)

## <a name="azure-media-video-thumbnails"></a>Azure Media Video Thumbnails
Ten procesor może pomóc w tworzeniu podsumowań długich filmów, automatycznie wybierając interesujące fragmenty z źródłowego wideo. Jest to przydatne, gdy chcesz zapewnić szybki przegląd tego, czego można się spodziewać w długim filmie. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Tworzenie podsumowania wideo za pomocą miniatur wideo w usłudze Azure Media Video](media-services-video-summarization.md)

![Analizowanie wideo](./media/media-services-portal-analyze/media-services-portal-analyze008.png)

### <a name="job-name"></a>Nazwa zadania
Przyjazna nazwa, która pozwala zidentyfikować zadanie. [W tym](media-services-portal-check-job-progress.md) artykule opisano sposób monitorowania postępu zadania. 

### <a name="output-file"></a>Plik wyjściowy
Przyjazna nazwa, która umożliwia identyfikację zawartości wyjściowej. 

## <a name="azure-media-content-moderator"></a>Moderator zawartości multimediów platformy Azure
Ten procesor pomaga wykrywać potencjalne treści dla dorosłych i rasistowskie w filmach. Procesor automatycznie wykrywa zdjęcia i klatki kluczowe w filmie. Ocenia klatki kluczowe dla możliwych treści dla dorosłych lub rasistowskich i sugeruje recenzje na podstawie domyślnych progów. Aby uzyskać szczegółowe informacje i przykłady, zobacz [Moderowanie klipów wideo za pomocą moderatora zawartości multimediów platformy Azure](media-services-content-moderation.md)

![Umiarkowane filmy](./media/media-services-portal-analyze/media-services-portal-analyze-content-moderator.PNG)

### <a name="version"></a>Wersja 
Użyj "2.0".

### <a name="mode"></a>Tryb
Wersja 2.0 ignoruje to `Mode` ustawienie.

## <a name="next-steps"></a>Następne kroki
Wyświetlanie ścieżek szkoleniowych usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
