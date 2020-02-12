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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 1d9d63aa6b3da1b8d8389722bd5af0eeed585d03
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134974"
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu koder Wirecast 
> [!div class="op_single_selector"]
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [TriCaster](media-services-configure-tricaster-live-encoder.md)
>
>

W tym artykule opisano sposób konfigurowania kodera Wirecast na żywo usługi [Telestream](https://www.telestream.net/wirecast/overview.htm) w celu wysyłania strumienia pojedynczego szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli jesteś w systemie Mac lub Linux, użyj Azure Portal, aby utworzyć [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Kodery muszą obsługiwać protokół TLS 1,2 w przypadku używania protokołów RTMP. Użyj Wirecast w wersji 13.0.2 lub nowszej z powodu wymagania TLS 1,2.

## <a name="prerequisites"></a>Wymagania wstępne
* [Utwórz konto Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, brak punktu końcowego przesyłania strumieniowego uruchomiona. Aby uzyskać więcej informacji, zobacz [Zarządzanie punktami końcowymi przesyłania strumieniowego na koncie Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję narzędzia [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) .
* Uruchom narzędzie i nawiąż połączenie z kontem usługi AMS.

## <a name="tips"></a>Porady
* Jeśli to możliwe, przy użyciu połączenia internetowego hardwired.
* Jest regułą podczas określania wymagań dotyczących przepustowości dwukrotnie różnych przesyłania strumieniowego. Chociaż nie jest to wymagane, pomaga złagodzić skutki przeciążenie sieci.
* Podczas korzystania z koderów opartych na oprogramowaniu, zamknąć wszystkie zbędne programy.

## <a name="create-a-channel"></a>Tworzenie kanału
1. W narzędziu AMSE przejdź do karty **Live** , a następnie kliknij prawym przyciskiem myszy w obszarze kanału. Wybierz pozycję **Utwórz kanał...** w menu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz pozycję **standardowa** dla opcji Live Encoding, z protokołem wejściowym ustawionym na wartość **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że wybrano wartość **Uruchom nowy kanał teraz** .

3. Kliknij pozycję **Utwórz kanał**.

   ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału można [skonfigurować koder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Naliczanie opłat rozpoczyna się, jak kanał przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [Stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfigure_wirecast_rtmp-configure-the-telestream-wirecast-encoder"></a><a id="configure_wirecast_rtmp" />skonfigurować koder Wirecast dla Telestream
W tym samouczku są używane następujące ustawienia danych wyjściowych. W pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

**Film wideo**:

* Koder-dekoder: H.264
* Profil: Wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 KB/s
* Ramka kluczowa: 2 sekundy (60 sekund)
* Klatki, szybkości: 30

**Dźwięk**:

* Koder-dekoder: AAC (LC)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Otwórz aplikację Telestream Wirecast z tego komputera, użycia i konfigurowania protokołu RTMP przesyłania strumieniowego.
2. Skonfiguruj dane wyjściowe, przechodząc do karty **dane wyjściowe** i wybierając pozycję **Ustawienia wyjściowe...** .

    Upewnij się, że **miejsce docelowe danych wyjściowych** jest ustawione na **serwer RTMP**.
3. Kliknij przycisk **OK**.
4. Na stronie Ustawienia Ustaw pole **docelowe** , które ma być **Azure Media Services**.

    Profil kodowania jest wstępnie wybrany dla **platformy Azure H. 264 720 16:9 (1280x720)** . Aby dostosować te ustawienia, wybierz ikonę koła zębatego z prawej strony listy rozwijanej, a następnie wybierz pozycję **nowe ustawienie wstępne**.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Skonfiguruj ustawienia wstępne kodera.

    Nazwę ustawienia i sprawdź, czy następujące zalecane ustawienia:

    **Plików**

   * Koder: MainConcept H.264
   * Liczba klatek na sekundę: 30
   * Średnia szybkość transmisji bitów: 5000 kbitów na sekundę (mogą być dostosowywane według ograniczenia sieci)
   * Profil: Main
   * Klatka kluczowa co: 60 klatek

     **Dźwięku**

   * Docelowa szybkość transmisji bitów: 192 kbitów na sekundę
   * Częstotliwość próbkowania: 44 100 kHz

     ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Naciśnij pozycję **Zapisz**.

    Pola Encoding ma teraz nowo utworzony profil dostępne do wyboru.

    Upewnij się, że jest zaznaczona opcja nowy profil.
7. Pobierz adres URL wejścia kanału, aby przypisać go do **punktu końcowego Wirecast RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu od **rozpoczęcia** na **uruchomiony**możesz uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź w dół, aby umieścić wskaźnik myszy nad **Kopiuj wejściowy adres URL do schowka** , a następnie wybierz **podstawowy adres URL wejścia**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. W oknie **Ustawienia wyjściowe** Wirecast wklej te informacje w polu **adres** sekcji danych wyjściowych i przypisz nazwę strumienia.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kliknij przycisk **OK**.
2. Na głównym ekranie **Wirecast** upewnij się, że źródła danych wejściowych dla wideo i audio są gotowe, a następnie kliknij pozycję **strumień** w lewym górnym rogu.

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Zanim klikniesz pozycję **Stream**, **musisz** upewnić się, że kanał jest gotowy.
> Ponadto upewnij się, że nie pozostawić kanał w stanie gotowości bez udziału danych wejściowych, źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. W menu Umieść wskaźnik myszy nad **odtwarzaniem wersji zapoznawczej** i wybierz pozycję **z Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu kanału musi być resetowany i dostosować ustawień kodera. Aby uzyskać wskazówki, zobacz artykuł dotyczący [rozwiązywania problemów](media-services-troubleshooting-live-streaming.md) .  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu odtwarzania kanału, Utwórz program. Na karcie na **żywo** w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz pozycję **Utwórz nowy program**.  

    ![Wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nazwij program i, w razie potrzeby, Dostosuj **Długość okna archiwum** (domyślnie cztery godziny). Można również określić lokalizację przechowywania lub pozostaw domyślny.  
3. Zaznacz pole **Uruchom program teraz** .
4. Kliknij pozycję **Utwórz program**.  

   >[!NOTE]
   >Tworzenie programów zajmuje mniej czasu niż Tworzenie kanałów.
       
5. Po uruchomieniu programu Potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do **odtwarzania programów** , a następnie wybierając opcję **z Azure Media Player**.  
6. Po potwierdzeniu ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie **Kopiuj wyjściowy adres URL do schowka** (lub Pobierz te informacje z opcji **Informacje o programie i ustawienia** z menu).

Strumień jest teraz gotowy do osadzonych w odtwarzaczu lub dystrybuowane do odbiorców do przeglądania na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Aby uzyskać wskazówki, zobacz artykuł dotyczący [rozwiązywania problemów](media-services-troubleshooting-live-streaming.md) .

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekaż opinię
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
