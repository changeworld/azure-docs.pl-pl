---
title: Skonfiguruj koder NewTek TriCaster, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów | Microsoft Docs
description: W tym temacie pokazano, jak skonfigurować koder TriCaster na żywo, aby wysyłał strumień o pojedynczej szybkości transmisji bitów do kanałów AMS, dla których włączono obsługę kodowania na żywo.
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
ms.openlocfilehash: 0e793a5aa7d619b0bb7a1d3efcdf665ea400c555
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "69016737"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera TriCaster NewTek, aby wysłać strumień na żywo o pojedynczej szybkości transmisji bitów  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Element aktywny](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym artykule pokazano, jak skonfigurować koder [NewTek TriCaster](https://newtek.com/products/tricaster-40.html) Live Encoder, aby wysyłał strumień o pojedynczej szybkości transmisji bitów do kanałów AMS, dla których włączono obsługę kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> W przypadku korzystania z TriCaster w celu wysyłania danych w kanale informacyjnym do kanałów usługi AMS, w których włączono obsługę kodowania na żywo, w zdarzeniu na żywo mogą występować problemy z odtwarzaniem wideo/audio, jeśli używasz niektórych funkcji TriCaster, takich jak szybkie wycinanie między źródłami danych lub przełączanie do/z kart. Zespół AMS pracuje nad rozwiązaniem tych problemów, dopóki nie jest to zalecane.
>
>

## <a name="prerequisites"></a>Wymagania wstępne

* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, brak punktu końcowego przesyłania strumieniowego uruchomiona. Aby uzyskać więcej informacji, zobacz [Zarządzanie punkty końcowe przesyłania strumieniowego w konto usługi Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) narzędzia.
* Uruchom narzędzie i nawiąż połączenie z kontem usługi AMS.

## <a name="tips"></a>Porady

* Jeśli to możliwe, przy użyciu połączenia internetowego hardwired.
* Jest regułą podczas określania wymagań dotyczących przepustowości dwukrotnie różnych przesyłania strumieniowego. Chociaż nie jest to wymagane, pomaga złagodzić skutki przeciążenie sieci.
* Podczas korzystania z koderów opartych na oprogramowaniu, zamknąć wszystkie zbędne programy.

## <a name="create-a-channel"></a>Tworzenie kanału

1. W użyciu narzędzia AMSE, przejdź do **Live** , a następnie kliknij prawym przyciskiem myszy na obszarze kanału. Wybierz **tworzenia kanału...** w menu.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzenia kanału**.

   ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Naliczanie opłat rozpoczyna się, jak kanał przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_tricaster_rtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfigurowanie kodera NewTek TriCaster

W tym samouczku są używane następujące ustawienia danych wyjściowych. W pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

**Film wideo**:

* Wymaga H. 264
* Profilu Wysoka (poziom 4,0)
* Multimedia 5000 KB/s
* Kluczowych 2 sekundy (60 s)
* Szybkość klatek: 30

**Dźwięk**:

* Wymaga AAC (LC)
* Multimedia 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji

1. Utwórz nowy projekt **NewTek TriCaster** w zależności od używanego źródła danych wejściowych wideo.
2. W tym projekcie Znajdź przycisk **strumień** , a następnie kliknij ikonę koła zębatego obok niej, aby uzyskać dostęp do menu Konfiguracja strumienia.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otwarciu menu kliknij pozycję **Nowy** pod nagłówkiem połączenie. Po wyświetleniu monitu o typ połączenia wybierz pozycję **Adobe Flash**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kliknij przycisk **OK**.
5. Profil KODER FMLE można teraz zaimportować, klikając strzałkę listy rozwijanej w obszarze **profil przesyłania strumieniowego** i przechodząc do **przeglądania**.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Przejdź do lokalizacji, w której zapisano skonfigurowany profil KODER FMLE.
7. Zaznacz go, a następnie naciśnij przycisk **OK**.

    Po przekazaniu profilu przejdź do następnego kroku.
8. Pobierz adres URL wejścia kanału, aby przypisać go do **punktu końcowego TriCaster RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu z **od** do **systemem**, możesz uzyskać wejściowego adresu URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź w dół, aby umieścić wskaźnik myszy nad **Kopiuj wejściowy adres URL do schowka** , a następnie wybierz **podstawowy adres URL wejścia**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Wklej te informacje w polu **Lokalizacja** w obszarze **Flash Server** w projekcie TriCaster. W polu **Identyfikator strumienia** Przypisz także nazwę strumienia.

    Jeśli informacje o strumieniu zostały dodane do profilu KODER FMLE, można je również zaimportować do tej sekcji, klikając pozycję **Importuj ustawienia**, przechodząc do zapisanego profilu koder FMLE i klikając **przycisk OK**. Odpowiednie pola serwera Flash powinny wypełnić informacjami z KODER FMLE.

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po zakończeniu kliknij przycisk **OK** w dolnej części ekranu. Gdy dane wejściowe wideo i audio do TriCaster są gotowe, Rozpocznij przesyłanie strumieniowe do AMS, klikając przycisk **strumień** .

     ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Przed kliknięciem przycisku **Stream**, możesz **musi** upewnij się, że kanał jest gotowy.
> Ponadto upewnij się, że nie pozostawić kanał w stanie gotowości bez udziału danych wejściowych, źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania korzystania z wersji zapoznawczej** i wybierz **za pomocą usługi Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Jeśli zostanie wyświetlony komunikat o błędzie, należy zresetować i dopasować ustawienia kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykuł, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program

1. Po potwierdzeniu odtwarzania kanału, Utwórz program. W obszarze **Live** karty przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy na obszarze program i wybierz **utworzyć nowy Program**.  

    ![TriCaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
2. Nazwa programu i w razie potrzeby dostosuj **długość okna archiwizacji** (które domyślnie używa do czterech godzin). Można również określić lokalizację przechowywania lub pozostaw domyślny.  
3. Sprawdź **teraz uruchomić Program** pole.
4. Kliknij przycisk **utworzyć Program**.  

    >[!NOTE]
    >Tworzenie programów zajmuje mniej czasu niż Tworzenie kanałów.
        
5. Po uruchomieniu programu, upewnij się, odtwarzania, klikając prawym przyciskiem myszy program i przechodząc do **odtwarzania programy** , a następnie wybierając **za pomocą usługi Azure Media Player**.  
6. Po potwierdzeniu, ponownie kliknij prawym przyciskiem myszy program i wybierz **skopiuj dane wyjściowe adres URL do Schowka** (lub pobranie tych informacji z **Program informacje i ustawienia** opcję z menu).

Strumień jest teraz gotowy do osadzonych w odtwarzaczu lub dystrybuowane do odbiorców do przeglądania na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykuł, aby uzyskać wskazówki.

## <a name="next-step"></a>Następny krok

Przejrzyj ścieżki szkoleniowe dotyczące usługi Media Services.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
