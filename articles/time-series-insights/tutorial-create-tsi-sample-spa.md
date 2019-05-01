---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć jednostronicową aplikację internetową, która wykonuje zapytania i renderuje dane ze środowiska usługi TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 18f5c14a9427f4d7e34a802b2bcc0612a51a804a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64573181"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia własnych aplikacji jednej strony sieci web dostęp do danych usługi Time Series Insights. W szczególności poznasz:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową

> [!NOTE]
> * Kod źródłowy, w tym samouczku znajduje się na [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Usługa Time Series Insights [Przykładowa aplikacja klienckiej](https://insights.timeseries.azure.com/clientsample) znajduje się do wyświetlenia ukończonej aplikacji używanych w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/), jeśli jeszcze go nie masz.

* Należy także uzyskać bezpłatną kopię programu Visual Studio. Pobierz [wersji Community 2017 r. lub 2019](https://www.visualstudio.com/downloads/) na rozpoczęcie pracy.

* Należy także **usług IIS Express**, **narzędzia Web Deploy**, i **narzędzia core usług Azure Cloud Services** składników dla programu Visual Studio. Dodaj te, modyfikując instalację programu Visual Studio.

## <a name="application-design-overview"></a>Omówienie projektu aplikacji

Jednostronicowych Przykładowa aplikacja usługi Time Series Insights stanowi podstawę dla projektu i kod używany w ramach tego samouczka. Kod obejmuje użycie biblioteki JavaScript klienta usługi TSI. Biblioteka klienta TSI udostępnia abstrakcję dla dwóch głównych kategorii interfejsu API:

- **Metody otoki na potrzeby wywoływania interfejsów API zapytań usługi TSI**: Interfejsy API REST, które umożliwiają wykonywanie zapytań dotyczących danych usługi TSI przy użyciu wyrażeń opartych na formacie JSON. Te metody są zorganizowane w przestrzeni nazw `TsiClient.server` biblioteki.

- **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody używane do wizualizacji danych usługi TSI na stronie internetowej. Te metody są zorganizowane w przestrzeni nazw `TsiClient.ux` biblioteki.

W tym samouczku będziemy również używać danych ze środowiska usługi TSI przykładowej aplikacji. Szczegółowe informacje na temat struktury przykładowej aplikacji usługi TSI i korzystania z biblioteki klienta usługi TSI można znaleźć w samouczku [Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

Przed skompilowaniem aplikacji trzeba ją zarejestrować za pomocą usługi Azure AD. Rejestracja udostępnia konfigurację tożsamości aplikacji, dzięki której można używać funkcji OAuth na potrzeby rejestracji jednokrotnej. Standard OAuth wymaga, aby aplikacje SPA używały „niejawnego” przyznawania autoryzacji, które będzie aktualizowane w manifeście aplikacji. Manifest aplikacji to reprezentacja JSON konfiguracji tożsamości aplikacji.

1. Zaloguj się do witryny [Azure portal](https://portal.azure.com) przy użyciu konta platformy Azure.  
1. Wybierz **usługi Azure Active Directory** zasobu w okienku po lewej stronie, następnie **rejestracje aplikacji**, następnie **+ rejestrowanie nowej aplikacji**.

   [![Rejestrowanie aplikacji w witrynie Azure portal usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. Na **Utwórz** strony, wypełnij wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa** | Podaj opisową nazwę rejestracji.  
   **Typ aplikacji** | Ponieważ kompilujesz aplikację internetową SPA, pozostaw typ „Aplikacja internetowa/interfejs API”.
   **Adres URL logowania** | Wprowadź adres URL strony głównej/logowania w aplikacji. Ponieważ aplikacja będzie hostowana w usłudze Azure App Service (dalej), należy użyć adresu URL w ciągu "https:\//azurewebsites.net" domeny. W tym przykładzie nazwa jest oparta na nazwie rejestracji.

   Gdy skończysz, kliknij przycisk **Utwórz** w celu utworzenia rejestracji nowej aplikacji.

   [![Rejestrowanie aplikacji w witrynie Azure portal usługi Azure AD — tworzenie](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Aplikacje zasobów oferują interfejsy API REST do użycia przez inne aplikacje i są również rejestrowane w usłudze Azure AD. Interfejsy API zapewniają szczegółowy/zabezpieczony dostęp do aplikacji klientów dzięki ujawnianiu „zakresów”. Ponieważ aplikacja będzie wywoływać interfejs API „Azure Time Series Insights”, musisz określić interfejs API i zakres, dla którego w czasie wykonywania będziesz żądać uprawnienia/przyznawać je. Wybierz **ustawienia**, następnie **wymagane uprawnienia**, następnie **+ Dodaj**.

   [![Witryna Azure portal usługi Azure AD Dodaj uprawnienia](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **1 Wybierz interfejs API** w celu określenia interfejsu API usługi TSI. Na stronie **Wybieranie interfejsu API** w polu wyszukiwania wprowadź ciąg „azure time”. Następnie wybierz pozycję "Usługi Azure Time Series Insights" interfejsu API na liście wyników i kliknij przycisk **wybierz**.

   [![Witryna Azure portal usługi Azure AD należy dodać uprawnienia — interfejs API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Teraz możesz określić zakres interfejsu API. Ponownie na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **2 Wybierz uprawnienia**. Na stronie **Włączanie dostępu** wybierz zakres „Uzyskiwanie dostępu do usługi Azure Time Series Insights”. Kliknij przycisk **wybierz**, co spowoduje zwrócenie umożliwia **dostępu Dodaj interfejs API** strony, gdzie należy kliknąć **gotowe**.

   [![Witryna Azure portal usługi Azure AD należy dodać uprawnienia — zakres](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. Po powrocie na stronę **Wymagane uprawnienia** zwróć uwagę, że interfejs API „Azure Time Series Insights” jest teraz wyświetlany na liście. Będziesz również potrzebować uprawnienia wstępnej zgody dla wszystkich użytkowników, aby aplikacja mogła uzyskiwać dostęp do interfejsu API i zakresu. Kliknij przycisk **udzielić uprawnień** znajdujący się u góry, a następnie wybierz pozycję **tak**.

   [![Witryna Azure portal usługi Azure AD wymagane uprawnienia — zgoda](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Jak wspomniano wcześniej, musisz również zaktualizować manifest aplikacji. Kliknij nazwę aplikacji w obszarze nawigacji za pomocą linków do stron nadrzędnych, aby wrócić na stronę **Zarejestrowana aplikacja**. Wybierz **manifestu**, zmień `oauth2AllowImplicitFlow` właściwości `true`, następnie kliknij przycisk **Zapisz**.

   [![Manifest aktualizacji witryny Azure portal usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. Na koniec kliknij obszar nawigacji za pomocą linków do stron nadrzędnych, aby ponownie wrócić do strony **Zarejestrowana aplikacja**, i skopiuj właściwości adresu URL **strony głównej** i **identyfikatora aplikacji** dla aplikacji. Użyjesz tych właściwości w późniejszym kroku.

   [![Właściwości witryny Azure portal usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Kompilowanie i publikowanie aplikacji internetowej

1. Utwórz katalog do przechowywania plików projektu aplikacji. Następnie przejdź do każdego z następujących adresów URL, kliknij prawym przyciskiem myszy łącze "Raw" w prawym górnym prawy obszar strony i "Zapisz jako" w katalogu projektu.

   > [!NOTE]
   > W zależności od przeglądarki przed zapisaniem pliku być może trzeba będzie poprawić rozszerzenie nazwy pliku (na HTML lub CSS).

   - [**index.html**](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): Kod HTML i JavaScript dla strony.
   - [**sampleStyles.css**]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Arkusz stylów CSS.

1. Sprawdź, czy program Visual Studio ma niezbędne składniki, które są zainstalowane.

    [![VS - modify zainstalowanych składników](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    * Będziesz potrzebować **usług IIS Express**, **narzędzia Web Deploy**, i **narzędzia core usług Azure Cloud Services** składników dla programu Visual Studio.

    > [!NOTE]
    > Środowisko programu Visual Studio może się nieco różnić od opisanej przykładów w zależności od wersji i ustawień konfiguracji.

1. Uruchom program Visual Studio i zaloguj się do niego, aby utworzyć projekt aplikacji internetowej. W menu **Plik** wybierz pozycję **Otwórz**, a następnie wybierz opcję **Witryna internetowa**.

    [![VS — Utwórz nowe rozwiązanie](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. Na **Otwórz witrynę sieci Web** okno dialogowe, wybierz katalog roboczy, w którym są przechowywane pliki HTML i CSS, a następnie kliknij przycisk **Otwórz**.

   [![Programu VS — Otwórz witrynę sieci web plik](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. Otwórz **Eksploratora rozwiązań** z menu **Widok** programu Visual Studio. Powinieneś zobaczyć nowe rozwiązanie, zawierający projekt witryny sieci web (ikona globu), który zawiera pliki HTML i CSS.

   [![Programu VS - rozwiązania explorer nowe rozwiązanie](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Przed opublikowaniem aplikacji, musisz zmienić ustawienia konfiguracji w **index.html**.

   a. Przełącz zależności od projektowania do produkcji, uncommenting trzy wiersze, w obszarze komentarz `"PROD RESOURCE LINKS"`. Komentarz trzy wiersze, w obszarze komentarz `"DEV RESOURCE LINKS"`.

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Powinien być odpowiednio oznaczone jako zależności:

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

   b. Następnie należy skonfigurować aplikację do używania Twojego identyfikatora rejestracji aplikacji usługi Azure Active Directory. Zmiana `clientID` i `postLogoutRedirectUri` pola używane **identyfikator aplikacji** i **adres URL strony głównej** skopiowane w **kroku 9** z [zarejestrować Aplikacja z usługą Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Na przykład:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   c. Zapisz **index.html** po wprowadzeniu tych zmian.

1. Teraz publikowanie aplikacji sieci web do subskrypcji platformy Azure jako usługi Azure App Service:  

   > [!NOTE]
   > Niektóre pola poniższych okien dialogowych są wypełniane przy użyciu danych z subskrypcji platformy Azure. W tej sytuacji, zanim będzie można kontynuować, całkowite załadowanie każdego okna dialogowego może potrwać kilka sekund.  

   a. Kliknij prawym przyciskiem myszy węzeł projektu witryny sieci web w **Eksploratora rozwiązań**i wybierz **publikowania aplikacji sieci Web**.  

      [![VS - Eksplorator rozwiązań publikowanie aplikacji sieci web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   b. Wybierz **Start** umożliwiającą publikowania aplikacji.

      [![VS - profil publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   c. Wybierz subskrypcję, której chcesz używać do publikowania aplikacji. Wybierz **TsiSpaApp** projektu. Następnie **OK**:

      [![VS - Publikuj profil — usługa app service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   d. Kliknij przycisk **Publikuj** do wdrożenia aplikacji sieci web.

      [![VS — publikowanie aplikacji internetowej — dane wyjściowe dziennika publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   e. W oknie **Dane wyjściowe** programu Visual Studio powinien pojawić się dziennik pomyślnego publikowania. Po ukończeniu wdrażania program Visual Studio otworzy również aplikację internetową na karcie przeglądarki z monitem o zalogowanie. Po pomyślnym zalogowaniu zostaną wyświetlone wszystkie formanty TSI wypełniony danymi.

## <a name="troubleshooting"></a>Rozwiązywanie problemów  

Kod/warunek błędu | Opis
---------------------| -----------
*AADSTS50011: brak adresów odpowiedzi zarejestrowanych dla aplikacji.* | Brak rejestracji w usłudze Azure AD **adres URL odpowiedzi** właściwości. Przejdź na stronę **Ustawienia** / **Adresy URL odpowiedzi** dla rejestracji aplikacji usługi Azure AD. Upewnij się, że **logowania jednokrotnego** adres URL określony w **kroku 3** z [zarejestrować aplikację w usłudze Azure AD](#register-the-application-with-azure-ad) jest obecny.
*AADSTS50011: Odpowiedź adres url określony w żądaniu jest niezgodna z adresów URL odpowiedzi skonfigurowane dla aplikacji: "\<Aplikacji identyfikator GUID >".* | Identyfikator `postLogoutRedirectUri` określony w kroku 4.b sekcji [Kompilowanie i publikowanie aplikacji internetowej](#build-and-publish-the-web-application) musi odpowiadać wartości właściwości **Ustawienia** / **Adresy URL odpowiedzi** w rejestracji aplikacji usługi Azure AD. Należy również zmienić **docelowy adres URL** używać `https`, na **kroku 5** z [podczas tworzenia i publikowania aplikacji sieci web](#build-and-publish-the-web-application).
Aplikacja internetowa jest ładowana, ale ma stronę logowania bez stylu i tylko z tekstem na białym tle. | Sprawdź, czy ścieżki omówione w **kroku 4** z [podczas tworzenia i publikowania aplikacji sieci web](#build-and-publish-the-web-application) są poprawne. Jeśli aplikacja internetowa nie może znaleźć plików CSS, styl strony będzie niepoprawny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku tworzysz kilka działających usług platformy Azure. Jeśli nie planujesz ukończenia pracy z tą serią samouczków, zalecamy usunięcie wszystkich zasobów w celu uniknięcia niepotrzebnych kosztów.

W menu portalu Azure po lewej stronie:

1. Wybierz **grup zasobów**, następnie wybierz grupę zasobów utworzoną w środowisku usługi TSI. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**.
1. Wybierz **grup zasobów**, następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązań symulacji urządzenia. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową

Ten samouczek przedstawia integrację z usługą Azure AD przy użyciu tożsamości zalogowanego użytkownika w celu uzyskania tokenu dostępu. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API usługi TSI przy użyciu tożsamości aplikacji demona/usługi, zobacz:

> [!div class="nextstepaction"]
> [Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)