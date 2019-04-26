---
title: Konfigurowanie logowania dla organizacji usługi Azure Active Directory — Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Konfigurowanie logowania dla konkretnej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 0da27c5ce872d1b4e1b4c63f6f4207bb5ca4d6ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60316361"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Konfigurowanie logowania dla konkretnej organizacji usługi Azure Active Directory w usłudze Azure Active Directory B2C

>[!NOTE]
> Ta funkcja jest dostępna w publicznej wersji zapoznawczej. Nie należy używać tej funkcji w środowisku produkcyjnym.

Aby użyć usługi Azure Active Directory (Azure AD) jako [dostawcy tożsamości](active-directory-b2c-reference-oauth-code.md) w usłudze Azure AD B2C, musisz utworzyć aplikację, który go reprezentuje. W tym artykule dowiesz się, jak włączyć logowania dla użytkowników z określonej usługi Azure AD organizacji za pomocą użytkownika przepływ w usłudze Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Tworzenie aplikacji usługi Azure AD

Aby włączyć logowania dla użytkowników z określonym organizacji usługi Azure AD, musisz zarejestrować aplikację w organizacji dzierżawy usługi Azure AD, która nie jest taka sama jak dzierżawy usługi Azure AD B2C.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawy usługi Azure AD, klikając pozycję Filtr katalogów i subskrypcji w górnym menu i wybierając pozycję katalogu, który zawiera dzierżawy usługi Azure AD.
3. Wybierz **wszystkich usług** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz **rejestracje aplikacji**.
4. Wybierz pozycję **Rejestrowanie nowej aplikacji**.
5. Wprowadź nazwę aplikacji. Na przykład `Azure AD B2C App`.
6. Aby uzyskać **typ aplikacji**, wybierz opcję `Web app / API`.
7. Aby uzyskać **adres URL logowania**, wprowadź następujący adres URL w małych liter, gdzie `your-B2C-tenant-name` jest zastępowana nazwą dzierżawy usługi Azure AD B2C. Na przykład `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Wszystkie adresy URL powinny teraz być za pomocą [z usługi b2clogin.com](b2clogin.md).

8. Kliknij pozycję **Utwórz**. Kopiuj **identyfikator aplikacji** na później.
9. Wybierz aplikację, a następnie wybierz **ustawienia**.
10. Wybierz **klucze**wprowadź opis klucza, wybierz czas trwania, a następnie kliknij przycisk **Zapisz**. Skopiuj wartość klucza, który jest wyświetlany na później.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurowanie usługi Azure AD jako dostawcy tożsamości

1. Pamiętaj, że używasz katalogu, który zawiera dzierżawy usługi Azure AD B2C, klikając **filtr katalogów i subskrypcji** w górnym menu i wybierając katalog, który zawiera dzierżawy usługi Azure AD B2C.
2. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
3. Wybierz **dostawców tożsamości**, a następnie wybierz pozycję **Dodaj**.
4. Wprowadź **nazwa**. Wprowadź na przykład "Contoso usługi Azure AD".
5. Wybierz **typ dostawcy tożsamości**, wybierz opcję **Open ID Connect (wersja zapoznawcza)**, a następnie kliknij przycisk **OK**.
6. Kliknij przycisk **skonfiguruj tego dostawcę tożsamości**
7. Aby uzyskać **adres url metadanych**, wprowadź adres URL następujących, zastępując `your-AD-tenant-domain` z nazwą domeny dzierżawy usługi Azure AD. Na przykład `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Dla **identyfikator klienta**, wprowadź identyfikator aplikacji, które wcześniej zapisaną i **klucz tajny klienta**, wprowadź wartości klucza, który wcześniej zapisane.
9. Opcjonalnie wprowadź wartość dla **Domain_hint**. Na przykład `ContosoAD`. Jest to wartość do wykorzystania przy odwoływaniu się do tego dostawcy tożsamości za pomocą *domain_hint* w żądaniu. 
10. Kliknij przycisk **OK**.
11. Wybierz **Mapowanie oświadczeń tego dostawcy tożsamości** i ustaw następujące oświadczeń:
    
    - Aby uzyskać **identyfikator użytkownika**, wprowadź `oid`.
    - Aby uzyskać **nazwę wyświetlaną**, wprowadź `name`.
    - Aby uzyskać **imię**, wprowadź `given_name`.
    - Aby uzyskać **nazwisko**, wprowadź `family_name`.
    - Aby uzyskać **E-mail**, wprowadź `unique_name`.

12. Kliknij przycisk **OK**, a następnie kliknij przycisk **Utwórz** Aby zapisać konfigurację.
