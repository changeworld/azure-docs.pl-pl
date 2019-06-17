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
ms.openlocfilehash: d267ff3a43438d9fe6e4e21f0ac023cfa6675f19
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65956307"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Autoryzowanie kont deweloperów za pomocą usługi Azure Active Directory w usłudze Azure API Management

W tym artykule pokazano, jak umożliwić dostęp do portalu dla deweloperów dla użytkowników z usługi Azure Active Directory (Azure AD). Ten przewodnik pokazuje również jak zarządzać grupami użytkowników usługi Azure AD przez dodanie grup zewnętrznych, które zawierają użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne

- Wykonaj procedury przedstawione w następującym przewodniku Szybki start: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
- Importowanie i publikowanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [importowanie i publikowanie](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Autoryzowanie kont deweloperów za pomocą usługi Azure AD

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz ![strzałkę](./media/api-management-howto-aad/arrow.png).
3. Typ **api** w polu wyszukiwania.
4. Wybierz **usługi API Management**.
5. Wybierz swoje wystąpienie usługi API Management.
6. W obszarze **zabezpieczeń**, wybierz opcję **tożsamości**.
7. Wybierz **+ Dodaj** od góry.

    **Dostawcy tożsamości Dodaj** zostanie wyświetlone okienko po prawej stronie.
8. W obszarze **typ dostawcy**, wybierz opcję **usługi Azure Active Directory**.

    Formanty, które umożliwiają wprowadź inne potrzebne informacje są wyświetlane w okienku. Formanty obejmują **identyfikator klienta** i **klucz tajny klienta**. (Możesz uzyskać informacje o tych kontrolek w dalszej części tego artykułu.)
9. Zwróć uwagę na zawartość **adresu URL przekierowania**.
    
   ![Kroki, aby dodać dostawcę tożsamości w witrynie Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. W przeglądarce otwórz inną kartę. 
11. Przejdź do [witryna Azure portal — rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908) Aby zarejestrować aplikację w usłudze Active Directory.
12. W obszarze **Zarządzaj**, wybierz opcję **rejestracje aplikacji**.
13. Wybierz **nowej rejestracji**. Na **rejestrowania aplikacji** ustaw następujące wartości:
    
* Ustaw **nazwa** na nazwę opisową. np. *portalu dla deweloperów*
* Ustaw **obsługiwane typy kont** do **kont w tym katalogu organizacji tylko**. 
* Ustaw **identyfikator URI przekierowania** wartość uzyskana w kroku 9. 
* Wybierz **zarejestrować**. 

14.  Po zarejestrowaniu aplikacji, skopiuj **identyfikator aplikacji (klienta)** z **Przegląd** strony. 
15. Wróć do wystąpienia usługi API Management. W **dostawcy tożsamości Dodaj** okna, Wklej **identyfikator aplikacji (klienta)** wartością do **identyfikator klienta** pole.
16. Przejdź z powrotem do konfiguracji usługi Azure AD, wybierz **certyfikaty i klucze tajne** w obszarze **Zarządzaj**. Wybierz **nowy wpis tajny klienta** przycisku. Wprowadź wartość w **opis**, wybierz dowolną opcję, aby uzyskać **Expires** i wybierz polecenie **Dodaj**. Skopiuj wartość klucza tajnego klienta przed opuszczeniem strony. Będą one potrzebne w kolejnym kroku. 
17. W obszarze **Zarządzaj**, wybierz opcję **uwierzytelniania** , a następnie wybierz **tokeny Identyfikatora** w obszarze **przyznawanie niejawne**
18. Wróć do wystąpienia usługi API Management, Wklej klucz tajny w **klucz tajny klienta** pole.

    > [!IMPORTANT]
    > Upewnij się zaktualizować **klucz tajny klienta** przed wygaśnięciem klucza. 
    >  
    >

19. **Dostawcy tożsamości Dodaj** okno zawiera również **dozwolone dzierżaw** pola tekstowego. Określ domeny wystąpień usługi Azure AD, do których chcesz udzielić dostępu do interfejsów API wystąpienia usługi API Management. Możesz oddzielić wiele domen tabulacji, spacjami lub przecinkami.

> [!NOTE]
> Można określić wiele domen w **dozwolone dzierżaw** sekcji. Zanim każdy użytkownik może zalogować się w innej domenie niż oryginalną domenę, w którym aplikacja została zarejestrowana, tylko administrator globalny innej domeny musi udzielić uprawnień do dostępu do danych katalogu aplikacji. Aby udzielić uprawnień, należy administrator globalny:. Przejdź do `https://<URL of your developer portal>/aadadminconsent` (na przykład https://contoso.portal.azure-api.net/aadadminconsent).
> b. Wpisz nazwę domeny dzierżawy usługi Azure AD, który ma zostać zapewniony dostęp.
> c. Wybierz **przesłać**. 

20.  Po określeniu pożądanej konfiguracji, wybierz **Dodaj**.

Po zapisaniu zmian użytkowników w określonej usłudze Azure AD wystąpienia zalogować się do portalu dla deweloperów, wykonując kroki opisane w [Zaloguj się do portalu dla deweloperów przy użyciu konta usługi Azure AD](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Dodaj zewnętrzne grupy usługi Azure AD

Po włączeniu dostępu dla użytkowników w wystąpieniu usługi Azure AD, można dodać grupy usługi Azure AD w usłudze API Management. Następnie można łatwiej zarządzać skojarzenie deweloperów w grupie z żądane produkty.

 > [!IMPORTANT]
 > Aby dodać zewnętrznego grupy usługi Azure AD, należy najpierw skonfigurować wystąpienie usługi Azure AD na **tożsamości** kartę zgodnie z procedurą w poprzedniej sekcji. Ponadto aplikacja musi otrzymać dostęp do interfejsu API programu Graph usługi Azure AD, za pomocą `Directory.Read.All` uprawnień. 

Dodaj zewnętrzne Azure grup usługi AD z **grup** kartę wystąpienia usługi API Management.

1. Wybierz kartę **Grupy**.
2. Wybierz **grupy AAD Dodaj** przycisku.
   ![Przycisk "Dodaj grupę usługi AAD"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Wybierz grupę, do której chcesz dodać.
4. Naciśnij klawisz **wybierz** przycisku.

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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
