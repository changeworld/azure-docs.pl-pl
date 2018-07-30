---
title: Dostosowywanie strony logowania na potrzeby dzierżawy usługi Azure AD | Microsoft Docs
description: Dowiedz się, jak dodać znakowanie firmowe do strony logowania platformy Azure
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 07/20/2018
ms.author: lizross
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 7804d6b0d4a100997fb545e678458424dac6ceed
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2018
ms.locfileid: "39227292"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Szybki start: dodawanie znakowania firmowego do strony logowania w usłudze Azure AD
Aby uniknąć nieporozumień, wiele firm chce zastosować spójny wygląd i zachowanie we wszystkich witrynach sieci Web i usługach, którymi zarządzają. Usługa Azure Active Directory (Azure AD) oferuje tę funkcję, umożliwiając dostosowanie wyglądu strony logowania przez dodanie logo firmy i niestandardowych schematów kolorów. Strona logowania jest wyświetlana, gdy zalogujesz się do aplikacji internetowych, takich jak Office 365, które używają usługi Azure AD jako dostawcy tożsamości. Na tej stronie możesz wprowadzić swoje poświadczenia.

> [!NOTE]
> * Dodawanie znakowania firmowego jest funkcją dostępną tylko w przypadku zakupienia licencji warstwy Premium lub Podstawowa usługi Azure AD albo posiadania licencji usługi Office 365. Aby dowiedzieć się, czy funkcja jest obsługiwana przez dany typ licencji, zajrzyj na stronę [z informacjami o cenach usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Klienci w Chinach mogą używać wersji Premium i Podstawowa usługi Azure AD za pośrednictwem wystąpienia usługi Azure Active Directory dostępnego na całym świecie. Wersje Premium i Podstawowa usługi Azure AD nie są obecnie obsługiwane w usłudze platformy Azure świadczonej przez firmę 21Vianet w Chinach. Aby uzyskać więcej informacji, porozmawiaj z nami na [forum usługi Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Dostosowywanie strony logowania

<!--You can customize the following elements on the sign-in page: <attach image>-->

Dostosowania znakowania firmowego są wyświetlane na stronie logowania do usługi Azure AD, gdy użytkownicy uzyskują dostęp do adresu URL specyficznego dla dzierżawy, takiego jak [*https://outlook.com/contoso.com*](https://outlook.com/contoso.com), albo gdy zmienna domeny jest przekazywana w adresie URL, np. [*https://passwordreset.microsoftonline.com/?whr=contoso.com*](https://passwordreset.microsoftonline.com/?whr=contoso.com)

Na przykład gdy użytkownicy odwiedzają witrynę www.office.com, na stronie logowania nie są wyświetlane żadne dostosowania znakowania firmowego, ponieważ użytkownik nie wprowadził jeszcze poświadczeń. Znakowanie firmowe pojawia się, gdy użytkownik wprowadzi identyfikator lub wybierze kafelek.

> [!NOTE]
> * Nazwa domeny musi mieć stan „Aktywna” w części **Domeny** witryny Azure Portal, w której skonfigurowano znakowanie. Aby uzyskać więcej informacji, zobacz [Dodawanie niestandardowej nazwy domeny](add-custom-domain.md).
> * Znakowanie na stronie logowania nie jest przenoszone na stronę logowania do osobistych kont Microsoft. Jeśli pracownicy lub goście biznesowi logują się przy użyciu osobistego konta Microsoft, ich strony logowania nie zawierają znakowania organizacji.


### <a name="banner-logo"></a>Baner logo 

Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Baner logo jest wyświetlany na stronach logowania i panelu dostępu.<br>Na stronie logowania logo pojawia się po wprowadzeniu nazwy użytkownika. | JPG z przezroczystością lub PNG<br>Maksymalna wysokość: 36 pikseli<br>Maksymalna szerokość: 245 pikseli | W tym miejscu użyj logo organizacji.<br>Użyj obrazu przezroczystego. Nie zakładaj, że tło będzie białe.<br>Nie dodawaj wypełnienia wokół logo na obrazie, ponieważ logo będzie wyglądać na nieproporcjonalnie małe.

### <a name="username-hint"></a>Wskazówka dotycząca nazwy użytkownika   
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ta opcja dostosowuje tekst wskazówki w polu nazwy użytkownika. | Tekst Unicode, maksymalnie 64 znaki<br>Tylko zwykły tekst | Jeśli oczekujesz, że użytkownicy (goście) spoza organizacji będą logować się do aplikacji, zalecamy rezygnację z konfigurowania tej opcji.
            
### <a name="sign-in-page-text"></a>Tekst strony logowania   
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ta opcja jest wyświetlana w dolnej części formularza logowania i może służyć do przekazywania informacji dodatkowych, takich jak numer telefonu działu pomocy technicznej lub oświadczenie prawne. | Tekst Unicode, maksymalnie 256 znaków<br>Tylko zwykły tekst (bez linków lub tagów HTML)    

### <a name="sign-in-page-image"></a>Obraz strony logowania  
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ta opcja jest wyświetlana w tle strony logowania, jest zakotwiczona w środku obszaru do przeglądania oraz umożliwia skalowanie i przycinanie w celu wypełnienia okna przeglądarki.    <br>Ten obraz nie jest wyświetlany na wąskich ekranach, np. na telefonach komórkowych.<br>Podczas ładowania strony czarna maska z nieprzezroczystością o wartości 0,55 jest stosowana na tym obrazie. | JPG lub PNG<br>Wymiary obrazu: 1920 x 1080 pikseli<br>Rozmiar pliku: &lt; 300 KB | <br>Używaj obrazów w przypadku braku dużej koncentracji na obiekcie. Nieprzezroczysty formularz logowania jest wyświetlany na środku tego obrazu i może zakrywać dowolną część obrazu, w zależności od rozmiaru okna przeglądarki.<br>Zachowaj mały rozmiar pliku, aby zapewnić szybkie ładowanie. 

### <a name="sign-in-page-background-color"></a>Kolor tła strony logowania
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ten kolor jest używany zamiast obrazu tła w przypadku połączeń o niskiej przepustowości. | Kolor RGB w formacie szesnastkowym (przykład: #FFFFFF) | Zalecamy używanie podstawowego koloru banera logo lub koloru organizacji.

### <a name="square-logo-image"></a>Kwadratowy obraz logo
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ten obraz jest wyświetlany podczas instalacji na nowych komputerach przedsiębiorstwa z systemem Windows 10. Tworzy on kontekst dla pracowników, gdy konfigurują oni nowy komputer służbowy. Obraz jest wyświetlany w przypadku dzierżaw korzystających z rozwiązania [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) do wdrażania urządzeń służbowych oraz na stronach wprowadzania hasła w innych środowiskach systemu Windows 10. | PNG z przezroczystością (preferowany) lub JPG<br>Wymiary obrazu: 240 x 240 pikseli<br>Rozmiar pliku: &lt; 10 KB | W tym miejscu użyj logo organizacji.<br> Użyj obrazu przezroczystego.<br>Nie zakładaj, że tło będzie białe.<br>Nie dodawaj wypełnienia do logo na obrazie, ponieważ logo będzie wyglądać na nieproporcjonalnie małe.

### <a name="show-option-to-remain-signed-in"></a>Wyświetlanie opcji umożliwiającej pozostanie zalogowanym
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Logowanie do usługi Azure AD oferuje użytkownikowi opcję umożliwiającą pozostanie zalogowanym podczas zamykania i ponownego otwierania przeglądarki. To ustawienie powoduje ukrycie tej opcji.<br>Ustaw je na **Nie**, aby ukryć tę opcję przed użytkownikami. | &nbsp; | Ukrywanie opcji nie ma wpływu na okres istnienia sesji.<br>Niektóre funkcje usługi SharePoint Online oraz pakietu Office 2010 zależą od tego, czy użytkownicy mogą wybrać opcję pozostania zalogowanym. Jeśli ta opcja zostanie ustawiona na **Nie**, użytkownicy mogą otrzymywać dodatkowe i nieoczekiwane monity o zalogowanie.

> [!NOTE]
> Wszystkie elementy są opcjonalne. Jeśli na przykład wybierzesz baner logo bez obrazu tła, na stronie logowania zostanie wyświetlone logo i obraz tła witryny docelowej (np. usługi Office 365).

## <a name="add-company-branding-to-your-directory"></a>Dodawanie znakowania firmowego do katalogu

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym dzierżawy.
2. Wybierz kolejno pozycje **Azure Active Directory** > **Znakowanie firmowe** > **Edytuj**.
  
  ![Otwieranie znakowania niestandardowego](./media/customize-branding/navigation-to-branding.png)
3. Zmodyfikuj elementy, które chcesz dostosować. Wszystkie elementy są opcjonalne.
  
  ![Edytowanie znakowania niestandardowego](./media/customize-branding/edit-branding.png)
4. Po zakończeniu wybierz pozycję **Zapisz**.

Uwzględnienie zmian znakowania na stronie logowania może potrwać do godziny.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dodawanie specyficznego dla języka znakowania firmowego do katalogu

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz kolejno pozycje **Azure Active Directory** > **Znakowanie firmowe** > **Nowy język**.
  
  ![Dodawanie elementów znakowania specyficznego dla języka](./media/customize-branding/add-language.png)
3. Zmodyfikuj elementy, które chcesz dostosować. Wszystkie elementy są opcjonalne.
4. Po zakończeniu wybierz pozycję **Zapisz**.

Uwzględnienie zmian znakowania na stronie logowania może potrwać do godziny.

## <a name="next-steps"></a>Następne kroki
W tym przewodniku Szybki start przedstawiono sposób dodawania znakowania firmowego do katalogu usługi Azure AD. 

Poniższego linku możesz użyć do skonfigurowania znakowania firmowego w usłudze Azure AD za pomocą witryny Azure Portal.

> [!div class="nextstepaction"]
> [Configure company branding (Konfigurowanie oznaczenia marką firmy)](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 