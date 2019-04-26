---
title: 'Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights | Microsoft Docs'
description: Dowiedz się, jak utworzyć jednostronicową aplikację internetową, która wykonuje zapytania i renderuje dane ze środowiska usługi TSI.
author: ashannon7
ms.service: time-series-insights
ms.topic: tutorial
ms.date: 06/14/2018
ms.author: anshan
manager: cshankar
ms.custom: seodec18
ms.openlocfilehash: a121ec3c0e769df35ca7ca20eb9cf85210d857da
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193569"
---
# <a name="tutorial-create-an-azure-time-series-insights-single-page-web-app"></a>Samouczek: Tworzenie jednostronicowej aplikacji internetowej usługi Azure Time Series Insights

Ten samouczek przeprowadzi Cię przez proces tworzenia własnej jednostronicowej aplikacji internetowej (SPA) umożliwiającej uzyskiwanie dostępu do danych usługi TSI z modelowaniem opartym na [przykładowej aplikacji usługi Time Series Insights (TSI)](https://insights.timeseries.azure.com/clientsample). Niniejszy samouczek zawiera informacje na temat następujących zagadnień:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową 

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz konto [bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/), jeśli jeszcze go nie masz. 

Musisz również zainstalować program Visual Studio, jeśli ta czynność nie została jeszcze wykonana. Na potrzeby tego samouczka możesz [pobrać/zainstalować bezpłatną wersję Community lub bezpłatną wersję próbną](https://www.visualstudio.com/downloads/).

## <a name="application-design-overview"></a>Omówienie projektu aplikacji

Jak wspomniano, przykładowa aplikacja usługi TSI stanowi podstawę projektu i kodu używanego w tym samouczku. Kod obejmuje użycie biblioteki JavaScript klienta usługi TSI. Biblioteka klienta TSI udostępnia abstrakcję dla dwóch głównych kategorii interfejsu API:

- **Metody otoki na potrzeby wywoływania interfejsów API zapytań usługi TSI**: Interfejsy API REST, które umożliwiają wykonywanie zapytań dotyczących danych usługi TSI przy użyciu wyrażeń opartych na formacie JSON. Te metody są zorganizowane w przestrzeni nazw `TsiClient.server` biblioteki.
- **Metody tworzenia i wypełniania kilku typów kontrolek wykresów**: Metody używane do wizualizacji danych usługi TSI na stronie internetowej. Te metody są zorganizowane w przestrzeni nazw `TsiClient.ux` biblioteki.

W tym samouczku będziemy również używać danych ze środowiska usługi TSI przykładowej aplikacji. Szczegółowe informacje na temat struktury przykładowej aplikacji usługi TSI i korzystania z biblioteki klienta usługi TSI można znaleźć w samouczku [Poznawanie biblioteki klienta języka JavaScript dla usługi Azure Time Series Insights](tutorial-explore-js-client-lib.md).

## <a name="register-the-application-with-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD 

Przed skompilowaniem aplikacji trzeba ją zarejestrować za pomocą usługi Azure AD. Rejestracja udostępnia konfigurację tożsamości aplikacji, dzięki której można używać funkcji OAuth na potrzeby rejestracji jednokrotnej. Standard OAuth wymaga, aby aplikacje SPA używały „niejawnego” przyznawania autoryzacji, które będzie aktualizowane w manifeście aplikacji. Manifest aplikacji to reprezentacja JSON konfiguracji tożsamości aplikacji. 

1. Zaloguj się do witryny [Azure portal](https://portal.azure.com) przy użyciu konta platformy Azure.  
1. Wybierz zasób **Azure Active Directory** w okienku po lewej stronie, a następnie kolejno wybierz pozycje **Rejestracje aplikacji** i **+ Rejestrowanie nowej aplikacji**:  
   
   ![Usługa Azure AD w witrynie Azure Portal — rejestrowanie aplikacji](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration.png)

1. Na stronie **Tworzenie** wypełnij pola wymaganych parametrów:
   
   Parametr|Opis
   ---|---
   **Nazwa** | Podaj opisową nazwę rejestracji.  
   **Typ aplikacji** | Ponieważ kompilujesz aplikację internetową SPA, pozostaw typ „Aplikacja internetowa/interfejs API”.
   **Adres URL logowania** | Wprowadź adres URL strony głównej/logowania w aplikacji. Ponieważ aplikacja będzie hostowana w usłudze Azure App Service (dalej), należy użyć adresu URL w ciągu "https:\//azurewebsites.net" domeny. W tym przykładzie nazwa jest oparta na nazwie rejestracji.

   Gdy skończysz, kliknij przycisk **Utwórz** w celu utworzenia rejestracji nowej aplikacji.

   ![Usługa Azure AD w witrynie Azure Portal: rejestracja aplikacji — tworzenie](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-create.png)

1. Aplikacje zasobów oferują interfejsy API REST do użycia przez inne aplikacje i są również rejestrowane w usłudze Azure AD. Interfejsy API zapewniają szczegółowy/zabezpieczony dostęp do aplikacji klientów dzięki ujawnianiu „zakresów”. Ponieważ aplikacja będzie wywoływać interfejs API „Azure Time Series Insights”, musisz określić interfejs API i zakres, dla którego w czasie wykonywania będziesz żądać uprawnienia/przyznawać je. Wybierz kolejno pozycje **Ustawienia**, **Wymagane uprawnienia** i **+ Dodaj**:

   ![Usługa Azure AD w witrynie Azure Portal — dodawanie uprawnień](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms.png)

1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **1 Wybierz interfejs API** w celu określenia interfejsu API usługi TSI. Na stronie **Wybieranie interfejsu API** w polu wyszukiwania wprowadź ciąg „azure time”. Następnie wybierz z listy wyników interfejs API „Azure Time Series Insights” i kliknij pozycję **Wybierz**: 

   ![Usługa Azure AD w witrynie Azure Portal: dodawanie uprawnień — interfejs API](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api.png)

1. Teraz możesz określić zakres interfejsu API. Ponownie na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **2 Wybierz uprawnienia**. Na stronie **Włączanie dostępu** wybierz zakres „Uzyskiwanie dostępu do usługi Azure Time Series Insights”. Kliknij przycisk **Wybierz**. Spowoduje to powrót na stronę **Dodawanie dostępu do interfejsu API**. Kliknij na niej pozycję **Gotowe**:

   ![Usługa Azure AD w witrynie Azure Portal — zakres](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-add-perms-api-scopes.png)

1. Po powrocie na stronę **Wymagane uprawnienia** zwróć uwagę, że interfejs API „Azure Time Series Insights” jest teraz wyświetlany na liście. Będziesz również potrzebować uprawnienia wstępnej zgody dla wszystkich użytkowników, aby aplikacja mogła uzyskiwać dostęp do interfejsu API i zakresu. Kliknij przycisk **Udziel uprawnień** w górnej części strony, a następnie wybierz pozycję **Tak**:

   ![Usługa Azure AD w witrynie Azure Portal: wymagane uprawnienia — zgoda](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-required-permissions-consent.png)

1. Jak wspomniano wcześniej, musisz również zaktualizować manifest aplikacji. Kliknij nazwę aplikacji w obszarze nawigacji za pomocą linków do stron nadrzędnych, aby wrócić na stronę **Zarejestrowana aplikacja**. Wybierz pozycję **Manifest**, zmień właściwość `oauth2AllowImplicitFlow` na `true`, a następnie kliknij przycisk **Zapisz**:

   ![Usługa Azure AD w witrynie Azure Portal — aktualizowanie manifestu](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-update-manifest.png)

1. Na koniec kliknij obszar nawigacji za pomocą linków do stron nadrzędnych, aby ponownie wrócić do strony **Zarejestrowana aplikacja**, i skopiuj właściwości adresu URL **strony głównej** i **identyfikatora aplikacji** dla aplikacji. Użyjesz tych właściwości w kolejnym kroku:

   ![Usługa Azure AD w witrynie Azure Portal — właściwości](media/tutorial-create-tsi-sample-spa/ap-aad-app-registration-application.png)

## <a name="build-and-publish-the-web-application"></a>Kompilowanie i publikowanie aplikacji internetowej

1. Utwórz katalog do przechowywania plików projektu aplikacji. Następnie przejdź do każdego z poniższych adresów URL, kliknij prawym przyciskiem myszy link „nieprzetworzony” w prawym górnym obszarze strony i użyj polecenia „Zapisz jako” w celu zapisania w katalogu projektu:

   > [!NOTE]
   > W zależności od przeglądarki przed zapisaniem pliku być może trzeba będzie poprawić rozszerzenie nazwy pliku (na HTML lub CSS).

   - **index.HTML**: kod HTML i JavaScript na stronie: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/index.html
   - **sampleStyles.css:** Arkusz stylów CSS: https://github.com/Microsoft/tsiclient/blob/tutorial/pages/tutorial/sampleStyles.css
    
1. Uruchom program Visual Studio i zaloguj się do niego, aby utworzyć projekt aplikacji internetowej. W menu **Plik** wybierz pozycję **Otwórz**, a następnie wybierz opcję **Witryna internetowa**. W oknie dialogowym **Otwieranie witryny internetowej** wybierz katalog roboczy, w którym były przechowywane pliki HTML i CSS, a następnie kliknij pozycję **Otwórz**:

   ![VS — Plik, otwieranie witryny internetowej](media/tutorial-create-tsi-sample-spa/vs-file-open-web-site.png)

1. Otwórz **Eksploratora rozwiązań** z menu **Widok** programu Visual Studio. Powinno zostać wyświetlone nowe rozwiązanie zawierające projekt witryny internetowej (ikona kuli ziemskiej) z plikami HTML i CSS:

   ![VS — nowe rozwiązanie w Eksploratorze rozwiązań](media/tutorial-create-tsi-sample-spa/vs-solution-explorer.png)

1. Przed opublikowaniem aplikacji musisz zaktualizować fragmenty kodu JavaScript w pliku **index.html**: 

   a. Najpierw zmień ścieżki do odwołania do plików arkusza stylów i kodu JavaScript w elemencie `<head>`. Otwórz plik **index.html** w rozwiązaniu programu Visual Studio i znajdź poniższe wiersze kodu JavaScript. Usuń komentarz do trzech wierszy w obszarze „PROD RESOURCE LINKS” (Linki do zasobów produkcyjnych) i dodaj komentarz do trzech wierszy w obszarze „DEV RESOURCE LINKS” (Linki do zasobów deweloperskich):
   
      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=2-20&highlight=10-13,15-18)]

      Zmieniony kod powinien wyglądać podobnie do poniższego przykładu:

      ```javascript
      <!-- PROD RESOURCE LINKS -->
      <link rel="stylesheet" type="text/css" href="sampleStyles.css"></link>
      <script src="https://unpkg.com/tsiclient@1.1.4/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="https://unpkg.com/tsiclient@1.1.4/tsiclient.css"></link>

      <!-- DEV RESOURCE LINKS -->
      <!-- <link rel="stylesheet" type="text/css" href="pages/samples/sampleStyles.css"></link>
      <script src="dist/tsiclient.js"></script>
      <link rel="stylesheet" type="text/css" href="dist/tsiclient.css"></link> -->
      ```

   b. Następnie zmień logikę tokenu dostępu w celu użycia rejestracji nowej aplikacji usługi Azure AD. Zmień odpowiednio następujące zmienne `clientID` i `postLogoutRedirectUri`, aby korzystać z identyfikatora aplikacji i adresu URL strony głównej skopiowanego w kroku 9 sekcji [Rejestrowanie aplikacji w usłudze Azure AD](#register-the-application-with-azure-ad):

      [!code-javascript[head-sample](~/samples-javascript/pages/tutorial/index.html?range=147-153&highlight=4-5)]

      Zmieniony kod powinien wyglądać podobnie do poniższego przykładu:

      ```javascript
      clientId: '8884d4ca-b9e7-403a-bd8a-366d0ce0d460',
      postLogoutRedirectUri: 'https://tsispaapp.azurewebsites.net',
      ``` 

   c. Zapisz plik **index.html** po zakończeniu edytowania.

1. Teraz opublikuj aplikację internetową w obrębie swojej subskrypcji platformy Azure jako usługę Azure App Service:  

   > [!NOTE]
   > Niektóre pola poniższych okien dialogowych są wypełniane przy użyciu danych z subskrypcji platformy Azure. W tej sytuacji, zanim będzie można kontynuować, całkowite załadowanie każdego okna dialogowego może potrwać kilka sekund.  

   a. Kliknij prawym przyciskiem myszy węzeł projektu witryny internetowej w **Eksploratorze rozwiązań** i wybierz polecenie **Opublikuj aplikację internetową**:  

      ![VS — Eksplorator rozwiązań, publikowanie aplikacji internetowej](media/tutorial-create-tsi-sample-spa/vs-solution-explorer-publish-web-app.png)

   b. Wybierz pozycję **Microsoft Azure App Service**, aby utworzyć cel publikowania:  

      ![VS — profil publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-profile-target.png)  

   c. Wybierz subskrypcję do użycia na potrzeby publikowania aplikacji, a następnie kliknij pozycję „Nowa”: 

      ![VS — profil publikowania — usługa aplikacji](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service.png)  

   d. Zaczekaj kilka sekund na załadowanie wszystkich pól okna dialogowego **Tworzenie usługi App Service**, a następnie zmodyfikuj następujące pola:
   
      Pole | Opis
      ---|---
      **Nazwa aplikacji** | Zmień nazwę rejestracji aplikacji usługi Azure AD używaną w kroku 3 sekcji [Rejestrowanie aplikacji w usłudze Azure AD](#register-the-application-with-azure-ad). 
      **Grupa zasobów** | Przy użyciu przycisku **Nowa...** zmień wartość na odpowiadającą wartości pola **Nazwa aplikacja**.
      **Plan usługi App Service** | Przy użyciu przycisku **Nowa...** zmień wartość na odpowiadającą wartości pola **Nazwa aplikacja**.

      Po zakończeniu kliknij pozycję **Gotowe**. Przycisk **Eksportuj** w lewym dolnym rogu jest na kilka sekund zastępowany przyciskiem „Wdrażanie:” podczas tworzenia zasobów platformy Azure. Po utworzeniu zasobów nastąpi powrót do poprzedniego okna dialogowego. 

      ![VS — profil publikowania — dodawanie nowej usługi aplikacji](media/tutorial-create-tsi-sample-spa/vs-publish-profile-app-service-create.png)  

   e. Po powrocie do okna dialogowego **Publikowanie** upewnij się, że pole **Metoda publikowania** zostało ustawione na wartość „Web Deploy”. Zmień także wartość **Docelowy adres URL**, aby używać protokołu `https` zamiast `http`, w celu dopasowania do rejestracji aplikacji usługi Azure AD. Następnie kliknij przycisk „Publikuj”, aby wdrożyć aplikację internetową:  

      ![VS — publikowanie aplikacji internetowej — publikowanie usługi App Service](media/tutorial-create-tsi-sample-spa/vs-publish-publish.png)  

   f. W oknie **Dane wyjściowe** programu Visual Studio powinien pojawić się dziennik pomyślnego publikowania. Po ukończeniu wdrażania program Visual Studio otworzy również aplikację internetową na karcie przeglądarki z monitem o zalogowanie. Po pomyślnym zalogowaniu zobaczysz wszystkie kontrolki usługi TSI wypełnione przy użyciu danych:  

      [![VS — publikowanie aplikacji internetowej — dane wyjściowe dziennika publikowania](media/tutorial-create-tsi-sample-spa/vs-publish-output.png)](media/tutorial-create-tsi-sample-spa/vs-publish-output.png#lightbox)

      ![Aplikacja SPA usługi TSI — logowanie](media/tutorial-create-tsi-sample-spa/tsispaapp-azurewebsites-net.png)  

## <a name="troubleshooting"></a>Rozwiązywanie problemów  

Kod/warunek błędu | Opis
---------------------| -----------
*AADSTS50011: brak adresów odpowiedzi zarejestrowanych dla aplikacji.* | Brak właściwości „Adres URL odpowiedzi” w rejestracji usługi Azure AD. Przejdź na stronę **Ustawienia** / **Adresy URL odpowiedzi** dla rejestracji aplikacji usługi Azure AD. Sprawdź, czy adres URL **logowania jednokrotnego** określony w kroku 3 sekcji [Rejestrowanie aplikacji w usłudze Azure AD](#register-the-application-with-azure-ad) jest obecny. 
*AADSTS50011: Odpowiedź adres url określony w żądaniu jest niezgodna z adresów URL odpowiedzi skonfigurowane dla aplikacji: "\<Aplikacji identyfikator GUID >".* | Identyfikator `postLogoutRedirectUri` określony w kroku 4.b sekcji [Kompilowanie i publikowanie aplikacji internetowej](#build-and-publish-the-web-application) musi odpowiadać wartości właściwości **Ustawienia** / **Adresy URL odpowiedzi** w rejestracji aplikacji usługi Azure AD. Pamiętaj o zmianie wartości **Docelowy adres URL** w celu używania protokołu `https`, tak jak w kroku 5.e sekcji [Kompilowanie i publikowanie aplikacji internetowej](#build-and-publish-the-web-application).
Aplikacja internetowa jest ładowana, ale ma stronę logowania bez stylu i tylko z tekstem na białym tle. | Sprawdź, czy ścieżki omówione w kroku 4.a sekcji [Kompilowanie i publikowanie aplikacji internetowej](#build-and-publish-the-web-application) są prawidłowe. Jeśli aplikacja internetowa nie może znaleźć plików CSS, styl strony będzie niepoprawny.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W tym samouczku tworzysz kilka działających usług platformy Azure. Jeśli nie planujesz ukończenia pracy z tą serią samouczków, zalecamy usunięcie wszystkich zasobów w celu uniknięcia niepotrzebnych kosztów. 

W menu portalu Azure po lewej stronie:

1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów utworzoną dla środowiska TSI. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**. 
1. Kliknij ikonę **Grupy zasobów**, a następnie wybierz grupę zasobów, która została utworzona przez akcelerator rozwiązania symulacji urządzeń. W górnej części strony kliknij opcję **Usuń grupę zasobów**, wprowadź nazwę grupy zasobów, a następnie kliknij przycisk **Usuń**. 

## <a name="next-steps"></a>Kolejne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Projekt aplikacji
> * Jak zarejestrować aplikację w usłudze Azure Active Directory (AD)
> * Jak skompilować, opublikować i przetestować aplikację internetową 

Ten samouczek przedstawia integrację z usługą Azure AD przy użyciu tożsamości zalogowanego użytkownika w celu uzyskania tokenu dostępu. Aby dowiedzieć się, jak uzyskać dostęp do interfejsu API usługi TSI przy użyciu tożsamości aplikacji demona/usługi, zobacz:

> [!div class="nextstepaction"]
> [Uwierzytelnianie i autoryzacja na potrzeby interfejsu API usługi Azure Time Series Insights](time-series-insights-authentication-and-authorization.md)
