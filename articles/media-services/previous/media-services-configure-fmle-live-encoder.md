---
title: Skonfiguruj koder KODER FMLE, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów | Microsoft Docs
description: W tym temacie pokazano, jak skonfigurować koder Flash Media Live Encoder (KODER FMLE) w celu wysłania strumienia pojedynczego szybkości transmisji bitów do kanałów AMS, dla których włączono obsługę kodowania na żywo.
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
ms.author: juliako
ms.reviewer: cenkdin;anilmur
ms.openlocfilehash: 09d9bdffefe9204e9f58b8f07af5b21228269f6c
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/15/2019
ms.locfileid: "69016754"
---
# <a name="use-the-fmle-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera KODER FMLE, aby wysłać strumień na żywo o pojedynczej szybkości transmisji bitów 
> [!div class="op_single_selector"]
> * [FMLE](media-services-configure-fmle-live-encoder.md)
> * [Tricaster](media-services-configure-tricaster-live-encoder.md)
> * [Wirecast](media-services-configure-wirecast-live-encoder.md)
>
>

W tym artykule pokazano, jak skonfigurować koder [Flash Media Live Encoder](https://www.adobe.com/products/flash-media-encoder.html) (koder FMLE) w celu wysłania strumienia pojedynczego szybkości transmisji bitów do kanałów AMS, dla których włączono obsługę kodowania na żywo. Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) przy użyciu narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze Windows. Jeśli na komputerze Mac lub Linux, użyj witryny Azure portal do utworzenia [kanały](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

W tym samouczku opisano użycie AAC. KODER FMLE jednak domyślnie nie obsługuje AAC. Trzeba zakupić wtyczkę do kodowania AAC, na przykład z MainConcept: [Wtyczka AAC](https://www.mainconcept.com/products/plug-ins/plug-ins-for-adobe/aac-encoder-fmle.html)

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

    ![KODER FMLE](./media/media-services-fmle-live-encoder/media-services-fmle1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W obszarze Ustawienia kanału wybierz **standardowa** opcji kodowanie na żywo przy użyciu protokołu danych wejściowych, ustaw **RTMP**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzenia kanału**.

   ![KODER FMLE](./media/media-services-fmle-live-encoder/media-services-fmle2.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-fmle-live-encoder.md).

> [!IMPORTANT]
> Należy pamiętać, że rozliczanie zaczyna się zaraz po przeniesieniu kanału w stan gotowości. Aby uzyskać więcej informacji, zobacz [stany kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

## <a id=configure_fmle_rtmp></a>Konfigurowanie kodera KODER FMLE
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
1. Przejdź do interfejsu kodera (KODER FMLE) usługi Flash Media Live na używanej maszynie.

    Interfejs to jedna Strona główna ustawień. Zwróć uwagę na następujące zalecane ustawienia, aby rozpocząć przesyłanie strumieniowe przy użyciu usługi KODER FMLE.

   * Format: Szybkość klatek na sekundę 264: 30,00
   * Rozmiar danych wejściowych: 1280 x 720
   * Szybkość transmisji bitów: 5000 KB/s (można je dostosować w zależności od ograniczeń sieci)  

     ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle3.png)

     W przypadku korzystania ze źródeł z przeplotem zaznacz opcję "Usuń przeplot"
2. Wybierz ikonę klucza obok pozycji Format, te dodatkowe ustawienia powinny być następujące:

   * Profilu Główny
   * Poziom: 4.0
   * Częstotliwość klatek kluczowych: 2 sekundy

     ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle4.png)
3. Ustaw następujące ważne ustawienie audio:

   * Format: AAC
   * Częstotliwość próbkowania: 44100 Hz
   * Multimedia 192 kb/s

     ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle5.png)
4. Pobierz adres URL wejścia kanału, aby przypisać go do **punktu końcowego RTMP**koder FMLE.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Po zmianie stanu z **od** do **systemem**, możesz uzyskać wejściowego adresu URL.

    Gdy kanał jest uruchomiony, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do umieść kursor nad **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowy adres URL danych wejściowych**.  

    ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle6.png)
5. Wklej te informacje w polu **adres URL FMS** w sekcji dane wyjściowe i przypisz nazwę strumienia.

    ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle7.png)

    Aby uzyskać dodatkową nadmiarowość, Powtórz te kroki z dodatkowym wejściowym adresem URL.
6. Wybierz przycisk **Połącz**.

> [!IMPORTANT]
> Przed kliknięciem przycisku **Połącz** **musisz** upewnić się, że kanał jest gotowy.
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

    ![koder FMLE](./media/media-services-fmle-live-encoder/media-services-fmle9.png)
2. Nadaj nazwę programowi i, w razie potrzeby, Dostosuj **Długość okna archiwum** (wartość domyślna to 4 godziny). Można również określić lokalizację przechowywania lub pozostaw domyślny.  
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
