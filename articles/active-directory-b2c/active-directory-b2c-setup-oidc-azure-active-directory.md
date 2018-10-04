---
title: Konfigurowanie logowania konta usługi Azure Active Directory wbudowanych zasad w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie logowania w usłudze Azure Active Directory kont wbudowanych zasad w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 64cd440947c95de92ea156c14e4c524ecdc8e76c
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268820"
---
# <a name="set-up-sign-in-azure-active-directory-accounts-a-built-in-policy-in-azure-active-directory-b2c"></a>Konfigurowanie logowania konta usługi Azure Active Directory wbudowanych zasad w usłudze Azure Active Directory B2C

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowisku produkcyjnym.

W tym artykule pokazano, jak włączyć logowanie dla użytkowników z określonych organizacji usługi Azure Active Directory (Azure AD) przy użyciu wbudowanych zasad w usłudze Azure Active Directory (Azure AD) B2C.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć logowania dla użytkowników z określonym organizacji usługi Azure AD, musisz zarejestrować aplikację w organizacji dzierżawy usługi Azure AD.

>[!NOTE]
>`Contoso.com` Służy do organizacyjne dzierżawy usługi Azure AD i `fabrikamb2c.onmicrosoft.com` służy jako dzierżawy usługi Azure AD B2C w poniższych instrukcjach.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C (fabrikamb2c.onmicrosoft.com), klikając pozycję Filtr katalogów i subskrypcji w górnym menu i wybierając pozycję katalogu, który zawiera dzierżawy usługi Azure AD B2C.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. Aby uzyskać **typ aplikacji**, wybierz opcję `Web app / API`.
7. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL w małych liter, gdzie `your-tenant` jest zastępowana nazwą dzierżawy usługi Azure AD B2C (fabrikamb2c.onmicrosoft.com):

    ```
    https://yourtenant.b2clogin.com/your-tenant.onmicrosoft.com/oauth2/authresp
    ```

8. Kliknij pozycję **Utwórz**. Kopiuj **identyfikator aplikacji** na później.
9. Wybierz aplikację, a następnie wybierz **ustawienia**.
10. Wybierz **klucze**wprowadź opis klucza, wybierz czas trwania, a następnie kliknij przycisk **Zapisz**. Skopiuj wartość klucza, który jest wyświetlany na później.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości w dzierżawie

1. Pamiętaj, że używasz katalogu, który zawiera organizacji usługi Azure AD dzierżawy (contoso.com), klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy.
2. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **usługi Azure AD B2C**.
3. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź **nazwa**. Wprowadź na przykład "Contoso usługi Azure AD".
5. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Open ID Connect**i kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Aby uzyskać **adres url metadanych**, wprowadź adres URL następujących, zastępując `your-tenant` nazwą dzierżawy usługi Azure AD. Na przykład "contoso.com":

    ```
    https://login.microsoftonline.com/your-tenant/.well-known/openid-configuration
    ```

8. Opcjonalnie wprowadź wartość dla **domeny** (np. `ContosoAD`). Jest to wartość do wykorzystania przy odwoływaniu się do tego dostawcy tożsamości za pomocą *domain_hint* w żądaniu. 
9. Kliknij przycisk **OK**.
10. Wybierz **Mapowanie oświadczeń tego dostawcy tożsamości** i ustaw następujące oświadczeń:
    
    - Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
    - Aby uzyskać **nazwę wyświetlaną**, wprowadź `name`.
    - Aby uzyskać **imię**, wprowadź `given_name`.
    - Aby uzyskać **nazwisko**, wprowadź `family_name`.
    - Aby uzyskać **E-mail**, wprowadź `unique_name`.

11. Kliknij przycisk **OK**, a następnie **Utwórz** Aby zapisać konfigurację.
