---
title: Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie atrybutów niestandardowych dla aplikacji w usłudze Azure Active Directory B2C do zbierania informacji o klientach.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968778"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C

 Każda aplikacja przeznaczonych dla klientów ma unikatowe wymagania muszą być uzyskane informacje. Dzierżawy usługi Azure Active Directory (Azure AD) B2C zawiera zestaw wbudowanych informacji przechowywanych w atrybutach, takie jak imię, nazwisko, miasta i kodu pocztowego. Za pomocą usługi Azure AD B2C można rozszerzyć zbiór atrybutów przechowywanych na każdym koncie klienta. 
 
 Możesz utworzyć atrybuty niestandardowe w [witryny Azure portal](https://portal.azure.com/) i używać ich zasady tworzenia konta, zasady rejestracji lub logowania lub zasady edytowania profilu. Można odczytać i zapisać te atrybuty przy użyciu [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md). Użyj atrybutów niestandardowych w usłudze Azure AD B2C [platformy Azure AD Graph interfejsu API rozszerzenia schematu usługi Directory](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutów niestandardowych

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **atrybutów użytkownika**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa** atrybutu niestandardowego (na przykład "ShoeSize")
6. Wybierz **— typ danych**. Tylko **ciąg**, **logiczna**, i **Int** są dostępne.
7. Opcjonalnie wprowadź **opis** w celach informacyjnych. 
8. Kliknij przycisk **Utwórz**.

Atrybut niestandardowy jest teraz dostępna na liście **atrybutów użytkownika** i do użycia w zasadach. Atrybut niestandardowy jest tylko tworzony jest używany w żadnych zasadach po raz pierwszy, a nie w przypadku, gdy dodać go do listy **atrybutów użytkownika**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Użyj atrybutu niestandardowego w zasadach

1. W ramach dzierżawy usługi Azure AD B2C wybierz **zasady rejestracji lub logowania**.
2. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby go otworzyć. 
3. Kliknij pozycję **Edytuj**.
4. Wybierz **atrybuty tworzenia konta** , a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij przycisk **OK**.
5. Wybierz **oświadczeń aplikacji** , a następnie wybierz atrybutu niestandardowego. Kliknij przycisk **OK**.
6. Kliknij pozycję **Zapisz**.

Możesz użyć **Uruchom teraz** funkcji w zasadach, aby sprawdzić środowisko klienta. Powinien zostać wyświetlony **ShoeSize** na liście atrybutów zebranych podczas podróży rejestracji i zobaczyć ją w tokenie wysyłanych z powrotem do aplikacji.

