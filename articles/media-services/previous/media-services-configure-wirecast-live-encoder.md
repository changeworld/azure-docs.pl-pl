---
title: Konfiguruj koder Telestream Wirecast do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: 'W tym temacie pokazano, jak skonfigurować koder na żywo Wirecast wysyłać strumień o pojedynczej szybkości transmisji bitów z kanałami usługi AMS, obsługującymi kodowanie na żywo. '
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: d0da69601bfc6fd09c10b30d45195722781d87d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61232142"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu koder Wirecast 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [Telestream Wirecast](https://www.telestream.net/wirecast/overview.htm) koder na żywo, aby wysłać strumień o pojedynczej szybkości transmisji bitów do usługi AMS kanały, które są włączone dla kodowania na żywo.  Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

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

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzenia kanału**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Naliczanie opłat rozpoczyna się, jak kanał przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigurewirecastrtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />Konfiguruj koder Telestream Wirecast
W tym samouczku są używane następujące ustawienia danych wyjściowych. W pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

**Film wideo**:

* Codec: H.264
* Profil: Wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 KB/s
* Klatki kluczowe: 2 sekundy (60 sekund)
* Szybkość klatek: 30

**Dźwięk**:

* Codec: AAC (LC)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Otwórz aplikację Telestream Wirecast z tego komputera, użycia i konfigurowania protokołu RTMP przesyłania strumieniowego.
2. Konfigurowanie danych wyjściowych, przechodząc do **dane wyjściowe** kartę i wybierając polecenie **ustawienia danych wyjściowych...** .

    Upewnij się, że **miejsce docelowe danych wyjściowych** ustawiono **serwera RTMP**.
3. Kliknij przycisk **OK**.
4. Na stronie Ustawienia zestawu **docelowy** pole było **usługi Azure Media Services**.

    Profil kodowanie jest wstępnie wybrane do **H.264 Azure 720 p 16:9 (1280 x 720)**. Aby dostosować te ustawienia, wybierz ikonę koła zębatego, aby po prawej stronie listy rozwijanej, a następnie wybierz **nowe ustawienie wstępne**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Skonfiguruj ustawienia wstępne kodera.

    Nazwę ustawienia i sprawdź, czy następujące zalecane ustawienia:

    **Film wideo**

   * Koder: MainConcept H.264
   * Liczba klatek na sekundę: 30
   * Średnia szybkość transmisji bitów: 5000 kbitów na sekundę (mogą być dostosowywane według ograniczenia sieci)
   * Profil: Główny
   * Klatka kluczowa co: 60 klatek

     **Audio**

   * Docelowa szybkość transmisji bitów: 192 kbitów na sekundę
   * Częstotliwość próbkowania: 44 100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Naciśnij pozycję **Zapisz**.

    Pola Encoding ma teraz nowo utworzony profil dostępne do wyboru.

    Upewnij się, że jest zaznaczona opcja nowy profil.
7. Pobierz kanał dane wejściowe podane przez adres URL, aby przypisać ją do Wirecast **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu z **od** do **systemem**, możesz uzyskać wejściowego adresu URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do umieść kursor nad **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowy adres URL danych wejściowych**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. W Wirecast **ustawienia wyjściowej** okna, wklej tę informację w **adres** pole sekcji danych wyjściowych, a następnie przypisz nazwę strumienia.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kliknij przycisk **OK**.
2. W głównym **Wirecast** Sprawdź źródeł danych wejściowych dla audio i wideo będzie gotowe, a następnie naciśnij klawisz **Stream** w lewym górnym rogu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Przed kliknięciem przycisku **Stream**, możesz **musi** upewnij się, że kanał jest gotowy.
> Ponadto upewnij się, że nie pozostawić kanał w stanie gotowości bez udziału danych wejściowych, źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania korzystania z wersji zapoznawczej** i wybierz **za pomocą usługi Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu kanału musi być resetowany i dostosować ustawień kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykuł, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu odtwarzania kanału, Utwórz program. W obszarze **Live** karty przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy na obszarze program i wybierz **utworzyć nowy Program**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
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

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
