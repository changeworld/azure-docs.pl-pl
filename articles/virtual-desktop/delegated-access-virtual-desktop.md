---
title: Delegowanego dostępu w Windows wirtualnego pulpitu (wersja zapoznawcza) — platformy Azure
description: Jak delegować funkcje administracyjne we wdrożeniu Windows wirtualnego Desktop w wersji zapoznawczej, wraz z przykładami.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 250aea52de63a6397ce00e9cadcadf3a8ba39858
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60870510"
---
# <a name="delegated-access-in-windows-virtual-desktop-preview"></a>Delegowany dostęp w wersji zapoznawczej pulpitu wirtualnego Windows

Windows wirtualnego pulpitu (wersja zapoznawcza) ma modelu delegowanego dostępu, który pozwala zdefiniować ilość dostępu, który jest dany użytkownik może mieć, przypisując im rolę. Przypisanie roli ma trzy składniki: podmiot zabezpieczeń, definicja roli i zakresu. Model delegowanego dostępu Windows pulpitu wirtualnego jest oparty na modelu RBAC platformy Azure. Aby dowiedzieć się więcej na temat przypisania ról określonych oraz ich składników, zobacz [Omówienie kontroli dostępu opartej na rolach na platformie Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-built-in-roles).

Pulpit wirtualny Windows delegowanego dostępu obsługuje następujące wartości dla każdego elementu przypisania roli:

* Podmiot zabezpieczeń
    * Użytkownicy
    * Jednostki usługi
* Definicja roli
    * Wbudowane role
* Scope
    * Dzierżawa grupy
    * Dzierżaw
    * Pule hosta
    * Grupy aplikacji

## <a name="built-in-roles"></a>Wbudowane role

Delegowanego dostępu w pulpitu wirtualnego Windows ma kilka definicji wbudowanych ról, które można przypisać do użytkowników i nazwy główne usług.

* Właściciel pulpitu zdalnego można zarządzać wszystkim łącznie z dostępem do zasobów.
* Współautor usług pulpitu zdalnego mogą zarządzać wszystkim oprócz dostępu do zasobów.
* Czytnik usług pulpitu zdalnego może przeglądać wszystko, ale nie może wprowadzać żadnych zmian.
* Operator usług pulpitu zdalnego można wyświetlić działania diagnostyczne.

## <a name="powershell-cmdlets-for-role-assignments"></a>Polecenia cmdlet programu PowerShell dla przypisania roli

Możesz uruchomić następujące polecenia cmdlet, aby tworzyć, wyświetlać i edytować przypisania roli:

* **Get-RdsRoleAssignment** Wyświetla listę przypisań ról.
* **Nowe RdsRoleAssignment** tworzy nowe przypisanie roli.
* **Zestaw RdsRoleAssignment** edytuje przypisań ról.

### <a name="accepted-parameters"></a>Akceptowane parametry

Można zmodyfikować podstawowe trzy polecenia cmdlet z następującymi parametrami:

* **AadTenantId**: Określa identyfikator dzierżawy usługi Azure Active Directory, w którym nazwa główna usługi jest członkiem.
* **AppGroupName**: Nazwa grupy aplikacji pulpitu zdalnego.
* **Diagnostyka**: Określa zakres diagnostyki. (Muszą łączyć się z oboma **infrastruktury** lub **dzierżawy** parametrów.)
* **HostPoolName**: Nazwa puli hosta usług pulpitu zdalnego.
* **Infrastruktura**: Określa zakres infrastruktury.
* **RoleDefinitionName**: Nazwa roli usług pulpitu zdalnego kontroli dostępu opartej na rolach, przypisane do użytkownika, grupy lub aplikacji. (Na przykład właściciela usług pulpitu zdalnego, zdalnego pulpitu usług czytnika itd.)
* **ServerPrincipleName**: Nazwa aplikacji usługi Azure Active Directory.
* **SignInName**: adres e-mail użytkownika lub główną nazwę użytkownika.
* **TenantName**: Nazwa dzierżawy usług pulpitu zdalnego.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowy wykaz każda rola może używać poleceń cmdlet programu PowerShell, zobacz [dokumentacja programu PowerShell](/powershell/windows-virtual-desktop/overview).

Aby uzyskać wskazówki dotyczące sposobu konfigurowania środowiska pulpitu wirtualnego Windows, zobacz [środowisko w wersji zapoznawczej pulpitu wirtualnego Windows](environment-setup.md).
