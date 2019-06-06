---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć aplikację internetową jednej strony, która wykonuje kwerendę i renderuje dane ze środowiska usługi Azure Time Series Insights.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 04/25/2019
ms.author: dpalled
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: 2f25267b95e9ed5f7d5f6e6373fb9e3807927a7f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735351"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia własnych aplikacji jednej strony sieci web (SPA) dostęp do danych usługi Azure Time Series Insights. 

Niniejszy samouczek zawiera informacje na temat następujących zagadnień:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (Azure AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową

> [!NOTE]
> * Kod źródłowy, w tym samouczku znajduje się na [GitHub](https://github.com/Microsoft/tsiclient/tree/tutorial/pages/tutorial).
> * Usługa Time Series Insights [Przykładowa aplikacja klienckiej](https://insights.timeseries.azure.com/clientsample) znajduje się do wyświetlenia ukończonej aplikacji używanych w tym samouczku.

## <a name="prerequisites"></a>Wymagania wstępne

* Zaloguj się w celu [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/) Jeśli nie masz jeszcze jeden.

* Bezpłatną kopię programu Visual Studio. Pobierz [wersji Community 2017 lub 2019](https://www.visualstudio.com/downloads/) na rozpoczęcie pracy.

* Usługi IIS Express, Web Deploy i usług Azure Cloud Services podstawowe narzędzia składniki dla programu Visual Studio. Dodaj składniki, modyfikując instalację programu Visual Studio.

## <a name="application-design"></a>Projekt aplikacji

Przykład usługi Time Series Insights SPA jest podstawą projektu i kod używany w tym samouczku. Kod używa biblioteki klienta JavaScript Insights serii czasu. Biblioteka klienta usługi Time Series Insights udostępnia abstrakcję do dwie główne kategorie interfejsu API:

- **Otoka metod wywoływania usługi Time Series Insights kwerendy interfejsów API**: Interfejsy API REST, których można użyć do zapytania usługi Time Series Insights danych za pomocą wyrażeń opartych na formacie JSON. Te metody są zorganizowane w przestrzeni nazw TsiClient.server biblioteki.

- **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody, których można użyć do wizualizacji danych usługi Time Series Insights na stronie sieci Web. Te metody są zorganizowane w przestrzeni nazw TsiClient.ux biblioteki.

Ten samouczek używa również dane ze środowiska usługi Time Series Insights do aplikacji przykładowej. Aby uzyskać szczegółowe informacje o strukturze usługi Time Series Insights Przykładowa aplikacja i jak go używa biblioteki klienta usługi Time Series Insights, zapoznaj się z samouczkiem [zapoznaj się z biblioteki klienckiej JavaScript Insights serii czasu Azure](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

Przed utworzeniem aplikacji, należy zarejestrować go za pomocą usługi Azure AD. Rejestracji zawiera Konfiguracja tożsamości, dzięki czemu aplikacja może użyć obsługi uwierzytelniania OAuth dla logowania jednokrotnego. OAuth wymaga aplikacji jednostronicowych typu grant niejawna autoryzacja. Należy zaktualizować autoryzacji w manifeście aplikacji. Manifest aplikacji to reprezentacja JSON konfiguracji tożsamości aplikacji.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu swojego konta subskrypcji platformy Azure.  
1. Wybierz pozycję **Azure Active Directory** > **Rejestracje aplikacji** > **Rejestrowanie nowej aplikacji**.

   [![Portal Azure — rejestrowanie aplikacji Begin usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png#lightbox)

1. W **Utwórz** okienku i wypełnij wymagane parametry.

   Parametr|Opis
   ---|---
   **Nazwa** | Wprowadź nazwę opisową rejestracji.  
   **Typ aplikacji** | Pozostaw **aplikacji/interfejs API sieci Web**.
   **Adres URL logowania** | Wprowadź adres URL logowania (macierzysty) strony aplikacji. Ponieważ aplikacja będzie później hostowane w usłudze Azure App Service, musisz użyć adresu URL przy użyciu protokołu https:\//azurewebsites.net domeny. W tym przykładzie nazwa jest oparta na nazwie rejestracji.

   Wybierz **Utwórz** do utworzenia nowej rejestracji aplikacji.

   [![Witryna Azure portal — tworzenie opcji w okienku rejestracji aplikacji usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png#lightbox)

1. Zasób aplikacji zawiera inne aplikacje mogą używać interfejsów API REST. Interfejsy API są także zarejestrowane w usłudze Azure AD. Interfejsy API zapewniają szczegółowe, bezpiecznego dostępu do aplikacji klienta przez udostępnianie *zakresy*. Ponieważ Twoja aplikacja wywołuje interfejs API usługi Azure czas serii szczegółowe informacje, należy określić interfejsu API i zakresu. Dla interfejsu API i zakresu, w czasie wykonywania jest udzielane uprawnienie. Wybierz **ustawienia** > **wymagane uprawnienia** > **Dodaj**.

   [![Portal Azure — w przypadku dodawania opcji dodawania uprawnień usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png#lightbox)

1. W **dostępu Dodaj interfejs API** okienku wybierz **1 Wybierz interfejs API** do określenia interfejsu API usługi Azure czas serii szczegółowych informacji. W **wybierz interfejs API** okienko, w polu wyszukiwania wprowadź **usługi azure time**. Następnie wybierz **Azure Time Series Insights** na liście wyników. Wybierz pozycję **Wybierz**.

   [![Witryna Azure portal — opcji wyszukiwania, aby dodać uprawnienia usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png#lightbox)

1. Aby wybrać zakres dla interfejsu API w **dostępu Dodaj interfejs API** okienku wybierz **2 uprawnienia Select**. W **Włącz dostęp za pomocą** okienku wybierz **usługi dostępu do usługi Azure Time Series Insights** zakresu. Wybierz pozycję **Wybierz**. Następuje powrót do **dostępu Dodaj interfejs API** okienka. Wybierz pozycję **Done** (Gotowe).

   [![Witryna Azure portal — Ustawianie zakresu dodawania uprawnień usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png#lightbox)

1. W **wymagane uprawnienia** okienko, interfejs API Azure czas serii Insights są teraz wyświetlane. Ponadto należy podać uprawnienia wstępne wyrażania zgody dla aplikacji, dostęp do interfejsu API i zakresu dla wszystkich użytkowników. Wybierz **udzielić uprawnień**, a następnie wybierz pozycję **tak**.

   [![Witryna Azure portal — Grant option uprawnienia do dodawania usługi Azure AD wymagane uprawnienia](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png#lightbox)

1. Jak wspomniano wcześniej, należy również zaktualizować manifest aplikacji. W poziomie menu w górnej części okienka ("nawigacji"), wybierz nazwę aplikacji, aby powrócić do **zarejestrowana aplikacja** okienka. Wybierz **manifestu**, zmień `oauth2AllowImplicitFlow` właściwości `true`, a następnie wybierz pozycję **Zapisz**.

   [![Witryna Azure portal — manifest aktualizacji usługi Azure AD](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png#lightbox)

1. W obszarze nawigacji, wybierz nazwę aplikacji, aby powrócić do **zarejestrowana aplikacja** okienka. Skopiuj wartości pól **strony głównej** i **identyfikator aplikacji** dla aplikacji. W dalszej części tego samouczka możesz użyć tych właściwości.

   [![Portal Azure — kopiowanie adres URL strony głównej i identyfikator aplikacji wartości dla swojej aplikacji](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png#lightbox)

## <a name="build-and-publish-the-web-application"></a>Kompilowanie i publikowanie aplikacji internetowej

1. Utwórz katalog do przechowywania plików projektu aplikacji. Następnie przejdź do każdego z następujących adresów URL. Kliknij prawym przyciskiem myszy **Raw** łącze w prawym górnym rogu strony, a następnie wybierz **Zapisz jako** do zapisania plików w katalogu projektu.

   - [*index.html*](https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html): HTML i JavaScript dla strony
   - [*sampleStyles.css*]( https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css): Arkusz stylów CSS

   > [!NOTE]
   > W zależności od przeglądarki może być konieczne zmienić rozszerzeń plików HTML i CSS przed zapisaniem pliku.

1. Sprawdź, czy wymagane składniki są zainstalowane w programie Visual Studio. Składniki narzędzi podstawowych usług IIS Express, Web Deploy i usług Azure Cloud Services dla programu Visual Studio muszą być zainstalowane.

    [![Visual Studio — modyfikowanie zainstalowanych składników](media/tutorial-create-tsi-sample-spa/vs-installation.png)](media/tutorial-create-tsi-sample-spa/vs-installation.png#lightbox)

    > [!NOTE]
    > Środowisko programu Visual Studio może się nieco różnić od opisanej przykładów w zależności od ustawień konfiguracji i wersji.

1. Otwórz program Visual Studio i zaloguj się. Aby utworzyć projekt dla aplikacji sieci web w **pliku** menu, wybierz opcję **Otwórz** > **witryny sieci Web**.

    [![Visual Studio — Utwórz nowe rozwiązanie](media/tutorial-create-tsi-sample-spa/vs-solution-create.png)](media/tutorial-create-tsi-sample-spa/vs-solution-create.png#lightbox)

1. W **Otwórz witrynę sieci Web** okienku wybierz katalog roboczy, w którym są przechowywane pliki HTML i CSS, a następnie wybierz **Otwórz**.

   [![Visual Studio — menu plik przy użyciu opcji otwierania i witryny sieci Web](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png#lightbox)

1. W programie Visual Studio **widoku** menu, wybierz opcję **Eksploratora rozwiązań**. Zostanie otwarte nowe rozwiązanie. Zawiera ona projektu witryny sieci Web (ikona globu), który zawiera pliki HTML i CSS.

   [![Visual Studio — nowe rozwiązanie w Eksploratorze rozwiązań](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png#lightbox)

1. Przed opublikowaniem aplikacji możesz zmienić ustawienia konfiguracji w *index.html*.

   1. Usuń znaczniki komentarza trzy wiersze, w obszarze komentarz `"PROD RESOURCE LINKS"` przełączyć zależności od projektowania do produkcji. Komentarz trzy wiersze, w obszarze komentarz `"DEV RESOURCE LINKS"`.

      [!code-html[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Zależności powinny ujęta w taki sposób, jak w poniższym przykładzie:

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

   1. Aby skonfigurować aplikację do używania Identyfikatora rejestracji aplikacji usługi Azure AD, zmień `clientID` i `postLogoutRedirectUri` wartości do użycia wartości **identyfikator aplikacji** i **strony głównej** skopiowany w kroku 9 [ Rejestrowanie aplikacji w usłudze Azure AD](#register-the-application-with-azure-ad).

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Na przykład:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ```

   1. Po zakończeniu wprowadzania modyfikacji Zapisz *index.html*.

1. Opublikuj aplikację sieci web w ramach subskrypcji Azure jako usługi Azure App Service.  

   > [!NOTE]
   > Kilka opcji, zrzuty ekranu, które zostały przedstawione w poniższych krokach są wypełniane automatycznie przy użyciu danych z Twojej subskrypcji platformy Azure. Może potrwać kilka sekund na każde okienko ładuje się poprawnie.  

   1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł projektu witryny sieci Web, a następnie wybierz **publikowania aplikacji sieci Web**.  

      [![Visual Studio — wybierz opcję Eksploratora rozwiązań publikowania aplikacji sieci Web](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png#lightbox)

   1. Wybierz **Start** umożliwiającą publikowania aplikacji.

      [![Visual Studio — okienko profilu publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png#lightbox)

   1. Wybierz subskrypcję, której chcesz użyć, aby opublikować aplikację. Wybierz **TsiSpaApp** projektu. Następnie wybierz przycisk **OK**.

      [![Visual Studio — profil publikowania okienko usługi App Service](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png#lightbox)

   1. Wybierz **Publikuj** do wdrożenia aplikacji sieci web.

      [![Visual Studio — opcja publikowania i publikowane dane wyjściowe dziennika](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-profile-output.png#lightbox)

   1. Dziennik pomyślne publikowania jest wyświetlany w programie Visual Studio **dane wyjściowe** okienka. Po zakończeniu wdrażania programu Visual Studio otwiera aplikację sieci web na karcie przeglądarki i wyświetla monit dotyczący logowania. Po pomyślnym zalogowaniu formanty usługi Time Series Insights są wypełniane przy użyciu danych.

## <a name="troubleshoot"></a>Rozwiązywanie problemów  

Kod/warunek błędu | Opis
---------------------| -----------
*AADSTS50011: brak adresów odpowiedzi zarejestrowanych dla aplikacji.* | Brak rejestracji w usłudze Azure AD **adres URL odpowiedzi** właściwości. Przejdź do **ustawienia** > **adresy URL odpowiedzi** za rejestrację aplikacji usługi Azure AD. Upewnij się, że **logowania jednokrotnego** adresu URL określonego w kroku 3 procedury [zarejestrować aplikację w usłudze Azure AD](#register-the-application-with-azure-ad) jest obecny.
*AADSTS50011: Odpowiedź adres url określony w żądaniu jest niezgodna z adresów URL odpowiedzi skonfigurowane dla aplikacji: "\<Aplikacji identyfikator GUID >".* | `postLogoutRedirectUri` Określonego w kroku 6 [podczas tworzenia i publikowania aplikacji sieci web](#build-and-publish-the-web-application) musi odpowiadać wartości określone w obszarze **ustawienia** > **adresy URL odpowiedzi** w rejestrację aplikacji usługi Azure AD. Należy również zmienić wartość **docelowy adres URL** używać *https* na kroku 5 w [podczas tworzenia i publikowania aplikacji sieci web](#build-and-publish-the-web-application).
Ładuje aplikację sieci web, ale ma stylu, tylko tekst strony logowania, na białym tle. | Sprawdź, czy ścieżki omówione w kroku 4 w [podczas tworzenia i publikowania aplikacji sieci web](#build-and-publish-the-web-application) są poprawne. Jeśli aplikacja internetowa nie może znaleźć plików CSS, styl strony będzie niepoprawny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku tworzysz kilka działających usług platformy Azure. Jeśli nie zamierzasz zakończyć tę serię samouczków, zaleca się usunięcie wszystkich zasobów, aby uniknąć ponoszenia niepotrzebnych kosztów.

W portalu Azure menu po lewej stronie:

1. Wybierz **grup zasobów**, a następnie wybierz grupę zasobów utworzoną w ramach środowiska usługi Time Series Insights. W górnej części strony wybierz **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz **Usuń**.
1. Wybierz **grup zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązań symulacji urządzenia. W górnej części strony wybierz **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie wybierz **Usuń**.

## <a name="next-steps"></a>Kolejne kroki

W tym samouczku omówiono:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure AD
> * Jak skompilować, opublikować i przetestować aplikację internetową

W tym samouczku integruje się z usługą Azure AD i korzysta z tożsamości użytkownika, który jest zalogowany w celu uzyskania tokenu dostępu. Aby dowiedzieć się, jak dostęp do interfejsu API usługi Insights serii czasu przy użyciu tożsamość aplikacji usługi lub demona, znajduje się w artykule:

> [!div class="nextstepaction"]
> [Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
