---
title: Konfiguruj koder NewTek tricaster do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować koder na żywo Tricaster wysyłać strumień o pojedynczej szybkości transmisji bitów z kanałami usługi AMS, obsługującymi kodowanie na żywo.
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
ms.author: juliako;cenkd;anilmur
ms.openlocfilehash: 6e09ce83296fccfbcb4a04913d55961e0da4de79
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720801"
---
# <a name="use-the-newtek-tricaster-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu koder NewTek tricaster  
> [!div class="op_single_selector"]
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Elemental Live](media-services-configure-elemental-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [koder NewTek TriCaster](https://newtek.com/products/tricaster-40.html) koder na żywo, aby wysłać strumień o pojedynczej szybkości transmisji bitów do usługi AMS kanały, które są włączone dla kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

> [!NOTE]
> Korzystając z Tricaster wysyłania w udziale, źródła danych do usługi AMS kanały, obsługującymi kodowanie na żywo, może istnieć występującymi audio/wideo w zdarzenia na żywo w przypadku określonych funkcji Tricaster, takich jak szybkie Wycinanie między źródła danych lub przełączenie z plansz. Zespół usługi AMS pracuje się nad rozwiązywanie tych problemów w międzyczasie, nie zaleca się korzystanie z tych funkcji.
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

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzenia kanału**.

   ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-tricaster-live-encoder.md#configure_tricaster_rtmp).

> [!IMPORTANT]
> Naliczanie opłat rozpoczyna się, jak kanał przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a name="a-idconfiguretricasterrtmpconfigure-the-newtek-tricaster-encoder"></a><a id="configure_tricaster_rtmp"/>Konfiguruj koder NewTek tricaster

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

1. Utwórz nową **koder NewTek TriCaster** projektu, w zależności od tego, jakie źródła danych wejściowych wideo jest używany.
2. Jeden raz w tym projekcie, Znajdź **Stream** przycisk, a następnie kliknij ikonę koła zębatego obok niego dostęp do menu konfiguracji strumienia.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster3.png)
3. Po otwarciu menu, kliknij przycisk **New** pod nagłówkiem połączenia. Po wyświetleniu monitu dla typu połączenia, wybierz **Adobe Flash**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster4.png)
4. Kliknij przycisk **OK**.
5. Teraz można zaimportować profil FMLE, klikając strzałkę listy rozwijanej w obszarze **przesyłania strumieniowego profilu** i przechodząc do **Przeglądaj**.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster5.png)
6. Przejdź do gdzie skonfigurowanego profilu FMLE został zapisany.
7. Zaznacz go, a następnie naciśnij klawisz **OK**.

    Po przekazaniu profilu, przejdź do następnego kroku.
8. Pobierz kanał dane wejściowe podane przez adres URL, aby przypisać ją do Tricaster **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu z **od** do **systemem**, możesz uzyskać wejściowego adresu URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do umieść kursor nad **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowy adres URL danych wejściowych**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster6.png)
9. Wklej tę informację w **lokalizacji** pola pod **Flash Server** w obrębie projektu Tricaster. Również przypisać nazwę strumienia w **identyfikator Stream** pola.

    Jeśli informacje o strumieniu została dodana do profilu FMLE, jego można również zaimportować do tej sekcji, klikając **importowanie ustawień**, przechodząc do zapisywanego profilu FMLE i klikając pozycję **OK**. Odpowiednie pola Flash Server należy wypełnić przy użyciu informacji z FMLE.

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster7.png)
10. Po zakończeniu kliknij przycisk **OK** w dolnej części ekranu. Po zakończeniu wejścia audio i wideo do Tricaster było rozpocząć przesyłanie strumieniowe do usługi AMS, klikając **Stream** przycisku.

     ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster11.png)

> [!IMPORTANT]
> Przed kliknięciem przycisku **Stream**, możesz **musi** upewnij się, że kanał jest gotowy.
> Ponadto upewnij się, że nie pozostawić kanał w stanie gotowości bez udziału danych wejściowych, źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania korzystania z wersji zapoznawczej** i wybierz **za pomocą usługi Azure Media Player**.  

    ![tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster8.png)

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu, będzie trzeba zresetować kanał i dostosować ustawień kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykuł, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program

1. Po potwierdzeniu odtwarzania kanału, Utwórz program. W obszarze **Live** karty przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy na obszarze program i wybierz **utworzyć nowy Program**.  

    ![Tricaster](./media/media-services-tricaster-live-encoder/media-services-tricaster9.png)
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
