---
title: Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory B2C
titleSuffix: Azure API Management
description: Dowiedz się, jak autoryzować użytkowników przy użyciu usługi Azure Active Directory B2C w usłudze API Management.
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
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475497"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Jak autoryzować konta deweloperów przy użyciu usługi Azure Active Directory B2C w usłudze Azure API Management

## <a name="overview"></a>Omówienie

Usługa Azure Active Directory B2C to rozwiązanie do zarządzania tożsamościami w chmurze dla aplikacji internetowych i mobilnych przeznaczonych dla konsumentów. Można go używać do zarządzania dostępem do portalu dla deweloperów. W tym przewodniku przedstawiono konfigurację, która jest wymagana w usłudze zarządzania interfejsami API do integracji z usługą Azure Active Directory B2C. Aby uzyskać informacje dotyczące włączania dostępu do portalu dla deweloperów przy użyciu klasycznej usługi Azure Active Directory, zobacz [Jak autoryzować konta deweloperów przy użyciu usługi Azure Active Directory].

> [!NOTE]
> Aby wykonać kroki opisane w tym przewodniku, musisz najpierw mieć dzierżawę usługi Azure Active Directory B2C, aby utworzyć aplikację. Ponadto musisz mieć gotowe zasady rejestracji i logowania. Aby uzyskać więcej informacji, zobacz [omówienie usługi Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autoryzowanie kont deweloperów przy użyciu usługi Azure Active Directory B2C

1. Aby rozpocząć, zaloguj się do [witryny Azure portal](https://portal.azure.com) i znajdź wystąpienie usługi API Management.

   > [!NOTE]
   > Jeśli nie utworzono jeszcze wystąpienia usługi zarządzania interfejsami API, zobacz [Tworzenie wystąpienia usługi zarządzania interfejsami API][Create an API Management service instance] w [samouczku Wprowadzenie do usługi Azure API Management][Get started with Azure API Management].

1. W obszarze **Tożsamości**. Kliknij **pozycję +Dodaj** u góry.

   Po prawej stronie pojawi się okienko **Dodaj dostawcę tożsamości.** Wybierz **pozycję Azure Active Directory B2C**.
    
   ![Dodawanie usługi AAD B2C jako dostawcy tożsamości][api-management-howto-add-b2c-identity-provider]

1. Skopiuj **adres URL przekierowania**.

   ![Adres URL przekierowania adresu URL dostawcy tożsamości AAD B2C][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. Na nowej karcie uzyskaj dostęp do dzierżawy usługi Azure Active Directory B2C w witrynie Azure portal i otwórz blok **Aplikacje.**

   ![Zarejestruj nową aplikację 1][api-management-howto-aad-b2c-portal-menu]

1. Kliknij przycisk **Dodaj,** aby utworzyć nową aplikację B2C usługi Azure Active Directory.

   ![Zarejestruj nową aplikację 2][api-management-howto-aad-b2c-add-button]

1. W **bloku Nowa aplikacja** wprowadź nazwę aplikacji. Wybierz **pozycję Tak** w obszarze Web **App/Web API**i wybierz pozycję **Tak** w obszarze **Zezwalaj na przepływ niejawny**. Następnie wklej **adres URL przekierowania** skopiowany w kroku 3 do pola tekstowego **Odpowiedz adres URL.**

   ![Zarejestruj nową aplikację 3][api-management-howto-aad-b2c-app-details]

1. Jeśli korzystasz z nowego portalu dla deweloperów (nie starszego portalu dewelopera), w liczba oświadczeń aplikacji **uwzględnij podane imię,** **nazwisko**i **identyfikator obiektu użytkownika.**

    ![Oświadczenia aplikacji](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Kliknij przycisk **Utwórz**. Po utworzeniu aplikacji pojawia się w **aplikacji** bloku. Kliknij nazwę aplikacji, aby wyświetlić jej szczegóły.

   ![Zarejestruj nową aplikację 4][api-management-howto-aad-b2c-app-created]

1. W bloku **Właściwości** skopiuj **identyfikator aplikacji** do schowka.

   ![Identyfikator aplikacji 1][api-management-howto-aad-b2c-app-id]

1. Przełącz się z powrotem do okienka **Dostawca tożsamości** Dodawania interfejsu API i wklej go do pola tekstowego **Identyfikator klienta.**
    
1.  Przełącz się z powrotem do rejestracji aplikacji B2C, kliknij przycisk **Klawisze,** a następnie kliknij pozycję **Generuj klawisz**. Kliknij **przycisk Zapisz,** aby zapisać konfigurację i wyświetlić **klucz aplikacji**. Skopiuj klucz do schowka.

    ![Klucz aplikacji 1][api-management-howto-aad-b2c-app-key]

1.  Przełącz się z powrotem do okienka **Dostawca tożsamości Dodawania interfejsu** API i wklej klucz do pola tekstowego Klucz tajny **klienta.**
    
1.  Określ nazwę domeny dzierżawy usługi Azure Active Directory B2C w **dzierżawie usługi Signin**.

1.  Pole **Urząd** umożliwia kontrolowanie adresu URL logowania usługi Azure AD B2C do użycia. Ustaw wartość **na<your_b2c_tenant_name>.b2clogin.com**.

1. Określ **zasady rejestracji** i zasady **logowania** z zasad dzierżawy B2C. Opcjonalnie można również podać **zasady edycji profilu** i zasady **resetowania hasła.**

1. Po określeniu żądanej konfiguracji kliknij przycisk **Zapisz**.

    Po zapisaniu zmian deweloperzy będą mogli tworzyć nowe konta i logować się do portalu dla deweloperów przy użyciu usługi Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Portal dla deweloperów — dodawanie uwierzytelniania konta usługi Azure AD B2C

W portalu dla deweloperów logowanie za pomocą programu AAD B2C jest możliwe za pomocą **przycisku logowania: Widżet OAuth.** Widżet jest już uwzględniony na stronie logowania domyślnej zawartości portalu dla deweloperów.

Mimo że nowe konto zostanie utworzone automatycznie za każdym razem, gdy nowy użytkownik zaloguje się za pomocą AAD B2C, możesz dodać ten sam widżet do strony rejestracji.

Formularz rejestracji: Widżet **OAuth** reprezentuje formularz używany do rejestracji w OAuth.

> [!IMPORTANT]
> Należy [ponownie opublikować portal,](api-management-howto-developer-portal-customize.md#publish) aby zmiany aad zostały wprowadzone.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Starszy portal dla deweloperów — jak zarejestrować się w usłudze Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Aby zarejestrować konto dewelopera przy użyciu usługi Azure Active Directory B2C, otwórz nowe okno przeglądarki i przejdź do portalu dla deweloperów. Kliknij przycisk **Zarejestruj się.**

   ![Portal dla deweloperów 1][api-management-howto-aad-b2c-dev-portal]

2. Wybierz opcję zarejestrowania się w **usłudze Azure Active Directory B2C**.

   ![Portal dla deweloperów 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Zostaniesz przekierowany do zasad rejestracji skonfigurowanych w poprzedniej sekcji. Zarejestruj się, używając adresu e-mail lub jednego z istniejących kont społecznościowych.

   > [!NOTE]
   > Jeśli usługa Azure Active Directory B2C jest jedyną opcją, która jest włączona na karcie **Tożsamości** w portalu wydawcy, nastąpi przekierowanie bezpośrednio do zasad rejestracji.

   ![Portal dla deweloperów][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po zakończeniu rejestracji zostanie przekierowany z powrotem do portalu dla deweloperów. Teraz zalogujesz się do portalu dla deweloperów dla wystąpienia usługi api Management.

    ![Rejestracja zakończona][api-management-registration-complete]

## <a name="next-steps"></a>Następne kroki

*  [Omówienie usługi B2C usługi Azure Active Directory]
*  [Usługa Azure Active Directory B2C: rozszerzalna struktura zasad]
*  [Używanie konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Używanie konta Google jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Używanie konta LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Używanie konta na Facebooku jako dostawcy tożsamości w usłudze Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Omówienie usługi B2C usługi Azure Active Directory]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Jak autoryzować konta deweloperów przy użyciu usługi Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Usługa Azure Active Directory B2C: rozszerzalna struktura zasad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Używanie konta Microsoft jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Używanie konta Google jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Używanie konta na Facebooku jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Używanie konta LinkedIn jako dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
