---
title: Tworzenie strumienia na żywo usługi Azure Media Services za pomocą portalu i wirecastu
description: Dowiedz się, jak utworzyć strumień usługi Azure Media Service na żywo
services: media-services
ms.service: media-services
ms.topic: quickstart
ms.author: inhenkel
author: IngridAtMicrosoft
ms.date: 03/25/2020
ms.openlocfilehash: e5bdd75ca61d53a64f003633d74e3d8f7992a98b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80336329"
---
# <a name="create-a-azure-media-services-live-stream-with-the-portal-and-wirecast"></a>Tworzenie strumienia na żywo usługi Azure Media Services za pomocą portalu i wirecastu

W tym przewodniku wprowadzenie przyjęto założenie, że masz subskrypcję platformy Azure i utworzono konto usługi Azure Media Services.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="log-in-to-the-azure-portal"></a>Logowanie do witryny Azure Portal

Otwórz ulubioną przeglądarkę internetową i przejdź do witryny [Microsoft Azure Portal](https://portal.azure.com/). Wprowadź swoje poświadczenia, aby zalogować się do portalu. Widok domyślny to pulpit nawigacyjny usług.

W tym przewodniku Szybki start omówimy:

- Konfigurowanie kodera lokalnego z bezpłatną próbą telestream Wirecast
- Konfigurowanie transmisji na żywo
- Konfigurowanie wyjść strumienia na żywo
- Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego
- Korzystanie z programu Azure Media Player w celu wyświetlania transmisji na żywo i danych wyjściowych na żądanie

Aby zachować prostotę, użyjemy predefiniowanych kodowania dla usługi Azure Media Services w wirecast, kodowanie chmury przekazu i RTMP.

## <a name="setting-up-an-on-premises-encoder-with-wirecast"></a>Konfigurowanie kodera lokalnego za pomocą wirecastu

1. Pobierz i zainstaluj Wirecast dla swojego systemu operacyjnego pod adresemhttps://www.telestream.net
1. Uruchom aplikację i użyj ulubionego adresu e-mail, aby zarejestrować produkt.  Zachowaj otwartą aplikację.
1. Otrzymasz wiadomość e-mail z prośbą o zweryfikowanie adresu e-mail, a następnie aplikacja rozpocznie bezpłatną wersję próbną.
1. POLECAMY: Obejrzyj film instruktażowy na ekranie aplikacji otwierającej.

## <a name="setting-up-an-azure-media-services-live-stream"></a>Konfigurowanie strumienia na żywo usługi Azure Media Services

1. Po przejściu do konta usługi Azure Media Services w portalu wybierz opcję **Przesyłanie strumieniowe na żywo** z listy usługi Media Services.<br/>
![Wybierz link do transmisji strumieniowej na żywo](media/live-events-wirecast-quickstart/select-live-streaming.png)<br/>
1. Kliknij **pozycję Dodaj wydarzenie na żywo,** aby utworzyć nowe wydarzenie transmisji strumieniowej na żywo.<br/>
![Ikona Dodaj wydarzenie na żywo](media/live-events-wirecast-quickstart/add-live-event.png)<br/>
1. Wprowadź nazwę nowego zdarzenia, takiego jak *TestLiveEvent,* w polu **Nazwa** zdarzenia na żywo.<br/>
![Pole tekstowe nazwy zdarzenia na żywo](media/live-events-wirecast-quickstart/live-event-name.png)<br/>
1. W polu **Opis** należy wprowadzić opcjonalny opis zdarzenia.
1. Wybierz przycisk opcji **"Pass-through– brak opcji" kodowania w chmurze.**<br/>
![Przycisk radiowy kodowania w chmurze](media/live-events-wirecast-quickstart/cloud-encoding.png)
1. Wybierz przycisk radiowy **RTMP.**
1. Upewnij się, że dla zdarzenia na żywo **Rozpocznij** transmisję radiową Wybrano przycisk Brak, aby zapobiec naliczaniu opłat za wydarzenie na żywo, zanim będzie gotowe.  (Rozliczenia rozpoczną się po rozpoczęciu wydarzenia na żywo). ![Przycisk opcji rozpocznij transmisję na żywo](media/live-events-wirecast-quickstart/start-live-event-no.png)<br/>
1. Kliknij przycisk **Przejrzyj + utwórz,** aby przejrzeć ustawienia.
1. Kliknij przycisk **Utwórz,** aby utworzyć wydarzenie na żywo. Następnie nastąpi powrót do widoku aukcji wydarzeń na żywo.
1. Kliknij link do właśnie **utworzonego wydarzenia na żywo.** Należy zauważyć, że zdarzenie zostało zatrzymane.
1. Zachowaj tę stronę otwartą w przeglądarce.  Wrócimy do niego później.

## <a name="setting-up-a-live-stream-with-wirecast-studio"></a>Konfigurowanie transmisji na żywo w Wirecast Studio

1. Zakładając, że nadal masz otwartą aplikację Wirecast, wybierz polecenie **Utwórz pusty dokument** z menu głównego, a następnie kliknij przycisk **Kontynuuj**.
![Ekran startowy wirecast](media/live-events-wirecast-quickstart/open-empty-document.png)
1. Umieść wskaźnik myszy na pierwszej warstwie w obszarze Warstwy Wirecast.  Kliknij wyświetloną ikonę **Dodaj** i wybierz dane wejściowe wideo, które chcesz przesyłać strumieniowo.  Zostanie otwarte okno dialogowe Warstwa wzorcowa 1.<br/>
![Wirecast dodaj ikonę](media/live-events-wirecast-quickstart/add-icon.png)
1. Z menu **wybierz polecenie Przechwytywanie wideo,** a następnie wybierz aparat, którego chcesz użyć. Jeśli wybierzesz kamerę, widok z kamery pojawi się w obszarze Podgląd.
![Ekran wyboru zdjęcia wideo wirecast](media/live-events-wirecast-quickstart/video-shot-selection.png)
1. Umieść wskaźnik myszy na drugiej warstwie w obszarze Warstwy Wirecast. Kliknij wyświetloną ikonę **Dodaj** i wybierz dane wejściowe audio, które chcesz przesyłać strumieniowo.  Zostanie otwarte okno dialogowe Warstwa wzorcowa 2.
1. Z menu **wybierz polecenie Przechwytywanie dźwięku,** a następnie wybierz wejście audio, którego chcesz użyć.
![Ekran wyboru zdjęcia audio wirecast](media/live-events-wirecast-quickstart/audio-shot-select.png)
1. Z menu głównego wybierz pozycję **Ustawienia wyjściowe**.  Zostanie wyświetlone okno dialogowe Dane wyjściowe.
1. Wybierz **usługę Azure Media Services** z listy rozwijanej danych wyjściowych.  Ustawienie danych wyjściowych dla usługi Azure Media Services automatycznie wypełni *większość* ustawień danych wyjściowych.<br/>
![Ekran ustawień wyjścia wirecast](media/live-events-wirecast-quickstart/azure-media-services.png)
1. W następnej sekcji powrócisz do usługi Azure Media Services w przeglądarce, aby skopiować *wejściowy adres URL,* aby wprowadzić go w ustawieniach danych wyjściowych.

### <a name="copy-and-paste-the-input-url"></a>Kopiowanie i wklejanie wejściowego adresu URL

1. Na stronie usługi Azure Media Services w portalu kliknij przycisk **Start,** aby rozpocząć wydarzenie transmisji na żywo. (Rozliczenia rozpoczynają się teraz).<br/>
![Ikona Start](media/live-events-wirecast-quickstart/start.png)
2. Kliknij przełącznik **Bezpieczne/Niezabezpiecz,** aby ustawić go na **Niezabezpieczony**.  Spowoduje to ustawienie protokołu na RTMP zamiast RTMPS.
3. Skopiuj **wejściowy adres URL** do schowka.
![Wejściowy adres URL](media/live-events-wirecast-quickstart/input-url.png)
4. Przełącz się do aplikacji Wirecast i wklej **wejściowy adres URL** w polu **Adres** w ustawieniach danych wyjściowych.<br/>
![Adres URL danych wejściowych wirecast](media/live-events-wirecast-quickstart/input-url-wirecast.png)
5. Kliknij **przycisk Ok**.

## <a name="setting-up-outputs"></a>Konfigurowanie wyjść

Ta część skonfiguruje swoje wyjścia i pozwoli Ci zapisać nagranie transmisji na żywo.  

> [!NOTE]
> Aby przesyłać strumieniowo to dane wyjściowe, punkt końcowy przesyłania strumieniowego musi być uruchomiony.  Zobacz Uruchamianie domyślnej sekcji punktu końcowego przesyłania strumieniowego poniżej.

1. Kliknij **łącze Utwórz wyjścia** poniżej przeglądarki wideo Wyjścia.
1. Jeśli chcesz, edytuj nazwę danych wyjściowych w polu **Nazwa** na coś bardziej przyjaznego dla użytkownika, aby można było je łatwo znaleźć później.
![Pole nazwa wyjściowa](media/live-events-wirecast-quickstart/output-name.png)
1. Zostaw na razie wszystkie pozostałe pola w spokoju.
1. Kliknij **przycisk Dalej** dodaj lokalizator przesyłania strumieniowego.
1. Zmień nazwę lokalizatora na coś bardziej przyjaznego dla użytkownika, jeśli chcesz.
![Pole nazwa lokalizatora](media/live-events-wirecast-quickstart/live-event-locator.png)
1. Zostaw wszystko inne na tym ekranie w spokoju na razie.
1. Kliknij przycisk **Utwórz**.

## <a name="starting-the-broadcast"></a>Rozpoczynanie transmisji

1. W programie Wirecast wybierz polecenie **Wyjście > Start / Stop nadawania > Uruchom usługę Azure Media Services: Usługa Azure Media Services** z menu głównego.  Gdy strumień został wysłany do zdarzenia na żywo, okno na żywo w wirecast pojawi się w odtwarzaczu wideo zdarzenia na żywo na stronie wydarzenia na żywo w usłudze Azure Media Services.

   ![Elementy menu programu Start](media/live-events-wirecast-quickstart/start-broadcast.png)

1. Kliknij przycisk **Przejdź** pod oknem podglądu, aby rozpocząć nadawanie wideo i dźwięku wybranego dla warstw Wirecast.

   ![Przycisk Wirecast Go](media/live-events-wirecast-quickstart/go-button.png)

   > [!TIP]
   > Jeśli wystąpi błąd, spróbuj przeładować gracza, klikając link Przeładuj odtwarzacz nad odtwarzaczem.

## <a name="running-the-default-streaming-endpoint"></a>Uruchamianie domyślnego punktu końcowego przesyłania strumieniowego

1. Upewnij się, że punkt końcowy przesyłania strumieniowego jest uruchomiony, wybierając **punkty końcowe przesyłania strumieniowego** na liście usługi Media Services. Zostaniesz przesuń do strony punktów końcowych przesyłania strumieniowego.<br/>
![Element menu punktu końcowego przesyłania strumieniowego](media/live-events-wirecast-quickstart/streaming-endpoints.png)
1. Jeśli domyślny stan punktu końcowego przesyłania strumieniowego zostanie zatrzymany, kliknij **domyślny** punkt końcowy przesyłania strumieniowego. Spowoduje to wyświetlenie strony dla tego punktu końcowego.
1. Kliknij przycisk **Uruchom**.  Spowoduje to uruchomienie punktu końcowego przesyłania strumieniowego.<br/>
![Element menu punktu końcowego przesyłania strumieniowego](media/live-events-wirecast-quickstart/start.png)

## <a name="play-the-output-broadcast-with-azure-media-player"></a>Odtwarzanie transmisji wyjściowej za pomocą programu Azure Media Player

1. Skopiuj **adres URL przesyłania strumieniowego** w odtwarzaczu wideo wyjściowym.
1. Otwórz demo programu Azure Media Player w przeglądarce sieci Webhttps://ampdemo.azureedge.net/azuremediaplayer.html
1. Wklej **adres URL przesyłania strumieniowego** do pola adresu URL programu Azure Media Player.
1. Kliknij przycisk **Aktualizuj odtwarzacz.**
1. Kliknij ikonę **odtwarzania** na filmie, aby zobaczyć transmisję na żywo.

## <a name="stopping-the-broadcast"></a>Zatrzymywanie transmisji

Jeśli uważasz, że masz wystarczającą ilość treści, zatrzymaj transmisję.

1. W Wirecast, kliknij przycisk **emisji.**  Spowoduje to zatrzymanie transmisji z Wirecast.
1. W portalu kliknij przycisk **Zatrzymaj**. Pojawi się komunikat ostrzegawczy, że strumień na żywo zatrzymać, ale dane wyjściowe staną się teraz zasób na żądanie.
1. Kliknij przycisk **Zatrzymaj** w komunikacie ostrzegawczym. Program Azure Media Player będzie teraz wyświetlał błąd, ponieważ strumień na żywo nie jest już dostępny.

## <a name="play-the-on-demand-output-with-the-azure-media-player"></a>Odtwarzanie danych wyjściowych na żądanie za pomocą programu Azure Media Player

Dane wyjściowe, które zostały utworzone, są teraz dostępne dla przesyłania strumieniowego na żądanie, o ile punkt końcowy przesyłania strumieniowego jest uruchomiony.

1. Przejdź do listy usługi Media Services i wybierz pozycję **Zasoby**.
1. Znajdź dane wyjściowe zdarzenia utworzone wcześniej i kliknij **łącze do zasobu**. Zostanie otwarta strona wyjściowa zasobu.
1. Skopiuj **adres URL przesyłania strumieniowego** pod odtwarzaczem wideo dla zasobu.
1. Wróć do programu Azure Media Player w przeglądarce i wklej **adres URL przesyłania strumieniowego** do pola adresu URL programu Azure Media Player.
1. Kliknij **pozycję Aktualizuj odtwarzacz**.
1. Kliknij ikonę **odtwarzania** na filmie, aby wyświetlić zasób na żądanie.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

> [!IMPORTANT]
> Zatrzymaj usługi! Po wykonaniu kroków w tym przewodniku Szybki start, należy pamiętać, aby zatrzymać zdarzenie na żywo i punktu końcowego przesyłania strumieniowego lub nadal będą naliczane za czas, w którym pozostają uruchomione. Aby zatrzymać wydarzenie na żywo, zobacz Zatrzymanie transmisji, kroki 2 i 3 powyżej.

### <a name="stopping-the-streaming-endpoint"></a>Zatrzymywanie punktu końcowego przesyłania strumieniowego

1. Na liście usługi Media Services wybierz pozycję **Punkty końcowe przesyłania strumieniowego**.
2. Kliknij **domyślny** punkt końcowy przesyłania strumieniowego, który został uruchomiony wcześniej. Spowoduje to otwarcie strony punktu końcowego.
3. Kliknij **na Stop**.  Spowoduje to zatrzymanie punktu końcowego przesyłania strumieniowego.

> [!TIP]
> Jeśli nie chcesz, aby zasoby z tego zdarzenia, należy je usunąć, aby zapobiec naliczane do magazynu.

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Jaki artykuł jest następny w kolejności](./live-events-outputs-concept.md)
