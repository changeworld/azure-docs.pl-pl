---
title: Skonfiguruj koder Haivision KB do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów do platformy Azure | Microsoft Docs
description: W tym temacie opisano sposób konfigurowania kodera Haivision KB na żywo w celu wysłania strumienia pojedynczego szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo.
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
ms.author: juliako
ms.openlocfilehash: 7bb3db4861842e145689682035adc3c691538adf
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297793"
---
# <a name="use-the-haivision-kb-live-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera Haivision KB Live, aby wysłać strumień na żywo o pojedynczej szybkości transmisji bitów  
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Haivision](media-services-configure-kb-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)

W tym temacie pokazano, jak skonfigurować koder [kodera HAVISION KB na żywo](https://www.haivision.com/products/kb-series/) w celu wysłania strumienia o pojedynczej szybkości transmisji bitów do kanałów usługi AMS obsługujących kodowanie na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
*   Dostęp do kodera Haivision KB z uruchomionym programem SW v 5.01 lub nowszym.
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

## <a name="configure-the-haivision-kb-encoder"></a>Konfigurowanie kodera Haivision KB
W tym samouczku są używane następujące ustawienia danych wyjściowych. W pozostałej części tej sekcji opisano kroki konfiguracji szczegółowo.

Wideo:
-   Wymaga H. 264
-   Profilu Wysoka (poziom 4,0)
-   Multimedia 5000 KB/s
-   Kluczowych 2 sekundy (60 ramek)
-   Szybkość klatek: 30

Dźwięku
-   Wymaga AAC (LC)
-   Multimedia 192 kb/s
-   Częstotliwość próbkowania: 44,1 kHz

## <a name="configuration-steps"></a>Kroki konfiguracji
1.  Zaloguj się do interfejsu użytkownika Haivision KB.
2.  Kliknij **przycisk menu** w centrum sterowania kanału i wybierz polecenie **Dodaj kanał**  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.15.09 AM](./media/media-services-configure-kb-live-encoder/step2.png)
3.  Wpisz **nazwę kanału** w polu Nazwa, a następnie kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.19.07 AM](./media/media-services-configure-kb-live-encoder/step3.png)
4.  Wybierz **Źródło danych wejściowych kanału** z listy rozwijanej **Źródło danych wejściowych** i kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.20.44 AM](./media/media-services-configure-kb-live-encoder/step4.png)
5.  Z listy rozwijanej **szablon kodera** wybierz pozycję **wielokrotna H264-720-AAC-192** , a następnie kliknij przycisk Dalej.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.23.15 AM](./media/media-services-configure-kb-live-encoder/step5.png)
6.  Z listy rozwijanej **Wybierz nowe dane wyjściowe** wybierz opcję **RTMP** i kliknij przycisk Dalej.  
    ![Zrzut ekranu 2017-08-14 o godzinie 9.27.51 AM](./media/media-services-configure-kb-live-encoder/step6.png)
7.  W oknie **dane wyjściowe kanału** Wypełnij informacje o usłudze Azure Stream. Wklej link **RTMP** z konfiguracji wstępnego kanału w obszarze **serwer** . W obszarze **Nazwa wyjściowa** wpisz nazwę kanału. W obszarze szablon nazwy strumienia Użyj szablonu RTMPStreamName_% video_bitrate%, aby nawiązać nazwę strumienia.
    ![Zrzut ekranu 2017-08-14 o godzinie 9.33.17 AM](./media/media-services-configure-kb-live-encoder/step7.png)
8.  Kliknij przycisk Dalej, a następnie kliknij przycisk Gotowe.
9.  Kliknij **przycisk Odtwórz** , aby uruchomić kanał kodera.  
    ![Haivision KB. png](./media/media-services-configure-kb-live-encoder/step9.png)

## <a name="test-playback"></a>Podczas odtwarzania testu
Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. W menu Umieść wskaźnik myszy nad odtwarzaniem wersji zapoznawczej i wybierz pozycję z Azure Media Player.

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu kanału musi być resetowany i dostosować ustawień kodera. Aby uzyskać wskazówki, zobacz artykuł dotyczący rozwiązywania problemów.

## <a name="create-a-program"></a>Utwórz program
1.  Po potwierdzeniu odtwarzania kanału, Utwórz program. Na karcie na żywo w narzędziu AMSE kliknij prawym przyciskiem myszy w obszarze programu i wybierz pozycję Utwórz nowy program.
[Haivision](./media/media-services-configure-kb-live-encoder/program.png)
1.  Nazwij program i, w razie potrzeby, Dostosuj długość okna archiwum (domyślnie cztery godziny). Można również określić lokalizację przechowywania lub pozostaw domyślny.
2.  Zaznacz pole Uruchom program teraz.
3.  Kliknij pozycję Utwórz program.
4.  Po uruchomieniu programu Potwierdź odtwarzanie, klikając prawym przyciskiem myszy program i przechodząc do odtworzenia programów, a następnie wybierając pozycję z Azure Media Player.
5.  Po potwierdzeniu ponownie kliknij prawym przyciskiem myszy program i wybierz polecenie Kopiuj wyjściowy adres URL do schowka (lub Pobierz te informacje z opcji Informacje o programie i ustawienia z menu).

Strumień jest teraz gotowy do osadzonych w odtwarzaczu lub dystrybuowane do odbiorców do przeglądania na żywo.

> [!NOTE]
> Tworzenie programów zajmuje mniej czasu niż Tworzenie kanałów.
