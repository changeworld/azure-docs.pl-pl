---
title: Dodaj dostawcę usługi Azure AD za pomocą wbudowanych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać dostawcy tożsamości Open ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 4e756a4bf3d9e42b47c0b96a6bf73e8e2b267b77
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712389"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Usługi Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Azure AD za pomocą wbudowanych zasad

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać funkcji w środowisku produkcyjnym.

W tym artykule przedstawiono, jak włączyć logowanie użytkowników z określonych zasad wbudowany organizacji usługi Azure Active Directory (Azure AD).

## <a name="create-an-azure-ad-app"></a>Utwórz aplikację usługi Azure AD

Aby włączyć logowanie użytkowników z określonej usługi Azure AD w organizacji, należy zarejestrować aplikację w organizacyjnej dzierżawy usługi Azure AD.

>[!NOTE]
> Używamy "contoso.com" dla organizacji dzierżawy usługi Azure AD i "fabrikamb2c.onmicrosoft.com" jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz konto. Z **katalogu** wybierz organizacyjnej dzierżawy usługi Azure AD, w którym będzie rejestrował aplikacji (contoso.com).
1. Wybierz **wszystkie usługi** w okienku po lewej stronie, a następnie wyszukaj "Rejestracji aplikacji".
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę dla aplikacji (na przykład `Azure AD B2C App`).
1. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja sieci Web**.
1. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` zastępuje nazwę dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Wartość "yourtenant" musi być małych liter w **adres URL logowania**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Zapisz identyfikator aplikacji, który będzie używany w następnej sekcji jako identyfikator klienta.
1. W obszarze **ustawienia** bloku, wybierz opcję **klucze**.
1. Wprowadź **klucza opis** w obszarze **hasła** sekcji i ustaw **czas trwania** do "Nigdy nie wygasa". 
1. Kliknij przycisk **zapisać**i zanotuj klucz uzyskany **wartość**, który będzie używany jako klucz tajny klienta w następnej sekcji.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości w Twojej dzierżawie

1. Na górnym pasku wybierz konto. Z **katalogu** wybierz dzierżawy usługi Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Przejdź do menu Ustawienia usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w portalu Azure.
1. W menu Ustawienia usługi Azure AD B2C, kliknij polecenie **dostawców tożsamości**.
1. Kliknij pozycję **+Dodaj** w górnej części bloku.
1. Podaj przyjazną nazwę w polu **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Contoso usługi Azure AD".
1. Kliknij przycisk **typ dostawcy tożsamości**, wybierz pozycję **Open ID Connect**i kliknij przycisk **OK**.
1. Kliknij przycisk **skonfigurować ten dostawca tożsamości**
1. Dla **adres url metadanych**, wprowadź następujący adres URL, gdzie `yourtenant` zastępuje nazwę dzierżawy usługi Azure AD (np. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Aby uzyskać **identyfikator klienta** i **klucz tajny klienta**, wprowadź identyfikator aplikacji i klucza z poprzedniej sekcji.
1. Należy zachować wartość domyślną dla **zakres**, który powinien być ustawiony na `openid`.
1. Należy zachować wartość domyślną dla **typ odpowiedzi**, który powinien być ustawiony na `code`.
1. Należy zachować wartość domyślną dla **tryb odpowiedzi**, który powinien być ustawiony na `form_post`.
1. Opcjonalnie wprowadź wartość dla **domeny** (np. `ContosoAD`). Jest to wartość do użycia podczas odwoływania się do tego dostawcy tożsamości za pomocą *domain_hint* w żądaniu. 
1. Kliknij przycisk **OK**.
1. Polecenie **mapy dostawcy tożsamości oświadczeń**.
1. Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
1. Aby uzyskać **Nazwa wyświetlana**, wprowadź `name`.
1. Aby uzyskać **imię**, wprowadź `given_name`.
1. Aby uzyskać **nazwisko**, wprowadź `family_name`.
1. Aby uzyskać **E-mail**, wprowadź `unique_name`
1. Kliknij przycisk **OK**, a następnie **Utwórz** Aby zapisać konfigurację.

## <a name="next-steps"></a>Kolejne kroki

Dodaj nowo utworzony dostawcy tożsamości usługi Azure AD do wbudowanych zasad i przekazywania informacji pozwalających na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
