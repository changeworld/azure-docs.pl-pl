---
title: Konfigurowanie kodera NewTek TriCaster do wysyłania pojedynczego strumienia transmisji bitów na żywo | Dokumenty firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować koder Tricaster na żywo do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 8973181a-3059-471a-a6bb-ccda7d3ff297
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 7909fbb958a66d00616d4ed1b844d02bb47d997e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152503"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera NewTek TriCaster, aby wysłać pojedynczy strumień transmisji bitów na żywo  
> [!div class="op_single_selector"]
> * [Tricaster ( Tricaster )](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Narzędzie Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

W tym artykule pokazano, jak skonfigurować koder na żywo [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) do wysyłania pojedynczego strumienia szybkości transmisji bitów do kanałów usługi AMS, które są włączone do kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku pokazano, jak zarządzać usługą Azure Media Services (AMS) za pomocą narzędzia Usługi Azure Media Services Explorer (AMSE). To narzędzie działa tylko na komputerze z systemem Windows. Jeśli korzystasz z komputera Mac lub Linux, użyj witryny Azure portal do tworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programów](media-services-portal-creating-live-encoder-enabled-channel.md).

W przypadku używania tricaster do wysyłania kanału informacyjnego do kanałów AMS, które są włączone do kodowania na żywo, w wydarzeniu na żywo mogą występować usterki wideo/audio, jeśli korzystasz z niektórych funkcji Tricaster, takich jak szybkie cięcie między kanałami lub przełączanie się na/z łupków. Zespół USŁUGI AMS pracuje nad rozwiązaniem tych problemów, do tego czasu nie zaleca się korzystania z tych funkcji.

> [!NOTE]
>  Rozważ przejście do TLS 1.2, wstępnie zmachtniej wersji TLS.

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, że jest uruchomiony punkt końcowy przesyłania strumieniowego. Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego na koncie usługi Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję narzędzia [AMSE.](https://github.com/Azure/Azure-Media-Services-Explorer)
* Uruchom narzędzie i połącz się ze swoim kontem AMS.

## <a name="tips"></a>Porady

* Gdy to możliwe, używaj przewodowego połączenia internetowego.
* Dobrą praktyką jest podwajanie szybkości transmisji bitów podczas określania wymagań dotyczących przepustowości. Chociaż nie jest to wymóg obowiązkowy, pomaga złagodzić wpływ przeciążenia sieci.
* W przypadku korzystania z koderów programowych zamknij wszelkie niepotrzebne programy.

## <a name="create-a-channel"></a>Tworzenie kanału

1. W narzędziu AMSE przejdź do karty **Aktywne** i kliknij prawym przyciskiem myszy obszar kanału. Wybierz **pozycję Utwórz kanał...** z menu.

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Określ nazwę kanału, pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz opcję **Standardowy** dla opcji Kodowanie na żywo z protokołem wejściowym ustawionym na **RTMP**. Możesz pozostawić wszystkie inne ustawienia, tak jak jest.

    Upewnij się, że zostanie wybrana **opcja Rozpocznij nowy kanał.**

3. Kliknij **pozycję Utwórz kanał**.

   ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Uruchomienie kanału może potrwać nawet 20 minut.
>
>

Podczas uruchamiania kanału można [skonfigurować koder](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Rozliczenia rozpoczyna się, jak tylko kanał przechodzi w stan gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="configure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurowanie kodera NewTek TriCaster

W tym samouczku używane są następujące ustawienia wyjściowe. W dalszej części tej sekcji opisano kroki konfiguracji bardziej szczegółowo.

**Film:**

* Koder-dekoder: H.264
* Profil: wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 kb/s
* Klatka kluczowa: 2 sekundy (60 sekund)
* Liczba klatek na sekundę: 30

**Dźwięk**:

* Koder-dekoder: AAC (LC)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji

1. Utwórz nowy projekt **NewTek TriCaster** w zależności od używanego źródła wejścia wideo.
2. Po zakończeniu tego projektu znajdź przycisk **Strumień** i kliknij ikonę koła zębatego obok niego, aby uzyskać dostęp do menu konfiguracji strumienia.

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otwarciu menu kliknij pozycję **Nowy** pod nagłówkiem Połączenie. Po wyświetleniu monitu o typ połączenia wybierz pozycję **Adobe Flash**.

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kliknij przycisk **OK**.
5. Profil FMLE można teraz zaimportować, klikając strzałkę rozwijaną w obszarze **Profil przesyłania strumieniowego** i przechodząc do **pozycji Przeglądaj**.

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Przejdź do miejsca, w którym został zapisany skonfigurowany profil FMLE.
7. Wybierz go i naciśnij **przycisk OK**.

    Po przesłaniu profilu przejdź do następnego kroku.
8. Pobierz adres URL wejściowy kanału, aby przypisać go do **punktu końcowego**Tricaster RTMP .

    Przejdź z powrotem do narzędzia AMSE i sprawdź stan ukończenia kanału. Po zmianie stanu z **Początkowe** na **Uruchomione**można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź w dół, aby najechać kursorem na **adres URL kopiowania danych wejściowych do schowka,** a następnie wybierz **pozycję Podstawowy adres URL danych wejściowych**.  

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Wklej te informacje w polu **Lokalizacja** w obszarze **Serwer flash w** projekcie Tricaster. Przypisz również nazwę strumienia w polu **Identyfikator strumienia.**

    Jeśli informacje o strumieniu zostały dodane do profilu FMLE, można je również zaimportować do tej sekcji, klikając pozycję **Ustawienia importu,** przechodząc do zapisanego profilu FMLE i klikając **przycisk OK**. Odpowiednie pola serwera flash powinny być wypełniane informacjami z FMLE.

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po zakończeniu kliknij przycisk **OK** u dołu ekranu. Gdy wejścia wideo i audio do Tricaster są gotowe, rozpocznij przesyłanie strumieniowe do usługi AMS, klikając przycisk **Strumień.**

     ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Przed kliknięciem **przycisku Strumień** **należy upewnić** się, że kanał jest gotowy.
> Upewnij się również, że kanał nie jest gotowy bez źródła danych wejściowych przez okres dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Testowanie odtwarzania

Przejdź do narzędzia AMSE i kliknij prawym przyciskiem myszy testowany kanał. Z menu umieść wskaźnik myszy na **playbacku w wersji zapoznawczej** i wybierz **pozycję za pomocą programu Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Jeśli strumień pojawi się w odtwarzaczu, koder został poprawnie skonfigurowany do łączenia się z programem AMS.

Jeśli zostanie wyświetlony błąd, kanał będzie musiał zostać zresetowany i dostosować ustawienia kodera. Zapoznaj się z [artykułem dotyczącym rozwiązywania problemów,](media-services-troubleshooting-live-streaming.md) aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Tworzenie programu

1. Po potwierdzeniu odtwarzania kanału utwórz program. W obszarze karty **Aktywne** w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz polecenie **Utwórz nowy program**.  

    ![tricaster ( tricaster )](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Nazwij program i, w razie potrzeby, dostosuj **długość okna archiwum** (domyślnie do czterech godzin). Można również określić lokalizację magazynu lub pozostawić jako domyślną.  
3. Zaznacz pole **Start programu teraz.**
4. Kliknij **pozycję Utwórz program**.  

    >[!NOTE]
    >Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.
        
5. Po uruchomieniu programu potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do **odtwarzania programu,** a następnie wybierając za **pomocą programu Azure Media Player**.  
6. Po potwierdzeniu kliknij prawym przyciskiem myszy program i wybierz pozycję **Skopiuj wyjściowy adres URL do Schowka** (lub pobierz te informacje z opcji **Informacje i ustawienia programu** z menu).

Strumień jest teraz gotowy do umieszczenia w odtwarzaczu lub rozdania go odbiorcom w celu oglądania na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zapoznaj się z [artykułem dotyczącym rozwiązywania problemów,](media-services-troubleshooting-live-streaming.md) aby uzyskać wskazówki.

## <a name="next-step"></a>Następny krok

Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
