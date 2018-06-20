---
title: 'Azure Active Directory Domain Services: Włącz ograniczone delegowanie protokołu kerberos | Dokumentacja firmy Microsoft'
description: Włącz ograniczone delegowanie protokołu kerberos w domenach zarządzanych usług domenowych Azure Active Directory
services: active-directory-ds
documentationcenter: ''
author: mahesh-unnikrishnan
manager: mtillman
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.component: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2018
ms.author: maheshu
ms.openlocfilehash: fc4e738e95799838c5761e8b1ee5973aefea7dc6
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213844"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Skonfiguruj ograniczone delegowanie protokołu Kerberos (KCD) do domeny zarządzanej
Wiele aplikacji muszą uzyskać dostęp do zasobów w kontekście użytkownika. Usługa Active Directory obsługuje mechanizm delegowania protokołu Kerberos, co umożliwia przypadek użycia wywołuje. Ponadto można ograniczyć delegowania tak, aby tylko określonych zasobów można uzyskać w kontekście użytkownika. Azure domen zarządzanych w usługach domenowych AD różnią się od tradycyjnych domen usługi Active Directory, ponieważ są one bardziej bezpieczny sposób zablokowana.

W tym artykule przedstawiono sposób konfigurowania ograniczonego delegowania protokołu Kerberos w domenie zarządzanej usług domenowych Azure AD.

## <a name="kerberos-constrained-delegation-kcd"></a>Ograniczone delegowanie protokołu Kerberos (KCD)
Delegowanie protokołu Kerberos umożliwia konta personifikować innego podmiotu zabezpieczeń (na przykład użytkownik) dostęp do zasobów. Należy wziąć pod uwagę aplikacji sieci web, który uzyskuje dostęp do sieci web zaplecza interfejsu API w kontekście użytkownika. W tym przykładzie użytkownik personifikuje aplikacji sieci web (uruchomione w kontekście konta usługi lub konto komputera/urządzenia), podczas uzyskiwania dostępu do zasobów (zaplecza interfejsu API sieci web). Delegowanie protokołu Kerberos jest niebezpieczne, ponieważ nie ogranicza zasobów, których dostęp konta personifikacji w kontekście użytkownika.

Ograniczone delegowanie protokołu Kerberos (KCD) ogranicza usługi/zasoby, do których określony serwer może działać w imieniu użytkownika. Tradycyjny KCD wymaga uprawnień administratora domeny, aby skonfigurować konto domeny dla usługi i ogranicza konta do jednej domeny.

Tradycyjny KCD ma również kilka problemów związanych z nim. W starszych systemach operacyjnych Jeśli administrator domeny konfigurował KCD na podstawie konta usługi, administrator usług nie miał możliwości wiedzieć, które usługi frontonu delegowane do usług zasobów są jego własnością. A usługa frontonu, którą mógł delegować do usługi zasobów potencjalny punkt ataku. Jeśli takie naruszenie nastąpi naruszenie serwera udostępniającego usługę frontonu i był on skonfigurowany do delegowania usług zasobów, zasobów usługi również mogły zostać naruszone.

> [!NOTE]
> W domenie zarządzanej usług domenowych Azure AD nie masz uprawnienia administratora domeny. W związku z tym **tradycyjnych KCD na podstawie konta nie można skonfigurować w domenie zarządzanej**. Użyj KCD oparte na zasobach, zgodnie z opisem w tym artykule. Ten mechanizm również jest bardziej bezpieczne.
>
>

## <a name="resource-based-kcd"></a>Oparte na zasobach KCD
Z systemu Windows Server 2012 lub nowszym administratorów usługi uzyskać możliwość konfigurowania ograniczonego delegowania dla usługi. W tym modelu administrator usług zaplecza można akceptować lub odrzucać określonych usług frontonu z za pomocą KCD. Ten model jest nazywany **oparte na zasobach KCD**.

Oparte na zasobach KCD została skonfigurowana przy użyciu programu PowerShell. Możesz użyć `Set-ADComputer` lub `Set-ADUser` poleceń cmdlet, w zależności od tego, czy konto personifikacji jest konto komputera lub konto serwisu konta użytkownika.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Skonfiguruj oparte na zasobach KCD dla konta komputera do domeny zarządzanej
Założono, że masz aplikację sieci web uruchomionej na komputerze "contoso100-webapp.contoso100.com". Wymaga dostępu do zasobu (interfejs API sieci web uruchomiony na "contoso100-api.contoso100.com") w kontekście użytkownicy domeny. Oto, jak należy skonfigurować oparte na zasobach KCD dla tego scenariusza:

1. [Tworzenie niestandardowych jednostki Organizacyjnej](active-directory-ds-admin-guide-create-ou.md). Możesz delegować uprawnienia do zarządzania tej niestandardowej jednostki Organizacyjnej dla użytkowników w ramach domeny zarządzanej.
2. Obydwie maszyny wirtualne (jeden aplikacją sieci web i jeden uruchomiony interfejs API sieci web) należy przyłączyć do domeny zarządzanej. Utwórz konta tych komputerów w jednostce Organizacyjnej niestandardowych.
3. Teraz skonfiguruj KCD oparte na zasobach za pomocą następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Konta komputerów dla aplikacji sieci web i interfejsu API sieci web muszą być niestandardowych OU, w której masz uprawnienia do konfigurowania KCD oparte na zasobach. Nie można skonfigurować oparte na zasobach KCD dla konta komputera w kontenerze "Komputery kontrolera domeny usługi AAD" wbudowanych.
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Skonfiguruj oparte na zasobach KCD dla konta użytkownika w domenie zarządzanej
Założono, że aplikacja sieci web uruchomiona jako konto usługi "appsvc" i musi on mieć dostęp do zasobu (interfejsu API sieci web uruchomiona jako konto usługi - "backendsvc") w kontekście użytkowników domeny. Oto, jak należy skonfigurować oparte na zasobach KCD dla tego scenariusza.

1. [Tworzenie niestandardowych jednostki Organizacyjnej](active-directory-ds-admin-guide-create-ou.md). Możesz delegować uprawnienia do zarządzania tej niestandardowej jednostki Organizacyjnej dla użytkowników w ramach domeny zarządzanej.
2. Dołącz do maszyny wirtualnej uruchomionej w sieci web zaplecza interfejsu API zasobów do domeny zarządzanej. Utwórz konto komputera w jednostce Organizacyjnej niestandardowych.
3. Tworzenie konta usługi (na przykład "appsvc"), użyta do uruchomienia aplikacji sieci web w jednostce Organizacyjnej niestandardowych.
4. Teraz skonfiguruj KCD oparte na zasobach za pomocą następującego polecenia programu PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Konto komputera dla interfejsu API sieci web zaplecza i potrzeby konta usługi w niestandardowych OU, w której masz uprawnienia do konfigurowania KCD oparte na zasobach. Nie można skonfigurować KCD zasobów dla konta komputera w kontenerze "Komputery kontrolera domeny usługi AAD" wbudowanych lub kont użytkowników w kontenerze Wbudowane "Użytkowników usługi AAD kontrolera domeny". W związku z tym nie można użyć kont użytkowników synchronizowanych z usługą Azure AD do skonfigurowania KCD oparte na zasobach.
>

## <a name="related-content"></a>Powiązana zawartość
* [Usługi domenowe AD Azure - Przewodnik wprowadzający](active-directory-ds-getting-started.md)
* [Omówienie delegowania ograniczonego protokołu Kerberos](https://technet.microsoft.com/library/jj553400.aspx)
