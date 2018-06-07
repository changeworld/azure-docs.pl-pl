---
title: Funkcje usługi synchronizacji programu Azure AD Connect i Konfiguracja | Dokumentacja firmy Microsoft
description: Zawiera opis funkcji po stronie usługi dla usługi synchronizacji programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 213aab20-0a61-434a-9545-c4637628da81
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 2dbe14f6c7f8f9f1dcfaf733dc884df5adf919aa
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34593175"
---
# <a name="azure-ad-connect-sync-service-features"></a>Funkcje usługi synchronizacji programu Azure AD Connect
Funkcja synchronizacji programu Azure AD Connect zawiera dwa składniki:

* Składnik lokalnego o nazwie **synchronizacja programu Azure AD Connect**, nazywany również **aparatu synchronizacji**.
* Usługi znajdującej się w usłudze Azure AD, znanej także jako **usługi synchronizacji programu Azure AD Connect**

W tym temacie opisano sposób następujące funkcje **usługi synchronizacji programu Azure AD Connect** pracy i jak można je również skonfigurować za pomocą środowiska Windows PowerShell.

Te ustawienia są konfigurowane przez [Azure Active Directory modułu dla środowiska Windows PowerShell](https://aka.ms/aadposh). Pobierz i zainstaluj go niezależnie od Azure AD Connect. Polecenia cmdlet, opisane w niniejszym dokumencie zostały wprowadzone w systemie [wersji marca 2016 (kompilacja 9031.1)](http://social.technet.microsoft.com/wiki/contents/articles/28552.microsoft-azure-active-directory-powershell-module-version-release-history.aspx#Version_9031_1). Jeśli nie masz cmdlet opisane w tym temacie lub tworzy takiego samego wyniku, upewnij się, że korzystać z najnowszej wersji.

Aby sprawdzić konfigurację w katalogu usługi Azure AD, uruchom `Get-MsolDirSyncFeatures`.  
![Get-MsolDirSyncFeatures wyników](./media/active-directory-aadconnectsyncservice-features/getmsoldirsyncfeatures.png)

Wiele z tych ustawień można zmienić tylko przy użyciu usługi Azure AD Connect.

Następujące ustawienia można skonfigurować, `Set-MsolDirSyncFeature`:

| DirSyncFeature | Komentarz |
| --- | --- |
| [EnableSoftMatchOnUpn](#userprincipalname-soft-match) |Pozwala obiektów Dołącz userPrincipalName oprócz podstawowego adresu SMTP. |
| [SynchronizeUpnForManagedUsers](#synchronize-userprincipalname-updates) |Umożliwia aparatu synchronizacji można zaktualizować atrybutu userPrincipalName zarządzane licencjonowanych użytkowników (niefederacyjnych). |

Po włączeniu funkcji nie można wyłączyć ponownie.

> [!NOTE]
> Z 24 sierpnia 2016 funkcję *zduplikowany atrybut odporności* jest domyślnie włączona dla nowej usługi Azure AD katalogów. Ta funkcja będzie również wprowadzanie i włączone na katalogi utworzone przed tą datą. Otrzymasz wiadomość e-mail z powiadomieniem, gdy katalog ma pobrać tę funkcję.
> 
> 

Następujące ustawienia są skonfigurowane przy użyciu usługi Azure AD Connect i nie można zmodyfikować przez `Set-MsolDirSyncFeature`:

| DirSyncFeature | Komentarz |
| --- | --- |
| DeviceWriteback |[Azure AD Connect: Włączanie zapisywania zwrotnego urządzeń](active-directory-aadconnect-feature-device-writeback.md) |
| DirectoryExtensions |[Synchronizacja programu Azure AD Connect: rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md) |
| [DuplicateProxyAddressResiliency<br/>DuplicateUPNResiliency](#duplicate-attribute-resiliency) |Umożliwia atrybutu kwarantannie, gdy jest duplikatem innego obiektu, a nie awarii całego obiektu podczas eksportowania. |
| PasswordSync |[Implementowanie synchronizacji skrótów haseł z synchronizacji Azure AD Connect](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |
| UnifiedGroupWriteback |[Podglądu: Zapisywanie zwrotne grup](active-directory-aadconnect-feature-preview.md#group-writeback) |
| UserWriteback |Nie są obecnie obsługiwane. |

## <a name="duplicate-attribute-resiliency"></a>Zduplikowany atrybut odporności
Zamiast się niepowodzeniem, aby udostępnić obiekty z zduplikowane nazwy UPN / proxyAddresses, zduplikowany atrybut jest "poddane kwarantannie" i przypisano wartości tymczasowej. Po rozwiązaniu konfliktu tymczasowej nazwy UPN jest automatycznie zmieniany na poprawną wartość. Aby uzyskać więcej informacji, zobacz [tożsamości synchronizacji i zduplikowany atrybut odporności](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md).

## <a name="userprincipalname-soft-match"></a>UserPrincipalName nietrwałego dopasowania
Gdy ta funkcja jest włączona, soft-match jest włączona dla nazwy UPN oprócz [podstawowego adresu SMTP](https://support.microsoft.com/kb/2641663), który jest zawsze włączona. Soft-match jest używany do dopasowywania istniejących użytkowników w chmurze w usłudze Azure AD z lokalnymi użytkownikami.

Jeśli konieczne jest dopasowanie lokalnych kont usługi AD z istniejących kont utworzonych w chmurze i nie używasz usługi Exchange Online, a następnie ta funkcja jest przydatna. W tym scenariuszu zwykle nie ma powodu ustawić atrybutu SMTP w chmurze.

Ta funkcja jest na domyślnie dla nowo utworzone katalogów usługi Azure AD. Możesz sprawdzić, czy ta funkcja jest włączona dla Ciebie, uruchamiając:  

```
Get-MsolDirSyncFeatures -Feature EnableSoftMatchOnUpn
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, następnie możesz je włączyć, uruchamiając:  

```
Set-MsolDirSyncFeature -Feature EnableSoftMatchOnUpn -Enable $true
```

## <a name="synchronize-userprincipalname-updates"></a>Synchronizowanie aktualizacji userPrincipalName
W przeszłości aktualizacje przy użyciu usługi synchronizacji z lokalnym atrybutu UserPrincipalName został zablokowany, chyba że oba te warunki są spełnione:

* Użytkownik jest zarządzany (niefederacyjnych).
* Użytkownik nie ma przypisanej licencji.

Aby uzyskać więcej informacji, zobacz [nazwy użytkownika w usłudze Office 365, Azure lub Intune nie są zgodne lokalną nazwą UPN lub alternatywnym Identyfikatorem logowania](https://support.microsoft.com/kb/2523192).

Włączenie tej funkcji umożliwia aparatu synchronizacji można zaktualizować właściwości userPrincipalName, gdy jest zmienione na lokalnym i używasz synchronizacji skrótów haseł. Jeśli używasz federacyjnych, ta funkcja nie jest obsługiwana.

Ta funkcja jest na domyślnie dla nowo utworzone katalogów usługi Azure AD. Możesz sprawdzić, czy ta funkcja jest włączona dla Ciebie, uruchamiając:  

```
Get-MsolDirSyncFeatures -Feature SynchronizeUpnForManagedUsers
```

Jeśli ta funkcja nie jest włączona dla katalogu usługi Azure AD, następnie możesz je włączyć, uruchamiając:  

```
Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $true
```

Po włączeniu tej funkcji, zostaną one istniejące wartości userPrincipalName — jest. Dla następnej zmiany userPrincipalName atrybutu lokalną synchronizacja różnicowa normalne, użytkownicy zaktualizuje głównej nazwy użytkownika.  

## <a name="see-also"></a>Zobacz także
* [Synchronizacja programu Azure AD Connect](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

