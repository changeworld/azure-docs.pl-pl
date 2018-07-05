---
title: Dodaj dostawcę usługi Azure AD przy użyciu wbudowanych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dodać dostawcę tożsamości Open ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 70d3a19b715052fe658102929a1c29cf3db2d595
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443733"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Usługi Azure Active Directory B2C: Zaloguj się przy użyciu konta usługi Azure AD za pomocą wbudowanych zasad

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowisku produkcyjnym.

W tym artykule pokazano, jak włączyć logowania dla użytkowników z określonych zasad wbudowanych organizacji usługi Azure Active Directory (Azure AD).

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć logowania dla użytkowników z określonym organizacji usługi Azure AD, musisz zarejestrować aplikację w organizacji dzierżawy usługi Azure AD.

>[!NOTE]
> Używamy "contoso.com" w celu organizacyjne dzierżawy usługi Azure AD i "fabrikamb2c.onmicrosoft.com" jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na górnym pasku wybierz swoje konto. Z **katalogu** wybierz organizacyjne dzierżawy usługi Azure AD, w którym będzie rejestrował aplikacji (contoso.com).
1. Wybierz **wszystkich usług** w okienku po lewej stronie i wyszukaj "Rejestracje aplikacji".
1. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
1. Wprowadź nazwę aplikacji (na przykład `Azure AD B2C App`).
1. Jako typ aplikacji wybierz pozycję **Interfejs API/aplikacja internetowa**.
1. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL, gdzie `yourtenant` jest zastępowana nazwą dzierżawy usługi Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Wartość "yourtenant" musi być tylko małe litery w **adres URL logowania**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Zapisz identyfikator aplikacji, który będzie używany w następnej sekcji, jako identyfikator klienta.
1. W obszarze **ustawienia** bloku wybierz **klucze**.
1. Wprowadź **Opis klucza** w obszarze **haseł** sekcji i ustaw **czas trwania** do "Nigdy nie wygasa". 
1. Kliknij przycisk **Zapisz**i zanotuj klucz uzyskany **wartość**, który będzie używany w następnej sekcji jako klucz tajny klienta.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości w dzierżawie

1. Na górnym pasku wybierz swoje konto. Z **katalogu** listy, wybierz dzierżawę usługi Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Przejdź do menu ustawień usługi Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) w witrynie Azure portal.
1. W menu ustawień usługi Azure AD B2C, kliknij polecenie **dostawców tożsamości**.
1. Kliknij pozycję **+Dodaj** w górnej części bloku.
1. Podaj przyjazną **nazwa** konfiguracji dostawcy tożsamości. Wprowadź na przykład "Contoso usługi Azure AD".
1. Kliknij przycisk **typ dostawcy tożsamości**, wybierz opcję **Open ID Connect**i kliknij przycisk **OK**.
1. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
1. Aby uzyskać **adres url metadanych**, wprowadź następujący adres URL, gdzie `yourtenant` jest zastępowana nazwą dzierżawy usługi Azure AD (np. `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Aby uzyskać **identyfikator klienta** i **klucz tajny klienta**, wprowadź identyfikator aplikacji i klucza z poprzedniej sekcji.
1. Zachowaj wartość domyślną dla **zakres**, która powinna być równa `openid`.
1. Zachowaj wartość domyślną dla **typ odpowiedzi**, która powinna być równa `code`.
1. Zachowaj wartość domyślną dla **tryb odpowiedzi**, która powinna być równa `form_post`.
1. Opcjonalnie wprowadź wartość dla **domeny** (np. `ContosoAD`). Jest to wartość do wykorzystania przy odwoływaniu się do tego dostawcy tożsamości za pomocą *domain_hint* w żądaniu. 
1. Kliknij przycisk **OK**.
1. Kliknij pozycję **Mapowanie oświadczeń tego dostawcy tożsamości**.
1. Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
1. Aby uzyskać **nazwę wyświetlaną**, wprowadź `name`.
1. Aby uzyskać **imię**, wprowadź `given_name`.
1. Aby uzyskać **nazwisko**, wprowadź `family_name`.
1. Aby uzyskać **E-mail**, wprowadź `unique_name`
1. Kliknij przycisk **OK**, a następnie **Utwórz** Aby zapisać konfigurację.

## <a name="next-steps"></a>Kolejne kroki

Dodaj nowo utworzony dostawcy tożsamości usługi Azure AD w celu wbudowanych zasad i wyraź swoje opinie do [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
