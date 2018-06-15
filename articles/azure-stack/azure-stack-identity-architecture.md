---
title: Architektura tożsamości stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o architekturze tożsamości używanych w usłudze Azure stosu.
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
ms.date: 2/28/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a6da27740efd613b8a81ffa85092d6b00b3e47d8
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
ms.locfileid: "34257492"
---
# <a name="identity-architecture-for-azure-stack"></a>Architektura tożsamości Azure stosu
Przed wybraniem dostawcy tożsamości do użycia z stosu Azure należy poznać istotnych różnic między opcje usługi Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS). 

## <a name="capabilities-and-limitations"></a>Możliwości i ograniczenia 
Dostawcy tożsamości, która zostanie wybrana, można ograniczyć opcje, w tym obsługi wielu dzierżawców. 

  

|Możliwość lub scenariusza        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Połączenie z Internetem     |Yes       |Optional (Opcjonalność)|
|Obsługa wielu dzierżawców     |Yes       |Nie      |
|Syndykacja Marketplace       |Yes       |Tak. Wymagane jest użycie [w trybie offline zespolonego Marketplace](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) narzędzia.|
|Obsługa usługi Active Directory Authentication Library (ADAL) |Yes |Yes|
|Obsługa narzędzi, takich jak wiersza polecenia platformy Azure, programu Visual Studio i programu PowerShell  |Yes |Yes|
|Tworzenie nazwy główne usług za pośrednictwem portalu Azure     |Yes |Nie|
|Tworzenie nazwy główne usług certyfikatów      |Yes |Yes|
|Utwórz jednostki usługi z kluczy tajnych (klucze)    |Yes |Nie|
|Aplikacje mogą używać usługi wykresu           |Yes |Nie|
|Aplikacje mogą używać dostawcy tożsamości dla logowania |Yes |Tak. Wymaga aplikacji możliwości utworzenia federacji z lokalnego wystąpienia usług AD FS. |

## <a name="topologies"></a>Topologie
W poniższych sekcjach przedstawiono discus różne topologie tożsamości, które można użyć.

### <a name="azure-ad-single-tenant-topology"></a>Usługi Azure AD: Topologia pojedynczej dzierżawy 
Domyślnie po zainstalowaniu stosu Azure i przy użyciu usługi Azure AD, Azure stosu używa topologii pojedynczej dzierżawy. 

Topologia pojedynczej dzierżawy jest przydatna, gdy:
- Wszyscy użytkownicy są częścią tej samej dzierżawy.
- Dostawcy usług znajduje się wystąpienie stosu Azure dla organizacji. 

![Azure stosu pojedynczej dzierżawy topologii z usługą Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Ta topologia zawiera następujące właściwości:
- Azure stosu rejestruje wszystkie aplikacje i usługi do tej samej usługi Azure AD dzierżawy katalogu. 
- Azure stosu uwierzytelnia tylko użytkownicy i aplikacje z tego katalogu, w tym tokenów. 
- Tożsamości dla dzierżawy użytkowników i administratorów (operatorom chmury) znajdują się w tej samej dzierżawy katalogu. 
- Aby umożliwić użytkownikowi dostęp do tego środowiska Azure stosu z innego katalogu, należy [zaprosić użytkowników jako gość](azure-stack-identity-overview.md#guest-users) do katalogu dzierżawcy. 

### <a name="azure-ad-multi-tenant-topology"></a>Usługi Azure AD: Topologia wielu dzierżawy
Operatorzy chmury można skonfigurować stosu Azure, aby zezwolić na dostęp do aplikacji przez dzierżawców z jedną lub kilkoma organizacjami. Użytkownicy uzyskują dostęp do aplikacji za pośrednictwem portalu użytkownika. Portal administracyjny (wykorzystywane przez operatorowi chmury) w tej konfiguracji jest ograniczony do użytkowników z jednego katalogu. 

Topologia wielodostępne jest przydatna, gdy:
- Dostawcy usług chce, aby użytkownicy uzyskują dostęp stosu Azure z wielu organizacji.

![Azure stosu wielodostępne topologii z usługą Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Ta topologia zawiera następujące właściwości:
- Dostęp do zasobów powinna znajdować się na zasadzie dla organizacji. 
- Użytkownicy z jednej organizacji powinna być nie można udzielić dostępu do zasobów dla użytkowników spoza organizacji. 
- Tożsamości dla administratorów (operatorom chmury) może być w oddzielnym katalogu dzierżawcy tożsamości użytkowników. Ta separacja zapewnia izolację konta na poziomie dostawcy tożsamości. 
 
### <a name="ad-fs"></a>AD FS  
Topologia usług AD FS, jest wymagany, gdy spełniony jest jeden z następujących warunków:
- Azure stosu nie połączyć się z Internetem.
- Stos Azure można połączyć się z Internetem, ale użytkownik chce używać usług AD FS dla dostawcy tożsamości.
  
![Azure topologię stosu za pomocą usług AD FS](media/azure-stack-identity-architecture/adfs.png)

Ta topologia zawiera następujące właściwości:
- Do obsługi tej topologii w środowisku produkcyjnym, wbudowane wystąpienia usług AD FS stosu Azure należy zintegrować z istniejącym wystąpieniem usług AD FS, która została skopiowana przez usługę Active Directory za pomocą zaufania federacyjnego. 
- Usługa wykresu w stosie Azure można zintegrować z istniejącego wystąpienia usługi Active Directory. Można również użyć usługi OData na podstawie interfejsu API programu Graph, która obsługuje interfejsy API, które są zgodne z interfejsu API programu Azure AD Graph. 

  Aby pracować interaktywnie z wystąpieniem usługi Active Directory, interfejsu API programu Graph wymaga poświadczeń użytkownika z wystąpieniem usługi Active Directory, które mają uprawnienia tylko do odczytu. 
  - Wbudowane wystąpienia usług AD FS jest oparta na systemie Windows Server 2016. 
  - Swoich wystąpień usług AD FS i usługi Active Directory muszą być oparte na systemie Windows Server 2012 lub nowszym. 
  
  Między wystąpieniem usługi Active Directory i wbudowane wystąpienia usług AD FS interakcji nie są ograniczone do protokołu OpenID Connect i użyciem wzajemnie obsługiwanych protokołów. 
  - Konta użytkowników są tworzone i zarządzane w lokalnym wystąpieniu usługi Active Directory.
  - Nazwy główne usług i rejestracji dla aplikacji są zarządzane w wbudowanych wystąpienia usługi Active Directory.



## <a name="next-steps"></a>Kolejne kroki
- [Omówienie tożsamości](azure-stack-identity-overview.md)   
- [Integracja Datacenter - tożsamości](azure-stack-integrate-identity.md)
