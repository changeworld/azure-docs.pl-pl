---
title: Dostosowywanie strony logowania dla dzierżawy usługi Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać znakowanie firmowe do platformy Azure strony logowania
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: article
ms.date: 05/23/2018
ms.author: curtand
ms.reviewer: kexia
custom: it-pro
ms.openlocfilehash: 5573399b33da64df8b9bbf78a9583572cf148d7b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34713851"
---
# <a name="quickstart-add-company-branding-to-your-sign-in-page-in-azure-ad"></a>Szybki Start: Dodawanie znakowania firmowego do stron logowania w usłudze Azure AD
Aby uniknąć nieporozumień, wiele firm chce zastosować spójny wygląd i zachowanie we wszystkich witrynach sieci Web i usługach, którymi zarządzają. Azure Active Directory (Azure AD) zapewnia tę funkcję, umożliwiając dostosowanie wyglądu strony logowania z logo firmy i niestandardowych schematów kolorów. Strona logowania zostanie wyświetlony po zalogowaniu się do aplikacji opartych na sieci web takich jak Office 365 korzystających z usługi Azure AD jako dostawcy tożsamości. Użytkownik interakcji z tej strony, aby wprowadzić swoje poświadczenia.

> [!NOTE]
> * Logo firmy jest dostępna tylko w przypadku zakupu dla usługi Azure AD Premium lub podstawowa licencji lub ma licencji usługi Office 365. Aby dowiedzieć się, czy funkcja jest obsługiwana przez użytkownika typu licencji, sprawdź [cenową strona informacji o usłudze Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
> 
> * Wersje usługi Azure AD Premium i podstawowa są dostępne dla klientów w Chinach przy użyciu wystąpienia na całym świecie usługi Azure Active Directory. Wersje usługi Azure AD Premium i podstawowa nie są obecnie obsługiwane w Azure świadczonej przez 21Vianet w Chinach. Aby uzyskać więcej informacji, Porozmawiaj z nami pod [Azure Active Directory Forum](https://feedback.azure.com/forums/169401-azure-active-directory/).

## <a name="customizing-the-sign-in-page"></a>Dostosowywanie strony logowania

<!--You can customize the following elements on the sign-in page: <attach image>-->

Dostosowywanie znakowania wyświetlane na stronie logowania usługi Azure AD, gdy użytkownicy uzyskują dostęp do adresu URL specyficznego dla dzierżawy takich jak firmy [ *https://outlook.com/contoso.com* ](https://outlook.com/contoso.com).

Na przykład podczas odwiedzania www.office.com, strony logowania nie wyświetla żadnych firmy znakowania dostosowań, ponieważ użytkownik nie ma jeszcze wprowadzone poświadczenia. Gdy użytkownik wprowadza identyfikator użytkownika lub wybiera kafelka użytkownika, wyświetla logo firmy.

> [!NOTE]
> * Nazwa domeny musi występować jako "Aktywna" w **domen** części portalu Azure, w którym skonfigurowano znakowanie. Aby uzyskać więcej informacji, zobacz [Dodawanie niestandardowej nazwy domeny](add-custom-domain.md).
> * Znakowanie na stronie logowania nie jest przenoszone na stronę logowania do osobistych kont Microsoft. Jeśli firm gości lub pracowników, na których Zaloguj się za pomocą osobistego konta Microsoft, ich strony logowania nie odzwierciedla znakowanie organizacji.


### <a name="banner-logo"></a>Baner logo 

Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Baner logo jest wyświetlany na przy logowaniu i strony panelu dostępu.<br>Na stronie logowania logo pokazuje po wprowadzeniu nazwy użytkownika. | Przezroczysty JPG lub PNG<br>Maksymalna wysokość: 36 pikseli<br>Maksymalna szerokość: 245 pikseli | W tym miejscu użyć logo organizacji.<br>Użyj przezroczystego obrazu. Nie wolno zakładać, że będzie białe tło.<br>Nie dodawaj wypełnienia wokół logo obraz lub logo będzie wyglądać nieproporcjonalnie małych.

### <a name="username-hint"></a>Wskazówka dotycząca nazwy użytkownika   
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ta opcja dostosowuje tekst wskazówki w polu nazwy użytkownika. | Tekst Unicode, maksymalnie 64 znaki.<br>Tylko zwykły tekst | Jeśli gościa użytkowników spoza organizacji, aby zalogować się do aplikacji, zaleca się nie ustawisz takiego wyboru.
            
### <a name="sign-in-page-text"></a>Tekst strony logowania   
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ta opcja jest wyświetlana w dolnej części formularza logowania i może służyć do komunikowania się dodatkowe informacje, takie jak numer telefonu do działu pomocy technicznej lub klauzulę prawną. | Tekst Unicode, maksymalnie 256 znaków<br>Tylko zwykły tekst (bez linków lub tagów HTML)    

### <a name="sign-in-page-image"></a>Obraz strony logowania  
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
To opcja jest dostępna w tle strony logowania jest zakotwiczona Centrum widoczny miejsca i możliwość skalowania i zbiorów do wypełnienia okna przeglądarki.    <br>Ten obraz nie jest wyświetlany na wąskich ekranach takich jak telefony komórkowe.<br>Czarne maski z 0.55 nieprzezroczystość jest stosowane za pośrednictwem tego obrazu podczas ładowania strony. | JPG lub PNG<br>Wymiary obrazu: 1920 x 1080 pikseli<br>Rozmiar pliku: &lt; 300 KB | <br>Obrazy w przypadku, gdy nie ma fokusu silne podmiotu. Nieprzezroczyste formularz logowania zostanie wyświetlony w Centrum ten obraz i może obejmować dowolną część obrazu, w zależności od rozmiaru okna przeglądarki.<br>Zachowaj rozmiar pliku jest mały, aby zapewnić szybkie ładować. 

### <a name="sign-in-page-background-color"></a>Kolor tła strony logowania
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Kolor używany zamiast obraz tła połączeń o niskiej przepustowości. | Kolor RGB w formacie szesnastkowym (przykład: #FFFFFF | Zaleca się przy użyciu podstawowego koloru baneru logo lub kolor Twojej organizacji.

### <a name="square-logo-image"></a>Obraz logo kwadratowe
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Ten obraz pojawia się podczas instalacji dla nowych komputerów z przedsiębiorstwa systemu Windows 10. Tworzy kontekst pracownikom podczas konfigurowania swoimi Komputerami roboczymi nowe. Obraz jest wyświetlany w przypadku dzierżaw, które używają [Windows AutoPilot](https://blogs.windows.com/business/2017/06/29/delivering-modern-promise-windows-10/?utm_source=dlvr.it&utm_medium=twitter#gDTp1u6q35bvDWIS.97) wdrażania urządzeń pracy oraz na wprowadzanie hasła zmniejszania stron w innych systemu Windows 10. | Przezroczysty (preferowane) PNG lub JPG<br>Wymiary obrazu: 240 x 240 pikseli<br>Rozmiar pliku: &lt; 10 KB | W tym miejscu użyć logo organizacji.<br> Użyj przezroczystego obrazu.<br>Nie wolno zakładać, że będzie białe tło.<br>Nie należy dodawać uzupełnienie do logo w obrazie lub logo będzie wyglądać nieproporcjonalnie mała.

### <a name="show-option-to-remain-signed-in"></a>Wyświetl opcję umożliwiającą pozostanie zalogowanym
Opis | Ograniczenia | Zalecenia
------- | ------- | ----------
Azure AD logowania zapewnia użytkownika, którego opcja pozostaje zalogowany po zamknięciu i ponownie otwórz w przeglądarce. To ustawienie powoduje ukrycie tej opcji.<br>Ustaw **nr** ukrycia tej opcji od użytkowników. | &nbsp; | Ukrywanie opcji nie ma wpływu na okres istnienia sesji.<br>Niektóre funkcje pakietu Office 2010 i SharePoint Online są zależne od użytkownicy będą mogli wybrać wylogować. Jeśli ustawisz tę opcję, **nr**, użytkownicy mogą Zobacz dodatkowe i nieoczekiwane monit logowania.

> [!NOTE]
> Wszystkie elementy są opcjonalne. Na przykład jeśli określisz Baner logo z żadnego obrazu tła strony logowania zostaną wyświetlone logo i obraz tła dla lokacji docelowej (na przykład usługi Office 365).

## <a name="add-company-branding-to-your-directory"></a>Dodawanie znakowania firmowego do katalogu

1. Zaloguj się do [Centrum administracyjnego usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym dla dzierżawy.
2. Wybierz **usługi Azure Active Directory** > **firmy znakowania** > **Edytuj**.
  
  ![Otwieranie znakowania niestandardowych](./media/customize-branding/navigation-to-branding.png)
3. Zmodyfikuj elementy, które chcesz dostosować. Wszystkie elementy są opcjonalne.
  
  ![Edytuj niestandardowe znakowanie](./media/customize-branding/edit-branding.png)
4. Gdy wszystko będzie gotowe, wybierz **zapisać**.

Może potrwać do godziny wszelkie zmiany wprowadzone do strony logowania znakowania na.

## <a name="add-language-specific-company-branding-to-your-directory"></a>Dodaj specyficzne dla języka znakowanie firmowe do katalogu

1. Zaloguj się w [centrum administracyjnym usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta, które jest administratorem globalnym katalogu.
2. Wybierz **usługi Azure Active Directory** > **firmy znakowania** > **nowy język**.
  
  ![Dodawanie znakowania elementy specyficzne dla języka](./media/customize-branding/add-language.png)
3. Zmodyfikuj elementy, które chcesz dostosować. Wszystkie elementy są opcjonalne.
4. Gdy wszystko będzie gotowe, wybierz **zapisać**.

Może potrwać do godziny wszelkie zmiany wprowadzone do strony logowania znakowania na.

## <a name="next-steps"></a>Kolejne kroki
W tym szybkiego startu kiedy znasz już jak dodać znakowanie firmowe do katalogu usługi Azure AD. 

Poniższe łącze służy do konfigurowania firmowe w usłudze Azure AD z portalu Azure.

> [!div class="nextstepaction"]
> [Configure company branding (Konfigurowanie oznaczenia marką firmy)](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/LoginTenantBrandingBlade) 