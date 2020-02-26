---
title: Włącz uwierzytelnianie Active Directory za pośrednictwem protokołu SMB dla Azure Files
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB dla udziałów plików platformy Azure za pośrednictwem Active Directory. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/23/2020
ms.author: rogarana
ms.openlocfilehash: 6dba1dcbfd5d8b957565dcd1353af6e37a57a81d
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599251"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Włącz uwierzytelnianie Active Directory za pośrednictwem protokołu SMB dla udziałów plików platformy Azure

[Azure Files](storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) przez dwa typy usług domenowych: Azure Active Directory Domain Services (Azure AD DS) (ga) i Active Directory (AD) (wersja zapoznawcza). Ten artykuł koncentruje się na nowo wprowadzonej (w wersji zapoznawczej) obsłudze usługi domena usługi Active Directory na potrzeby uwierzytelniania w udziałach plików platformy Azure. Jeśli interesuje Cię włączenie uwierzytelniania za pomocą usługi Azure AD DS (GA) dla udziałów plików platformy Azure, zapoznaj się z [naszym artykułem w temacie](storage-files-identity-auth-active-directory-domain-service-enable.md).

> [!NOTE]
> Udziały plików platformy Azure obsługują tylko uwierzytelnianie dla jednej usługi domeny, Azure Active Directory usługi domeny (Azure AD DS) lub Active Directory (AD). 
>
> Tożsamości usługi AD używane do uwierzytelniania udziałów plików platformy Azure muszą być synchronizowane z usługą Azure AD. Synchronizacja skrótów haseł jest opcjonalna. 
> 
> Uwierzytelnianie usługi AD nie obsługuje uwierzytelniania na kontach komputerów utworzonych w usłudze AD. 
> 
> Uwierzytelnianie usługi AD może być obsługiwane tylko w przypadku jednego lasu usługi AD, w którym zarejestrowano konto magazynu. Można uzyskać dostęp tylko do udziałów plików platformy Azure z poświadczeniami usługi AD z pojedynczego lasu usługi AD. Jeśli potrzebujesz dostępu do udziału plików platformy Azure z innego lasu, upewnij się, że skonfigurowano odpowiednie zaufanie lasu, aby uzyskać szczegółowe informacje, zobacz [często zadawane pytania](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) .  
> 
> Uwierzytelnianie usługi AD dla funkcji dostępu SMB i trwałości list ACL jest obsługiwane dla udziałów plików platformy Azure zarządzanych przez Azure File Sync.
>
> Azure Files obsługuje uwierzytelnianie Kerberos za pomocą usługi AD z szyfrowaniem RC4-HMAC. Szyfrowanie AES Kerberos nie jest jeszcze obsługiwane.

Po włączeniu usługi AD dla udziałów plików platformy Azure za pośrednictwem protokołu SMB komputery przyłączone do domeny usługi AD mogą instalować udziały plików platformy Azure przy użyciu istniejących poświadczeń usługi AD. Tę możliwość można włączyć za pomocą środowiska usługi AD hostowanego na maszynach Premium lub hostowanych na platformie Azure.

Tożsamości usługi AD służące do uzyskiwania dostępu do udziałów plików platformy Azure muszą zostać zsynchronizowane z usługą Azure AD w celu wymuszenia uprawnień plików na poziomie udziału za pośrednictwem standardowego modelu [kontroli dostępu opartego na rolach (RBAC)](../../role-based-access-control/overview.md) . [Listy DACL w stylu systemu Windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) dotyczące plików/katalogów przeprowadzonych z istniejących serwerów plików zostaną zachowane i wymuszone. Ta funkcja zapewnia bezproblemową integrację z infrastrukturą domeny usługi AD przedsiębiorstwa. Gdy zastąpisz Premium serwery plików z udziałami plików platformy Azure, istniejący użytkownicy mogą uzyskiwać dostęp do udziałów plików platformy Azure od ich bieżących klientów przy użyciu funkcji logowania jednokrotnego, bez wprowadzania żadnych zmian w poświadczeniach w użyciu.  
 
## <a name="prerequisites"></a>Wymagania wstępne 

Przed włączeniem uwierzytelniania usługi AD dla udziałów plików platformy Azure upewnij się, że zostały spełnione następujące wymagania wstępne: 

- Wybierz lub Utwórz środowisko usługi AD i zsynchronizuj go z usługą Azure AD. 

    Tę funkcję można włączyć w nowym lub istniejącym środowisku usługi AD. Tożsamości używane na potrzeby dostępu muszą być synchronizowane z usługą Azure AD. Dzierżawa usługi Azure AD i udział plików, do których uzyskujesz dostęp, muszą być skojarzone z tą samą subskrypcją. 

    Aby skonfigurować środowisko domeny usługi AD, zapoznaj się z [omówieniem Active Directory Domain Services](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Jeśli nie zsynchronizujesz usługi AD z usługą Azure AD, postępuj zgodnie ze wskazówkami zawartymi w artykule [co to jest tożsamość hybrydowa z Azure Active Directory?](../../active-directory/hybrid/whatis-hybrid-identity.md) , aby określić preferowaną metodę uwierzytelniania i Azure AD Connect opcji konfiguracji. 

- Przyłączanie do domeny komputera lokalnego lub maszyny wirtualnej platformy Azure do usługi AD (nazywanej również AD DS). 

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi AD z komputera lub maszyny wirtualnej, urządzenie musi zostać przyłączone do domeny do usługi AD. Informacje o sposobach przyłączania do usługi AD można znaleźć w [przyłączeniu komputera do domeny](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Wybierz lub Utwórz konto usługi Azure Storage w [obsługiwanym regionie](#regional-availability). 

    Upewnij się, że konto magazynu zawierające Twoje udziały plików nie zostało już skonfigurowane do uwierzytelniania za pomocą usługi Azure AD DS. Jeśli na koncie magazynu jest włączone uwierzytelnianie Azure Files Azure AD DS, należy je wyłączyć przed zmianą usługi AD. Oznacza to, że istniejące listy ACL skonfigurowane w środowisku usługi Azure AD DS należy ponownie skonfigurować do prawidłowego wymuszania uprawnień.
    
    Aby uzyskać informacje na temat tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w Azure Files](storage-how-to-create-file-share.md).
    
    W celu uzyskania optymalnej wydajności zalecamy wdrożenie konta magazynu w tym samym regionie, w którym znajduje się maszyna wirtualna, z której ma być uzyskiwany dostęp do udziału. 

- Sprawdź łączność, instalując udziały plików platformy Azure przy użyciu klucza konta magazynu. 

    Aby sprawdzić, czy Twoje urządzenie i udział plików są prawidłowo skonfigurowane, spróbuj zainstalować udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [Korzystanie z udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Dostępność regionalna

Azure Files uwierzytelnianie usługi AD jest dostępne w [większości regionów publicznych](https://azure.microsoft.com/global-infrastructure/regions/), podzbiór regionów, w których nie jest jeszcze dostępny, to:

- Zachodnie stany USA
- Zachodnie stany USA 2
- Wschodnie stany USA
- Wschodnie stany USA 2
- Europa Zachodnia
- Europa Północna

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Przed włączeniem uwierzytelniania usługi AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure zaleca się zapoznanie się z [wymaganiami wstępnymi](#prerequisites) i upewnienie się, że wykonano wszystkie kroki. Wymagania wstępne sprawdzają, czy Twoje środowiska usługi AD, usługi Azure AD i usługi Azure Storage są prawidłowo skonfigurowane. 

Następnie Udziel dostępu do zasobów Azure Files przy użyciu poświadczeń usługi AD: 

- Włącz Azure Files uwierzytelnianie usługi AD na koncie magazynu.  

- Przypisywanie uprawnień dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi), która jest zsynchronizowana z docelową tożsamością usługi AD. 

- Skonfiguruj listy ACL za pośrednictwem protokołu SMB dla katalogów i plików. 

- Zainstaluj udział plików platformy Azure z dołączonej do domeny usługi Active Directory. 

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy służący do włączania uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure. 

![Diagram przepływu pracy usługi AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Uwierzytelnianie usługi AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure jest obsługiwane tylko na maszynach lub maszynach wirtualnych działających w systemach operacyjnych starszych niż Windows 7 lub Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Włącz uwierzytelnianie usługi AD dla konta 

Aby włączyć uwierzytelnianie usługi AD za pośrednictwem protokołu SMB dla udziałów plików platformy Azure, należy najpierw zarejestrować konto magazynu w usłudze AD, a następnie ustawić wymagane właściwości domeny na koncie magazynu. Gdy ta funkcja jest włączona na koncie magazynu, ma zastosowanie do wszystkich nowych i istniejących udziałów plików na koncie. Użyj `join-AzStorageAccountForAuth`, aby włączyć tę funkcję. Szczegółowy opis przepływu pracy typu end-to-end można znaleźć w sekcji poniżej. 

> [!IMPORTANT]
> `join-AzStorageAccountForAuth` polecenie cmdlet wprowadzi modyfikacje w środowisku usługi AD. Przeczytaj poniższe wyjaśnienie, aby lepiej zrozumieć, co robi, aby upewnić się, że masz odpowiednie uprawnienia do wykonywania polecenia i czy zastosowane zmiany są dostosowane do zasad zgodności i zabezpieczeń. 

`join-AzStorageAccountForAuth` polecenie cmdlet przeprowadzi odpowiednik przyłączania do domeny w trybie offline w imieniu wskazanego konta magazynu. Spowoduje to utworzenie konta w domenie usługi AD, [konta komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) lub [konta logowania do usługi](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts). Utworzone konto usługi AD reprezentuje konto magazynu w domenie usługi AD. Jeśli konto usługi AD jest tworzone w ramach jednostki organizacyjnej (OU) usługi AD, która wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta usługi AD spowoduje błędy uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby dowiedzieć się, jak zaktualizować hasło, zobacz [Aktualizowanie hasła do konta usługi AD](#update-ad-account-password).

Za pomocą następującego skryptu można przeprowadzić rejestrację i włączyć funkcję lub, Alternatywnie można wykonać operacje wykonywane ręcznie przez skrypt. Te operacje są opisane w sekcji po skrypcie. Nie musisz wykonywać obu tych czynności.

### <a name="1-check-prerequisites"></a>1. Sprawdź wymagania wstępne
- [Pobierz i rozpakuj moduł AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Zainstaluj i uruchom moduł w urządzeniu przyłączonym do usługi AD przy użyciu poświadczeń usługi AD, które mają uprawnienia do tworzenia konta logowania do usługi lub konta komputera w docelowej usłudze AD.
-  Uruchom skrypt przy użyciu poświadczeń usługi AD, które są synchronizowane z usługą Azure AD. Poświadczenia usługi AD muszą mieć uprawnienia właściciela konta magazynu lub roli RBAC współautora.
- Upewnij się, że konto magazynu jest w [obsługiwanym regionie](#regional-availability).

### <a name="2-execute-ad-enablement-script"></a>2. Wykonaj skrypt włączania usługi AD
Pamiętaj, aby zastąpić wartości symboli zastępczych własnymi parametrami, przed wykonaniem polecenia w programie PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope Currentuser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

#Register the target storage account with your active directory environment under the target OU
join-AzStorageAccountForAuth -ResourceGroupName "<resource-group-name-here>" -Name "<storage-account-name-here>" -DomainAccountType "<ServiceLogonAccount|ComputerAccount>" -OrganizationUnitName "<ou-name-here>"
```

Poniższy opis podsumowuje wszystkie akcje wykonywane po wykonaniu polecenia cmdlet `join-AzStorageAccountForAuth`. Te kroki można wykonać ręcznie, jeśli wolisz nie używać polecenia:

> [!NOTE]
> Jeśli skrypt Join-AzStorageAccountForAuth został już wykonany powyżej, przejdź do następnej sekcji "3. Upewnij się, że funkcja jest włączona. Nie trzeba ponownie wykonywać operacji poniżej.

#### <a name="a-checking-environment"></a>a. Sprawdzanie środowiska

Po pierwsze sprawdza środowisko. W celu sprawdzenia, czy [Active Directory PowerShell](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) jest zainstalowana i czy powłoka jest wykonywana z uprawnieniami administratora. Następnie sprawdza, czy jest zainstalowany [moduł AZ. Storage 1.11.1-Preview](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) , i instaluje go, jeśli nie jest. Jeśli te testy zakończyły się powodzeniem, sprawdzi, czy w usłudze AD znajduje się [konto komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślne) lub [konto logowania do usługi](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) , które zostało już utworzone przy użyciu nazwy SPN/UPN jako "CIFS/konto-magazynu-nazwa-tutaj. plik. Core. Windows. NET". Jeśli konto nie istnieje, zostanie utworzone zgodnie z opisem w sekcji b poniżej.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ręczne tworzenie tożsamości reprezentującej konto magazynu w usłudze AD

Aby ręcznie utworzyć to konto, Utwórz nowy klucz Kerberos dla konta magazynu przy użyciu `New-AzStorageAccountKey -KeyName kerb1`. Następnie użyj tego klucza Kerberos jako hasła do konta. Ten klucz jest używany tylko podczas konfiguracji i nie może być używany dla operacji kontroli ani płaszczyzny danych na koncie magazynu.

Po utworzeniu tego klucza Utwórz konto usługi lub komputera w jednostce organizacyjnej. Użyj następującej specyfikacji: SPN: "CIFS/Storage-account-name-tutaj. File. Core. Windows. NET" Password: klucz Kerberos dla konta magazynu.

Jeśli jednostka organizacyjna wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym okresem ważności hasła, aby zapobiec błędom uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby uzyskać szczegółowe informacje, zobacz temat [Aktualizowanie hasła konta usługi AD](#update-ad-account-password) .

Zachowaj identyfikator SID nowo utworzonego konta, który będzie potrzebny do następnego kroku.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>c. Włącz funkcję na koncie magazynu

Następnie skrypt włączy tę funkcję na koncie magazynu. Aby ręcznie wykonać tę konfigurację, podaj szczegóły konfiguracji właściwości domeny w poniższym poleceniu, a następnie uruchom je. Identyfikator SID konta magazynu wymagany w poniższym poleceniu jest identyfikatorem SID tożsamości utworzonej w usłudze AD (sekcja b powyżej).

```PowerShell
#Set the feature flag on the target storage account and provide the required AD domain information

Set-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>" -EnableActiveDirectoryDomainServiesForFile $true -ActiveDirectoryDomainName "<your-domain-name-here>" -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" -ActiveDirectoryForestName "<your-forest-name-here>" -ActiveDirectoryDomainGuid "<your-guid-here>" -ActiveDirectoryDomainsid "<your-domain-sid-here>" -ActiveDirectoryAzureStirageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Upewnij się, że funkcja jest włączona

Możesz sprawdzić, czy funkcja jest włączona na koncie magazynu, można użyć następującego skryptu:

```PowerShell
#Get the target storage account
$storageaccount = Get-AzStorageAccount -ResourceGroupName "<your-resource-group-name-here>" -Name "<your-storage-account-name-here>"

#List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

#List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Pomyślnie włączono funkcję na koncie magazynu. Mimo że funkcja jest włączona, nadal trzeba wykonać dodatkowe czynności, aby móc prawidłowo korzystać z funkcji.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Pomyślnie włączono uwierzytelnianie usługi AD za pośrednictwem protokołu SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure za pomocą tożsamości usługi AD. Aby udzielić dodatkowych użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami podanymi w sekcji [przypisywanie uprawnień dostępu](#assign-access-permissions-to-an-identity) do używania tożsamości i [konfigurowania uprawnień NTFS dla protokołu SMB](#configure-ntfs-permissions-over-smb) .

## <a name="update-ad-account-password"></a>Aktualizowanie hasła konta usługi AD

Jeśli zarejestrowano tożsamość/konto usługi AD reprezentujące konto magazynu w jednostce organizacyjnej, która wymusza czas wygaśnięcia hasła, należy je obrócić przed maksymalnym okresem ważności hasła. Niepowodzenie aktualizacji hasła konta usługi AD spowoduje niepowodzenia uwierzytelniania w celu uzyskania dostępu do udziałów plików platformy Azure.  

Aby wyzwolić rotację haseł, można uruchomić polecenie `Update-AzStorageAccountADObjectPassword` z modułu AzFilesHybrid. Polecenie cmdlet wykonuje akcje podobne do rotacji kluczy konta magazynu. Pobiera drugi klucz Kerberos konta magazynu i używa go do aktualizowania hasła zarejestrowanego konta w usłudze AD. Następnie ponownie generuje docelowy klucz Kerberos konta magazynu i aktualizuje hasło zarejestrowanego konta w usłudze AD. To polecenie cmdlet należy uruchomić w środowisku przyłączonym do domeny usługi AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword -RotateToKerbKey kerb2 -ResourceGroupName "your-resource-group-name-here" -StorageAccountName "your-storage-account-name-here"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files i sposobu korzystania z usługi AD za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Omówienie usługi Azure Files uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
