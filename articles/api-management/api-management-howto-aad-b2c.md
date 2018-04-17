---
title: Autoryzowanie konta dewelopera przy użyciu usługi Azure Active Directory B2C - Azure API Management | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i autoryzacji użytkowników przy użyciu usługi Azure Active Directory B2C w usłudze API Management.
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
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 1268905d8a992d0cac65174142f6a3870f38f0d8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
> [!WARNING]
> Integracja z usługą Azure Active Directory B2C jest dostępna w [deweloperów i Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) tylko warstwy.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Sposób autoryzowania konta dewelopera przy użyciu usługi Azure Active Directory B2C w usłudze Azure API Management
## <a name="overview"></a>Przegląd
Usługa Azure Active Directory B2C jest chmury rozwiązania do zarządzania tożsamościami internetowych i aplikacji dla urządzeń przenośnych. Można go użyć do zarządzania dostępem do swojego portalu dla deweloperów. Ten przewodnik przedstawia konfigurację, która jest wymagana w usłudze API Management do integracji z usługi Azure Active Directory B2C. Aby uzyskać informacje na temat włączania dostępu do portalu dla deweloperów przy użyciu klasycznego Azure Active Directory, zobacz [sposób autoryzowania konta dewelopera przy użyciu usługi Azure Active Directory].

> [!NOTE]
> Aby wykonać kroki opisane w tym przewodniku, najpierw musi mieć dzierżawy usługi Azure Active Directory B2C, aby utworzyć aplikację w. Ponadto konieczne są gotowe zasady rejestracji i logowania. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Autoryzowanie konta dewelopera przy użyciu usługi Azure Active Directory B2C

1. Aby rozpocząć, kliknij przycisk **portal wydawcy** w portalu Azure usługi Zarządzanie interfejsami API. Spowoduje to przejście do portalu wydawcy usługi API Management.

   ![Publisher portal][api-management-management-console]

   > [!NOTE]
   > Jeśli jeszcze nie utworzono wystąpienie usługi API Management, zobacz [Utwórz wystąpienie usługi Zarządzanie interfejsami API] [ Create an API Management service instance] w [Wprowadzenie — samouczek usługi Azure API Management][Get started with Azure API Management].

2. Na **zarządzanie interfejsami API** menu, kliknij przycisk **zabezpieczeń**. Na **tożsamości** , wybierz pozycję **usługi Azure Active Directory B2C**.

  ![1 tożsamości zewnętrznych][api-management-howto-aad-b2c-security-tab]

3. Zanotuj **adresem URL przekierowania** i przełączyć się do usługi Azure Active Directory B2C w portalu Azure.

  ![2 tożsamości zewnętrznych][api-management-howto-aad-b2c-security-tab-reply-url]

4. Kliknij przycisk **aplikacji** przycisku.

  ![Zarejestrować nową aplikację 1][api-management-howto-aad-b2c-portal-menu]

5. Kliknij przycisk **Dodaj** przycisk, aby utworzyć nową aplikację usługi Azure Active Directory B2C.

  ![Zarejestrować nową aplikację 2][api-management-howto-aad-b2c-add-button]

6. W **nowej aplikacji** bloku, wprowadź nazwę aplikacji. Wybierz **tak** w obszarze **interfejsu API sieci Web i aplikacji sieci Web**i wybierz polecenie **tak** w obszarze **Zezwalaj przepływu niejawnego**. Następnie skopiuj **adresem URL przekierowania** z **usługi Azure Active Directory B2C** sekcji **tożsamości** w portalu wydawcy, a następnie wklej ją do **adres URL odpowiedzi** pola tekstowego.

  ![Zarejestrować nową aplikację 3][api-management-howto-aad-b2c-app-details]

7. Kliknij przycisk **Utwórz**. Po utworzeniu aplikacji jest wyświetlany w **aplikacji** bloku. Kliknij nazwę aplikacji, aby wyświetlić jego szczegóły.

  ![Zarejestrować nową aplikację 4][api-management-howto-aad-b2c-app-created]

8. Z **właściwości** bloku, kopiowania **identyfikator aplikacji** do Schowka.

  ![Identyfikator aplikacji 1][api-management-howto-aad-b2c-app-id]

9. Przełączyć się do portalu wydawcy i wklej identyfikator do **identyfikator klienta** pola tekstowego.

  ![Identyfikator aplikacji 2][api-management-howto-aad-b2c-client-id]

10. Wrócić do portalu Azure, kliknij przycisk **klucze** przycisk, a następnie kliknij przycisk **generowanie klucza**. Kliknij przycisk **zapisać** Aby zapisać konfigurację i wyświetlić **klucz aplikacji**. Skopiuj klucz do Schowka.

  ![Klucz aplikacji 1][api-management-howto-aad-b2c-app-key]

11. Przełączyć się do portalu wydawcy i Wklej klucz do **klucz tajny klienta** pola tekstowego.

  ![Klucz aplikacji 2][api-management-howto-aad-b2c-client-secret]

12. Określ nazwę domeny dzierżawy usługi Azure Active Directory B2C w **dozwolone dzierżawy**.

  ![Dozwolone dzierżawy][api-management-howto-aad-b2c-allowed-tenant]

13. Określ **zasad rejestracji** i **zasad Signin**. Opcjonalnie można też podać **zasady edycji profilu** i **zasady resetowania hasła**.

  ![Zasady][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Aby uzyskać więcej informacji dotyczących zasad, zobacz [usługi Azure Active Directory B2C: rozszerzona platforma zasad].

14. Po określeniu odpowiednią konfigurację, kliknij przycisk **zapisać**.

  Po zapisaniu zmian deweloperzy będzie mógł tworzyć nowych kont i zaloguj się do portalu dla deweloperów przy użyciu usługi Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Załóż konto dewelopera przy użyciu usługi Azure Active Directory B2C

1. Aby utworzyć konto dewelopera przy użyciu usługi Azure Active Directory B2C, Otwórz nowe okno przeglądarki i przejdź do portalu dla deweloperów. Kliknij przycisk **Zarejestruj** przycisku.

   ![Portalu dla deweloperów 1][api-management-howto-aad-b2c-dev-portal]

2. Wybierz konto z **usługi Azure Active Directory B2C**.

   ![Portalu dla deweloperów 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Możesz są przekierowywane do zasad rejestracji, skonfigurowanego w poprzedniej sekcji. Wybierz utworzyć konto przy użyciu adresu e-mail lub jeden z istniejących kont społecznościowych.

   > [!NOTE]
   > Jeśli usługi Azure Active Directory B2C jest jedyną opcją jest włączona na **tożsamości** kartę w portalu wydawcy użytkownik zostanie przekierowany do zasad rejestracji bezpośrednio.

   ![Portal dla deweloperów][api-management-howto-aad-b2c-dev-portal-b2c-options]

   Po zakończeniu rejestracja jest przekierowany do portalu dla deweloperów. Obecnie zalogowano Cię do portalu dla deweloperów wystąpienia usługi Zarządzanie interfejsami API.

    ![Zakończono rejestrację][api-management-registration-complete]

## <a name="next-steps"></a>Kolejne kroki

*  [Omówienie usługi Azure Active Directory B2C]
*  [usługi Azure Active Directory B2C: rozszerzona platforma zasad]
*  [Użyj konta Microsoft jako dostawca tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta Google funkcję dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta LinkedIn funkcję dostawcy tożsamości w usłudze Azure Active Directory B2C]
*  [Użyj konta usługi Facebook jako dostawca tożsamości w usłudze Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Omówienie usługi Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[sposób autoryzowania konta dewelopera przy użyciu usługi Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[usługi Azure Active Directory B2C: rozszerzona platforma zasad]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Użyj konta Microsoft jako dostawca tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Użyj konta Google funkcję dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Użyj konta usługi Facebook jako dostawca tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Użyj konta LinkedIn funkcję dostawcy tożsamości w usłudze Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
