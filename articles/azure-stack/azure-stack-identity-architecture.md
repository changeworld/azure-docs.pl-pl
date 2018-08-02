---
title: Architektura tożsamości dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o architekturze tożsamości, których można użyć z usługą Azure Stack.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/01/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: bf69c71a8b361e4a147263bc60324573c710818f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412700"
---
# <a name="identity-architecture-for-azure-stack"></a>Architektura tożsamości dla usługi Azure Stack
Przed wybraniem dostawcy tożsamości do użycia z usługą Azure Stack, poznać ważne różnice między opcje usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Możliwości i ograniczeń 
Dostawcy tożsamości, który wybierzesz można ograniczyć opcje, takie jak obsługa wielu dzierżawców. 

  

|Funkcja lub scenariusza        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Połączenie z Internetem     |Yes       |Optional (Opcjonalność)|
|Obsługa wielu dzierżawców     |Yes       |Nie      |
|Elementy oferty w portalu Marketplace |Yes       |Tak. Wymagane jest użycie [offline syndykacji Marketplace](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) narzędzia.|
|Wsparcie dla Active Directory Authentication Library (ADAL) |Yes |Yes|
|Obsługa narzędzi, takich jak wiersza polecenia platformy Azure, programu Visual Studio i programu PowerShell  |Yes |Yes|
|Tworzenie jednostek usługi w witrynie Azure portal     |Yes |Nie|
|Tworzenie jednostki usługi przy użyciu certyfikatów      |Yes |Yes|
|Tworzenie jednostki usługi przy użyciu kluczy tajnych (klucze)    |Yes |Nie|
|Aplikacje mogą używać usługa programu Graph           |Yes |Nie|
|Aplikacje mogą używać dostawcy tożsamości do logowania |Yes |Tak. Wymaga aplikacji federowania z lokalnego wystąpienia usług AD FS. |

## <a name="topologies"></a>Topologie
Następujące sekcje discus różnych topologii tożsamości, które są dostępne.

### <a name="azure-ad-single-tenant-topology"></a>Usługi Azure AD: Topologia pojedynczej dzierżawy 
Domyślnie podczas instalowania usługi Azure Stack i używaj usługi Azure AD, Azure Stack używa topologii z jedną dzierżawą. 

Topologia pojedynczej dzierżawy jest przydatne, gdy:
- Wszyscy użytkownicy są częścią tej samej dzierżawy.
- Dostawcy usług znajduje się wystąpienie usługi Azure Stack w organizacji. 

![Azure Stack pojedynczej dzierżawy topologii z usługą Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Ta topologia zawiera następujące właściwości:
- Usługa Azure Stack rejestruje wszystkie aplikacje i usługi do tej samej usługi Azure AD dzierżawy katalogu. 
- Usługa Azure Stack jest uwierzytelniany tylko użytkownicy i aplikacje z tego katalogu, w tym tokenów. 
- Tożsamości w dzierżawie użytkowników i administratorów (operatorom chmury) znajdują się w tej samej dzierżawie katalogu. 
- Aby umożliwić użytkownikowi dostęp do tego środowiska Azure Stack z innego katalogu, musisz mieć [zaprosić użytkownika jako gościa](azure-stack-identity-overview.md#guest-users) do katalogu dzierżawy. 

### <a name="azure-ad-multi-tenant-topology"></a>Usługi Azure AD: Topologia wielu dzierżaw
Operatorzy chmury można skonfigurować usługi Azure Stack, aby zezwolić na dostęp do aplikacji przez dzierżawców z jednej lub więcej organizacji. Użytkownikom dostęp do aplikacji za pośrednictwem portalu użytkowników. Portal administracyjny (używany przez operator chmury) w tej konfiguracji jest ograniczona do użytkowników z jednego katalogu. 

Topologia wielu dzierżawców jest przydatne, gdy:
- Dostawca usług chce umożliwić użytkownikom dostęp do usługi Azure Stack z wielu organizacji.

![Azure stosu wielodostępnych topologii z usługą Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Ta topologia zawiera następujące właściwości:
- Dostęp do zasobów powinien być na podstawie poszczególnych organizacji. 
- Użytkownicy z jednej z organizacji powinna być nie można udzielić dostępu do zasobów dla użytkowników, którzy są spoza organizacji. 
- Tożsamości dla administratorów (operatorom chmury) może być w dzierżawie oddzielny katalog z tożsamości użytkowników. Ten rozdział zapewnia izolację kont na poziomie dostawcy tożsamości. 
 
### <a name="ad-fs"></a>AD FS  
Topologia usług AD FS, jest wymagany, gdy spełniony jest dowolny z następujących warunków:
- Usługa Azure Stack nie połączenie z Internetem.
- Usługa Azure Stack można połączyć się z Internetem, ale użytkownik chce użyć usług AD FS dla dostawcy tożsamości.
  
![Azure Stack topologii, za pomocą usług AD FS](media/azure-stack-identity-architecture/adfs.png)

Ta topologia zawiera następujące właściwości:
- Do obsługi tej topologii w środowisku produkcyjnym, możesz zintegrować wbudowanego wystąpienia usług AD FS usługi Azure Stack przy użyciu istniejącego wystąpienia usług AD FS, która jest wspierana przez usługę Active Directory za pomocą zaufania federacyjnego. 
- Usługa programu Graph w usłudze Azure Stack można zintegrować z istniejące wystąpienie usługi Active Directory. Można również użyć usługi OData na podstawie interfejsu API programu Graph, która obsługuje interfejsy API, które są zgodne z interfejsem API programu Graph usługi Azure AD. 

  Do interakcji z wystąpieniem usługi Active Directory, interfejsu API programu Graph wymaga poświadczeń użytkownika z wystąpienia usługi Active Directory, które mają uprawnienia tylko do odczytu. 
  - Wbudowane wystąpienia usług AD FS jest oparta na systemie Windows Server 2016. 
  - Wystąpień usług AD FS i usługi Active Directory muszą być oparte na systemie Windows Server 2012 lub nowszym. 
  
  Między wystąpienia usługi Active Directory i wbudowane wystąpienia usług AD FS interakcje nie są ograniczone do protokołu OpenID Connect, a ich użyć wzajemnie obsługiwanych protokołów. 
  - Konta użytkowników są tworzone i zarządzane w Twoim wystąpieniu usługi Active Directory w środowisku lokalnym.
  - Jednostki usługi i rejestracji dla aplikacji do zarządzania wbudowanego wystąpienia usługi Active Directory.



## <a name="next-steps"></a>Kolejne kroki
- [Tożsamość — omówienie](azure-stack-identity-overview.md)   
- [Integracja z centrum danych — tożsamość](azure-stack-integrate-identity.md)
