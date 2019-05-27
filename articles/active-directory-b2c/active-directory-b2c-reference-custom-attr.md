---
title: Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Definiowanie atrybutów niestandardowych dla aplikacji w usłudze Azure Active Directory B2C do zbierania informacji o klientach.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 629b872f78a30592fc00cb268066970b12b20561
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2019
ms.locfileid: "65952807"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C

 Każda aplikacja przeznaczonych dla klientów ma unikatowe wymagania muszą być uzyskane informacje. Dzierżawy usługi Azure Active Directory (Azure AD) B2C zawiera zestaw wbudowanych informacji przechowywanych w atrybutach, takie jak imię, nazwisko, miasta i kodu pocztowego. Za pomocą usługi Azure AD B2C można rozszerzyć zbiór atrybutów przechowywanych na każdym koncie klienta. 
 
 Możesz utworzyć atrybuty niestandardowe w [witryny Azure portal](https://portal.azure.com/) i używać ich przepływy tworzenia konta użytkownika, przepływy użytkownika rejestracji lub logowania lub edytowania przepływy użytkownika profilu. Można odczytać i zapisać te atrybuty przy użyciu [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md). Użyj atrybutów niestandardowych w usłudze Azure AD B2C [platformy Azure AD Graph interfejsu API rozszerzenia schematu usługi Directory](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).  

> [!NOTE]
> Obsługa nowszej [interfejsu API Microsoft Graph](https://docs.microsoft.com/graph/overview?view=graph-rest-1.0) zapytań usługi Azure AD B2C dzierżawa jest nadal w fazie projektowania.
>

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutów niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**. 

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Wybieranie katalogu](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **atrybutów użytkownika**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwa** atrybutu niestandardowego (na przykład "ShoeSize")
6. Wybierz **— typ danych**. Tylko **ciąg**, **logiczna**, i **Int** są dostępne.
7. Opcjonalnie wprowadź **opis** w celach informacyjnych. 
8. Kliknij pozycję **Utwórz**.

Atrybut niestandardowy jest teraz dostępna na liście **atrybutów użytkownika** i do użytku w swoich przepływach użytkowników. Atrybut niestandardowy jest tylko tworzony jest używany w przepływie użytkownika po raz pierwszy, a nie w przypadku, gdy dodać go do listy **atrybutów użytkownika**. 


## <a name="use-a-custom-attribute-in-your-user-flow"></a>Użyj atrybutu niestandardowego przepływu użytkownika

1. W ramach dzierżawy usługi Azure AD B2C wybierz **przepływy użytkownika**.
2. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby go otworzyć. 
4. Wybierz **atrybutów użytkownika** , a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij pozycję **Zapisz**.
5. Wybierz **oświadczeń aplikacji** , a następnie wybierz atrybutu niestandardowego. 
6. Kliknij pozycję **Zapisz**.

Po utworzeniu nowego użytkownika za pomocą przepływu użytkownika, który używa atrybutu niestandardowego nowo utworzony obiekt może być odpytywany w [programu Azure AD Graph Explorer](https://docs.microsoft.com/azure/active-directory/develop/active-directory-graph-api-quickstart). Możesz też użyć [ **uruchomić przepływ użytkownika** ](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) funkcję na przepływ użytkownika, aby sprawdzić środowisko klienta. Powinien zostać wyświetlony **ShoeSize** na liście atrybutów zebranych podczas podróży rejestracji i zobaczyć ją w tokenie wysyłanych z powrotem do aplikacji. 

