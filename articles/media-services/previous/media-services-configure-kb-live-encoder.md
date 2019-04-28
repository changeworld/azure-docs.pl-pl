---
title: Konfiguruj koder Haivision KB do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów na platformie Azure | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować koder na żywo Haivision KB, aby wysłać strumień o pojedynczej szybkości transmisji bitów w kanałach usługi AMS, obsługującymi kodowanie na żywo.
services: media-services
documentationcenter: ''
author: dbgeorge
manager: vsood
editor: ''
ms.assetid: 0d2f1e81-51a6-4ca9-894a-6dfa51ce4c70
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;dbgeorge
ms.openlocfilehash: 058a1f964eb14d89628c92cbadd80511b7a27bae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61230575"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu kodera na żywo Haivision KB  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

W tym temacie przedstawiono sposób konfigurowania [koder na żywo Havision KB](https://www.haivision.com/products/kb-series/) kodera, aby wysłać strumień o pojedynczej szybkości transmisji bitów do usługi AMS kanały, które są włączone dla kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
*   Dostęp do koder Haivision KB, uruchomiony w wersji SW 5.01, lub nowszej.
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
[Haivision](./media/media-services-configure-kb-live-encoder/channel.png)
2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest. Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.
3. Kliknij przycisk **utworzenia kanału**.
[Haivision](./media/media-services-configure-kb-live-encoder/livechannel.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.

## <a name="configure-the-haivision-kb-encoder"></a>Konfiguruj koder Haivision KB
W tym samouczku są używane następujące ustawienia danych wyjściowych. W pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

Wideo:
-   Codec: H.264
-   Profil: Wysoki (poziom 4.0)
-   Szybkość transmisji bitów: 5000 KB/s
-   Klatki kluczowe: 2 sekundy (60 klatek)
-   Szybkość klatek: 30

Audio:
-   Codec: AAC (LC)
-   Szybkość transmisji bitów: 192 kb/s
-   Częstotliwość próbkowania: 44,1 kHz

## <a name="configuration-steps"></a>Kroki konfiguracji
1.  Zaloguj się do interfejsu użytkownika Haivision KB.
2.  Kliknij pozycję **przycisk Menu** w kanale — centrum sterowania i wybierz pozycję **Dodaj kanał**  
    ![Zrzut ekranu 2017-08-14 w 9.15.09 AM](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Typ **nazwy kanału** nazwę pola, a następnie kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 w 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wybierz **źródła danych wejściowych kanału** z **źródła danych wejściowych** listy rozwijanej i kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 w 9.20.44 AM](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z **szablonu Encoder** listy rozwijanej wybierz **AAC 192, H264 720 w-** i kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 w 9.23.15 AM](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z **wybierz nowe dane wyjściowe** listy rozwijanej wybierz **RTMP** i kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 w 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  Z **kanału danych wyjściowych** okna, wypełnij informacje o usłudze Azure stream. Wklej **RTMP** łącze z konfiguracji początkowej kanału w **serwera** obszaru. W **Nazwa wyjściowego** obszaru wpisz nazwę kanału. W obszarze Stream nazwy szablonu należy użyć szablonu RTMPStreamName_ % video_bitrate % nazwę strumienia.
    ![Zrzut ekranu 2017-08-14 w 9.33.17 AM](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kliknij przycisk Dalej, a następnie kliknij przycisk Gotowe.
9.  Kliknij przycisk **przycisk Odtwórz** uruchomić kanału kodera.  
    ![Haivision KB.png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Podczas odtwarzania testu
Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu Umieść kursor nad odtwarzania korzystania z wersji zapoznawczej, a następnie wybierz za pomocą usługi Azure Media Player.

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu kanału musi być resetowany i dostosować ustawień kodera. Zobacz artykuł dotyczący rozwiązywania problemów w celu uzyskania wskazówek.

## <a name="create-a-program"></a>Utwórz program
1.  Po potwierdzeniu odtwarzania kanału, Utwórz program. Na karcie na żywo w przy użyciu narzędzia AMSE kliknij prawym przyciskiem myszy na obszarze program i wybierz pozycję Utwórz nowy Program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nadaj nazwę programu i w razie potrzeby Dostosuj długość okna archiwizacji, (która domyślnie 4 godziny). Można również określić lokalizację przechowywania lub pozostaw domyślny.
2.  Pole teraz Program początek wyboru.
3.  Kliknij pozycję Utwórz Program.
4.  Po uruchomieniu programu potwierdzić odtwarzania, klikając prawym przyciskiem myszy program i przechodząc do odtwarzania programy i następnie wybierz przy użyciu usługi Azure Media Player.
5.  Po potwierdzeniu, ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie Kopiuj dane wyjściowe adres URL do Schowka lub pobierać te informacje z informacji o programie i opcji ustawień menu.

Strumień jest teraz gotowy do osadzonych w odtwarzaczu lub dystrybuowane do odbiorców do przeglądania na żywo.

> [!NOTE]
> Tworzenie programów zajmuje mniej czasu niż Tworzenie kanałów.
