---
title: Zarządzanie uwierzytelnianiem | Mapy platformy Microsoft Azure
description: Użyj witryny Azure portal do zarządzania uwierzytelnianiem w usługach Microsoft Azure Maps.
author: philmea
ms.author: philmea
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: dfe73971f29ea362fdd0ddd654e705b622ab1866
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335527"
---
# <a name="manage-authentication-in-azure-maps"></a>Zarządzanie uwierzytelnianiem w usłudze Azure Maps

Po utworzeniu konta usługi Azure Maps identyfikator klienta i klucze są tworzone w celu obsługi uwierzytelniania usługi Azure Active Directory (Azure AD) i uwierzytelniania klucza udostępnionego.

## <a name="view-authentication-details"></a>Wyświetlanie szczegółów uwierzytelniania

Po utworzeniu konta usługi Azure Maps są generowane klucze podstawowe i pomocnicze. Zaleca się używanie klucza podstawowego jako klucza subskrypcji podczas wywoływania usługi [Azure Maps za pomocą uwierzytelniania klucza udostępnionego.](https://docs.microsoft.com/azure/azure-maps/azure-maps-authentication#shared-key-authentication) Klucz pomocniczy można użyć w scenariuszach, takich jak stopniowe zmiany klucza. Aby uzyskać więcej informacji, zobacz [Uwierzytelnianie w usłudze Azure Maps](https://aka.ms/amauth).

Szczegóły uwierzytelniania można wyświetlić w witrynie Azure portal. Tam, na swoim koncie, w menu **Ustawienia** wybierz **polecenie Uwierzytelnianie**.

![Szczegóły uwierzytelniania](./media/how-to-manage-authentication/how-to-view-auth.png)


## <a name="register-and-configure-an-azure-ad-app"></a>Rejestrowanie i konfigurowanie aplikacji usługi Azure AD

1. W witrynie Azure portal na liście usług platformy Azure wybierz pozycję**Rejestracje** > aplikacji **usługi Azure Active Directory** > Nowa**rejestracja**.  

    ![Rejestracja aplikacji](./media/how-to-manage-authentication/app-registration.png)

1. Jeśli aplikacja została już zarejestrowana, przejdź do następnego kroku. Jeśli aplikacja nie została zarejestrowana, wprowadź **nazwę**, wybierz **typ konta pomocy technicznej,** a następnie wybierz pozycję **Zarejestruj**.  

    ![Szczegóły rejestracji aplikacji](./media/how-to-manage-authentication/app-create.png)

1. Aby przypisać delegowane uprawnienia interfejsu API do usługi Azure Maps, przejdź do aplikacji. Następnie w obszarze **Rejestracje aplikacji**wybierz pozycję Uprawnienia interfejsu API Dodaj **uprawnienie** > **Add a permission**. W obszarze **Interfejsy API używa się interfejsów API, których używa moja organizacja,** wyszukaj i wybierz pozycję **Usługi Azure Maps**.

    ![Dodawanie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/app-permissions.png)

1. Zaznacz pole wyboru obok pozycji **Access Azure Maps**, a następnie wybierz pozycję Dodaj **uprawnienia**.

    ![Wybieranie uprawnień interfejsu API aplikacji](./media/how-to-manage-authentication/select-app-permissions.png)

1. Wykonaj jeden z następujących kroków, w zależności od metody uwierzytelniania. 

    * Jeśli aplikacja używa uwierzytelniania tokenu użytkownika za pomocą narzędzia `oauth2AllowImplicitFlow`Azure Maps Web SDK, włącz opcję . Aby ją włączyć, w sekcji **Manifest** rejestracji `oauth2AllowImplicitFlow` aplikacji ustawiona na true. 
    
       ![Manifest aplikacji](./media/how-to-manage-authentication/app-manifest.png)

    * Jeśli aplikacja korzysta z uwierzytelniania serwera lub aplikacji, na stronie rejestracji aplikacji przejdź do **strony Certyfikaty & wpisy tajne**. Następnie prześlij certyfikat klucza publicznego lub utwórz hasło, wybierając **pozycję Nowy klucz tajny klienta**. 
    
       ![Tworzenie klucza tajnego klienta](./media/how-to-manage-authentication/app-keys.png)

        Jeśli utworzysz hasło, po wybraniu opcji **Dodaj**, skopiuj je i bezpiecznie zapisz. Użyjesz tego hasła, aby uzyskać tokeny z usługi Azure AD.

       ![Dodawanie klucza tajnego klienta](./media/how-to-manage-authentication/add-key.png)


## <a name="grant-role-based-access-control-to-azure-maps"></a>Udziel kontroli dostępu opartej na rolach do usługi Azure Maps

Po skojarzenia konta usługi Azure Maps z dzierżawą usługi Azure AD można udzielić kontroli dostępu. Możesz udzielić *kontroli dostępu opartej na rolach* (RBAC), przypisując użytkownika, grupę lub aplikację do jednej lub kilku ról kontroli dostępu usługi Azure Maps. 

1. Przejdź do **konta usługi Azure Maps**. Wybierz**przypisanie roli** **kontroli dostępu .** > 

    ![Grant RBAC](./media/how-to-manage-authentication/how-to-grant-rbac.png)

1. Na karcie **Przypisania ról** w obszarze **Rola**wybierz pozycję **Czytnik dat usługi Azure Maps (wersja zapoznawcza)**. W obszarze **Przypisywanie dostępu do**wybierz **pozycję użytkownik, grupa lub podmiot usługi Azure AD**. Wybierz użytkownika lub aplikację. Następnie wybierz pozycję **Zapisz**.

    ![Dodaj przypisanie roli](./media/how-to-manage-authentication/add-role-assignment.png)

## <a name="view-available-azure-maps-rbac-roles"></a>Wyświetlanie dostępnych ról RBAC usługi Azure Maps

Aby wyświetlić role RBAC dostępne dla usługi Azure Maps, przejdź do **kontroli dostępu (IAM)**. Wybierz pozycję **Role**, a następnie wyszukaj role zaczynające się od *usługi Azure Maps*. Te role usługi Azure Maps to role, do których można udzielić dostępu.

![Wyświetlanie dostępnych ról](./media/how-to-manage-authentication/how-to-view-avail-roles.png)


## <a name="view-azure-maps-rbac"></a>Zobacz usługi Azure Maps RBAC

RBAC zapewnia szczegółową kontrolę dostępu.

Aby wyświetlić użytkowników i aplikacje, którym przyznano rbac dla usługi Azure Maps, przejdź do **kontroli dostępu (IAM)**. Tam wybierz pozycję **Przypisania ról,** a następnie filtruj według **usługi Azure Maps**.

![Wyświetlanie użytkowników i aplikacji, którym przyznano RBAC](./media/how-to-manage-authentication/how-to-view-amrbac.png)


## <a name="request-tokens-for-azure-maps"></a>Żądania tokenów dla usługi Azure Maps

Po zarejestrowaniu aplikacji i skojarzyć ją z usługą Azure Maps, można zażądać tokenów dostępu.

Jeśli aplikacja używa uwierzytelniania tokenu użytkownika za pomocą narzędzia Azure Maps Web SDK, skonfiguruj stronę HTML przy użyciu identyfikatora klienta usługi Azure Maps i identyfikatora aplikacji usługi Azure AD.

Jeśli aplikacja używa uwierzytelniania serwera lub aplikacji, zażądaj tokenu `https://login.microsoftonline.com`z punktu końcowego tokenu usługi Azure AD. W swoim zgłoszeniu użyj następujących szczegółów: 

* Identyfikator zasobu usługi Azure AD`https://atlas.microsoft.com/`
* Identyfikator klienta usługi Azure Maps
* Identyfikator aplikacji usługi Azure AD
* Hasło lub certyfikat rejestracji aplikacji usługi Azure AD

| Środowisko platformy Azure   | Punkt końcowy tokenu usługi Azure AD | Identyfikator zasobu platformy Azure |
| --------------------|-------------------------|-------------------|
| Chmura publiczna platformy Azure        | `https://login.microsoftonline.com` | `https://atlas.microsoft.com/` |
| Chmura platformy Azure dla instytucji rządowych   | `https://login.microsoftonline.us`  | `https://atlas.microsoft.com/` | 

Aby uzyskać więcej informacji na temat żądania tokenów dostępu z usługi Azure AD dla użytkowników i podmiotów usługi, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [Azure AD i Azure Maps Web SDK](https://docs.microsoft.com/azure/azure-maps/how-to-use-map-control).

Znajdź metryki użycia interfejsu API dla swojego konta usługi Azure Maps:
> [!div class="nextstepaction"] 
> [Wyświetlanie metryk użycia](how-to-view-api-usage.md)

Zapoznaj się z przykładami, które pokazują, jak zintegrować usługę Azure AD z usługą Azure Maps:

> [!div class="nextstepaction"]
> [Przykłady uwierzytelniania usługi Azure AD](https://github.com/Azure-Samples/Azure-Maps-AzureAD-Samples)
