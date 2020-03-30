---
title: Monitoruj za pomocą wieloetapowych testów sieci Web — usługa Azure Application Insights
description: Konfigurowanie wieloetapowych testów sieci Web w celu monitorowania aplikacji sieci Web za pomocą usługi Azure Application Insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655944"
---
# <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

Możesz monitorować zarejestrowaną sekwencję adresów URL i interakcji ze stroną internetową za pomocą wieloetapowych testów internetowych. W tym artykule przejdziesz przez proces tworzenia wieloetapowego testu sieci web za pomocą programu Visual Studio Enterprise.

> [!NOTE]
> Wieloetapowe testy sieci web zależą od plików webtest programu Visual Studio. Ogłoszono, [announced](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) że Visual Studio 2019 będzie ostatnią wersją z funkcją webtest. Ważne jest, aby zrozumieć, że podczas gdy żadne nowe funkcje zostaną dodane, webtest funkcje w programie Visual Studio 2019 jest nadal obsługiwane i nadal będą obsługiwane podczas cyklu pomocy technicznej produktu. Zespół produktów usługi Azure Monitor zajął się pytaniami dotyczącymi przyszłości wieloetapowych testów dostępności [w tym miejscu.](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101)  

## <a name="pre-requisites"></a>Wymagania wstępne

* Visual Studio 2017 Enterprise lub więcej.
* Narzędzia do testowania wydajności sieci Web i testowania obciążenia programu Visual Studio.

Aby zlokalizować narzędzia testowe wymagane. Uruchom**składniki** >  **Programu Visual Studio Installer** > Individual**Debugowanie i testowanie** > **narzędzi do testowania wydajności sieci Web i testowania obciążenia**.

![Zrzut ekranu przedstawiający interfejs użytkownika instalatora programu Visual Studio z zaznaczonymi poszczególnymi składnikami z polem wyboru obok elementu dla narzędzi do testowania wydajności sieci Web i testowania obciążenia](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Wieloetapowe testy internetowe wiążą się z dodatkowymi kosztami. Aby dowiedzieć się więcej, zapoznaj się z [oficjalnym przewodnikiem cen .](https://azure.microsoft.com/pricing/details/application-insights/)

## <a name="record-a-multi-step-web-test"></a>Rejestrowanie wieloetapowego testu sieci Web 

> [!WARNING]
> Nie zalecamy już używania rejestratora wieloetapowego. Rejestrator został opracowany dla statycznych stron HTML z podstawowymi interakcjami i nie zapewnia funkcjonalnego doświadczenia dla nowoczesnych stron internetowych.

Aby uzyskać wskazówki dotyczące tworzenia testów internetowych programu Visual Studio, zapoznaj się z [oficjalną dokumentacją programu Visual Studio 2019](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Prześlij test internetowy

1. W portalu usługi Application Insights w okienku Dostępność wybierz pozycję **Utwórz** > **test testowy typu** > **Wieloetapowy test sieci Web**.

2. Ustaw lokalizacje testowe, częstotliwość i parametry alertu.

### <a name="frequency--location"></a>Lokalizacja & częstotliwości

|Ustawienie| Wyjaśnienie
|----|----|----|
|**Częstotliwość badania**| Ustawia, jak często test jest uruchamiany z każdej lokalizacji testowej. Przy domyślnej częstotliwości równej 5 minut i 5 lokalizacjach testu witryna będzie testowana średnio co minutę.|
|**Lokalizacje testowe**| Czy miejsca, z których nasze serwery wysyłają żądania internetowe do Twojego adresu URL. **Nasza minimalna liczba zalecanych lokalizacji testowych wynosi pięć,** aby zapewnić, że można odróżnić problemy w witrynie od problemów z siecią. Wybrać można maksymalnie 16 lokalizacji.

### <a name="success-criteria"></a>Kryteria sukcesu

|Ustawienie| Wyjaśnienie
|----|----|----|
| **Limit czasu testowania** |Zmniejsz tę wartość, aby otrzymywać alerty o powolnych odpowiedziach. Test jest uznawany za błąd, jeśli w tym okresie nie odebrano odpowiedzi z witryny. W przypadku wybrania opcji **Analizuj zależne żądania** wszystkie obrazy, pliki stylów, skrypty i inne zasoby zależne muszą zostać odebrane w tym okresie.|
| **Odpowiedź HTTP** | Zwrócony kod stanu, który jest liczony jako sukces. Kod 200 oznacza, że została zwrócona normalna strona sieci Web.|
| **Dopasowanie zawartości** | Ciąg, jak "Witamy!" Sprawdzamy, czy w każdej odpowiedzi występuje dokładna zgodność pod względem wielkości liter. Musi to być zwykły ciąg znaków bez symboli wieloznacznych. Pamiętaj, że w razie zmiany zawartości strony może być konieczne zaktualizowanie tego ciągu. **Tylko znaki angielskie są obsługiwane przez dopasowanie zawartości** |

### <a name="alerts"></a>Alerty

|Ustawienie| Wyjaśnienie
|----|----|----|
|**W czasie zbliżonym do rzeczywistego (wersja zapoznawcza)** | Zalecamy korzystanie z alertów w czasie rzeczywistym. Konfigurowanie tego typu alertów odbywa się po utworzeniu testu dostępności.  |
|**Wdrożenie klasyczne** | Nie zaleca się już używania klasycznych alertów dla nowych testów dostępności.|
|**Próg lokalizacji alertu**|Zalecamy co najmniej 3/5 lokalizacji. Optymalna relacja między wartością progową lokalizacji alertu a liczbą lokalizacji testowych jest progowa liczba **lokalizacji** = **alertów lokalizacji testowych — 2, z co najmniej pięcioma lokalizacjami testowymi.**|

## <a name="configuration"></a>Konfigurowanie

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Podłączanie czasu i liczb losowych do testu

Załóżmy, że testujesz narzędzie, które pobiera dane zależne od czasu (np. ceny akcji) z zewnętrznego źródła. Podczas rejestrowania testu sieci Web należy używać określonych godzin, ale ustawionych jako parametry testu: StartTime (Godzina rozpoczęcia) i EndTime (Godzina zakończenia).

![Mój niesamowity zrzut ekranu aplikacji stockowych](./media/availability-multistep/app-insights-72webtest-parameters.png)

Po uruchomieniu testu parametr EndTime powinien zawsze zawierać aktualny czas, a StartTime wartość czasu 15 minut wcześniej.

Wtyczka data daty testu sieci Web umożliwia obsługę parametryzacji czasów.

1. Dodaj wtyczkę testu sieci Web do każdej wartości parametru zmiennej, która jest potrzebna. Na pasku narzędzi testu sieci Web wybierz polecenie **Dodaj wtyczkę testu sieci Web**.
    
    ![Dodawanie wtyczki testu sieci Web](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    W tym przykładzie użyte zostaną dwa wystąpienia wtyczki typu Data i godzina. Jedno wystąpienie odpowiada wartości „15 minut temu”, a drugie „teraz”.

2. Otwórz właściwości każdej wtyczki. Nadaj jej nazwę i skonfiguruj, aby używać czasu bieżącego. W jednej z wtyczek ustaw właściwość Dodaj minuty = -15.

    ![Parametry kontekstu](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. W parametrach testu sieci Web użyj {{nazwa wtyczki}}, aby odwoływać się do nazwy wtyczki.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Teraz przekaż test do portalu. Wartości dynamiczne zostaną zastosowane w każdym przebiegu testu.

### <a name="dealing-with-sign-in"></a>Obsługa logowania

Jeśli użytkownicy logują się do aplikacji, dostępne są różne opcje symulowania logowania, które pozwolą przetestować strony dostępne po zalogowaniu. Zastosowane podejście zależy od typu zabezpieczeń zapewnianych przez aplikację.

We wszystkich przypadkach należy utworzyć konto w ramach aplikacji tylko na potrzeby testowania. Jeśli to możliwe, należy ograniczyć uprawnienia tego konta testowego, aby nie było możliwości, że testy sieci Web będą miały wpływ na rzeczywistych użytkowników.

**Prosta nazwa użytkownika i hasło** Nagraj test internetowy w zwykły sposób. Najpierw usuń pliki cookie.

**Uwierzytelnianie SAML**

|Nazwa właściwości| Opis|
|----|-----|
| Uri odbiorców | Identyfikator URI odbiorców dla tokenu SAML.  Jest to identyfikator URI dla usługi kontroli dostępu (ACS) — w tym obszar nazw usługi ACS i nazwa hosta. |
| Hasło certyfikatu | Hasło certyfikatu klienta, które zapewni dostęp do osadzonego klucza prywatnego. |
| Certyfikat klienta  | Wartość certyfikatu klienta z kluczem prywatnym w formacie zakodowanym w bazie Base64. |
| Identyfikator nazwy | Identyfikator nazwy tokenu |
| Nie po | Czas, dla którego token będzie prawidłowy.  Wartość domyślna to 5 minut. |
| Nie wcześniej niż | Czas, dla którego token utworzony w przeszłości będzie prawidłowy (aby rozwiązać czas pochyla).  Wartość domyślna to (ujemna) 5 minut. |
| Nazwa parametru kontekstu docelowego | Parametr kontekstu, który otrzyma wygenerowane twierdzenie. |


**Tajemnica klienta** Jeśli aplikacja ma trasę logowania, która obejmuje klucz tajny klienta, użyj tej trasy. Azure Active Directory (AAD) to przykład usługi, która umożliwia logowanie za pomocą klucza tajnego klienta. W usłudze AAD klucz tajny klienta jest kluczem aplikacji.

Poniżej przedstawiono przykładowy test internetowy aplikacji internetowej platformy Azure przy użyciu klucza aplikacji:

![Przykładowy zrzut ekranu](./media/availability-multistep/client-secret.png)

Pobierz token z usługi AAD przy użyciu klucza tajnego klienta (AppKey).
Wyodrębnij token elementu nośnego z odpowiedzi.
Wywołaj interfejs API przy użyciu tokenu elementu nośnego w nagłówku autoryzacji.
Upewnij się, że test sieci web jest rzeczywistym klientem — oznacza to, że ma własną aplikację w UAD — i użyj jego clientId + klucz aplikacji. Twoja usługa w fazie testów ma również własną aplikację w usłudze AAD: identyfikator URI identyfikatora app jest odzwierciedlany w teście sieci web w polu zasobu.

### <a name="open-authentication"></a>Uwierzytelnianie otwarte
Przykładem uwierzytelniania otwartego jest logowanie przy użyciu konta Microsoft lub Google. Wiele aplikacji, które używają protokołu OAuth, zapewnia alternatywną obsługę klucza tajnego klienta, więc przede wszystkim należy zbadać tę możliwość.

Jeśli w ramach testu należy zalogować się przy użyciu protokołu OAuth, ogólne podejście jest następujące:

Użyj narzędzia takiego jak Fiddler, aby sprawdzić ruch między przeglądarką sieci Web, witryną uwierzytelniającą a aplikacją.
Wykonaj co najmniej dwa logowania na różnych komputerach lub w różnych przeglądarkach albo w długich odstępach czasu (umożliwi to wygaśnięcie tokenów).
Porównując różne sesje, zidentyfikuj token przekazywany z powrotem z witryny uwierzytelniającej, który jest następnie przekazywany do serwera aplikacji po zalogowaniu.
Nagraj test sieci Web przy użyciu programu Visual Studio.
Sparametryzuj tokeny, ustawiając parametr, gdy token jest zwracany z witryny uwierzytelniającej i używając go w zapytaniu do tej witryny. Program Visual Studio podejmie próby parametryzacji testu, ale parametryzacja tokenów nie przebiegnie poprawnie.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Dedykowany [artykuł dotyczący rozwiązywania problemów](troubleshoot-availability.md).

## <a name="next-steps"></a>Następne kroki

* [Alerty o dostępności](availability-alerts.md)
* [Testy sieci web url ping](monitor-web-app-availability.md)
