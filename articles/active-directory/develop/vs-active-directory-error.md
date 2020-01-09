---
title: Diagnozowanie błędów za pomocą usługi połączonej z usługą Azure AD (Visual Studio)
description: Usługa połączona z usługą Active Directory wykryła niezgodny typ uwierzytelniania
author: ghogen
manager: jillfra
ms.assetid: dd89ea63-4e45-4da1-9642-645b9309670a
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.collection: M365-identity-device-management
ms.openlocfilehash: 379acbe8493f57fca97497b1b1483036ace905b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75423168"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnozowanie błędów za pomocą połączonej usługi Azure Active Directory

Podczas wykrywania poprzedniego kodu uwierzytelniania usługa połączona Azure Active Directory wykryła niezgodny typ uwierzytelniania.

Aby poprawnie wykryć poprzedni kod uwierzytelniania w projekcie, należy ponownie skompilować projekt. Jeśli widzisz ten błąd i nie masz poprzedniego kodu uwierzytelniania w projekcie, Odbuduj i spróbuj ponownie.

## <a name="project-types"></a>Typy projektów

Połączona usługa sprawdza typ projektu, który jest opracowywany, dzięki czemu może wstrzyknąć właściwą logikę uwierzytelniania do projektu. Jeśli istnieje jakikolwiek kontroler, który pochodzi od `ApiController` w projekcie, projekt jest traktowany jako projekt WebAPI. Jeśli istnieją tylko kontrolery, które pochodzą z `MVC.Controller` w projekcie, projekt jest traktowany jako projekt MVC. Połączona usługa nie obsługuje żadnego innego typu projektu.

## <a name="compatible-authentication-code"></a>Zgodny kod uwierzytelniania

Usługa połączona sprawdza również ustawienia uwierzytelniania, które zostały wcześniej skonfigurowane lub są zgodne z usługą. Jeśli wszystkie ustawienia są obecne, jest ono traktowane jako ponowny przypadek i zostanie otwarta usługa połączona.  Jeśli są obecne tylko niektóre ustawienia, jest ono traktowane jako przypadek błędu.

W projekcie MVC usługa połączona sprawdza następujące ustawienia, które wynikają z wcześniejszego użycia usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Ponadto połączona usługa sprawdza następujące ustawienia w projekcie interfejsu API sieci Web, które wynikają z wcześniejszego użycia usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Niezgodny kod uwierzytelniania

Na koniec usługa połączona próbuje wykryć wersje kodu uwierzytelniania, które zostały skonfigurowane z poprzednimi wersjami programu Visual Studio. Jeśli wystąpił ten błąd, oznacza to, że projekt zawiera niezgodny typ uwierzytelniania. Połączona usługa wykrywa następujące typy uwierzytelniania z poprzednich wersji programu Visual Studio:

* Uwierzytelnianie systemu Windows
* Indywidualne konta użytkowników
* Konta organizacyjne

W celu wykrycia uwierzytelniania systemu Windows w projekcie MVC, połączenie szuka elementu `authentication` w pliku `web.config`.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Aby wykryć uwierzytelnianie systemu Windows w projekcie interfejsu API sieci Web, połączona usługa szuka elementu `IISExpressWindowsAuthentication` w pliku `.csproj` projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Aby wykryć uwierzytelnianie poszczególnych kont użytkowników, połączona usługa szuka elementu Package w pliku `packages.config`.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Aby wykryć starą formę uwierzytelniania konta organizacji, połączona usługa szuka następującego elementu w`web.config`:

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Aby zmienić typ uwierzytelniania, Usuń niezgodny typ uwierzytelniania i spróbuj ponownie dodać podłączoną usługę.

Aby uzyskać więcej informacji, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).
