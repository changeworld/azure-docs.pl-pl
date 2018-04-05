---
title: Skonfiguruj koder Telestream Wirecast wysłać strumień na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: 'W tym temacie przedstawiono sposób konfigurowania kodera na żywo Wirecast do wysłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji. '
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 0e4fb0b7c915969da1760eaccc77aa399030752e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="use-the-wirecast-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj koder Wirecast, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów
> [!div class="op_single_selector"]
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [Elemental na żywo](media-services-configure-elemental-live-encoder.md)
> * [tricaster](media-services-configure-tricaster-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [Telestream Wirecast](http://www.telestream.net/wirecast/overview.htm) kodera na żywo do wysyłania strumienia pojedynczej szybkości transmisji bitów AMS kanałów, które są włączone kodowanie na żywo.  Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli na Mac lub Linux, użyj portalu Azure do utworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, brak punktu końcowego przesyłania strumieniowego uruchomiona. Aby uzyskać więcej informacji, zobacz [zarządzanie punktami końcowymi przesyłania strumieniowego w konta usługi Media Services](media-services-portal-manage-streaming-endpoints.md)
* Zainstaluj najnowszą wersję pakietu [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) narzędzia.
* Uruchom narzędzie i połącz się z kontem AMS.

## <a name="tips"></a>Porady
* Jeśli to możliwe, użyj połączenia internetowego hardwired.
* Regułą podczas określania wymaganiach odnośnie do przepustowości jest dwukrotnie przesyłania strumieniowego szybkości transmisji bitów. Chociaż nie jest to wymagane, ułatwia ograniczanie wpływu sieci przeciążona.
* Korzystając z koderów opartych na oprogramowaniu, zamknij wszystkie zbędne programy.

## <a name="create-a-channel"></a>Tworzenie kanału
1. W przy użyciu narzędzia AMSE, przejdź do **Live** , a następnie kliknij prawym przyciskiem myszy w obszarze kanału. Wybierz **Utwórz kanał...** z menu.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W ustawieniach kanału, wybierz **standardowe** dla opcji Live Encoding z protokołem wprowadzania ustawioną **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzyć kanał**.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-wirecast-live-encoder.md#configure_wirecast_rtmp).

> [!IMPORTANT]
> Rozliczanie zaczyna się, jak kanału przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stanów kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_wirecast_rtmp></a>Skonfiguruj koder Telestream Wirecast
W tym samouczku są używane następujące ustawienia danych wyjściowych. Pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

**Wideo**:

* Koder-dekoder: H.264
* Profil: Wysoki (poziom 4.0)
* Szybkość transmisji bitów: 5000 KB/s
* Klatki kluczowej: 2 sekundy (60 sekund)
* Szybkość klatek: 30

**Audio**:

* Koder-dekoder: AAC (LC —)
* Szybkość transmisji bitów: 192 kb/s
* Częstotliwość próbkowania: 44,1 kHz

### <a name="configuration-steps"></a>Kroki konfiguracji
1. Otwórz aplikację Telestream Wirecast z tego komputera, używane i skonfigurować do przesyłania strumieniowego RTMP.
2. Skonfiguruj dane wyjściowe, przechodząc do **dane wyjściowe** kartę i wybierając **ustawienia wyjściowej...** .

    Upewnij się, że **miejsce docelowe danych wyjściowych** ustawiono **serwera RTMP**.
3. Kliknij przycisk **OK**.
4. Na stronie Ustawienia ustawić **docelowego** ono być **usługi Azure Media Services**.

    Profil kodowanie jest wstępnie wybrane do **H.264 Azure 720 p 16:9 (1280 x 720)**. Aby dostosować te ustawienia, wybierz ikonę Koło zębate po prawej stronie listy rozwijanej, a następnie wybierz **nowe ustawienie wstępne**.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast3.png)
5. Skonfiguruj ustawienia kodera.

    Nazwę ustawienia i sprawdź, czy następujących zalecanych ustawień:

    **Video**

   * Koder: MainConcept H.264
   * Klatek na sekundę: 30
   * Średnia szybkość transmisji bitów: 5000 kbitów na sekundę (może być określany na podstawie ograniczenia sieci)
   * Profil: Main
   * Klatki co: 60 klatek

    **Audio**

   * Docelowa szybkość transmisji bitów: 192 kbitów/s
   * Częstotliwość próbkowania: 44 100 kHz

     ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast4.png)
6. Naciśnij klawisz **zapisać**.

    Pola Encoding ma teraz nowo utworzony profil dostępne do wyboru.

    Upewnij się, że wybrano nowy profil.
7. Get kanału wejściowych adres URL, aby przypisać Wirecast **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Gdy stan został zmieniony z **uruchamianie** do **systemem**, można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiona, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do aktywowania przez **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowego adresu URL danych wejściowych**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast6.png)
8. W Wirecast **ustawienia wyjściowej** okna, Wklej te informacje w **adres** pole sekcji danych wyjściowych i przypisz nazwę strumienia.

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast5.png)

1. Kliknij przycisk **OK**.
2. W głównym **Wirecast** Sprawdź źródeł danych wejściowych dla audio i wideo są gotowe, a następnie naciśnij **strumienia** w lewym górnym rogu.

   ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast7.png)

> [!IMPORTANT]
> Przed kliknięciem przycisku **strumienia**, możesz **musi** upewnij się, że kanał jest gotowy.
> Upewnij się również, nie należy pozostawiać kanału w stanie Gotowe bez wprowadzania wkład źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do przy użyciu narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania podglądu** i wybierz **z usługi Azure Media Player**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast8.png)

Strumień jest widoczna w player, następnie koder został poprawnie skonfigurowany do nawiązania połączenia usługi AMS.

Jeśli błąd kanału powinna zostać zresetowana i dostosowane ustawienia kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykułu, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu kanału odtwarzania, tworzenia programu. W obszarze **Live** w przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy w obszarze program i wybierz **utworzyć nowy Program**.  

    ![wirecast](./media/media-services-wirecast-live-encoder/media-services-wirecast9.png)
2. Nazwa programu, a w razie potrzeby dostosuj **długość okna archiwum** (który domyślnie 4 godziny). Można także określić miejsce przechowywania lub pozostaw domyślnie.  
3. Sprawdź **teraz uruchomić Program** pole.
4. Kliknij przycisk **utworzyć Program**.  

   >[!NOTE]
   >Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.
       
5. Po uruchomieniu programu potwierdzić odtwarzania przez kliknięcie prawym przyciskiem myszy program i przechodząc do **odtwarzania programach** , a następnie wybierając **z usługi Azure Media Player**.  
6. Po potwierdzeniu, ponownie kliknij prawym przyciskiem myszy program i wybierz **skopiuj dane wyjściowe adres URL do Schowka** (lub pobrać tych informacji z **programu informacji i ustawień** opcji z menu).

Strumień jest teraz gotowy do osadzonego w odtwarzacza lub dystrybuowane do odbiorców w celu wyświetlenia na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykułu, aby uzyskać wskazówki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
