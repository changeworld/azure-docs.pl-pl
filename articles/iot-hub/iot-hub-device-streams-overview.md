---
title: Strumieni urządzenia w usłudze Azure IoT Hub (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Omówienie strumieni urządzenia usługi IoT Hub.
author: rezasherafat
manager: briz
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: rezas
ms.openlocfilehash: eba08521cb28a29a08c8e4e48f0ec321cb0397c7
ms.sourcegitcommit: e5dcf12763af358f24e73b9f89ff4088ac63c6cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2019
ms.locfileid: "67137347"
---
# <a name="iot-hub-device-streams-preview"></a>Strumienie urządzenia Centrum IoT (wersja zapoznawcza)

Usługa Azure IoT Hub *strumieni urządzenia* ułatwia tworzenie bezpiecznych tuneli protokołu TCP dwukierunkowej dla różnych scenariuszy komunikacji z chmury do urządzenia. Strumień urządzeń są przenoszone przez przez usługę IoT Hub *punkt końcowy przesyłania strumieniowego* który działa jako serwer proxy między punktami końcowymi usług i urządzeń. Ten Instalator przedstawiony na diagramie poniżej, jest szczególnie przydatne, gdy urządzenia zapory sieciowej lub znajdują się w sieci prywatnej. W efekcie strumieni urządzenia usługi IoT Hub pomagają klientów adres trzeba osiągnąć urządzeń IoT w sposób przyjaznego dla zapory oraz bez konieczności szeroko otwarcia portów zapory sieciowych przychodzących lub wychodzących.

!["Omówienie strumieni urządzenia usługi IoT Hub"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

Stosowanie strumieni urządzenia usługi IoT Hub, urządzenia pozostaną bezpieczne i wystarczy otworzyć połączenia wychodzące TCP do punktu końcowego przesyłania strumieniowego usługi IoT hub za pośrednictwem portu 443. Po ustanowieniu strumienia usługi po stronie urządzenia i aplikacje każdy będzie miał dostęp programowy do obiektu klienta protokołu WebSocket, wysyłanie i odbieranie bajtów raw ze sobą. Niezawodność i kolejność gwarancje udostępniane przez ten tunel jest zgodna z protokołu TCP.

## <a name="benefits"></a>Korzyści

Strumienie urządzenia usługi IoT Hub zapewniają następujące korzyści:

* **Bezpieczna łączność przyjaznego dla zapory:** Urządzenia IoT jest osiągalna z punktów końcowych usług bez otwierania portu zapory dla ruchu przychodzącego w granicach urządzeń lub sieci (tylko łączności wychodzącej do usługi IoT Hub jest wymagane przez port 443).

* **Uwierzytelnianie:** Strony usług i urządzeń tunelu konieczne uwierzytelnianie za pomocą usługi IoT Hub przy użyciu odpowiednich poświadczeń.

* **Szyfrowanie:** Domyślnie strumieni urządzenia usługi IoT Hub za pomocą połączeń z obsługą protokołu TLS. Dzięki temu dane są zawsze szyfrowane niezależnie od tego, czy aplikacja używa szyfrowania, czy nie.

* **Prostota połączenia:** W wielu przypadkach korzystanie z urządzeń, strumieni eliminuje potrzebę stosowania złożonych konfiguracji wirtualnych sieci prywatnych do zapewniania łączności urządzeń IoT.

* **Zgodność z stos TCP/IP:** Strumienie urządzenia usługi IoT Hub może obsłużyć TCP/IP ruchu aplikacji. Oznacza to, że szeroką gamę protokołów własnościowych, jak również standardami mogą korzystać z tej funkcji.

* **Łatwość użycia w konfiguracji sieci prywatnej.** Usługa może komunikować się z urządzeniem, odwołując się do jego identyfikator urządzenia, a nie adres IP urządzenia. Jest to przydatne w sytuacjach, gdy urządzenie znajduje się w sieci prywatnej i ma prywatny adres IP, lub jego adres IP jest przypisywany dynamicznie i nie jest znana po stronie usługi.

## <a name="device-stream-workflows"></a>Przepływy pracy strumienia urządzenia

Strumień urządzeń jest inicjowane, gdy usługa żądań nawiązać połączenia z urządzeniem, podając jego identyfikator urządzenia. Ten przepływ pracy szczególnie pasuje do modelu komunikacji klient serwer, łącznie z protokołu SSH i RDP, gdy użytkownik nie chce zdalne połączenia z serwerem SSH lub RDP, działające na urządzeniu za pomocą programu klienta SSH lub RDP.

Proces tworzenia strumienia urządzenia wymaga negocjacji między urządzeniem, usługi, główny usługi IoT hub i punkty końcowe przesyłania strumieniowego. Gdy główny punkt końcowy usługi IoT hub organizuje tworzenia strumienia urządzenia, punkt końcowy przesyłania strumieniowego obsługuje ruch, który przepływa między usługą i urządzeniem.

### <a name="device-stream-creation-flow"></a>Przepływ tworzenia strumienia urządzenia

Programowe tworzenie strumienia urządzenia przy użyciu zestawu SDK obejmuje następujące kroki, które są także przedstawione na poniższej ilustracji:

!["Urządzenie procesu uzgadniania strumienia"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Aplikacja urządzenia rejestruje wywołanie zwrotne z wyprzedzeniem, aby otrzymywać powiadomienia o po zainicjowaniu nowy strumień urządzenia na urządzeniu. W tym kroku zwykle ma miejsce, gdy urządzenie jest uruchamiany i łączy się z Centrum IoT.

2. Program po stronie usługi inicjuje strumień urządzeń, w razie, podając identyfikator urządzenia (_nie_ adresu IP).

3. Usługa IoT hub powiadamia program po stronie urządzenia za pomocą wywołania zwrotnego, zarejestrowaną w kroku 1. Urządzenie może zaakceptować lub odrzucić żądanie inicjowania strumienia. Tę logikę można określoną dla scenariusza aplikacji. W przypadku odrzucenia żądania przesyłania strumieniowego przez urządzenie usługi IoT Hub informuje usługę. w przeciwnym razie wykonaj poniższe kroki.

4. Urządzenie tworzy bezpieczne połączenie TCP ruchu wychodzącego do punktu końcowego przesyłania strumieniowego za pośrednictwem portu 443 i uaktualniania połączenia do protokołu WebSocket. Adres URL punktu końcowego przesyłania strumieniowego, a także poświadczenia na potrzeby uwierzytelniania zarówno znajdują się na urządzeniu przez usługę IoT Hub jako część żądania wysłanego w kroku 3.

5. Ta usługa jest powiadamiany o wyniku urządzenia akceptowanie strumienia i przechodzi do utworzenia własnego klienta protokołu WebSocket do punktu końcowego przesyłania strumieniowego. Podobnie otrzymuje informacje na temat przesyłania strumieniowego punktu końcowego adresu URL i uwierzytelniania z usługi IoT Hub.

W trakcie procesu uzgadniania powyżej:

* Proces uzgadniania musi zostać zakończone w ciągu 60 sekund (krok 2 do 5), w przeciwnym razie uzgadnianie może zakończyć się niepowodzeniem z limitem czasu i usługi zostanie o tym powiadomiony.

* Po ukończeniu powyżej przepływ tworzenia strumienia punkt końcowy przesyłania strumieniowego będzie działał jako serwer proxy i będzie przesyłać ruch między usługą i urządzeniem za pośrednictwem ich odpowiednich funkcji WebSockets.

* Urządzenia i usługi muszą łączności wychodzącej do główny punkt końcowy usługi IoT Hub, a także punkt końcowy przesyłania strumieniowego za pośrednictwem portu 443. Adres URL z tych punktów końcowych dostępnej w *Przegląd* karta w portalu Centrum IoT.

* Niezawodność i kolejność gwarantuje strumień ustanowionych jest zgodna z protokołu TCP.

* Wszystkie połączenia usługi IoT Hub i punkt końcowy przesyłania strumieniowego Użyj TLS i są szyfrowane.

### <a name="termination-flow"></a>Kończenie działania przepływu

Strumień ustanowionych kończy działanie, gdy jedno z połączeń TCP do bramy bez dostępu do sieci (przez usługę lub urządzenie). Może to mieć miejsce dobrowolnie przez zamknięcie WebSocket na urządzenia lub usługi, programy lub niezamierzony w razie awarii limitu czasu lub proces łączności sieciowej. Po zakończeniu urządzenia lub połączenia usługi do punktu końcowego przesyłania strumieniowego połączenie TCP również (wymuszone) przestaną obowiązywać i usługi i urządzenia są odpowiedzialni za odtworzyć strumień, w razie potrzeby.

## <a name="connectivity-requirements"></a>Wymagania dotyczące łączności

Urządzenia i stronach usługi stream urządzenie musi być nawiązywanie połączeń z obsługą protokołu TLS do Centrum IoT i jego punktu końcowego przesyłania strumieniowego. Wymaga to łączności wychodzącej za pośrednictwem portu 443 z tymi punktami końcowymi. Nazwa hosta skojarzony z tymi punktami końcowymi można znaleźć na *Przegląd* kartę usługi IoT Hub, jak pokazano na poniższej ilustracji:

!["Urządzenie strumienia punkty końcowe"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatywnie informacji punkty końcowe użycia można pobrać przy użyciu wiersza polecenia platformy Azure w Centrum w sekcji właściwości, w szczególności `property.hostname` i `property.deviceStreams` kluczy.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

Wynikiem jest obiekt JSON, wszystkich punktów końcowych wymagających Twojego Centrum usług i urządzeń mogą się połączyć, aby ustanowić strumień urządzeń.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Upewnij się, zainstalowano wiersza polecenia platformy Azure w wersji 2.0.57 lub nowszej. Możesz pobrać najnowszą wersję z [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) strony.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Zezwalaj na połączenia wychodzącego do urządzeń, punkty końcowe przesyłania strumieniowego

Jak wspomniano na początku tego artykułu, urządzenie tworzy połączenie wychodzące do punktu końcowego przesyłania strumieniowego usługi IoT Hub podczas procesu inicjowania strumieni urządzenia. Na urządzeniu lub w jego sieci zapory muszą zezwalać na łączności wychodzącej do przesyłania strumieniowego bramy za pośrednictwem portu 443 (należy zauważyć, że komunikacja odbywa się za pośrednictwem połączenia protokołu WebSocket, który jest szyfrowana przy użyciu protokołu TLS).

Nazwa hosta punktu końcowego przesyłania strumieniowego urządzeń można znaleźć w portalu usługi Azure IoT Hub na karcie Przegląd. !["Urządzenie strumienia punkty końcowe"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Alternatywnie można znaleźć te informacje przy użyciu wiersza polecenia platformy Azure:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Upewnij się, zainstalowano wiersza polecenia platformy Azure w wersji 2.0.57 lub nowszej. Możesz pobrać najnowszą wersję z [interfejsu wiersza polecenia platformy Azure Zainstaluj](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) strony.
>

## <a name="troubleshoot-via-device-streams-activity-logs"></a>Rozwiązywanie problemów za pomocą urządzenia strumienie dzienników aktywności

Dzienniki usługi Azure Monitor można skonfigurować do zbierania dziennika aktywności strumieni urządzenia w usłudze IoT Hub. Może to być bardzo przydatne w Rozwiązywanie problemów ze scenariuszami.

Wykonaj poniższe kroki, aby skonfigurować dzienniki usługi Azure Monitor działań strumień urządzenie usługi IoT Hub:

1. Przejdź do *ustawień diagnostycznych* kartę w usłudze IoT Hub, a następnie kliknij *Włącz diagnostykę* łącza.

   !["Włączanie dzienniki diagnostyczne"](./media/iot-hub-device-streams-overview/device-streams-diagnostics-settings-pane.png)

2. Podaj nazwę dla ustawienia diagnostyki, a następnie wybierz *wysyłanie do usługi Log Analytics* opcji. Przejdziesz do istniejącego zasobu obszaru roboczego usługi Log Analytics wybierz lub Utwórz nową. Dodatkowo sprawdź *DeviceStreams* z listy.

    !["Włącz dzienniki strumieni urządzeń"](./media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png)

3. Teraz możesz uzyskać dostęp do dzienników strumieni urządzenia w obszarze *dzienniki* karta w portalu usługi IoT Hub. Strumieniowe przesyłanie dzienników aktywności — urządzenie pojawi się w `AzureDiagnostics` tabeli i mieć `Category=DeviceStreams`.

   Jak pokazano poniżej, tożsamości na urządzeniu docelowym i wynik operacji jest również dostępna w dziennikach.

   !["Dostęp do urządzenia strumieniowe przesyłanie dzienników"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

## <a name="regional-availability"></a>Dostępność regionalna

W publicznej wersji zapoznawczej strumienie urządzenia usługi IoT Hub są dostępne w regionach środkowe stany USA i centralnym stany USA — EUAP. Upewnij się, że Utwórz koncentrator w jednym z tych regionów.

## <a name="sdk-availability"></a>Dostępność zestawu SDK

Dwa boki każdego strumienia (po stronie usług i urządzeń) Użyj zestawu SDK usługi IoT Hub, aby ustanowić tunel. W publicznej wersji zapoznawczej klienci mogą wybierać spośród następujących języków zestawu SDK:

* C i C# przez zestaw SDK obsługuje strumienie urządzenie po stronie urządzenia.

* NodeJS i C# zestaw SDK obsługuje strumienie urządzenie po stronie usługi.

## <a name="iot-hub-device-stream-samples"></a>Przykłady strumień urządzenie usługi IoT Hub

Istnieją dwa [quickstart — przykłady](/azure/iot-hub) dostępny na stronie Centrum IoT Hub. Korzystanie z urządzeń, strumieni pokazują przez aplikacje.

* *Echo* w przykładzie pokazano programowe korzystanie z urządzeń, strumieni (przez bezpośrednie wywoływanie interfejsów API zestawu SDK).

* *Lokalnego serwera proxy* w przykładzie pokazano tunelowania ruchu aplikacji gotowych klient/serwer (na przykład protokołu SSH, protokołu RDP lub sieci web) za pośrednictwem urządzenia strumieni.

Te przykłady zostały omówione bardziej szczegółowo poniżej.

### <a name="echo-sample"></a>Przykładowe echo

Przykład echo pokazuje programowe używanie strumieni urządzenia, wysyłanie i odbieranie bajtów między aplikacjami usług i urządzeń. Należy pamiętać, że można użyć programów usług i urządzeń w różnych językach. Na przykład, można użyć programu urządzenia C za pomocą C# program usługi.

Poniżej przedstawiono przykłady echo:

* [C#usługi i usługi programu](quickstart-device-streams-echo-csharp.md)

* [Program usługi node.js](quickstart-device-streams-echo-nodejs.md)

* [Program urządzenia języka C](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Przykład lokalnego serwera proxy (dla protokołu SSH lub RDP)

Przykład serwera proxy w lokalnych pokazuje sposób włączyć tunelowanie ruchu istniejącej aplikacji, które polega na komunikacji między klientem a serwerem programu. Ten zestaw się sprawdza w przypadku protokołów klient serwer, takich jak SSH i RDP, gdzie po stronie usługi działa jako klient (uruchomione programy klienta SSH lub RDP), a po stronie urządzenia działa jako serwer (uruchomiony demon SSH lub RDP programy serwera).

W tej sekcji opisano używanie strumieni urządzenia umożliwiające użytkownikowi SSH na urządzeniu za pośrednictwem urządzenia strumieni (w przypadku protokołu RDP lub innej aplikacji klienta i serwera są podobne, używając odpowiedniego portu protokołu).

Instalator korzysta z dwóch *lokalnego serwera proxy* programy przedstawione na rysunku poniżej, a mianowicie *urządzenia lokalnego serwera proxy* i *lokalnej usługi serwera proxy*. Programy lokalnego serwera proxy są odpowiedzialni za [urządzenia strumienia inicjowania uzgadnianie](#device-stream-creation-flow) za pomocą usługi IoT Hub i wchodzenie w interakcje przy użyciu klienta SSH i demon SSH używanie gniazd regularne klient/serwer.

!["Urządzenie strumienia instalacji serwera proxy dla protokołu RDP/SSH"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. Użytkownik uruchamia lokalnej usługi serwera proxy, aby zainicjować strumień urządzenia na urządzeniu.

2. Serwer proxy urządzenia lokalnego akceptuje żądania rozpoczęcia przesyłania strumieniowego i utworzeniu tunelu do punktu końcowego przesyłania strumieniowego usługi IoT Hub (zgodnie z opisem powyżej).

3. Serwer proxy lokalne urządzenie łączy się punkt końcowy demon SSH nasłuchuje na porcie 22 na urządzeniu.

4. Lokalne usługi Serwer proxy nasłuchuje na porcie wyznaczonym oczekiwanie na nowe połączenia SSH z użytkownika (port 2222, używany w przykładzie, ale można skonfigurować dostępny port). Użytkownik wskazuje klienta SSH port serwera proxy usługi lokalnej na hoście lokalnym.

### <a name="notes"></a>Uwagi

* Powyższe kroki ukończyć tunel typu end-to-end między klientem protokołu SSH (po prawej stronie) do demona SSH (po lewej stronie). Część tego połączenie end-to-end polega na wysyłanie ruchu za pomocą strumienia urządzenia do usługi IoT Hub.

* Strzałki na rysunku wskazuje kierunek, w którym nawiązywane są połączenia między punktami końcowymi. W szczególności należy pamiętać, że nie ma żadnych połączeń przychodzących, przechodząc do urządzenia (to jest często blokowane przez zaporę).

* Wybór przy użyciu portu 2222 w serwerze proxy, lokalne usługi jest wybór dowolnego. Serwer proxy można skonfigurować do użycia dostępny port.

* Wybrany port 22 jest w tym przypadku zależnych od protokołu i specyficzne dla protokołu SSH. W przypadku protokołu RDP należy użyć portu 3389. Można to skonfigurować w podanych przykładowych programów.

Użyj poniższych linków, aby uzyskać instrukcje na temat sposobu uruchamiania programów lokalnego serwera proxy w wybranym języku. Podobnie jak [echo przykład](#echo-sample), lokalnych urządzeń i usługi serwera proxy programy można uruchamiać w różnych językach, ponieważ są one pełni współdziałały.

* [C#usługi i usługi programu](quickstart-device-streams-proxy-csharp.md)

* [Program usługi node.js](quickstart-device-streams-proxy-nodejs.md)

* [Program urządzenia języka C](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat urządzeń, strumieni, skorzystaj z poniższych łączy.

> [!div class="nextstepaction"]
> [Strumienie urządzenia na IoT Pokaż (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)