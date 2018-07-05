---
title: Niestandardowe atrybuty usługi Azure Active Directory B2C | Dokumentacja firmy Microsoft
description: Jak używać atrybutów niestandardowych w usłudze Azure Active Directory B2C do zbierania informacji o użytkownikach.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 893dfbae96d2cfea01b1f281f888e9281bf582f9
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441920"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Usługa Azure Active Directory B2C: Wykorzystaj niestandardowe atrybuty do zbierania informacji o użytkownikach
Katalogu usługi Azure Active Directory (Azure AD) B2C zawiera zestaw wbudowanych informacji (atrybuty): imię, nazwisko, Miasto, kod pocztowy i innych atrybutów. Jednak co aplikacjach udostępnianych klientom ma unikatowe wymagania na jakie atrybuty do zbierania z klientów. Za pomocą usługi Azure AD B2C można rozszerzyć zestaw atrybutów, przechowywane na każdym koncie konsumenta. Atrybuty niestandardowe można tworzyć na [witryny Azure portal](https://portal.azure.com/) i używać go w swojej zasady tworzenia konta, jak pokazano poniżej. Można odczytać i zapisać te atrybuty przy użyciu [interfejsu API usługi Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [!NOTE]
> Niestandardowe atrybuty użyj [platformy Azure AD Graph interfejsu API rozszerzenia schematu usługi Directory](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).
> 
> 

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutów niestandardowych
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w witrynie Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **atrybutów użytkownika**.
3. Kliknij pozycję **+Dodaj** w górnej części bloku.
4. Podaj **nazwa** atrybutu niestandardowego (na przykład "ShoeSize") i, opcjonalnie, **opis**. Kliknij przycisk **Utwórz**.
   
   > [!NOTE]
   > Tylko "String", "Boolean" i "Int" **typy danych** są obecnie dostępne.
   > 
   > 

Atrybut niestandardowy jest teraz dostępna na liście **atrybutów użytkownika**i do użycia w Twojej zasady tworzenia konta.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Użyj niestandardowego atrybutu w ramach zasad rejestracji
1. [Wykonaj następujące kroki, aby przejść do bloku funkcji B2C w witrynie Azure portal](active-directory-b2c-app-registration.md#navigate-to-b2c-settings).
2. Kliknij przycisk **Zasady tworzenia konta**.
3. Kliknij zasady tworzenia konta (na przykład "B2C_1_SiUp"), aby go otworzyć. Kliknij przycisk **Edytuj** w górnej części bloku.
4. Kliknij przycisk **atrybuty tworzenia konta** i wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij przycisk **OK**.
5. Kliknij przycisk **oświadczeń aplikacji** i wybierz atrybutu niestandardowego. Kliknij przycisk **OK**.
6. Kliknij przycisk **Zapisz** w górnej części bloku.

Funkcja "Uruchom teraz" w zasadach umożliwia Sprawdź środowiska klienta. Powinny teraz zobacz "ShoeSize" na liście atrybutów zebranych podczas rejestracji klienta i zobaczyć ją w tokenie wysyłanych z powrotem do aplikacji.

## <a name="notes"></a>Uwagi
* Wraz z zasady tworzenia konta atrybutów niestandardowych, które można również zasady rejestracji lub logowania i zasady edytowania profilu.
* Jest to znane ograniczenie atrybutów niestandardowych. Jest tylko tworzony jest używany w żadnych zasadach po raz pierwszy, a nie w przypadku, gdy dodać go do listy **atrybutów użytkownika**.

