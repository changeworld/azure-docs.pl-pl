---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć jednostronicową aplikację sieci Web, która wykonuje zapytania i renderuje dane ze środowiska Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/29/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 4d9af918c222107cfca5863309efb391b8e6d2e0
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/01/2019
ms.locfileid: "68720863"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia własnej jednostronicowej aplikacji sieci Web (SPA) w celu uzyskania dostępu do danych Azure Time Series Insights.

Niniejszy samouczek zawiera informacje na temat następujących zagadnień:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (Azure AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową

> [!NOTE]
> * Kod źródłowy dla tego samouczka jest dostępny w witrynie [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Przykładowa [aplikacja klienta](https://insights.timeseries.azure.com/clientsample) Time Series Insights jest hostowana, aby wyświetlić ukończoną aplikację używaną w tym samouczku.

Utwórz konto bezpłatnej [subskrypcji platformy Azure](https://azure.microsoft.com/free/) , jeśli jeszcze jej nie masz.

## <a name="prerequisites"></a>Wymagania wstępne

* Bezpłatna kopia programu Visual Studio. Pobierz [wersje społeczności 2017 lub 2019](https://www.visualstudio.com/downloads/) , aby rozpocząć pracę.

* Składniki narzędzi IIS Express, Web Deploy i Azure Cloud Services Core dla programu Visual Studio. Dodaj składniki, modyfikując instalację programu Visual Studio.

## <a name="understand-application-design"></a>Zrozumienie projektu aplikacji

Time Series Insights przykład SPA jest podstawą projektu i kodu używanego w tym samouczku. Kod używa biblioteki klienta Time Series Insights JavaScript. Biblioteka klienta Time Series Insights zawiera streszczenie dla dwóch głównych kategorii interfejsu API:

- **Metody otoki do wywoływania interfejsów API zapytań Time Series Insights**: Interfejsy API REST, których można użyć do wykonywania zapytań dotyczących danych Time Series Insights przy użyciu wyrażeń opartych na notacji JSON. Metody są zorganizowane w przestrzeni nazw TsiClient. Server biblioteki.

- **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody, których można użyć do wizualizacji Time Series Insights danych na stronie sieci Web. Metody są zorganizowane w przestrzeni nazw TsiClient. UX biblioteki.

Ten samouczek używa również danych ze środowiska Time Series Insights aplikacji przykładowej. Aby uzyskać szczegółowe informacje na temat struktury przykładowej aplikacji Time Series Insights i sposobu korzystania z biblioteki klienta Time Series Insights, zobacz Samouczek dotyczący [biblioteki klienta Azure Time Series Insights JavaScript](tutorial-explore-js-client-lib.md).

## <a name="register-with-azure-ad"></a>Rejestrowanie w usłudze Azure AD

[!INCLUDE [Azure Active Directory app registration](../../includes/time-series-insights-aad-registration.md)]

## <a name="build-and-publish"></a>Kompilowanie i publikowanie

1. Utwórz katalog do przechowywania plików projektu aplikacji. Następnie przejdź do każdego z następujących adresów URL. Kliknij prawym przyciskiem myszy łącze **RAW** w prawym górnym rogu strony, a następnie wybierz polecenie **Zapisz jako** , aby zapisać pliki w katalogu projektu.

   - [*index. html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): kod HTML i JavaScript dla strony
   - [*sampleStyles. css*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): arkusz stylów CSS

   > [!NOTE]
   > W zależności od przeglądarki może zajść konieczność zmiany rozszerzeń plików na HTML lub CSS przed zapisaniem pliku.

1. Sprawdź, czy wymagane składniki są zainstalowane w programie Visual Studio. Należy zainstalować składniki narzędzi IIS Express, Web Deploy i Azure Cloud Services Core dla programu Visual Studio.

    [![Visual Studio — modyfikowanie zainstalowanych składników](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Środowisko Visual Studio może się nieco różnić od przedstawionych przykładów w zależności od wersji i ustawień konfiguracji.

1. Otwórz program Visual Studio i zaloguj się. Aby utworzyć projekt dla aplikacji sieci Web, w menu **plik** wybierz polecenie **Otwórz** > **witrynę sieci Web**.

    [![Visual Studio — Tworzenie nowego rozwiązania](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. W okienku **Otwórz witrynę sieci Web** wybierz katalog roboczy, w którym ZAPISANO pliki HTML i CSS, a następnie wybierz pozycję **Otwórz**.

   [![Visual Studio — menu plik z opcjami otwierania i witryny sieci Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. W menu **Widok** programu Visual Studio wybierz pozycję **Eksplorator rozwiązań**. Zostanie otwarte nowe rozwiązanie. Zawiera projekt witryny sieci Web (ikona globusa), który zawiera pliki HTML i CSS.

   [![Visual Studio — nowe rozwiązanie w Eksplorator rozwiązań](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Przed opublikowaniem aplikacji należy zmienić ustawienia konfiguracji w *pliku index. html*.

   1. Usuń znaczniki komentarza z trzech wierszy pod `"PROD RESOURCE LINKS"` komentarzem, aby przełączyć zależności od programowania do środowiska produkcyjnego. Dodaj komentarz do trzech wierszy w komentarzu `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Twoje zależności powinny mieć komentarz podobny do następującego:

      ```HTML
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css">

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="./sampleStyles.css">
      <script src="../../dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="../../dist/tsiclient.css"> -->
      ```

   1. Aby skonfigurować aplikację do używania identyfikatora rejestracji aplikacji usługi `clientID` Azure AD, Zmień wartość na Użyj **identyfikatora aplikacji** skopiowanego w **kroku 3** , gdy aplikacja zostanie zarejestrowana w [celu korzystania z usługi Azure AD](#register-with-azure-ad). Jeśli w usłudze Azure AD został utworzony **adres URL wylogowania** , ustaw tę wartość jako `postLogoutRedirectUri` wartość.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Na przykład:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Po zakończeniu wprowadzania modyfikacji Zapisz *index. html*.

1. Opublikuj aplikację sieci Web w ramach subskrypcji platformy Azure jako Azure App Service.  

   > [!NOTE]
   > Kilka opcji zrzutu ekranu, które są wyświetlane w poniższych krokach, jest automatycznie wypełnianych danymi z subskrypcji platformy Azure. Całkowite załadowanie każdego okienka może potrwać kilka sekund.  

   1. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy węzeł projektu witryny sieci Web, a następnie wybierz pozycję **Publikuj aplikację sieci Web**.  

      [![Visual Studio — wybierz opcję Eksplorator rozwiązań publikowanie aplikacji sieci Web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Wybierz pozycję **Rozpocznij** , aby rozpocząć publikowanie aplikacji.

      [![Visual Studio — okienko Publikuj profil](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Wybierz subskrypcję, której chcesz użyć do opublikowania aplikacji. Wybierz projekt **TsiSpaApp** . Następnie wybierz przycisk **OK**.

      [![Visual Studio — okienko App Service publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Wybierz pozycję **Publikuj** , aby wdrożyć aplikację sieci Web.

      [![Visual Studio — opcja publikowania i dane wyjściowe publikowania dziennika](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. W okienku **danych wyjściowych** programu Visual Studio pojawi się komunikat o pomyślnym opublikowaniu. Po zakończeniu wdrażania program Visual Studio otworzy aplikację sieci Web na karcie przeglądarki i wyświetli prośbę o zalogowanie. Po pomyślnym zalogowaniu w formantach Time Series Insights są wypełniane dane.

## <a name="troubleshoot"></a>Rozwiązywanie problemów  

Kod/warunek błędu | Opis
---------------------| -----------
*AADSTS50011: brak adresów odpowiedzi zarejestrowanych dla aplikacji.* | W rejestracji usługi Azure AD brakuje właściwości **adresu URL odpowiedzi** . Przejdź do pozycji **Ustawienia** > **adresy URL odpowiedzi** dla rejestracji aplikacji usługi Azure AD. Sprawdź, czy **Identyfikator URI przekierowania** jest dostępny w **kroku 2** lub **4** , gdy zarejestrowano [aplikację w celu korzystania z usługi Azure AD](#register-with-azure-ad) .
*AADSTS50011: Adres URL odpowiedzi określony w żądaniu nie jest zgodny z adresami URL odpowiedzi skonfigurowanymi dla aplikacji: "\<Identyfikator aplikacji >".* |  >  [](#build-and-publish) Określony w kroku 6. b w kompilacji i publikacji aplikacja sieci Web musi być zgodna z wartością określoną w polu Ustawienia adresy URL odpowiedzi w rejestracji aplikacji usługi Azure AD. `postLogoutRedirectUri` |
Aplikacja sieci Web ładuje, ale ma niestylową stronę logowania tylko do tekstu, z białym tłem. | Sprawdź, czy ścieżki omówione w **kroku 6** [kompilowania i publikowania aplikacji sieci Web](#build-and-publish) są poprawne. Jeśli aplikacja internetowa nie może znaleźć plików CSS, styl strony będzie niepoprawny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku tworzysz kilka działających usług platformy Azure. Jeśli nie planujesz zakończyć tej serii samouczków, zalecamy usunięcie wszystkich zasobów, aby uniknąć ponoszenia niepotrzebnych kosztów.

W menu po lewej Azure Portal:

1. Wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów utworzoną dla środowiska Time Series Insights. W górnej części strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.
1. Wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez Akcelerator rozwiązania do symulacji urządzeń. W górnej części strony wybierz pozycję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku przedstawiono następujące informacje:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure AD
> * Jak skompilować, opublikować i przetestować aplikację internetową

Ten samouczek integruje się z usługą Azure AD i używa tożsamości użytkownika, który jest zalogowany w celu uzyskania tokenu dostępu. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API Time Series Insights przy użyciu tożsamości usługi lub aplikacji demona, zobacz ten artykuł:

> [!div class="nextstepaction"]
> [Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
