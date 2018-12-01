---
title: Autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory — usłudze Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak autoryzować użytkowników przy użyciu usługi Azure Active Directory w usłudze API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: adbe33bedf23e37c2d3fd485965dea86edbd269a
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681470"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory w usłudze Azure API Management

W tym artykule pokazano, jak umożliwić dostęp do portalu dla deweloperów dla użytkowników z usługi Azure Active Directory (Azure AD). Ten przewodnik pokazuje również jak zarządzać grupami użytkowników usługi Azure AD przez dodanie grup zewnętrznych, które zawierają użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzowanie kont deweloperów za pomocą usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
1. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
1. Typ **api** w polu wyszukiwania.
1. Wybierz **usługi API Management**.
1. Wybierz swoje wystąpienie usługi API Management.
1. W obszarze **zabezpieczeń**, wybierz opcję **tożsamości**.

1. Wybierz **+ Dodaj** od góry.

    **Dostawcy tożsamości Dodaj** zostanie wyświetlone okienko po prawej stronie.
1. W obszarze **typ dostawcy**, wybierz opcję **usługi Azure Active Directory**.

    Formanty, które umożliwiają wprowadź inne potrzebne informacje są wyświetlane w okienku. Formanty obejmują **identyfikator klienta** i **klucz tajny klienta**. (Możesz uzyskać informacje o tych kontrolek w dalszej części tego artykułu.)
1. Zwróć uwagę na zawartość **adresu URL przekierowania**.
    
   ![Kroki, aby dodać dostawcę tożsamości w witrynie Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
1. W przeglądarce otwórz inną kartę. 
1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
1. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
1. Typ **active**. **Usługi Azure Active Directory** zostanie wyświetlone okienko.
1. Wybierz pozycję **Azure Active Directory**.
1. W obszarze **ZARZĄDZAJ**, wybierz opcję **rejestracje aplikacji**.
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.

    ![Opcje tworzenia nowej rejestracji aplikacji](./media/api-management-howto-aad/api-management-with-aad002.png)

    **Utwórz** zostanie wyświetlone okienko po prawej stronie. To, gdzie możesz wprowadzić informacje istotne dla aplikacji usługi Azure AD.
1. Wprowadź nazwę aplikacji.
1. Typ aplikacji wybierz **aplikacji/interfejs API sieci Web**.
1. Zaloguj się adres URL wprowadź adres URL logowania swojego portalu dla deweloperów. W tym przykładzie adres URL logowania jest `https://apimwithaad.portal.azure-api.net/signin`.
1. Wybierz **Utwórz** do tworzenia aplikacji.
1. Aby znaleźć aplikację, wybierz **rejestracje aplikacji** i wyszukiwanie według nazwy.

    ![Pole, gdzie możesz wyszukać aplikację](./media/api-management-howto-aad/find-your-app.png)
1. Po zarejestrowaniu aplikacji przejdź do **adres URL odpowiedzi** i upewnij się, że **adresu URL przekierowania** jest ustawiona na wartość uzyskany w kroku 9. 
1. Jeśli chcesz skonfigurować aplikację (na przykład zmienić **adres URL Identyfikatora aplikacji**), wybierz opcję **właściwości**.

    ![Otwieranie okienka "Właściwości"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Jeśli wiele wystąpień usługi Azure AD będą stosowane dla tej aplikacji, wybierz opcję **tak** dla **wielodostępnych**. Wartość domyślna to **nie**.
1. Ustaw uprawnienia aplikacji, wybierając **wymagane uprawnienia**.
1. Wybierz swoją aplikację, a następnie wybierz **Odczyt danych katalogu** i **Zaloguj się i odczytuj profil użytkownika** pola wyboru.

    ![Pola wyboru dla uprawnień](./media/api-management-howto-aad/api-management-with-aad005.png)

1. Wybierz **udzielić uprawnień** zgody uprawnień aplikacji.

    Aby uzyskać więcej informacji o uprawnieniach aplikacji i uprawnień delegowanych, zobacz [uzyskiwania dostępu do interfejsu API programu Graph][Accessing the Graph API].
    
1. W okienku po lewej stronie, skopiuj **identyfikator aplikacji** wartość.

    ![Wartość "Identyfikator aplikacji"](./media/api-management-howto-aad/application-id.png)
1. Przejdź z powrotem do aplikacji interfejsu API zarządzania. 

    W **dostawcy tożsamości Dodaj** okna, Wklej **identyfikator aplikacji** wartość w **identyfikator klienta** pole.
1. Przejdź z powrotem do konfiguracji usługi Azure AD, a następnie wybierz pozycję **klucze**.
1. Utwórz nowy klucz, określając nazwę i czas trwania. 
1. Wybierz pozycję **Zapisz**. Klucz jest generowany.

    Skopiuj klucz do Schowka.

    ![Opcje tworzenia klucza](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Zanotuj ten klucz. Po zamknięciu okienko konfiguracji usługi Azure AD, klucz nie może ponownie wyświetlone.
    > 
    > 

1. Przejdź z powrotem do aplikacji interfejsu API zarządzania. 

    W **dostawcy tożsamości Dodaj** okna, Wklej klucz w **klucz tajny klienta** pola tekstowego.

    > [!IMPORTANT]
    > Upewnij się zaktualizować **klucz tajny klienta** przed wygaśnięciem klucza. 
    >  
    >

1. **Dostawcy tożsamości Dodaj** okno zawiera również **dozwolone dzierżaw** pola tekstowego. Określ domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usługi API Management. Możesz oddzielić wiele domen tabulacji, spacjami lub przecinkami.

    Można określić wiele domen w **dozwolone dzierżaw** sekcji. Zanim każdy użytkownik może zalogować się w innej domenie niż oryginalną domenę, w którym aplikacja została zarejestrowana, tylko administrator globalny innej domeny musi udzielić uprawnień do dostępu do danych katalogu aplikacji. Aby udzielić uprawnień, powinien administratora globalnego:
    
    a. Przejdź do `https://<URL of your developer portal>/aadadminconsent` (na przykład https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Wpisz nazwę domeny dzierżawy usługi Azure AD, który ma zostać zapewniony dostęp.
    
    c. Wybierz **przesłać**. 
    
    W poniższym przykładzie administrator globalny miaoaad.onmicrosoft.com próbuje przyznać uprawnienia do tego portalu określonego dla deweloperów. 

1. Po określeniu pożądanej konfiguracji, wybierz **Dodaj**.

    !["Dodaj" przycisk "Dodaj dostawcę tożsamości" okienka](./media/api-management-howto-aad/api-management-with-aad007.png)

Po zapisaniu zmian użytkowników w określonej usłudze Azure AD wystąpienia zalogować się do portalu dla deweloperów, wykonując kroki opisane w [Zaloguj się do portalu dla deweloperów przy użyciu konta usługi Azure AD](#log_in_to_dev_portal).

![Wprowadź nazwę dzierżawy usługi Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Na następnym ekranie administratora globalnego jest monit o potwierdzenie, zapewniając uprawnienia. 

![Potwierdzenie przypisywanie uprawnień](./media/api-management-howto-aad/api-management-permissions-form.png)

Jeśli administrator nieglobalnych próbuje zalogować przed uprawnienia administratora globalnego, próba logowania nie powiedzie się i zostanie wyświetlony ekran błędu.

## <a name="add-an-external-azure-ad-group"></a>Dodaj zewnętrzne grupy usługi Azure AD

Po włączeniu dostępu dla użytkowników w wystąpieniu usługi Azure AD, można dodać grupy usługi Azure AD w usłudze API Management. Następnie można łatwiej zarządzać skojarzenie deweloperów w grupie z żądane produkty.

Aby skonfigurować zewnętrznego grupy usługi Azure AD, należy najpierw skonfigurować wystąpienie usługi Azure AD na **tożsamości** kartę zgodnie z procedurą w poprzedniej sekcji. 

Dodaj zewnętrzne Azure grup usługi AD z **grup** kartę wystąpienia usługi API Management.

1. Wybierz kartę **Grupy**.
1. Wybierz **grupy AAD Dodaj** przycisku.
   ![Przycisk "Dodaj grupę usługi AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Wybierz grupę, do której chcesz dodać.
1. Naciśnij klawisz **wybierz** przycisku.

Po dodaniu zewnętrznych usługi Azure AD grupy, można przeglądać i skonfiguruj jego właściwości. Wybierz nazwę grupy z **grup** kartę. W tym miejscu można edytować **nazwa** i **opis** informacje dotyczące grupy.
 
Użytkownicy ze skonfigurowanego wystąpienia usługi Azure AD mogą teraz logować się do portalu dla deweloperów. Mogą wyświetlać i subskrybować żadnych grup, do których mają widoczność.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Zaloguj się do portalu dla deweloperów przy użyciu konta usługi Azure AD

Aby zalogować się do portalu dla deweloperów przy użyciu konta usługi Azure AD, które zostały skonfigurowane w poprzednich sekcjach:

1. Otwórz nowe okno przeglądarki, za pomocą adresu URL logowania z konfiguracji aplikacji usługi Active Directory, a następnie wybierz pozycję **usługi Azure Active Directory**.

   ![Strona logowania][api-management-dev-portal-signin]

1. Wprowadź poświadczenia użytkownika w usłudze Azure AD, a następnie wybierz pozycję **Zaloguj**.

   ![Logowanie się przy użyciu nazwy użytkownika i hasła][api-management-aad-signin]

1. Użytkownik może zostać wyświetlony monit z formularza rejestracji Jeśli wymagana jest żadnych dodatkowych informacji. Wypełnij formularz rejestracji, a następnie wybierz pozycję **Zarejestruj**.

   ![Przycisk "Zarejestruj" na formularz rejestracji][api-management-complete-registration]

Użytkownik jest zalogowany do portalu dla deweloperów dla swojego wystąpienia usługi API Management.

![Portal dla deweloperów po zakończeniu rejestracji][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
