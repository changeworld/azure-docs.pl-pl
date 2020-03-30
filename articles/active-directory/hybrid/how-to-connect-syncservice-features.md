---
title: Funkcje i konfiguracja usługi synchronizacji usługi Azure AD Connect | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje po stronie usługi Azure AD Connect dla usługi synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/25/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5486a8d8bd4c295f49e0ab847daf45d0fcab47ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300540"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkcje usługi synchronizacji programu Azure AD Connect

Funkcja synchronizacji usługi Azure AD Connect ma dwa składniki:

* Składnik lokalny o nazwie **Synchronizacja usługi Azure AD Connect**, nazywany także **aparatem synchronizacji.**
* Usługa zamieszkała w usłudze Azure AD, znana również jako **usługa synchronizacji usługi Azure AD Connect**

W tym temacie wyjaśniono, jak działają następujące funkcje **usługi synchronizacji usługi Azure AD Connect** i jak można je skonfigurować przy użyciu programu Windows PowerShell.

Te ustawienia są konfigurowane przez [moduł usługi Azure Active Directory dla programu Windows PowerShell](https://aka.ms/aadposh). Pobierz i zainstaluj go oddzielnie od usługi Azure AD Connect. Polecenia cmdlet udokumentowane w tym temacie zostały wprowadzone w [wydaniu marca 2016 (kompilacja 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Jeśli nie masz poleceń cmdlet udokumentowanych w tym temacie lub nie dają one tego samego wyniku, upewnij się, że uruchomisz najnowszą wersję.

Aby wyświetlić konfigurację w katalogu `Get-MsolDirSyncFeatures`usługi Azure AD, uruchom program .  
![Wynik Get-MsolDirSyncFeatures](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Wiele z tych ustawień można zmienić tylko przez usługę Azure AD Connect.

Następujące ustawienia można skonfigurować `Set-MsolDirSyncFeature`przez:

| DirSyncWyjema | Komentarz |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Umożliwia dołączeniu obiektów do userPrincipalName oprócz podstawowego adresu SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umożliwia aparatowi synchronizacji aktualizowanie atrybutu userPrincipalName dla użytkowników zarządzanych/licencjonowanych (niefederowanych). |

Po włączeniu funkcji nie można jej ponownie wyłączyć.

> [!NOTE]
> Od 24 sierpnia 2016 r. funkcja *Odporność atrybutu duplikaty* jest domyślnie włączona dla nowych katalogów usługi Azure AD. Ta funkcja zostanie również wdrożona i włączona w katalogach utworzonych przed tą datą. Otrzymasz powiadomienie e-mail, gdy katalog ma zostać włączony.
> 
> 

Następujące ustawienia są konfigurowane przez usługę Azure `Set-MsolDirSyncFeature`AD Connect i nie mogą być modyfikowane przez:

| DirSyncWyjema | Komentarz |
| --- | --- |
| DeviceWriteback |[Usługa Azure AD Connect: włączanie zapisywania zwrotnego na urządzeniach](how-to-connect-device-writeback.md) |
| KatalogWysoki |[Synchronizacja usługi Azure AD Connect: rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Umożliwia poddanie atrybutu kwarantannie, gdy jest duplikatem innego obiektu, a nie niepowodzeniem całego obiektu podczas eksportowania. |
| Synchronizacja skrótów haseł |[Implementowanie synchronizacji skrótów haseł z synchronizacją usługi Azure AD Connect](how-to-connect-password-hash-synchronization.md) |
|Uwierzytelnianie przekazywane|[Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)|
| Ujednolicony Powrót grupy |[Podgląd: Stornia grupowe](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Obecnie nie jest obsługiwana. |

## <a name="duplicate-attribute-resiliency"></a>Odporność atrybutów duplikatów

Zamiast nie aprowizować obiektów z duplikatami upn / proxyAddresses, zduplikowany atrybut jest "kwarantannie" i jest przypisana wartość tymczasowa. Po rozwiązaniu konfliktu tymczasowa nazwa UPN jest automatycznie zmieniana na właściwą wartość. Aby uzyskać więcej informacji, zobacz [Synchronizacja tożsamości i odporność atrybutów duplikatów](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>Nazwa miękka userPrincipalName

Gdy ta funkcja jest włączona, funkcja soft-match jest włączona dla niania upn oprócz [podstawowego adresu SMTP](https://support.microsoft.com/kb/2641663), który jest zawsze włączony. Dopasowanie programowe jest używane do dopasowywać istniejących użytkowników chmury w usłudze Azure AD do użytkowników lokalnych.

Jeśli musisz dopasować lokalne konta usługi AD do istniejących kont utworzonych w chmurze i nie używasz usługi Exchange Online, ta funkcja jest przydatna. W tym scenariuszu zazwyczaj nie masz powodu, aby ustawić atrybut SMTP w chmurze.

Ta funkcja jest domyślnie włączona dla nowo utworzonych katalogów usługi Azure AD. Możesz sprawdzić, czy ta funkcja jest włączona, uruchamiając:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, można ją włączyć, uruchamiając:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizowanie aktualizacji userPrincipalName

W przeszłości aktualizacje atrybutu UserPrincipalName przy użyciu usługi synchronizacji z lokalnego oprogramowania zostały zablokowane, chyba że oba te warunki są spełnione:

* Użytkownik jest zarządzany (niefederowany).
* Użytkownikowi nie przypisano licencji.

Aby uzyskać więcej informacji, zobacz [Nazwy użytkowników w usłudze Office 365, platformie Azure lub usłudze Intune nie są zgodne z lokalną siecią UPN lub alternatywnym identyfikatorem logowania.](https://support.microsoft.com/kb/2523192)

Włączenie tej funkcji umożliwia aparatowi synchronizacji aktualizowanie userPrincipalName po zmianie w środowisku lokalnym i użycie synchronizacji hash hasła lub uwierzytelniania przekazywanego.

Ta funkcja jest domyślnie włączona dla nowo utworzonych katalogów usługi Azure AD. Możesz sprawdzić, czy ta funkcja jest włączona, uruchamiając:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, można ją włączyć, uruchamiając:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po włączeniu tej funkcji istniejące wartości userPrincipalName pozostaną takie, jak jest. Przy następnej zmianie atrybutu userPrincipalName lokalnie normalna synchronizacja delta użytkowników zaktualizuje nazwy UPN.  

## <a name="see-also"></a>Zobacz też

* [Synchronizacja usługi Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
