---
title: Autoryzuj konta dewelopera przy użyciu Azure Active Directory API Management platformy Azure | Microsoft Docs
description: Dowiedz się, jak autoryzować użytkowników przy użyciu Azure Active Directory w API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b0c3487bb77f32483d6d65cd0a4b1f637267eabf
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74144357"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzuj konta dewelopera przy użyciu Azure Active Directory na platformie Azure API Management

W tym artykule opisano sposób włączania dostępu do portalu dla deweloperów dla użytkowników z Azure Active Directory (Azure AD). W tym przewodniku pokazano również, jak zarządzać grupami użytkowników usługi Azure AD, dodając grupy zewnętrzne zawierające użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzuj konta dewelopera przy użyciu usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz pozycję ![strzałkę](./media/api-management-howto-aad/arrow.png).
3. Wpisz w polu wyszukiwania **interfejs API** .
4. Wybierz pozycję **API Management Services**.
5. Wybierz swoje wystąpienie usługi API Management.
6. W obszarze **zabezpieczenia**wybierz pozycję **tożsamości**.
7. Wybierz pozycję **+ Dodaj** z góry.

    Po prawej stronie pojawi się okienko **Dodawanie dostawcy tożsamości** .
8. W obszarze **Typ dostawcy**wybierz pozycję **Azure Active Directory**.

    Kontrolki, które umożliwiają wprowadzanie innych niezbędnych informacji, są wyświetlane w okienku. Formanty obejmują **Identyfikator klienta** i **klucz tajny klienta**. (Informacje o tych kontrolkach są uzyskiwane w dalszej części artykułu).
9. Zanotuj zawartość **adresu URL przekierowania**.
    
   ![Procedura dodawania dostawcy tożsamości w Azure Portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. W przeglądarce Otwórz inną kartę. 
11. Przejdź do [Rejestracje aplikacji Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908) , aby zarejestrować aplikację w Active Directory.
12. W obszarze **Zarządzaj**wybierz pozycję **rejestracje aplikacji**.
13. Wybierz pozycję **Nowa rejestracja**. Na stronie **zarejestruj aplikację** Ustaw wartości w następujący sposób:
    
* Ustaw **nazwę** na opisową. na przykład *deweloper — Portal*
* Ustaw **obsługiwane typy kont** **tylko na konta w tym katalogu organizacji**. 
* Ustaw **Identyfikator URI przekierowania** na wartość uzyskaną w kroku 9. 
* Wybierz pozycję **zarejestruj**. 

14.  Po zarejestrowaniu aplikacji Skopiuj **Identyfikator aplikacji (klienta)** ze strony **Przegląd** . 
15. Wróć do wystąpienia API Management. W oknie **Dodawanie dostawcy tożsamości** wklej wartość **identyfikatora aplikacji (klienta)** do pola **Identyfikator klienta** .
16. Przejdź z powrotem do konfiguracji usługi Azure AD, wybierz pozycję **certyfikaty & wpisy tajne** w obszarze **Zarządzaj**. Wybierz przycisk **nowy klucz tajny klienta** . Wprowadź wartość w polu **Opis**, wybierz dowolną opcję **wygaśnięcia** i wybierz pozycję **Dodaj**. Skopiuj wartość klucza tajnego klienta przed opuszczeniem strony. Będą one potrzebne w kolejnym kroku. 
17. W obszarze **Zarządzaj**wybierz pozycję **uwierzytelnianie** , a następnie pozycję **tokeny ID** w obszarze **niejawne udzielenie**
18. Wróć do wystąpienia API Management, wklej wpis tajny do pola **klucz tajny klienta** .

    > [!IMPORTANT]
    > Upewnij się, że klucz **tajny klienta** został zaktualizowany przed wygaśnięciem klucza. 
    >  
    >

19. Okno **Dodawanie dostawcy tożsamości** zawiera również pole tekstowe **dozwolone dzierżawy** . W tym miejscu należy określić domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usług API Management. Można rozdzielić wiele domen z wierszami, spacjami lub przecinkami.

> [!NOTE]
> W sekcji **dozwolone dzierżawy** możesz określić wiele domen. Zanim użytkownik będzie mógł zalogować się z innej domeny niż oryginalna domena, w której zarejestrowano aplikację, Administrator globalny innej domeny musi udzielić uprawnienia aplikacji dostępu do danych katalogu. Aby udzielić uprawnienia, Administrator globalny powinien: a. Przejdź do `https://<URL of your developer portal>/aadadminconsent` (na przykład https://contoso.portal.azure-api.net/aadadminconsent).
> b. Wpisz nazwę domeny dzierżawy usługi Azure AD, do której chcesz udzielić dostępu.
> d. Wybierz **przesłać**. 

20.  Po określeniu odpowiedniej konfiguracji wybierz pozycję **Dodaj**.

Po zapisaniu zmian użytkownicy w określonym wystąpieniu usługi Azure AD mogą zalogować się do portalu dla deweloperów, wykonując czynności opisane w temacie [Logowanie do portalu dla deweloperów przy użyciu konta usługi Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Dodaj zewnętrzną grupę usługi Azure AD

Po włączeniu dostępu dla użytkowników w dzierżawie usługi Azure AD można dodać grupy usługi Azure AD do API Management. W związku z tym można kontrolować widoczność produktu przy użyciu grup usługi Azure AD.

Aby dodać zewnętrzną grupę usługi Azure AD do usługi APIM, należy najpierw wykonać poprzednią sekcję. Ponadto zarejestrowana aplikacja musi mieć udzielony dostęp do Azure Active Directory interfejs API programu Graph z uprawnieniami `Directory.ReadAll`, wykonując następujące czynności: 

1. Wróć do rejestracji aplikacji, która została utworzona w poprzedniej sekcji
2. Kliknij kartę **uprawnienia interfejsu API** , a następnie kliknij przycisk **+ Dodaj przycisk uprawnienia** 
3. W okienku **uprawnienia do interfejsu API żądania** wybierz kartę **interfejsy API firmy Microsoft** , a następnie przewiń w dół, aby znaleźć kafelek **Azure Active Directory Graph** w sekcji obsługiwane starsze interfejsy API, a następnie kliknij go. Następnie kliknij przycisk **uprawnienia aplikacji** , a następnie wybierz pozycję **katalog.** poddana uprawnienia, a następnie Dodaj uprawnienie przy użyciu przycisku u dołu. 
4. Kliknij przycisk **Udziel zgody administratora dla {dzierżawca}** , aby przyznać dostęp wszystkim użytkownikom w tym katalogu. 

Teraz możesz dodać zewnętrzne grupy usługi Azure AD z karty **grupy** wystąpienia API Management.

1. Wybierz kartę **Grupy**.
2. Wybierz przycisk **Dodaj grupę usługi AAD** .
   ![przycisk "Dodaj grupę usługi AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Wybierz grupę, którą chcesz dodać.
4. Naciśnij przycisk **Wybierz** .

Po dodaniu zewnętrznej grupy usługi Azure AD można przejrzeć i skonfigurować jej właściwości. Wybierz nazwę grupy na karcie **grupy** . W tym miejscu można edytować **nazwy** i informacje **opisu** dla grupy.
 
Użytkownicy ze skonfigurowanego wystąpienia usługi Azure AD mogą teraz logować się do portalu dla deweloperów. Mogą oni wyświetlać i subskrybować wszystkie grupy, do których mają wgląd.

## <a name="a-idlog_in_to_dev_portal-developer-portal---add-azure-ad-account-authentication"></a>Portal dla deweloperów <a id="log_in_to_dev_portal"/> — Dodawanie uwierzytelniania konta usługi Azure AD

W portalu dla deweloperów logowanie za pomocą usługi AAD jest możliwe za pomocą widżetu **przyciski uwierzytelniania OAuth** . Element widget jest już uwzględniony na stronie logowania domyślnej zawartości portalu deweloperów.

![Widżet przycisków usługi AAD](./media/api-management-howto-aad/portal-oauth-widget.png)

Mimo że nowe konto zostanie utworzone automatycznie za każdym razem, gdy nowy użytkownik zaloguje się za pomocą usługi AAD, można rozważyć dodanie tego samego widżetu do strony rejestracji.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Starszy Portal dla deweloperów — jak zalogować się przy użyciu usługi Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Aby zalogować się do portalu dla deweloperów przy użyciu konta usługi Azure AD skonfigurowanego w poprzednich sekcjach:

1. Otwórz nowe okno przeglądarki, używając adresu URL logowania z konfiguracji aplikacji Active Directory, a następnie wybierz pozycję **Azure Active Directory**.

   ![Strona logowania][api-management-dev-portal-signin]

1. Wprowadź poświadczenia jednego z użytkowników w usłudze Azure AD, a następnie wybierz pozycję **Zaloguj**.

   ![Logowanie przy użyciu nazwy użytkownika i hasła][api-management-aad-signin]

1. Jeśli wymagane są dodatkowe informacje, może zostać wyświetlony monit z formularzem rejestracji. Wypełnij formularz rejestracji i wybierz pozycję **zarejestruj się**.

   ![Przycisk "Utwórz konto" w formularzu rejestracji][api-management-complete-registration]

Użytkownik jest teraz zalogowany do portalu dla deweloperów dla Twojego wystąpienia usługi API Management.

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
