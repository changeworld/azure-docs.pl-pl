---
title: 'Azure AD Domain Services: Włączanie synchronizacji haseł | Dokumentacja firmy Microsoft'
description: Wprowadzenie do usługi Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 8731f2b2-661c-4f3d-adba-2c9e06344537
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: mstephen
ms.openlocfilehash: 37bcdcd304f702f210e755f45af71b2da10bd6e1
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2019
ms.locfileid: "66234880"
---
# <a name="enable-password-synchronization-to-azure-active-directory-domain-services"></a>Włączanie synchronizacji haseł w usługach Azure Active Directory Domain Services
W poprzednich zadaniach włączono usługi Azure Active Directory Domain Services dla dzierżawy usługi Azure Active Directory (Azure AD). Kolejnym krokiem jest włączenie synchronizacji skrótów poświadczeń wymaganych do uwierzytelniania NT LAN Manager (NTLM) i Kerberos w usługach Azure AD Domain Services. Po skonfigurowaniu synchronizacji poświadczeń użytkownik może zalogować się do domeny zarządzanej przy użyciu poświadczeń firmowych.

Wykonywane czynności są różne dla kont użytkowników tylko w chmurze i kont użytkowników synchronizowanych z katalogu lokalnego przy użyciu programu Azure AD Connect.

| **Typ konta użytkownika** | **Czynności do wykonania** |
| --- | --- |
| **Konta użytkownika synchronizowane z poziomu katalogu lokalnego** |**&#x2713;** [Postępuj zgodnie z instrukcjami w tym artykule](active-directory-ds-getting-started-password-sync-synced-tenant.md#task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad) |
| **Konta użytkowników chmury utworzone w usłudze Azure AD** |**&#x2713;** [Zsynchronizuj hasła dla kont użytkowników tylko w chmurze do domeny zarządzanej](active-directory-ds-getting-started-password-sync.md) |

> [!TIP]
> **Może być konieczne wykonanie obu zestawów czynności.**
> Jeśli dzierżawa usługi Azure AD ma kombinację użytkowników tylko w chmurze i użytkowników z lokalnej usługi AD, należy ukończyć oba zestawy czynności.
>

## <a name="task-5-enable-password-synchronization-to-your-managed-domain-for-user-accounts-synced-with-your-on-premises-ad"></a>Zadanie 5. Włączanie synchronizacji haseł do domeny zarządzanej dla kont użytkowników zsynchronizowanych z lokalną usługą AD
Zsynchronizowana dzierżawa usługi Azure AD jest ustawiona do synchronizacji z katalogiem lokalnym organizacji za pomocą programu Azure AD Connect. Domyślnie program Azure AD Connect nie synchronizuje skrótów poświadczeń protokołów NTLM i Kerberos w ramach usługi Azure AD. Aby korzystać z usługi Azure AD Domain Services, należy skonfigurować program Azure AD Connect w celu zsynchronizowania skrótów poświadczeń wymaganych do uwierzytelniania NTLM i Kerberos. Następujące kroki umożliwiają synchronizację wymaganych skrótów poświadczeń z katalogu lokalnego z dzierżawą usługi Azure AD.

> [!NOTE]
> **Jeśli organizacja ma konta użytkowników, które są synchronizowane z katalogu lokalnego, musisz włączyć synchronizację skrótów NTLM i Kerberos, aby można było używać domeny zarządzanej.** Zsynchronizowane konto użytkownika to konto utworzone w katalogu lokalnym, które jest synchronizowane z dzierżawą usługi Azure AD za pomocą programu Azure AD Connect.
>
>

### <a name="install-or-update-azure-ad-connect"></a>Instalowanie lub aktualizowanie programu Azure AD Connect
Na komputerze przyłączonym do domeny zainstaluj najnowszą zalecaną wersję programu Azure AD Connect. Jeśli masz istniejące wystąpienie instalatora programu Azure AD Connect, musisz zaktualizować go tak, aby można było korzystać z najnowszej wersji programu Azure AD Connect. Zawsze używaj najnowszej wersji programu Azure AD Connect, aby uniknąć znanych problemów i błędów, które mogły już zostać naprawione.

**[Pobieranie programu Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)**

Zalecana wersja: **1.1.614.0** — opublikowana 5 września 2017 r.

> [!WARNING]
> Aby umożliwić synchronizowanie starych poświadczeń haseł (wymaganych podczas uwierzytelniania NTLM i Kerberos) z dzierżawą usługi Azure AD, MUSISZ zainstalować najnowszą zalecaną wersję programu Azure AD Connect. Ta funkcja nie jest dostępna w poprzednich wersjach programu Azure AD Connect ani starszej wersji narzędzia DirSync.
>
>

Instrukcje dotyczące instalowania programu Azure AD Connect są dostępne w artykule [Wprowadzenie do programu Azure AD Connect](../active-directory/hybrid/whatis-hybrid-identity.md)

### <a name="enable-synchronization-of-ntlm-and-kerberos-credential-hashes-to-azure-ad"></a>Włączanie synchronizacji skrótów poświadczeń NTLM i Kerberos w usłudze Azure AD
Uruchom następujący skrypt programu PowerShell w każdym lesie usługi AD. Skrypt włącza synchronizację skrótów haseł protokołów NTLM i Kerberos dla wszystkich użytkowników lokalnych z Twoją dzierżawą usługi Azure AD. Skrypt inicjuje również pełną synchronizację w programie Azure AD Connect.

```powershell
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$azureadConnector = "<CASE SENSITIVE AZURE AD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $azureadConnector -Enable $true
```

Czas synchronizacji skrótów poświadczeń z usługą Azure AD zależy od rozmiaru katalogu (liczby użytkowników, grup itp.). Z haseł będzie można korzystać w domenie zarządzanej Usług domenowych Azure AD wkrótce po zsynchronizowaniu skrótów poświadczeń z usługą Azure AD.

## <a name="related-content"></a>Powiązana zawartość
* [Włączanie synchronizacji haseł w usługach AAD Domain Services dla katalogu usługi Azure AD tylko w chmurze](active-directory-ds-getting-started-password-sync.md)
* [Zarządzaj domeną usługi Azure AD Domain Services](manage-domain.md)
* [Dołączanie maszyny wirtualnej z systemem Windows do domeny zarządzanej usług Azure AD Domain Services](active-directory-ds-admin-guide-join-windows-vm.md)
* [Dołączanie maszyny wirtualnej z systemem Red Hat Enterprise Linux do domeny zarządzanej usług Azure AD Domain Services](active-directory-ds-admin-guide-join-rhel-linux-vm.md)
