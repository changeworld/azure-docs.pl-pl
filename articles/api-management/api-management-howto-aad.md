---
title: Autoryzowanie konta dewelopera przy użyciu usługi Azure Active Directory — Zarządzanie interfejsami API Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i autoryzacji użytkowników przy użyciu usługi Azure Active Directory w usłudze API Management.
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
ms.openlocfilehash: b0476865b19cd078b05e5def4a51c2df17315daa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzowanie konta dewelopera przy użyciu usługi Azure Active Directory w usłudze Azure API Management

W tym artykule przedstawiono sposób umożliwić dostęp do portalu dla deweloperów dla użytkowników z usługi Azure Active Directory (Azure AD). Ten przewodnik zawiera również sposób zarządzania przez dodanie zewnętrznej grupy, które zawierają użytkowników, grup użytkowników usługi Azure AD.

> [!NOTE]
> Integracja z usługą Azure AD jest dostępna w [Developer, Standard i Premium](https://azure.microsoft.com/pricing/details/api-management/) tylko warstwy.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i opublikować wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Import i opublikuj](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzowanie konta dewelopera przy użyciu usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
3. Typ **interfejsu api** w polu wyszukiwania.
4. Wybierz **usługi Zarządzanie interfejsami API**.
5. Wybierz wystąpienia usługi Zarządzanie interfejsami API.
6. W obszarze **zabezpieczeń**, wybierz pozycję **tożsamości**.

7. Wybierz **+ Dodaj** od góry.

    **Dostawcy tożsamości Dodaj** okienko pojawia się po prawej stronie.
8. W obszarze **typ dostawcy**, wybierz pozycję **usługi Azure Active Directory**.

    Formanty, które umożliwiają wprowadź inne potrzebne informacje są wyświetlane w okienku. Dostępne kontrolki **identyfikator klienta** i **klucz tajny klienta**. (Można pobrać informacji o tych kontrolek w dalszej części tego artykułu.)
9. Zanotuj zawartość **adresem URL przekierowania**.
    
   ![Procedura dodawania dostawcy tożsamości w portalu Azure](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. W przeglądarce otwórz innej karty. 
11. Przejdź do witryny [Azure Portal](https://portal.azure.com).
12. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
13. Typ **active**. **Usługi Azure Active Directory** pojawi się okienko.
14. Wybierz pozycję **Azure Active Directory**.
15. W obszarze **ZARZĄDZAJ**, wybierz pozycję **rejestracji aplikacji**.
16. Wybierz pozycję **Rejestrowanie nowej aplikacji**.

    ![Wybrane elementy do tworzenia nowej rejestracji aplikacji](./media/api-management-howto-aad/api-management-with-aad002.png)

    **Utwórz** okienko pojawia się po prawej stronie. To, gdzie możesz wprowadzić informacje istotne dla aplikacji usługi Azure AD.
17. Wprowadź nazwę dla aplikacji.
18. Wybierz typ aplikacji **aplikacji/interfejs API sieci Web**.
19. W przypadku URL logowania wprowadź adres URL logowania swojego portalu dla deweloperów. W tym przykładzie adres URL logowania jest https://apimwithaad.portal.azure-api.net/signin.
20. Wybierz **Utwórz** do tworzenia aplikacji.
21. Aby znaleźć aplikacji, wybierz **rejestracji aplikacji** i Wyszukaj według nazwy.

    ![Wpisz, gdzie Wyszukaj aplikację](./media/api-management-howto-aad/find-your-app.png)
22. Po zarejestrowaniu aplikacji, przejdź do **adres URL odpowiedzi** i upewnij się, że **adresem URL przekierowania** ma ustawioną wartość pochodzący z kroku 9. 
23. Jeśli chcesz skonfigurować aplikację (na przykład zmienić **adres URL Identyfikatora aplikacji**), wybierz pozycję **właściwości**.

    ![Otwieranie okienka "Właściwości"](./media/api-management-howto-aad/api-management-with-aad004.png)

    Jeśli wiele wystąpień usługi Azure AD będzie używany dla tej aplikacji, wybierz **tak** dla **dzierżawcza Multi**. Wartość domyślna to **nr**.
24. Ustawianie uprawnienia aplikacji przez wybranie **wymagane uprawnienia**.
25. Wybierz aplikację, a następnie wybierz **odczytuj dane katalogu** i **Zaloguj się i odczytuj profil użytkownika** pola wyboru.

    ![Pola wyboru dla uprawnień](./media/api-management-howto-aad/api-management-with-aad005.png)

    Aby uzyskać więcej informacji o uprawnieniach aplikacji i uprawnień delegowanych, zobacz [podczas uzyskiwania dostępu do interfejsu API programu Graph][Accessing the Graph API].
26. W okienku po lewej stronie, skopiuj **identyfikator aplikacji** wartość.

    ![Wartość "Identyfikator aplikacji"](./media/api-management-howto-aad/application-id.png)
27. Przełącz się do aplikacji interfejsu API zarządzania. 

    W **dostawcy tożsamości Dodaj** okna, Wklej **identyfikator aplikacji** wartość w **identyfikator klienta** pole.
28. Wrócić do konfiguracji usługi Azure AD, a następnie wybierz **klucze**.
29. Utwórz nowy klucz, określając nazwę i czas trwania. 
30. Wybierz pozycję **Zapisz**. Klucz jest generowany.

    Skopiuj klucz do Schowka.

    ![Wybrane elementy do tworzenia kluczy](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Zanotuj tego klucza. Po zamknięciu okienko konfiguracji usługi Azure AD, klucz nie może ponownie wyświetlone.
    > 
    > 
31. Przełącz się do aplikacji interfejsu API zarządzania. 

    W **dostawcy tożsamości Dodaj** okna, Wklej klucz w **klucz tajny klienta** pola tekstowego.

    > [!IMPORTANT]
    > Upewnij się zaktualizować **klucz tajny klienta** przed wygaśnięciem klucza. 
    >  
    >
32. **Dostawcy tożsamości Dodaj** okno zawiera również **dozwolone dzierżaw** pola tekstowego. Określ domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usługi Zarządzanie interfejsami API. Wiele domen można oddzielić newlines, spacjami lub przecinkami.

    Można określić wiele domen w **dozwolone dzierżaw** sekcji. Aby każdy użytkownik może zalogować się z innej domeny niż domena oryginalnego rejestracji aplikacji, administrator globalny innej domeny musi udzielić uprawnień do dostępu do danych katalogu aplikacji. Aby udzielić uprawnień, administrator globalny następujące czynności:
    
    a. Przejdź do `https://<URL of your developer portal>/aadadminconsent` (na przykład https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Wpisz nazwę domeny dzierżawy usługi Azure AD, które chcą, aby zapewnić dostęp do.
    
    c. Wybierz **przesłać**. 
    
    W poniższym przykładzie administrator globalny miaoaad.onmicrosoft.com próbuje nadać uprawnienia do tego portalu dewelopera w konkretnym. 

33. Po określeniu żądanej konfiguracji, wybierz **Dodaj**.

    !["Dodaj" przycisk "Dodaj dostawcy tożsamości" okienka](./media/api-management-howto-aad/api-management-with-aad007.png)

Po zapisaniu zmian, użytkownicy w określonej usłudze Azure AD wystąpienia móc zalogować się w portalu dla deweloperów, wykonując kroki opisane w [Zaloguj się do portalu dla deweloperów, korzystając z konta usługi Azure AD](#log_in_to_dev_portal).

![Wprowadź nazwę dzierżawy usługi Azure AD](./media/api-management-howto-aad/api-management-aad-consent.png)

Na następnym ekranie administratora globalnego jest monit o potwierdzenie, nadanie uprawnień. 

![Potwierdzenie przypisywanie uprawnień](./media/api-management-howto-aad/api-management-permissions-form.png)

Jeśli administrator globalny nie próbuje zalogować uprawnienia administratora globalnego, próba logowania nie powiedzie się i zostanie wyświetlony komunikat o błędzie.

## <a name="add-an-external-azure-ad-group"></a>Dodaj zewnętrzne grupy usługi Azure AD

Po włączeniu dostępu dla użytkowników w wystąpieniu usługi Azure AD, można dodać grup usługi Azure AD w usłudze API Management. Następnie mogą łatwiej zarządzać skojarzenie deweloperów w grupie z żądaną produktów.

Aby skonfigurować zewnętrzne grupy usługi Azure AD, należy najpierw skonfigurować wystąpienie usługi Azure AD na **tożsamości** kartę, wykonując poniższe kroki w poprzedniej sekcji. 

Dodaj zewnętrzne Azure AD grup z **grup** kartę wystąpienia interfejsu API zarządzania.

1. Wybierz kartę **Grupy**.
2. Wybierz **grupy dodać AAD** przycisku.
   ![Przycisk "Dodaj grupę AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Wybierz grupę, którą chcesz dodać.
4. Naciśnij klawisz **wybierz** przycisku.

Po dodaniu zewnętrznych usługi Azure AD grupy, można sprawdzić i skonfiguruj jego właściwości. Wybierz nazwę grupy z **grup** kartę. W tym miejscu można edytować **nazwa** i **opis** informacje o tworzonej grupie.
 
Użytkownicy ze skonfigurowanego wystąpienia usługi Azure AD można teraz logować się do portalu dla deweloperów. Mogą wyświetlać i subskrybować żadnej z grup, do których mają widoczność.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Zaloguj się do portalu dla deweloperów, korzystając z konta usługi Azure AD

Do logowania się do portalu dla deweloperów, korzystając z konta usługi Azure AD, skonfigurowanego w poprzednich sekcjach:

1. Otwórz nowe okno przeglądarki przy użyciu adresu URL logowania z konfiguracji aplikacji usługi Active Directory i wybierz **usługi Azure Active Directory**.

   ![Strona logowania][api-management-dev-portal-signin]

2. Wprowadź poświadczenia użytkowników w usłudze Azure AD, a następnie wybierz **Zaloguj**.

   ![Logowanie się przy użyciu nazwy użytkownika i hasła][api-management-aad-signin]

3. Użytkownik może zostać wyświetlony monit z formularza rejestracji Jeśli żadnych dodatkowych informacji jest wymagane. Wypełnij formularz rejestracji, a następnie wybierz **Zarejestruj**.

   !["Zarejestruj" przycisk w formularzu rejestracji][api-management-complete-registration]

Że użytkownik jest zalogowany do portalu dla deweloperów wystąpienia usługi Zarządzanie interfejsami API.

![Po zakończeniu rejestracji portalu dla deweloperów][api-management-registration-complete]

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
