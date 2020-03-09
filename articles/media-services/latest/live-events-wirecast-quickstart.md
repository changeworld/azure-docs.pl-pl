---
title: Tworzenie Azure Media Services strumienia na żywo z portalem i Wirecast
description: Dowiedz się, jak utworzyć strumień na żywo usługi Azure Media Service
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/06/2020
ms.openlocfilehash: c0eaf3907cbfcd86424b1d2cbc03930a7af72786
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927626"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Tworzenie Azure Media Services strumienia na żywo z portalem i Wirecast

W tym przewodniku Wprowadzenie założono, że masz subskrypcję platformy Azure i utworzono konto Azure Media Services.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne](https://azure.microsoft.com/free/) konto.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Otwórz ulubioną przeglądarkę internetową i przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

W tym przewodniku szybki start omówiono następujące zagadnienia:

- Konfigurowanie kodera lokalnego przy użyciu bezpłatnej wersji próbnej usługi Wirecast
- Konfigurowanie strumienia na żywo
- Konfigurowanie danych wyjściowych strumienia na żywo
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego
- Używanie Azure Media Player do wyświetlania strumienia na żywo i danych wyjściowych na żądanie

Aby zachować prostotę, użyjemy ustawienia wstępnego kodowania dla Azure Media Services w Wirecast, kodowaniu w chmurze i RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Konfigurowanie kodera lokalnego przy użyciu Wirecast

1. Pobierz i zainstaluj program Wirecast dla systemu operacyjnego w https://www.telestream.net
1. Uruchom aplikację i Użyj swojego ulubionego adresu e-mail do zarejestrowania produktu.  Pozostaw otwartą aplikację.
1. Otrzymasz wiadomość e-mail z prośbą o zweryfikowanie adresu e-mail, a następnie aplikacja zacznie korzystać z bezpłatnej wersji próbnej.
1. ZALECANE: Obejrzyj samouczek wideo na ekranie Otwieranie aplikacji.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Konfigurowanie Azure Media Services strumienia na żywo

1. Po przejściu do konta Azure Media Services w portalu wybierz pozycję **przesyłanie strumieniowe na żywo** z listy Media Services.
1. Kliknij pozycję **Dodaj wydarzenie na żywo** , aby utworzyć nowe zdarzenie przesyłania strumieniowego na żywo.
1. Wprowadź nazwę nowego zdarzenia, na przykład *TestLiveEvent* , w polu Live Event **name** .
1. Wprowadź opcjonalny opis zdarzenia w polu **Opis** .
1. Wybierz przycisk radiowy **przekazanie — bez kodowania w chmurze** .
1. Wybierz przycisk radiowy **RTMP** . 
1. Upewnij się, że **nie** wybrano przycisku radiowego dla uruchamiania zdarzenia na żywo, aby uniknąć naliczania opłat za wydarzenie na żywo sprzed jego gotowości.  (Rozliczenie rozpocznie się po rozpoczęciu zdarzenia na żywo).
1. Kliknij przycisk **Recenzja + Utwórz** , aby przejrzeć ustawienia.
1. Kliknij przycisk **Utwórz** , aby utworzyć wydarzenie na żywo. Następnie zostanie zwrócony widok listy zdarzeń na żywo.
1. Kliknij **link do** utworzonego właśnie zdarzenia na żywo. Zwróć uwagę, że zdarzenie zostało zatrzymane.
1. Pozostaw Tę stronę otwartą w przeglądarce.  Będziemy wrócić do niego później.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Konfigurowanie strumienia na żywo za pomocą programu Wirecast Studio

1. Przy założeniu, że nadal masz otwartą aplikację Wirecast, wybierz opcję **Utwórz pusty dokument** z menu głównego, a następnie kliknij przycisk **Kontynuuj**.
1. Umieść kursor nad pierwszą warstwą w obszarze warstwy Wirecast.  Kliknij ikonę **Dodaj** , która zostanie wyświetlona, a następnie wybierz dane wejściowe wideo, które chcesz przesłać strumieniowo.  Zostanie otwarte okno dialogowe warstwa główna 1.
1. Wybierz opcję **Przechwytywanie wideo** z menu, a następnie wybierz kamerę, której chcesz użyć. Widok z aparatu zostanie wyświetlony w obszarze podglądu.
1. Umieść kursor nad drugą warstwą w obszarze warstwy Wirecast. Kliknij ikonę **Dodaj** , która zostanie wyświetlona, a następnie wybierz wejście audio, które chcesz przesłać strumieniowo.  Zostanie otwarte okno dialogowe warstwa główna 2.
1. Wybierz z menu pozycję **przechwytywanie audio** , a następnie wybierz dane wejściowe audio, które mają być używane. 
1. Z menu głównego wybierz pozycję **Ustawienia wyjściowe**.  Zostanie wyświetlone okno dialogowe dane wyjściowe.
1. Wybierz pozycję **Azure Media Services** z listy rozwijanej dane wyjściowe.  Ustawienie danych wyjściowych dla Azure Media Services automatycznie wypełnia *większość* ustawień danych wyjściowych.
1. W następnej sekcji powrócisz do Azure Media Services w przeglądarce w celu skopiowania *wprowadzonego adresu URL* w celu wprowadzenia do ustawień danych wyjściowych.

### <a name="copy-and-paste-the-input-url"></a>Skopiuj i wklej wejściowy adres URL

1. Na stronie Azure Media Services portalu kliknij przycisk **Start** , aby uruchomić wydarzenie strumienia na żywo. (Rozliczenia zaczynają się teraz).
2. Kliknij przełącznik **bezpieczny/niezabezpieczony** , aby ustawić go jako **niezabezpieczony**.  Spowoduje to ustawienie protokołu na RTMP zamiast RTMP.
3. Skopiuj **wejściowy adres URL** do Schowka.
4. Przejdź do aplikacji Wirecast i wklej **wejściowy adres URL** do pola **Address** w ustawieniach danych wyjściowych.
5. Kliknij przycisk **OK**.

## <a name="setting-up-outputs"></a>Konfigurowanie danych wyjściowych

Ta część skonfiguruje dane wyjściowe i umożliwi zapisanie nagrania strumienia na żywo.  

> [!NOTE]
> Aby można było przesyłać strumieniowo te dane wyjściowe, musi być uruchomiony punkt końcowy przesyłania strumieniowego.  Zobacz Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego poniżej.

1. Kliknij link **Utwórz dane wyjściowe** poniżej widocznej przeglądarki wideo.
1. Jeśli chcesz, możesz zmienić nazwę danych wyjściowych w polu **Nazwa** na bardziej przyjazny dla użytkownika, aby ułatwić znalezienie go w przyszłości.
1. Pozostaw wszystkie pozostałe pola jako teraz.
1. Kliknij przycisk **dalej** Dodaj lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na bardziej przyjazny dla użytkownika, jeśli chcesz.
1. Pozostaw teraz wszystkie inne elementy na tym ekranie.
1. Kliknij przycisk **Utwórz**.

## <a name="starting-the-broadcast"></a>Rozpoczynanie emisji

1. W Wirecast wybierz pozycję **Output (wyjście) > Rozpocznij/Zatrzymaj emitowanie > rozpocznij Azure Media Services: Azure Media Services** z menu głównego.  Gdy strumień został wysłany do zdarzenia na żywo, aktywne okno w Wirecast będzie widoczne w odtwarzaczu wideo na żywo na stronie zdarzenia na żywo w Azure Media Services.

1. Kliknij przycisk **Przejdź** w oknie Podgląd, aby rozpocząć emitowanie wideo i dźwięku wybranego dla warstw Wirecast.

> [!TIP]
> Jeśli wystąpi błąd, spróbuj ponownie załadować odtwarzacz, klikając link Załaduj ponownie odtwarzacz powyżej odtwarzacza.

## <a name="running-the-default-streaming-endpoint"></a>Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego

1. Upewnij się, że punkt końcowy przesyłania strumieniowego jest uruchomiony, wybierając **punkty końcowe przesyłania strumieniowego** na liście Media Services. Nastąpi przekierowanie do strony punkty końcowe przesyłania strumieniowego.
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego jest zatrzymany, kliknij **domyślny** punkt końcowy przesyłania strumieniowego. Spowoduje to przejście do strony dla tego punktu końcowego.
1. Kliknij przycisk **Uruchom**.  Spowoduje to uruchomienie punktu końcowego przesyłania strumieniowego.

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Odtwórz emisję wyjściową za pomocą Azure Media Player

1. Skopiuj **adres URL przesyłania strumieniowego** z wyjściowego odtwarzacza wideo. 
1. W przeglądarce sieci Web otwórz Azure Media Player demonstracyjne https://ampdemo.azureedge.net/azuremediaplayer.html
1. Wklej **adres URL przesyłania strumieniowego** do pola adres URL Azure Media Player.
1. Kliknij przycisk **Aktualizuj odtwarzacz** .
1. Kliknij ikonę **odtwarzania** na filmie wideo, aby zobaczyć swój strumień na żywo.

## <a name="stopping-the-broadcast"></a>Zatrzymywanie emisji

Jeśli uważasz, że masz przesłaną strumieniowo wystarczającą zawartość, Zatrzymaj emisję.

1. W Wirecast kliknij przycisk Broadcast ( **emisja** ).  Spowoduje to zatrzymanie emisji z Wirecast.
1. W portalu kliknij pozycję **Zatrzymaj**. Zostanie wyświetlony komunikat ostrzegawczy informujący o zatrzymaniu strumienia na żywo, ale dane wyjściowe staną się teraz zasobem na żądanie.
1. Kliknij przycisk **Zatrzymaj** w komunikacie ostrzegawczym. W Azure Media Player zostanie również wyświetlony błąd, ponieważ strumień na żywo nie jest już dostępny.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Odtwórz dane wyjściowe na żądanie za pomocą Azure Media Player

Utworzone dane wyjściowe są teraz dostępne do przesyłania strumieniowego na żądanie, o ile jest uruchomiony punkt końcowy przesyłania strumieniowego.

1. Przejdź do listy Media Services i wybierz pozycję **zasoby**.
1. Znajdź utworzone wcześniej dane wyjściowe zdarzenia i kliknij **link do elementu zawartości**. Zostanie otwarta strona wyjście zasobu.
1. Skopiuj **adres URL przesyłania strumieniowego** w odtwarzaczu wideo dla elementu zawartości.
1. Wróć do Azure Media Player w przeglądarce i wklej **adres URL przesyłania strumieniowego** do pola adresu URL Azure Media Player.
1. Kliknij pozycję **Aktualizuj odtwarzacz**.
1. Kliknij ikonę **odtwarzania** na filmie wideo, aby wyświetlić zasób na żądanie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

  > [!IMPORTANT]
  > Zatrzymaj usługi! Po wykonaniu kroków opisanych w tym przewodniku Szybki Start należy się upewnić, że zatrzymasz wydarzenie na żywo i punkt końcowy przesyłania strumieniowego, lub nadal będą naliczane opłaty za czas ich działania. Aby zatrzymać wydarzenie na żywo, zapoznaj się z tematem zatrzymywanie emisji, kroki 2 i 3 powyżej.

### <a name="stopping-the-streaming-endpoint"></a>Zatrzymywanie punktu końcowego przesyłania strumieniowego

1. Z listy Media Services wybierz pozycję **punkty końcowe przesyłania strumieniowego**.
2. Kliknij wcześniej uruchomiony punkt **końcowy przesyłania** strumieniowego. Spowoduje to otwarcie strony punktu końcowego.
3. Kliknij przycisk **Zatrzymaj**.  Spowoduje to zatrzymanie punktu końcowego przesyłania strumieniowego.

>[!TIP]
>Jeśli nie chcesz zachować zasobów z tego zdarzenia, pamiętaj o ich usunięciu w celu zapobieżenia obciążeniu magazynem.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Jaki artykuł jest dalej w sekwencji](./live-events-outputs-concept.md)
