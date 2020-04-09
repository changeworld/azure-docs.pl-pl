---
title: Diagnozowanie błędów za pomocą usługi połączonej z usługą Azure AD (Visual Studio)
description: Usługa połączone z usługą active directory wykryła niezgodny typ uwierzytelniania
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 4b39aa77ea3895a606ad34a3bc9b70dba924a23f
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886096"
---
# <a name="diagnosing-errors-with-the-azure-active-directory-connected-service"></a>Diagnozowanie błędów za pomocą połączonej usługi Azure Active Directory

Podczas wykrywania poprzedniego kodu uwierzytelniania usługa połączona z usługą Azure Active Directory wykryła niezgodny typ uwierzytelniania.

Aby poprawnie wykryć poprzedni kod uwierzytelniania w projekcie, projekt musi zostać przebudowany. Jeśli widzisz ten błąd i nie masz poprzedniego kodu uwierzytelniania w projekcie, odbuduj i spróbuj ponownie.

## <a name="project-types"></a>Typy projektów

Połączona usługa sprawdza typ projektu, który tworzysz, dzięki czemu można wstrzyknąć logikę uwierzytelniania prawo do projektu. Jeśli istnieje dowolny kontroler, który `ApiController` pochodzi z projektu, projekt jest uważany za projekt WebAPI. Jeśli istnieją tylko kontrolery, `MVC.Controller` które wynikają z projektu, projekt jest uważany za projekt MVC. Połączona usługa nie obsługuje żadnego innego typu projektu.

## <a name="compatible-authentication-code"></a>Zgodny kod uwierzytelniania

Połączona usługa sprawdza również ustawienia uwierzytelniania, które zostały wcześniej skonfigurowane lub są zgodne z usługą. Jeśli wszystkie ustawienia są obecne, jest uważany za przypadek ponownego wejścia, a połączona usługa otwiera wyświetlanie ustawień.  Jeśli tylko niektóre ustawienia są obecne, jest uważany za przypadek błędu.

W projekcie MVC połączona usługa sprawdza, czy są używane następujące ustawienia, które wynikają z wcześniejszego korzystania z usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Ponadto usługa połączona sprawdza, czy w projekcie interfejsu API sieci Web nie ma następujących ustawień, które wynikają z wcześniejszego korzystania z usługi:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

## <a name="incompatible-authentication-code"></a>Niezgodny kod uwierzytelniania

Na koniec połączona usługa próbuje wykryć wersje kodu uwierzytelniania, które zostały skonfigurowane z poprzednimi wersjami programu Visual Studio. Jeśli ten błąd został wyświetlony, oznacza to, że projekt zawiera niezgodny typ uwierzytelniania. Połączona usługa wykrywa następujące typy uwierzytelniania z poprzednich wersji programu Visual Studio:

* Uwierzytelnianie systemu Windows
* Indywidualne konta użytkowników
* Konta organizacyjne

Aby wykryć uwierzytelnianie systemu Windows w `authentication` projekcie MVC, połączony szuka elementu w `web.config` pliku.

```xml
<configuration>
    <system.web>
        <authentication mode="Windows" />
    </system.web>
</configuration>
```

Aby wykryć uwierzytelnianie systemu Windows w projekcie `IISExpressWindowsAuthentication` interfejsu API sieci `.csproj` Web, połączona usługa wyszukuje element w pliku projektu:

```xml
<Project>
    <PropertyGroup>
        <IISExpressWindowsAuthentication>enabled</IISExpressWindowsAuthentication>
    </PropertyGroup>
</Project>
```

Aby wykryć uwierzytelnianie poszczególnych kont użytkowników, `packages.config` połączona usługa wyszukuje element pakietu w pliku.

```xml
<packages>
    <package id="Microsoft.AspNet.Identity.EntityFramework" version="2.1.0" targetFramework="net45" />
</packages>
```

Aby wykryć starą formę uwierzytelniania konta instytucji,`web.config`połączona usługa wyszukuje następujący element w :

```xml
<configuration>
    <appSettings>
        <add key="ida:Realm" value="***" />
    </appSettings>
</configuration>
```

Aby zmienić typ uwierzytelniania, usuń niezgodny typ uwierzytelniania i spróbuj ponownie dodać połączoną usługę.

Aby uzyskać więcej informacji, zobacz [Scenariusze uwierzytelniania dla usługi Azure AD](authentication-scenarios.md).
