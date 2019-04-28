---
title: Konfiguruj koder FMLE do wysyłania strumienia na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie pokazano, jak skonfigurować koder interfejsu Flash Media Live Encoder (FMLE), aby wysłać strumień o pojedynczej szybkości transmisji bitów w kanałach usługi AMS, obsługującymi kodowanie na żywo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 3113f333-517a-47a1-a1b3-57e200c6b2a2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako;cenkdin;anilmur
ms.openlocfilehash: 01bb628a6520488dcebf49a1e868213b955abc31
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61466016"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu koder FMLE 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

W tym artykule przedstawiono sposób konfigurowania [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) encoder (FMLE) do wysyłania strumień o pojedynczej szybkości transmisji bitów do usługi AMS kanały, które są włączone dla kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

W tym samouczku opisano sposób używania adaptacyjnych kontrolek aplikacji. Jednak domyślnie adaptacyjnych kontrolek aplikacji nie obsługuje FMLE. Należy zakupić wtyczkę AAC kodowania takich jak przez firmę MainConcept: [Wtyczkę AAC](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzenia kanału**.

   ![FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Należy pamiętać, że naliczanie opłat rozpoczyna się tak szybko, jak kanał przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfiguruj koder FMLE
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
1. Przejdź do programu Flash Media Live Encoder firmy (FMLE) interfejsu na maszynie używane.

    Interfejs jest jeden główna strona ustawień. Zwróć uwagę na następujące ustawienia, aby zacząć korzystać z przesyłania strumieniowego przy użyciu FMLE zalecane.

   * Format: Szybkość klatek H.264: 30.00
   * Rozmiar danych wejściowych: 1280 x 720
   * Szybkość transmisji bitów: (Może być określany na podstawie ograniczenia sieci) do 5000 KB/s  

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     Przy użyciu z przeplotem źródeł, skontaktuj się znacznik wyboru opcji "Opcji Usuń przeplot"
2. Wybierz ikonę klucza, obok Format, należy te dodatkowe ustawienia:

   * Profil: Główny
   * Poziom: 4.0
   * Ramka kluczowa częstotliwość: 2 sekundy

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ustawienie następujących ważnych audio:

   * Format: AAC
   * Częstotliwość próbkowania: 44100 Hz
   * Szybkość transmisji bitów: 192 kb/s

     ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Pobierz kanał dane wejściowe podane przez adres URL, aby przypisać ją do FMLE **punktu końcowego protokołu RTMP**.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu z **od** do **systemem**, możesz uzyskać wejściowego adresu URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do umieść kursor nad **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowy adres URL danych wejściowych**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Wklej tę informację w **FMS URL** pole sekcji danych wyjściowych, a następnie przypisz nazwę strumienia.

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Dodatkowe nadmiarowość Powtórz te kroki, przy użyciu pomocniczego adresu URL danych wejściowych.
6. Wybierz przycisk **Połącz**.

> [!IMPORTANT]
> Przed kliknięciem przycisku **Connect**, możesz **musi** upewnij się, że kanał jest gotowy.
> Ponadto upewnij się, że nie pozostawić kanał w stanie gotowości bez udziału danych wejściowych, źródła danych przez czas dłuższy niż > 15 minut.
>
>

## <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania korzystania z wersji zapoznawczej** i wybierz **za pomocą usługi Azure Media Player**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle8.png)

Jeśli strumień jest wyświetlany w odtwarzaczu, następnie kodera zostało poprawnie skonfigurowane nawiązać połączenia z usługi AMS.

Odebranie błędu kanału musi być resetowany i dostosować ustawień kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) artykuł, aby uzyskać wskazówki.  

## <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu odtwarzania kanału, Utwórz program. W obszarze **Live** karty przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy na obszarze program i wybierz **utworzyć nowy Program**.  

    ![fmle](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Nazwa programu i w razie potrzeby dostosuj **długość okna archiwizacji** (której wartość domyślna to 4 godziny). Można również określić lokalizację przechowywania lub pozostaw domyślny.  
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
