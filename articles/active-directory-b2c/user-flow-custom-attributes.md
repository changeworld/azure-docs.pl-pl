---
title: Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Zdefiniuj atrybuty niestandardowe aplikacji w usłudze Azure Active Directory B2C w celu zbierania informacji o klientach.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7b1ecfba0435f827c7c7a4d05da619998140bc6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186053"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w usłudze Azure Active Directory B2C

 Każda aplikacja skierowana do klienta ma unikatowe wymagania dotyczące informacji, które muszą być zbierane. Dzierżawa usługi Azure Active Directory B2C (Azure AD B2C) jest wyposażona we wbudowany zestaw informacji przechowywanych w atrybutach, takich jak imię, nazwisko, miasto i kod pocztowy. Za pomocą usługi Azure AD B2C można rozszerzyć zestaw atrybutów przechowywanych na każdym koncie klienta.

 Można utworzyć atrybuty niestandardowe w [witrynie Azure portal](https://portal.azure.com/) i używać ich w przepływach użytkownika rejestracji, przepływów użytkownika rejestracji lub logowania lub przepływów użytkowników edycji profilu. Te atrybuty można również odczytywać i zapisywać za pomocą [interfejsu API programu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutu niestandardowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
2. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**.

    ![Przełączanie się do swojej dzierżawy usługi Azure AD B2C](./media/user-flow-custom-attributes/switch-directories.png)

    Wybierz katalog zawierający Twoją dzierżawę.

    ![Dzierżawa B2C wyróżniona w filtrze Katalog i subskrypcja](./media/user-flow-custom-attributes/select-directory.PNG)

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz **pozycję Atrybuty użytkownika**, a następnie wybierz pozycję **Dodaj**.
5. Podaj **nazwę** atrybutu niestandardowego (na przykład "Rozmiar buta")
6. Wybierz **typ danych**. Dostępne są tylko **ciągi,** **boole**i **int.**
7. Opcjonalnie wprowadź **opis** w celach informacyjnych.
8. Kliknij przycisk **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście **atrybutów użytkownika** i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko przy pierwszym użyciu w dowolnym przepływie użytkownika, a nie po dodaniu go do listy **atrybutów użytkownika**.

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Używanie atrybutu niestandardowego w przepływie użytkownika

1. W dzierżawie usługi Azure AD B2C wybierz **pozycję Przepływy użytkownika**.
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby ją otworzyć.
1. Wybierz **atrybuty użytkownika,** a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij przycisk **Zapisz**.
1. Wybierz **pozycję Oświadczenia aplikacji,** a następnie wybierz atrybut niestandardowy.
1. Kliknij przycisk **Zapisz**.

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który używa nowo utworzonego atrybutu niestandardowego, obiekt może być wyszukiwany w [Eksploratorze programu Microsoft Graph.](https://developer.microsoft.com/graph/graph-explorer) Alternatywnie można użyć funkcji [Uruchom przepływ użytkownika](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows) w przepływie użytkownika, aby zweryfikować środowisko klienta. Teraz powinieneś zobaczyć **ShoeSize** na liście atrybutów zebranych podczas podróży rejestracji i zobaczyć go w tokenie wysłanym z powrotem do aplikacji.
