---
title: Funkcji usługi synchronizacji programu Azure AD Connect i Konfiguracja | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji po stronie usługi, usługa synchronizacji Azure AD Connect.
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
ms.openlocfilehash: be67a6f287e2d6e77070928cbe12542857696011
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680285"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkcji usługi synchronizacji programu Azure AD Connect

Funkcja synchronizacji programu Azure AD Connect ma dwa składniki:

* Składnik środowiska lokalnego, o nazwie **synchronizacji programu Azure AD Connect**, nazywane również **aparatu synchronizacji**.
* Usługi znajdujące się w usłudze Azure AD, znany także jako **Usługa synchronizacji Azure AD Connect**

W tym temacie opisano sposób, w jaki następujące funkcje **Usługa synchronizacji Azure AD Connect** pracy i jak można skonfigurować je przy użyciu programu Windows PowerShell.

Te ustawienia są konfigurowane przez [Azure Active Directory Module for Windows PowerShell](https://aka.ms/aadposh). Pobierz i zainstaluj je oddzielnie z usługi Azure AD Connect. Polecenia cmdlet opisane w niniejszym dokumencie zostały wprowadzone w [wydanej w marcu 2016 (kompilacja 9031.1)](https://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Jeśli nie masz polecenia cmdlet opisane w tym temacie lub generuje ten sam wynik, upewnij się, że uruchomieniu najnowszej wersji.

Aby wyświetlić konfigurację w katalogu usługi Azure AD, uruchom `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures result](./media/how-to-connect-syncservice-features/getmsoldirsyncfeatures.png)

Wiele z tych ustawień można zmienić tylko przy użyciu usługi Azure AD Connect.

Następujące ustawienia można skonfigurować przez `Set-MsolDirSyncFeature`:

| DirSyncFeature | Komentarz |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Pozwala obiektów odbędzie userPrincipalName oprócz podstawowego adresu SMTP użytkownika. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umożliwia aparatu synchronizacji można zaktualizować atrybutu userPrincipalName zarządzane/licencjonowanych użytkowników (inne niż federacyjne). |

Po włączeniu funkcji nie można wyłączyć ponownie.

> [!NOTE]
> Z 24 sierpnia 2016 r. funkcji *odporność względem zduplikowanych atrybutów* jest włączone domyślnie dla nowej usługi Azure AD katalogów. Ta funkcja zostanie także udostępniona i włączona katalogi utworzone przed tą datą. Otrzymasz wiadomość e-mail z powiadomieniem, gdy katalogu staje się włączenia tej funkcji.
> 
> 

Następujące ustawienia są konfigurowane przy użyciu usługi Azure AD Connect i nie może modyfikować `Set-MsolDirSyncFeature`:

| DirSyncFeature | Komentarz |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń](how-to-connect-device-writeback.md) |
| DirectoryExtensions |[Synchronizacja w programie Azure AD Connect: Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Zezwala na atrybut kwarantannie, gdy jest duplikatem innego obiektu, a nie awarii całego obiektu podczas eksportowania. |
| Synchronizacja skrótów haseł |[Implementowanie synchronizacji skrótów haseł z usługą Azure AD Connect sync](how-to-connect-password-hash-synchronization.md) |
|Uwierzytelnianie przekazywane|[Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego usługi Azure Active Directory](how-to-connect-pta.md)|
| UnifiedGroupWriteback |[Wersja zapoznawcza: Zapisywanie zwrotne grup](how-to-connect-preview.md#group-writeback) |
| UserWriteback |Nie są obecnie obsługiwane. |

## <a name="duplicate-attribute-resiliency"></a>Odporność na duplikowanie atrybutów

Zamiast przechodzenia do aprowizowania obiekty z zduplikowane UPN / proxyAddresses lub zduplikowanym atrybutem jest "objęte kwarantanną" i przypisano wartości tymczasowej. Po usunięciu konflikt tymczasowe nazwy UPN jest zmieniany na poprawną wartość automatycznie. Aby uzyskać więcej informacji, zobacz [tożsamości synchronizacji i odporności zduplikowanego atrybutu](how-to-connect-syncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName soft match

Po włączeniu tej funkcji miękkiego jest włączona dla nazwy UPN w uzupełnieniu do [podstawowego adresu SMTP użytkownika](https://support.microsoft.com/kb/2641663), który jest zawsze włączona. Opcji soft-match jest używany do dopasowywania istniejących użytkowników w chmurze w usłudze Azure AD przy użyciu lokalnych użytkowników.

Jeśli potrzebujesz dopasowanie środowiska lokalnego konta usługi AD z istniejących kont utworzonych w chmurze i nie używasz usługi Exchange Online, a następnie ta funkcja jest przydatna. W tym scenariuszu ogólnie nie masz powód, aby ustawić atrybut SMTP w chmurze.

Ta funkcja jest na domyślny dla nowo utworzony katalogów usługi Azure AD. Zostanie wyświetlony, jeśli ta funkcja jest włączona dla Ciebie, uruchamiając:  

```powershell
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, następnie można je włączyć, uruchamiając:  

```powershell
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizowanie aktualizacji userPrincipalName

W przeszłości aktualizacje atrybut UserPrincipalName przy użyciu usługi synchronizacji ze środowiska lokalnego zostało zablokowane, chyba że oba te warunki są spełnione:

* Użytkownik jest zarządzany (inne niż federacyjne).
* Użytkownikowi nie przypisano licencji.

Aby uzyskać więcej informacji, zobacz [nazwy użytkowników w usłudze Office 365, Azure lub Intune nie są zgodne lokalnymi nazwami UPN lub alternatywnym Identyfikatorem logowania](https://support.microsoft.com/kb/2523192).

Włączenie tej funkcji umożliwia aparatu synchronizacji można zaktualizować właściwości userPrincipalName, gdy jest zmienione w środowisku lokalnym i używasz hasła wyznaczania wartości skrótu synchronizacji lub uwierzytelniania przekazywanego. Jeśli używasz Federacji, ta funkcja nie jest obsługiwana.

Ta funkcja jest na domyślny dla nowo utworzony katalogów usługi Azure AD. Zostanie wyświetlony, jeśli ta funkcja jest włączona dla Ciebie, uruchamiając:  

```powershell
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, następnie można je włączyć, uruchamiając:  

```powershell
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po włączeniu tej funkcji, wciąż istniejące wartości userPrincipalName-to. Dla następnej zmiany userPrincipalName atrybutu w środowisku lokalnym synchronizacja różnicowa użytkowników zostanie zaktualizowana nazwa UPN.  

## <a name="see-also"></a>Zobacz także

* [Synchronizacja programu Azure AD Connect](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md).
