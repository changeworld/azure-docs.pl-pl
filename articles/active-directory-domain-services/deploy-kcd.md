---
title: 'Azure Active Directory Domain Services: Włącz ograniczone delegowanie protokołu Kerberos | Microsoft Docs'
description: Włącz ograniczone delegowanie protokołu Kerberos w Azure Active Directory Domain Services domenach zarządzanych
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: iainfou
ms.openlocfilehash: f234eaea0d4df3859ef9458ea334f1b7616add34
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612933"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD) w domenie zarządzanej
Wiele aplikacji musi uzyskać dostęp do zasobów w kontekście użytkownika. Active Directory obsługuje mechanizm o nazwie delegowanie protokołu Kerberos, który umożliwia użycie tego przypadku. Ponadto możesz ograniczyć delegowanie, aby uzyskać dostęp do tylko określonych zasobów w kontekście użytkownika. Azure AD Domain Services domeny zarządzane różnią się od tradycyjnych domen Active Directory, ponieważ są one bezpieczniejsze.

W tym artykule opisano sposób konfigurowania ograniczonego delegowania protokołu Kerberos w domenie zarządzanej Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Ograniczone delegowanie protokołu Kerberos (KCD)
Delegowanie Kerberos umożliwia kontu personifikację innego podmiotu zabezpieczeń (na przykład użytkownika) w celu uzyskania dostępu do zasobów. Rozważ użycie aplikacji sieci Web, która uzyskuje dostęp do internetowego interfejsu API zaplecza w kontekście użytkownika. W tym przykładzie aplikacja sieci Web (uruchomiona w kontekście konta usługi lub komputera/konta maszyny) personifikuje użytkownika podczas uzyskiwania dostępu do zasobu (interfejs API sieci Web zaplecza). Delegowanie protokołu Kerberos jest niezabezpieczone, ponieważ nie ogranicza zasobów, do których konto personifikacji może uzyskać dostęp w kontekście użytkownika.

Ograniczone delegowanie protokołu Kerberos (KCD) ogranicza usługi/zasoby, do których określony serwer może działać w imieniu użytkownika. Tradycyjna KCD wymaga uprawnień administratora domeny do skonfigurowania konta domeny dla usługi i ogranicza konto do pojedynczej domeny.

Tradycyjny KCD również zawiera kilka problemów skojarzonych z nim. W starszych systemach operacyjnych, jeśli administrator domeny skonfigurował KCD oparte na koncie dla usługi, administrator usługi nie miał użyteczny sposób, aby wiedzieć, które usługi frontonu zostały przekazane do usługi zasobów, do których należą. A dowolna usługa frontonu, która może delegować do usługi zasobów, reprezentuje potencjalny punkt ataku. Jeśli serwer, na którym jest hostowana usługa frontonu, został naruszony i został skonfigurowany do delegowania usług zasobów, można także złamać zabezpieczenia usług zasobów.

> [!NOTE]
> W domenie zarządzanej Azure AD Domain Services nie masz uprawnień administratora domeny. W związku z tym **tradycyjne KCD oparte na koncie nie można skonfigurować w domenie zarządzanej**. Użyj KCD opartego na zasobach zgodnie z opisem w tym artykule. Ten mechanizm jest również bezpieczniejszy.
>
>

## <a name="resource-based-kcd"></a>KCD oparte na zasobach
Począwszy od systemu Windows Server 2012, Administratorzy usługi mogą konfigurować ograniczone delegowanie dla swojej usługi. W tym modelu administrator usługi zaplecza może zezwalać na korzystanie z usługi KCD lub odmówić określonych usług frontonu. Ten model jest znany jako **KCD oparte na zasobach**.

KCD oparte na zasobach są konfigurowane przy użyciu programu PowerShell. Użyj `Set-ADComputer` poleceń cmdlet lub `Set-ADUser` , w zależności od tego, czy konto personifikacji jest kontem komputera czy kontem użytkownika/kontem usługi.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurowanie KCD opartego na zasobach dla konta komputera w domenie zarządzanej
Załóżmy, że aplikacja sieci Web jest uruchomiona na komputerze "contoso-webapp.contoso.com". Musi on uzyskać dostęp do zasobu (interfejsu API sieci Web działającego w systemie "contoso-api.contoso.com") w kontekście użytkowników domeny. Poniżej przedstawiono sposób konfigurowania KCD opartego na zasobach dla tego scenariusza:

1. [Utwórz niestandardową jednostkę organizacyjną](create-ou.md). Można delegować uprawnienia do zarządzania tą niestandardową jednostką organizacyjną dla użytkowników w domenie zarządzanej.
2. Dołącz obie maszyny wirtualne (z uruchomioną aplikacją internetową i z uruchomioną usługą Web API) do domeny zarządzanej. Utwórz te konta komputerów w ramach niestandardowej jednostki organizacyjnej.
3. Teraz Skonfiguruj KCD oparte na zasobach przy użyciu następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso-webapp.contoso.com
Set-ADComputer contoso-api.contoso.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Konta komputerów dla aplikacji sieci Web i internetowego interfejsu API muszą znajdować się w niestandardowej jednostce organizacyjnej, w której masz uprawnienia do konfigurowania KCD opartego na zasobach. Nie można skonfigurować KCD opartego na zasobach dla konta komputera w wbudowanym kontenerze "komputery DC" usługi AAD.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurowanie KCD opartego na zasobach dla konta użytkownika w domenie zarządzanej
Załóżmy, że aplikacja sieci Web działa jako konto usługi "appsvc" i musi uzyskać dostęp do zasobu (interfejs API sieci Web uruchomiony jako konto usługi-"backendsvc") w kontekście użytkowników domeny. Poniżej przedstawiono sposób konfigurowania KCD opartego na zasobach dla tego scenariusza.

1. [Utwórz niestandardową jednostkę organizacyjną](create-ou.md). Można delegować uprawnienia do zarządzania tą niestandardową jednostką organizacyjną dla użytkowników w domenie zarządzanej.
2. Dołącz do domeny zarządzanej maszynę wirtualną z uruchomionym interfejsem API/zasób sieci Web zaplecza. Utwórz konto komputera w ramach niestandardowej jednostki organizacyjnej.
3. Utwórz konto usługi (na przykład "appsvc") używane do uruchamiania aplikacji sieci Web w ramach niestandardowej jednostki organizacyjnej.
4. Teraz Skonfiguruj KCD oparte na zasobach przy użyciu następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Zarówno konto komputera dla internetowego interfejsu API zaplecza, jak i konto usługi muszą znajdować się w niestandardowej jednostce organizacyjnej, w której masz uprawnienia do konfigurowania KCD opartego na zasobach. Nie można skonfigurować KCD opartego na zasobach dla konta komputera w wbudowanym kontenerze "komputery kontrolera domeny usługi AAD" lub dla kont użytkowników w kontenerze "zalogowani użytkownicy DC" usługi AAD. Z tego względu nie można używać kont użytkowników synchronizowanych z usługą Azure AD w celu skonfigurowania KCD opartego na zasobach.
>

## <a name="related-content"></a>Powiązana zawartość
* [Przewodnik po Wprowadzenie Azure AD Domain Services](tutorial-create-instance.md)
* [Omówienie ograniczonego delegowania protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
