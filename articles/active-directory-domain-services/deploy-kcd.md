---
title: 'Azure Active Directory Domain Services: Włączyć ograniczone delegowanie protokołu kerberos | Dokumentacja firmy Microsoft'
description: Włączyć ograniczone delegowanie protokołu kerberos w domenach zarządzanych usługi Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
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
ms.author: mstephen
ms.openlocfilehash: 287aea990664cf01caf83e1871acf69e48210226
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246167"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Konfigurowanie ograniczonego delegowania protokołu Kerberos (KCD) w domenie zarządzanej
Wiele aplikacji wymaga dostępu do zasobów w kontekście użytkownika. Usługa Active Directory obsługuje do tego mechanizm nazywany delegowania protokołu Kerberos, który umożliwia ten przypadek użycia. Ponadto można ograniczyć delegowanie, tak, aby tylko określone zasoby mogą być dostępne w kontekście użytkownika. Usługa Azure AD Domain Services domeny zarządzane są różni się od tradycyjnych domen usługi Active Directory, ponieważ one bardziej bezpiecznie zablokowane.

W tym artykule pokazano, jak skonfigurować ograniczone delegowanie protokołu Kerberos w domenie zarządzanej usług domenowych Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Ograniczone delegowanie protokołu Kerberos (KCD)
Delegowanie protokołu Kerberos umożliwia konta personifikować innego podmiotu zabezpieczeń (na przykład użytkownik) dostęp do zasobów. Należy wziąć pod uwagę aplikacji sieci web, który uzyskuje dostęp do internetowego interfejsu API zaplecza w kontekście użytkownika. W tym przykładzie aplikacja sieci web (uruchomionego w kontekście konta usługi lub konto komputera/urządzenia) personifikuje użytkownika podczas uzyskiwania dostępu do zasobów (sieci web zaplecza interfejsu API). Delegowanie protokołu Kerberos jest niebezpieczne, ponieważ nie ogranicza zasoby, które będą mogli personifikacji konta w kontekście użytkownika.

Ograniczone delegowanie protokołu Kerberos (KCD) ogranicza usług/zasoby, do których określony serwer może działać w imieniu użytkownika. Tradycyjne ograniczonego delegowania protokołu Kerberos wymaga uprawnień administratora domeny, aby skonfigurować konto domeny dla usługi i ogranicza konto do pojedynczej domeny.

Tradycyjne ograniczonego delegowania protokołu Kerberos ma również kilka kwestii, o których skojarzone z nią. W starszych systemów operacyjnych Jeśli konto, na podstawie ograniczonego delegowania protokołu Kerberos dla usługi, skonfigurowaną przez administratora domeny administratora usługi było nie wygodny sposób, aby wiedzieć, które usługi frontonu delegowane do usług zasobów są jego własnością. A każda usługa frontonu, którą mógł delegować do usługi zasobów potencjalny punkt ataku. Jeśli zostało naruszone serwera udostępniającego usługę frontonu i był on skonfigurowany do delegowania usług zasobów, usług zasobów może również być narażone na ataki.

> [!NOTE]
> W domenie zarządzanej usług domenowych Azure AD nie masz uprawnienia administratora domeny. W związku z tym **tradycyjnych oparte na koncie ograniczonego delegowania protokołu Kerberos, nie można skonfigurować w domenie zarządzanej**. Użyj opartego na zasobach ograniczonego delegowania protokołu Kerberos, zgodnie z opisem w tym artykule. Ten mechanizm jest również bardziej bezpieczne.
>
>

## <a name="resource-based-kcd"></a>Oparte na zasobach ograniczonego delegowania protokołu Kerberos
Z systemu Windows Server 2012 lub nowszy Administratorzy usługi zyskujemy zdolność do konfigurowania delegowania ograniczonego dotyczące ich usługi. W tym modelu administrator usług zaplecza można zezwolić lub odmowa określonych usług frontonu z przy użyciu ograniczonego delegowania protokołu Kerberos. Model ten jest znany jako **opartego na zasobach ograniczonego delegowania protokołu Kerberos**.

Oparte na zasobach ograniczonego delegowania protokołu Kerberos jest skonfigurowany przy użyciu programu PowerShell. Możesz użyć `Set-ADComputer` lub `Set-ADUser` poleceń cmdlet, w zależności od tego, czy konto personifikacji konta komputera lub konto usługi/konta użytkownika.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Konfigurowanie opartego na zasobach ograniczonego delegowania protokołu Kerberos dla konta komputera w domenie zarządzanej
Załóżmy, że masz aplikację sieci web uruchomionego na komputerze "contoso100-webapp.contoso100.com". Musi uzyskać dostęp do zasobu (interfejs API sieci web uruchomiony "contoso100-api.contoso100.com") w kontekście użytkownicy domeny. Poniżej przedstawiono, jak należy skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla tego scenariusza:

1. [Tworzenie niestandardowej jednostki Organizacyjnej](create-ou.md). Można delegować uprawnienia do zarządzania tej niestandardowej jednostki Organizacyjnej dla użytkowników w domenie zarządzanej.
2. Obie maszyny wirtualne (działa aplikacja sieci web i interfejsu API sieci web działa) należy przyłączyć do domeny zarządzanej. Utwórz konta tych komputerów w jednostce Organizacyjnej niestandardowych.
3. Teraz należy skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos za pomocą następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Konta komputerów dla aplikacji internetowej i internetowego interfejsu API muszą znajdować się w niestandardowej jednostce Organizacyjnej, w którym masz uprawnienia do konfigurowania opartego na zasobach ograniczonego delegowania protokołu Kerberos. Nie można skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla konta komputera w kontenerze Wbudowane "Komputery usługi AAD DC".
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Konfigurowanie opartego na zasobach ograniczonego delegowania protokołu Kerberos dla kont użytkowników w domenie zarządzanej
Przyjęto założenie, że aplikacja sieci web jest uruchomiona jako konto usługi "appsvc" i potrzebny dostęp do zasobów (internetowy interfejs API uruchomiona jako konto usługi — "backendsvc") w kontekście użytkownicy domeny. Oto, jak należy skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla tego scenariusza.

1. [Tworzenie niestandardowej jednostki Organizacyjnej](create-ou.md). Można delegować uprawnienia do zarządzania tej niestandardowej jednostki Organizacyjnej dla użytkowników w domenie zarządzanej.
2. Dołącz do maszyny wirtualnej z systemem zaplecza zasobu internetowego interfejsu API/do domeny zarządzanej. Utwórz konto komputera w niestandardowej jednostce Organizacyjnej.
3. Tworzenie konta usługi (na przykład "appsvc"), używane do uruchamiania aplikacji sieci web w niestandardowej jednostce Organizacyjnej.
4. Teraz należy skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos za pomocą następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Konto komputera dla interfejsu API sieci web zaplecza i usługi konta muszą być w niestandardowej jednostce Organizacyjnej, w którym masz uprawnienia do konfigurowania opartego na zasobach ograniczonego delegowania protokołu Kerberos. Nie można skonfigurować opartego na zasobach ograniczonego delegowania protokołu Kerberos dla konta komputera w kontenerze Wbudowane "Komputery usługi AAD DC" lub dla kont użytkowników w kontenerze Wbudowane "Użytkownicy usługi AAD DC". W związku z tym nie możesz użyć kont użytkowników synchronizowanych z usługi Azure AD do skonfigurowania opartego na zasobach ograniczonego delegowania protokołu Kerberos.
>

## <a name="related-content"></a>Powiązana zawartość
* [Azure AD Domain Services — wprowadzenie — przewodnik](create-instance.md)
* [Omówienie delegowanie ograniczone protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
