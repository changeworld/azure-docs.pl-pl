---
title: Monitorowanie aplikacji sieci web za pomocą wieloetapowe testy sieci web i usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Konfigurowanie wieloetapowe testy sieci web do monitorowania aplikacji sieci web za pomocą usługi Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304877"
---
# <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

Można monitorować zarejestrowane sekwencję adresów URL i interakcje z witryny sieci Web za pośrednictwem wieloetapowe testy sieci web. W tym artykule opisano proces tworzenia wieloetapowego testu sieci web za pomocą programu Visual Studio Enterprise.

> [!NOTE]
> Wieloetapowe testy sieci web ma dodatkowych kosztów związanych z nimi. Aby dowiedzieć się więcej, zobacz [oficjalny przewodnik cen](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Wymagania wstępne

* Visual Studio Enterprise 2017 r. lub nowszej.
* Wydajność sieci web w usłudze Visual Studio i narzędzia do testowania obciążenia.

Aby zlokalizować wstępnie wymagane narzędzia do testowania. Uruchom **Instalatora programu Visual Studio** > **poszczególne składniki** > **debugowanie i testowanie**  >   **Wydajności sieci Web i narzędzia do testowania obciążenia**.

![Zrzut ekranu przedstawiający interfejs użytkownika Instalatora programu Visual Studio za pomocą poszczególnych składników wybrane za pomocą pola wyboru obok pozycji narzędzia do testowania obciążenia i wydajności sieci Web](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Rekord wieloetapowego testu sieci web

Aby utworzyć test wieloetapowy, nagraj scenariusz przy użyciu programu Visual Studio Enterprise, a następnie przekaż nagranie do usługi Application Insights. Usługa Application Insights odtwarza ten scenariusz w ustalonych odstępach czasu i weryfikuje odpowiedzi.

> [!IMPORTANT]
> * W testach nie można używać pętli ani funkcji kodowanych. Test musi być całkowicie zawarty w skrypcie webtest. Można jednak używać wtyczek standardowych.
> * W wieloetapowych testach internetowych obsługiwane są tylko angielskie znaki. Jeśli używasz programu Visual Studio w innych językach, zaktualizuj plik definicji testu internetowego w celu zmiany/wykluczenia znaków innych niż angielskie.

Nagraj sesję sieci Web w programie Visual Studio Enterprise.

1. Utwórz projekt testu obciążenia i wydajności sieci Web. **File** > **New** > **Project** > **Visual C#**  > **Test**

    ![Visual Studio nowy projekt interfejsu użytkownika](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Otwórz `.webtest` plik i Rozpocznij nagrywanie.

    ![Visual Studio test recording UI](./media/availability-multistep/open-web-test.png)

3. Klikaj elementy kroki mają test w celu symulowania jako część rejestrowania.

    ![Rejestrowanie działania interfejsu użytkownika przeglądarki](./media/availability-multistep/record.png)

4. Zmodyfikuj test, aby:

    * Dodać operacje sprawdzania poprawności odebranego tekstu i kodów odpowiedzi,
    * Usuń wszelkie interakcje uneccesary. Można również usunąć zależne żądania dotyczące obrazów lub Dodaj witryny śledzenia, które nie są istotne dla Ciebie, biorąc pod uwagę test sukcesu.
    
    Należy pamiętać, że możesz edytować tylko skrypt testu — można dodać niestandardowy kod lub wywoływać innych testów sieci web. Nie wstawiaj pętli do testu. Możesz używać standardowych wtyczek testów sieci Web.

5. Uruchom test w programie Visual Studio do sprawdzania poprawności i upewnij się, że działa.

    Moduł uruchamiający testy sieci web otwiera przeglądarkę sieci Web i powtarza nagrane akcje. Upewnij się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="upload-the-web-test"></a>Przekaż test sieci web

1. W portalu usługi Application Insights, w okienku dostępności wybierz **utworzyć Test** > **typ testu** > **wieloetapowego testu sieci web**.

2. Ustaw lokalizacje testu, częstotliwość i parametry alertu.

### <a name="frequency--location"></a>Częstotliwość & lokalizację

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Częstotliwość testów**| Określa, jak często wykonywany jest test w poszczególnych lokalizacjach testowych. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testu**| Są to miejsca, z których nasze serwery wysyłają żądania sieci web do adresu URL. **Nasze minimalna liczba lokalizacji testowych zalecane jest pięć** aby upewnić się, że móc odróżnić problemy z witryną od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

### <a name="success-criteria"></a>Kryteria powodzenia

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testu** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest uznawany za sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowania zawartości** | Ciąg, np. "Witaj!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Obsługiwane są wyłącznie znaki alfabetu angielskiego z dopasowania zawartości** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Niemal w czasie rzeczywistym (wersja zapoznawcza)** | Firma Microsoft zaleca korzystanie z alertów w czasie zbliżonym do rzeczywistego. Konfigurowanie tego rodzaju alert odbywa się po utworzeniu testu dostępności.  |
|**Wdrożenie klasyczne** | Nie zaleca się przy użyciu alertów klasycznych dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3 na dobę, 5 lokalizacjach. Jest optymalne relacji między próg lokalizacji alertu i liczba lokalizacji testowych **próg lokalizacji alertu** = **liczba lokalizacji testowych - 2, co najmniej 5 lokalizacjach testu.**|

## <a name="advanced-configuration"></a>Konfiguracja zaawansowana

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Podłączenie czasu i liczb losowych do testu

Załóżmy, że testujesz narzędzie, które pobiera dane zależne od czasu (np. ceny akcji) z zewnętrznego źródła. Podczas rejestrowania testu sieci Web należy używać określonych godzin, ale ustawionych jako parametry testu: StartTime (Godzina rozpoczęcia) i EndTime (Godzina zakończenia).

![Zrzut ekranu mojego podstawowych aplikacji](./media/availability-multistep/app-insights-72webtest-parameters.png)

Po uruchomieniu testu parametr EndTime powinien zawsze zawierać aktualny czas, a StartTime wartość czasu 15 minut wcześniej.

Web Test wtyczka daty i godziny zapewnia sposób obsługi Parametryzowanie czasu.

1. Dodaj wtyczkę testu sieci Web do każdej wartości parametru zmiennej, która jest potrzebna. Na pasku narzędzi testu sieci Web wybierz polecenie **Dodaj wtyczkę testu sieci Web**.
    
    ![Dodaj wtyczkę testu sieci Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    W tym przykładzie użyte zostaną dwa wystąpienia wtyczki typu Data i godzina. Jedno wystąpienie odpowiada wartości „15 minut temu”, a drugie „teraz”.

2. Otwórz właściwości każdej wtyczki. Nadaj jej nazwę i skonfiguruj, aby używać czasu bieżącego. W jednej z wtyczek ustaw właściwość Dodaj minuty = -15.

    ![Parametry kontekstu](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. W parametrach testu sieci Web użyj {{nazwa wtyczki}}, aby odwoływać się do nazwy wtyczki.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teraz przekaż test do portalu. Wartości dynamiczne zostaną zastosowane w każdym przebiegu testu.

### <a name="dealing-with-sign-in"></a>Obsługa logowania

Jeśli użytkownicy logują się do aplikacji, dostępne są różne opcje symulowania logowania, które pozwolą przetestować strony dostępne po zalogowaniu. Zastosowane podejście zależy od typu zabezpieczeń zapewnianych przez aplikację.

We wszystkich przypadkach należy utworzyć konto w ramach aplikacji tylko na potrzeby testowania. Jeśli to możliwe, należy ograniczyć uprawnienia tego konta testowego, aby nie było możliwości, że testy sieci Web będą miały wpływ na rzeczywistych użytkowników.

**Prosta nazwa użytkownika i hasło** Nagraj test sieci web w zwykły sposób. Najpierw usuń pliki cookie.

**Uwierzytelnianie SAML** Użyj wtyczki SAML, która jest dostępna do testów sieci web. Dostęp do wtyczki według...

**Klucz tajny klienta** Jeśli aplikacja ma trasę logowania, która obejmuje klucz tajny klienta, użyj tej trasy. Azure Active Directory (AAD) to przykład usługi, która umożliwia logowanie za pomocą klucza tajnego klienta. W usłudze AAD klucz tajny klienta jest kluczem aplikacji.

Poniżej przedstawiono przykładowy test internetowy aplikacji internetowej platformy Azure przy użyciu klucza aplikacji:

![Zrzut ekranu przykładu](./media/availability-multistep/client-secret.png)

Pobierz token z usługi AAD przy użyciu klucza tajnego klienta (AppKey).
Wyodrębnij token elementu nośnego z odpowiedzi.
Wywołaj interfejs API przy użyciu tokenu elementu nośnego w nagłówku autoryzacji.
Upewnij się, że test sieci web jest rzeczywistym klientem — to znaczy ma własną aplikację w usłudze AAD — i użyj jego identyfikatora klienta i aplikację klucza. Testowana usługa również ma własną aplikację w usłudze AAD: identyfikator URI Identyfikatora tej aplikacji jest uwzględniany w teście sieci web w polu zasobu.

### <a name="open-authentication"></a>Uwierzytelnianie otwarte
Przykładem uwierzytelniania otwartego jest logowanie przy użyciu konta Microsoft lub Google. Wiele aplikacji, które używają protokołu OAuth, zapewnia alternatywną obsługę klucza tajnego klienta, więc przede wszystkim należy zbadać tę możliwość.

Jeśli w ramach testu należy zalogować się przy użyciu protokołu OAuth, ogólne podejście jest następujące:

Użyj narzędzia takiego jak Fiddler, aby sprawdzić ruch między przeglądarką sieci Web, witryną uwierzytelniającą a aplikacją.
Wykonaj co najmniej dwa logowania na różnych komputerach lub w różnych przeglądarkach albo w długich odstępach czasu (umożliwi to wygaśnięcie tokenów).
Porównując różne sesje, zidentyfikuj token przekazywany z powrotem z witryny uwierzytelniającej, który jest następnie przekazywany do serwera aplikacji po zalogowaniu.
Nagraj test sieci Web przy użyciu programu Visual Studio.
Sparametryzuj tokeny, ustawiając parametr, gdy token jest zwracany z witryny uwierzytelniającej i używając go w zapytaniu do tej witryny. Program Visual Studio podejmie próby parametryzacji testu, ale parametryzacja tokenów nie przebiegnie poprawnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

W wersji dedykowanej [artykule dotyczącym rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Kolejne kroki

* [Alerty dostępności](availability-alerts.md)
* [Testy sieci web ping adresu URL](monitor-web-app-availability.md)
