---
author: billmath
ms.service: active-directory
ms.subservice: cloud-provisioning
ms.topic: include
ms.date: 10/16/2019
ms.author: billmath
ms.openlocfilehash: 3aa1571b46938b03f556fa124d3f0a2a70f2c5c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "79504284"
---
## <a name="steps-to-enable-single-sign-on"></a>Kroki umożliwiające logowanie jednokrotne
Inicjowanie obsługi administracyjnej w chmurze działa z logowania jednokrotnego.  Obecnie nie ma opcji włączenia rejestracji w stan przysłowiowy, gdy agent jest zainstalowany, jednak można użyć poniższych kroków, aby włączyć funkcję rejestracji na uwierzytelniania i używać go. 

### <a name="step-1-download-and-extract-azure-ad-connect-files"></a>Krok 1: Pobieranie i wyodrębnianie plików usługi Azure AD Connect
1.  Najpierw pobierz najnowszą wersję [usługi Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594)
2.  Otwórz wiersz polecenia przy użyciu uprawnień administracyjnych i przejdź do msi, które właśnie pobrano.
3.  Uruchom następujące czynności:`msiexec /a C:\filepath\AzureADConnect.msi /qb TARGETDIR=C:\filepath\extractfolder`
4. Zmień ścieżkę plików i folder wyodrębniania, aby dopasować ścieżkę pliku i nazwę folderu wyodrębniania.  Zawartość powinna teraz znajdować się w folderze wyodrębniania.

### <a name="step-2-import-the-seamless-sso-powershell-module"></a>Krok 2: Importowanie modułu Seamless SSO PowerShell

1. Pobierz i zainstaluj [program Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Przejdź do folderu `%programfiles%\Microsoft Azure Active Directory Connect`.
3. Zaimportuj moduł Seamless SSO PowerShell za pomocą tego polecenia: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-3-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 3: Pobierz listę lasów usługi Active Directory, na których włączono bezproblemowe logowanie jednokrotne

1. Uruchom program PowerShell jako administrator. W programie PowerShell zadzwoń do `New-AzureADSSOAuthenticationContext`programu . Po wyświetleniu monitu wprowadź poświadczenia administratora globalnego dzierżawy.
2. Zadzwoń `Get-AzureADSSOStatus`. To polecenie zawiera listę lasów usługi Active Directory (patrz lista "Domeny"), na której ta funkcja została włączona.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Włącz bezproblemowe logowanie jednokrotne dla każdego lasu usługi Active Directory

1. Zadzwoń `Enable-AzureADSSOForest`. Po wyświetleniu monitu wprowadź poświadczenia administratora domeny dla zamierzonego lasu usługi Active Directory.

   > [!NOTE]
   >Nazwa użytkownika poświadczeń administratora domeny musi być wprowadzona w formacie nazwy konta SAM (contoso\johndoe lub contoso.com\johndoe). Używamy części domeny nazwy użytkownika, aby zlokalizować kontroler domeny administratora domeny przy użyciu systemu DNS.

   >[!NOTE]
   >Używane konto administratora domeny nie może być członkiem grupy Użytkownicy chronieni. Jeśli tak, operacja zakończy się niepowodzeniem.

2. Powtórz poprzedni krok dla każdego lasu usługi Active Directory, w którym chcesz skonfigurować operację.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Włącz funkcję w dzierżawie

Aby włączyć tę funkcję w `Enable-AzureADSSO -Enable $true`dzierżawie, zadzwoń do .
