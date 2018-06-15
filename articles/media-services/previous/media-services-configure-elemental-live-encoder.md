---
title: Konfigurowanie kodera na żywo stanie wolnym wysłać strumień na żywo o pojedynczej szybkości transmisji bitów | Dokumentacja firmy Microsoft
description: W tym temacie przedstawiono sposób konfigurowania kodera na żywo stanie wolnym do wysłania do kanałów AMS, które są włączone dla kodowanie na żywo o pojedynczej szybkości transmisji.
services: media-services
documentationcenter: ''
author: cenkdin
manager: cfowler
editor: ''
ms.assetid: 9c6bf6a9-6273-4fdd-9477-f0e565280b5b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 01/05/2017
ms.author: cenkd;anilmur;juliako
ms.openlocfilehash: 35d8d0438d548e679405aefcbd1982fa1731874a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
ms.locfileid: "33789432"
---
# <a name="use-the-elemental-live-encoder-to-send-a-single-bitrate-live-stream"></a>Użyj kodera na żywo stanie wolnym, aby wysyłać strumień na żywo o pojedynczej szybkości transmisji bitów
> [!div class="op_single_selector"]
> * [Elemental na żywo](media-services-configure-elemental-live-encoder.md)
> * [tricaster](media-services-configure-tricaster-live-encoder.md)
> * [wirecast](media-services-configure-wirecast-live-encoder.md)
> * [FMLE](media-services-configure-fmle-live-encoder.md)
>
>

W tym temacie przedstawiono sposób konfigurowania [stanie wolnym Live](http://www.elementaltechnologies.com/products/elemental-live) kodera do wysyłania strumienia pojedynczej szybkości transmisji bitów AMS kanałów, które są włączone kodowanie na żywo.  Aby uzyskać więcej informacji, zobacz temat [Praca z kanałami obsługującymi funkcję Live Encoding w usłudze Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

W tym samouczku przedstawiono sposób zarządzania usługi Azure Media Services (AMS) za pomocą narzędzia Azure Media Services Explorer (AMSE). To narzędzie jest uruchamiane tylko na komputerze z systemem Windows. Jeśli na Mac lub Linux, użyj portalu Azure do utworzenia [kanałów](media-services-portal-creating-live-encoder-enabled-channel.md#create-a-channel) i [programy](media-services-portal-creating-live-encoder-enabled-channel.md).

## <a name="prerequisites"></a>Wymagania wstępne
* Musi mieć praktyczną wiedzę o tworzenie wydarzeń na żywo przy użyciu interfejsu sieci web stanie wolnym na żywo.
* [Tworzenie konta usługi Azure Media Services](media-services-portal-create-account.md)
* Upewnij się, brak punktu końcowego przesyłania strumieniowego uruchomiona. Aby uzyskać więcej informacji, zobacz [zarządzanie punktami końcowymi przesyłania strumieniowego w konta usługi Media Services](media-services-portal-manage-streaming-endpoints.md).
* Zainstaluj najnowszą wersję pakietu [AMSE](https://github.com/Azure/Azure-Media-Services-Explorer) narzędzia.
* Uruchom narzędzie i połącz się z kontem AMS.

## <a name="tips"></a>Porady
* Jeśli to możliwe, użyj połączenia internetowego hardwired.
* Regułą podczas określania wymaganiach odnośnie do przepustowości jest dwukrotnie przesyłania strumieniowego szybkości transmisji bitów. Chociaż nie jest to wymagane, może pomóc zmniejszyć skuteczność przeciążenie sieci.
* Gdy za pomocą oprogramowania na podstawie koderów, zamknij wszystkie zbędne programy.

## <a name="elemental-live-with-rtp-ingest"></a>Pozyskiwania stanie wolnym Live przy włączonej ochronie czasu rzeczywistego
W tej sekcji przedstawiono sposób konfigurowania kodera na żywo stanie wolnym, który wysyła strumień na żywo o pojedynczej szybkości transmisji bitów przy użyciu protokołu RTP.  Aby uzyskać więcej informacji, zobacz [strumieni MPEG-TS przy użyciu protokołu RTP](media-services-manage-live-encoder-enabled-channels.md#channel).

### <a name="create-a-channel"></a>Tworzenie kanału

1. W przy użyciu narzędzia AMSE, przejdź do **Live** karcie, a następnie kliknij prawym przyciskiem myszy w obszarze kanału. Wybierz **Utwórz kanał...** z menu.

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental1.png)

2. Określ nazwę kanału pole opisu jest opcjonalne. W ustawieniach kanału, wybierz **standardowe** dla opcji Live Encoding z protokołem wprowadzania ustawioną **RTP (MPEG-TS)**. Możesz pozostawić wszystkie inne ustawienia, ponieważ jest.

    Upewnij się, że **teraz uruchomić nowy kanał** jest zaznaczone.

3. Kliknij przycisk **utworzyć kanał**.

   ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental12.png)

> [!NOTE]
> Kanał może trwać tyle samo co 20 minut, aby rozpocząć.
>
>

Podczas uruchamiania kanału możesz [skonfigurować koder](media-services-configure-elemental-live-encoder.md#configure_elemental_rtp).

> [!IMPORTANT]
> Należy pamiętać, że rozliczanie zaczyna się jak kanału przechodzi do stanu gotowości. Aby uzyskać więcej informacji, zobacz [stanów kanału](media-services-manage-live-encoder-enabled-channels.md#states).
>
>

### <a id=configure_elemental_rtp></a>Konfigurowanie kodera na żywo stanie wolnym
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

#### <a name="configuration-steps"></a>Kroki konfiguracji
1. Przejdź do **stanie wolnym Live** sieci web interfejsu i skonfigurować kodera dla **UDP/TS** przesyłania strumieniowego.
2. Po utworzeniu nowego zdarzenia, przewiń w dół do grupy danych wyjściowych i Dodaj **UDP/TS** grupy wyjściowej.
3. Utwórz nowe dane wyjściowe, zaznaczając **nowego strumienia** , a następnie klikając polecenie **dodać danych wyjściowych**.  

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental13.png)

   > [!NOTE]
   > Zaleca się, że stanie wolnym zdarzenie ma kod czasowy, ustawione na "Zegara systemowego", aby pomóc koder ponownie w przypadku awarii strumienia.
   >
   >
4. Teraz, dane wyjściowe został utworzony, kliknij przycisk **dodać strumienia**. Teraz można skonfigurować ustawienia danych wyjściowych.
5. Przewiń w dół "Strumienia 1" został utworzony, kliknij przycisk **wideo** karcie po lewej stronie, a następnie rozwiń węzeł **zaawansowane** w sekcji Ustawienia.

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental4.png)

    Podczas aktywnego stanie wolnym ma szeroki zakres dostosowywania dostępne, następujące ustawienia są zalecane w przypadku wprowadzenie do przesyłania strumieniowego AMS.

   * Rozdzielczość: 1280 x 720
   * Szybkość klatek: 30
   * Rozmiar GOP: 60 klatek
   * Tryb przeplotu: progresywnego
   * Szybkość transmisji bitów: 5000000 bit/s (to może być określany na podstawie ograniczenia sieci)

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental5.png)

1. Pobierz wejściowy adres URL kanału.

    Przejdź z powrotem do przy użyciu narzędzia AMSE i sprawdzić stan ukończenia kanału. Gdy stan został zmieniony z **uruchamianie** do **systemem**, można uzyskać wejściowy adres URL.

    Gdy kanał jest uruchomiona, kliknij prawym przyciskiem myszy nazwę kanału, przejdź do aktywowania przez **kopia danych wejściowych z adresu URL do Schowka** , a następnie wybierz **podstawowego adresu URL danych wejściowych**.  

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental6.png)
2. Wklej te informacje w **miejsce docelowe głównej** pole Elemental. Wszystkie inne ustawienia mogą pozostać domyślny.

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental14.png)

    Nadmiarowości dodatkowe Powtórz te kroki dodatkowej adresu URL danych wejściowych, tworząc osobne karty "Wyjściowej" do przesyłania strumieniowego UDP/usług terminalowych.
3. Kliknij przycisk **Utwórz** (Jeśli utworzono nowe zdarzenie) lub **aktualizacji** (jeśli edycji istniejące zdarzenie), a następnie przejdź do uruchomienia kodera.

> [!IMPORTANT]
> Przed kliknięciem przycisku **Start** dla interfejsu sieci web stanie wolnym na żywo można **musi** upewnij się, że kanał jest gotowy.
> Upewnij się również, nie należy pozostawiać kanału w stanie Gotowe bez zdarzenia przez czas dłuższy niż > 15 minut.
>
>

Po działaniu strumienia 30 sekund, przejdź z powrotem do odtwarzania AMSE narzędzia i testowania.  

### <a name="test-playback"></a>Podczas odtwarzania testu

Przejdź do przy użyciu narzędzia AMSE, a następnie kliknij prawym przyciskiem myszy kanału, który ma zostać przetestowana. Z menu, umieść kursor nad **odtwarzania podglądu** i wybierz **z usługi Azure Media Player**.  

    ![Elemental](./media/media-services-elemental-live-encoder/media-services-elemental8.png)

Strumień jest widoczna w player, następnie koder został poprawnie skonfigurowany do nawiązania połączenia usługi AMS.

Jeśli błąd kanału należy zresetować i dostosowane ustawienia kodera. Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) tematu, aby uzyskać wskazówki.   

### <a name="create-a-program"></a>Utwórz program
1. Po potwierdzeniu kanału odtwarzania, tworzenia programu. W obszarze **Live** w przy użyciu narzędzia AMSE, kliknij prawym przyciskiem myszy w obszarze program i wybierz **utworzyć nowy Program**.  

    ![Stanie wolnym](./media/media-services-elemental-live-encoder/media-services-elemental9.png)
2. Nazwa programu, a w razie potrzeby dostosuj **długość okna archiwum** (który domyślnie 4 godziny). Można także określić miejsce przechowywania lub pozostaw domyślnie.  
3. Sprawdź **teraz uruchomić Program** pole.
4. Kliknij przycisk **utworzyć Program**.  

    >[!NOTE]
    > Tworzenie programu zajmuje mniej czasu niż tworzenie kanału.   
      
5. Po uruchomieniu programu potwierdzić odtwarzania przez kliknięcie prawym przyciskiem myszy program i przechodząc do **odtwarzania programach** , a następnie wybierając **z usługi Azure Media Player**.  
6. Po potwierdzeniu, kliknij prawym przyciskiem myszy program ponownie i wybierz **skopiuj dane wyjściowe adres URL do Schowka** (lub pobrać tych informacji z **programu informacji i ustawień** opcji z menu).

Strumień jest teraz gotowy do osadzonego w odtwarzacza lub dystrybuowane do odbiorców w celu wyświetlenia na żywo.  

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Zobacz [Rozwiązywanie problemów z](media-services-troubleshooting-live-streaming.md) tematu, aby uzyskać wskazówki.

## <a name="media-services-learning-paths"></a>Ścieżki szkoleniowe dotyczące usługi Media Services
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Przekazywanie opinii
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
