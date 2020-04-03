---
title: Włączanie uwierzytelniania usługi Active Directory za pomocą smb dla plików platformy Azure
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem SMB dla udziałów plików platformy Azure za pośrednictwem usługi Active Directory. Maszyny wirtualne systemu Windows przyłączone do domeny mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi AD.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: rogarana
ms.openlocfilehash: 0bf8960f1e97de45d5369f69c698311d0b4e3dbb
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80584516"
---
# <a name="enable-active-directory-authentication-over-smb-for-azure-file-shares"></a>Włączanie uwierzytelniania usługi Active Directory za pomocą smb dla udziałów plików platformy Azure

[Usługa Azure Files](storage-files-introduction.md) obsługuje uwierzytelnianie oparte na tożsamości za pośrednictwem bloku komunikatów serwera (SMB) za pośrednictwem dwóch typów usług domenowych: Usługi domenowe Active Directory Azure (Azure AD DS) (GA) i Active Directory (AD) (wersja zapoznawcza). W tym artykule skupiono się na nowo wprowadzonej (w wersji zapoznawczej) obsłudze wykorzystania usługi domenowej Active Directory do uwierzytelniania w udziałach plików platformy Azure. Jeśli chcesz włączyć uwierzytelnianie usług Azure AD DS (GA) dla udziałów plików platformy Azure, zapoznaj się z [naszym artykułem na ten temat.](storage-files-identity-auth-active-directory-domain-service-enable.md)

> [!NOTE]
> Udziały plików platformy Azure obsługują uwierzytelnianie tylko w jednej usłudze domeny, usłudze domenowej Usługi domeny Usługi Active Directory platformy Azure (Usługi Usługi Azure AD DS) lub Usługi Active Directory (AD). 
>
> Tożsamości usługi AD używane do uwierzytelniania udziału plików platformy Azure muszą być synchronizowane z usługą Azure AD. Synchronizacja skrótów haseł jest opcjonalna. 
> 
> Uwierzytelnianie usługi AD nie obsługuje uwierzytelniania kont komputerów utworzonych w u. 
> 
> Uwierzytelnianie usługi AD może być obsługiwane tylko w jednym lesie usługi AD, w którym jest zarejestrowane konto magazynu. Domyślnie można uzyskać dostęp do udziałów plików platformy Azure tylko przy użyciu poświadczeń usługi AD z jednego lasu usługi AD. Jeśli chcesz uzyskać dostęp do udziału plików platformy Azure z innego lasu, upewnij się, że masz skonfigurowane odpowiednie zaufanie lasu, zobacz [często zadawane pytania, aby](https://docs.microsoft.com/azure/storage/files/storage-files-faq#security-authentication-and-access-control) uzyskać szczegółowe informacje.  
> 
> Uwierzytelnianie usługi AD dla dostępu SMB i trwałości listy ACL jest obsługiwane dla udziałów plików platformy Azure zarządzanych przez usługę Azure File Sync.
>
> Usługa Azure Files obsługuje uwierzytelnianie Kerberos za pomocą usługi AD z szyfrowaniem RC4-HMAC. Szyfrowanie Protokołu Kerberos AES nie jest jeszcze obsługiwane.

Po włączeniu udziałów plików usługi AD for Azure za pośrednictwem przystawki SMB maszyny przyłączone do domeny usługi AD mogą instalować udziały plików platformy Azure przy użyciu istniejących poświadczeń usługi AD. Tę funkcję można włączyć za pomocą środowiska usługi AD hostowanego na komputerach wstępnie zdefiniowanych lub hostowanych na platformie Azure.

Tożsamości usługi AD używane do uzyskiwania dostępu do udziałów plików platformy Azure muszą być synchronizowane z usługą Azure AD, aby wymusić uprawnienia do plików na poziomie udziału za pośrednictwem standardowego modelu [kontroli dostępu opartego na rolach (RBAC).](../../role-based-access-control/overview.md) [Dacl w stylu windows](https://docs.microsoft.com/previous-versions/technet-magazine/cc161041(v=msdn.10)?redirectedfrom=MSDN) na pliki/katalogi przeniesione z istniejących serwerów plików zostaną zachowane i wymuszone. Ta funkcja zapewnia bezproblemową integrację z infrastrukturą domeny ad przedsiębiorstwa. Po zastąpieniu serwerów plików prem udziałami plików platformy Azure, istniejący użytkownicy mogą uzyskiwać dostęp do udziałów plików platformy Azure od swoich bieżących klientów za pomocą środowiska logowania jednokrotnego, bez żadnych zmian w używanych poświadczeniach.  
 
## <a name="prerequisites"></a>Wymagania wstępne 

Przed włączeniem uwierzytelniania usługi AD dla udziałów plików platformy Azure upewnij się, że zostały ukończone następujące wymagania wstępne: 

- Wybierz lub utwórz środowisko usługi AD i zsynchronizuj je z usługą Azure AD. 

    Funkcję można włączyć w nowym lub istniejącym środowisku usługi AD. Tożsamości używane do uzyskiwania dostępu muszą być synchronizowane z usługą Azure AD. Dzierżawy usługi Azure AD i udziału plików, które uzyskujesz dostęp musi być skojarzony z tej samej subskrypcji. 

    Aby skonfigurować środowisko domeny usługi AD, zapoznaj się z [omówieniem Usług domenowych Active Directory](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Jeśli nie zostały zsynchronizowane usługi AD do usługi Azure AD, postępuj zgodnie ze wskazówkami w [co to jest tożsamość hybrydowa z usługi Azure Active Directory w](../../active-directory/hybrid/whatis-hybrid-identity.md) celu określenia preferowanej metody uwierzytelniania i opcji konfiguracji usługi Azure AD Connect. 

- Dołącz do domeny komputera lokalnego lub maszyny wirtualnej platformy Azure do usługi AD (określane również jako USŁUGI AD DS). 

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi AD z komputera lub maszyny Wirtualnej, urządzenie musi być przyłączone do domeny do usługi AD. Aby uzyskać informacje dotyczące dołączania do domeny w u. DO USŁUGI AD, zobacz [Dołączanie komputera do domeny](https://docs.microsoft.com/windows-server/identity/ad-fs/deployment/join-a-computer-to-a-domain). 

- Wybierz lub utwórz konto magazynu platformy Azure w [obsługiwanym regionie](#regional-availability). 

    Upewnij się, że konto magazynu zawierające udziały plików nie jest jeszcze skonfigurowane dla uwierzytelniania usług Azure AD DS. Jeśli usługa Azure Files Usługa AD DS Authentication jest włączona na koncie magazynu, musi zostać wyłączona przed zmianą użycia usługi AD. Oznacza to, że istniejące listy ACL skonfigurowane w środowisku usług Azure AD DS będą musiały zostać ponownie skonfigurowane w celu prawidłowego wymuszania uprawnień.
    
    Aby uzyskać informacje dotyczące tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w usłudze Azure Files](storage-how-to-create-file-share.md).
    
    Aby uzyskać optymalną wydajność, zaleca się wdrożenie konta magazynu w tym samym regionie co maszyna wirtualna, z której planujesz uzyskać dostęp do udziału. 

- Zweryfikuj łączność, montując udziały plików platformy Azure przy użyciu klucza konta magazynu. 

    Aby sprawdzić, czy urządzenie i udział plików są poprawnie skonfigurowane, spróbuj złożyć udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [Używanie udziału plików platformy Azure w systemie Windows](storage-how-to-use-files-windows.md).

## <a name="regional-availability"></a>Dostępność regionalna

Uwierzytelnianie usługi Azure Files AD (wersja zapoznawcza) jest dostępne we [wszystkich regionach chmury publicznej.](https://azure.microsoft.com/global-infrastructure/regions/)

## <a name="workflow-overview"></a>Omówienie przepływu pracy

Przed włączeniem uwierzytelniania usługi AD za pośrednictwem SMB dla [udziałów](#prerequisites) plików platformy Azure, zaleca się przejść przez wymagania wstępne i upewnij się, że wykonano wszystkie kroki. Wymagania wstępne sprawdź, czy środowiska usługi AD, usługi Azure AD i usługi Azure Storage są poprawnie skonfigurowane. 

Następnie udziel dostępu do zasobów usługi Azure Files przy użyciu poświadczeń usługi AD: 

- Włącz uwierzytelnianie usługi Azure Files AD na koncie magazynu.  

- Przypisz uprawnienia dostępu do udziału do tożsamości usługi Azure AD (użytkownika, grupy lub jednostki usługi), która jest zsynchronizowana z docelową tożsamością usługi AD. 

- Konfigurowanie list ACL za kątem protokołu SMB dla katalogów i plików. 

- Instalowanie udziału plików platformy Azure z przyłączanej do domeny usługi AD maszyny Wirtualnej. 

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy umożliwiający włączenie uwierzytelniania usługi Azure AD za pomocą smb dla udziałów plików platformy Azure. 

![Diagram przepływu pracy usługi AD](media/storage-files-active-directory-domain-services-enable/diagram-files-ad.png)

> [!NOTE]
> Uwierzytelnianie usługi AD za pomocą smb dla udziałów plików platformy Azure jest obsługiwane tylko na komputerach lub maszynach wirtualnych uruchomionych w wersjach systemu operacyjnego nowszych niż Windows 7 lub Windows Server 2008 R2. 

## <a name="enable-ad-authentication-for-your-account"></a>Włączanie uwierzytelniania usługi AD dla swojego konta 

Aby włączyć uwierzytelnianie usługi AD za pomocą SMB dla udziałów plików platformy Azure, należy najpierw zarejestrować konto magazynu w usłudze AD, a następnie ustawić wymagane właściwości domeny na koncie magazynu. Gdy funkcja jest włączona na koncie magazynu, ma zastosowanie do wszystkich nowych i istniejących udziałów plików na koncie. Użyj, `join-AzStorageAccountForAuth` aby włączyć operację. Szczegółowy opis kompleksowego przepływu pracy można znaleźć w poniższej sekcji. 

> [!IMPORTANT]
> Polecenie `Join-AzStorageAccountForAuth` cmdlet spowoduje zmiany w środowisku usługi AD. Przeczytaj poniższe wyjaśnienie, aby lepiej zrozumieć, co robi, aby upewnić się, że masz odpowiednie uprawnienia do wykonania polecenia i że zastosowane zmiany są zgodne z zasadami zgodności i zabezpieczeń. 

Polecenie `Join-AzStorageAccountForAuth` cmdlet wykona odpowiednik sprzężenia domeny w trybie offline w imieniu wskazanego konta magazynu. Spowoduje to utworzenie konta w domenie usługi AD, [konta komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) lub konta logowania [do usługi.](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) Utworzone konto usługi AD reprezentuje konto magazynu w domenie usługi AD. Jeśli konto usługi AD jest tworzone w ramach jednostki organizacyjnej usługi AD (OU), która wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym wiekiem hasła. Nie można zaktualizować hasła konta usługi AD spowoduje błędy uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby dowiedzieć się, jak zaktualizować hasło, zobacz [Aktualizowanie hasła konta USŁUGI AD](#update-ad-account-password).

Do wykonania rejestracji i włączenia funkcji można użyć następującego skryptu lub, alternatywnie, można ręcznie wykonać operacje, które skrypt będzie. Te operacje są opisane w sekcji po skrypt. Nie musisz robić obu.

### <a name="1-check-prerequisites"></a>1. Sprawdź wymagania wstępne
- [Pobierz i rozpakowaj moduł AzFilesHybrid](https://github.com/Azure-Samples/azure-files-samples/releases)
- Zainstaluj i wykonaj moduł na urządzeniu, które jest domeną przyłączone do usługi AD z poświadczeniami usługi AD, które mają uprawnienia do tworzenia konta logowania usługi lub konta komputera w docelowej usłudze AD.
-  Uruchom skrypt przy użyciu poświadczeń usługi AD, który jest synchronizowany z usługą Azure AD. Poświadczenie usługi AD musi mieć uprawnienia właściciela konta magazynu lub roli RBAC współautora.
- Upewnij się, że twoje konto magazynu znajduje się w [obsługiwanym regionie](#regional-availability).

### <a name="2-domain-join-your-storage-account"></a>2. Domena dołącz do twojego konta magazynu
Pamiętaj, aby zastąpić wartości zastępcze własnymi w poniższych parametrach przed wykonaniem ich w programie PowerShell.

```PowerShell
#Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

#Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

#Login with an Azure AD credential that has either storage account owner or contributer RBAC assignment
Connect-AzAccount

#Select the target subscription for the current session
Select-AzSubscription -SubscriptionId "<your-subscription-id-here>"

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account, depends on the AD permission you have and preference. 
Join-AzStorageAccountForAuth `
        -ResourceGroupName "<resource-group-name-here>" `
        -Name "<storage-account-name-here>" `
        -DomainAccountType "ComputerAccount" `
        -OrganizationalUnitName "<ou-name-here>"
```

Poniższy opis podsumowuje wszystkie akcje `Join-AzStorageAccountForAuth` wykonywane podczas wykonywania polecenia cmdlet. Możesz wykonać te kroki ręcznie, jeśli nie chcesz używać polecenia:

> [!NOTE]
> Jeśli `Join-AzStorageAccountForAuth` skrypt powyżej został już pomyślnie wykonany, przejdź do następnej sekcji "3. Upewnij się, że funkcja jest włączona". Nie trzeba wykonywać operacje poniżej ponownie.

#### <a name="a-checking-environment"></a>a. Sprawdzanie środowiska

Najpierw sprawdza środowisko. W szczególności sprawdza, czy [program PowerShell usługi Active Directory](https://docs.microsoft.com/powershell/module/addsadministration/?view=win10-ps) jest zainstalowany i czy powłoka jest wykonywana z uprawnieniami administratora. Następnie sprawdza, czy [az.Storage 1.11.1-preview moduł](https://www.powershellgallery.com/packages/Az.Storage/1.11.1-preview) jest zainstalowany i instaluje go, jeśli nie jest. Jeśli te czeki przejdą, sprawdzi twoją usługę AD, czy istnieje [konto komputera](https://docs.microsoft.com/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (domyślne) lub konto logowania [do usługi,](https://docs.microsoft.com/windows/win32/ad/about-service-logon-accounts) które zostało już utworzone za pomocą nazwy SPN/UPN jako "cifs/your-storage-account-name-here.file.core.windows.net". Jeśli konto nie istnieje, utworzy je w sposób opisany w sekcji b poniżej.

#### <a name="b-creating-an-identity-representing-the-storage-account-in-your-ad-manually"></a>b. Ręczne tworzenie tożsamości reprezentującej konto magazynu w ucie ad

Aby utworzyć to konto ręcznie, utwórz nowy klucz `New-AzStorageAccountKey -KeyName kerb1`kerberos dla konta magazynu przy użyciu programu . Następnie użyj tego klucza kerberos jako hasła do konta. Ten klucz jest używany tylko podczas konfigurowania i nie może być używany do żadnych operacji kontroli lub płaszczyzny danych względem konta magazynu.

Po uzyskaniu tego klucza utwórz konto usługi lub komputera w ramach ou. Użyj następującej specyfikacji: SPN: "cifs/your-storage-account-name-here.file.core.windows.net" Hasło: Klucz Protokołu Kerberos dla twojego konta magazynu.

Jeśli twoja firma organizacyjna wymusza wygaśnięcie hasła, należy zaktualizować hasło przed maksymalnym wiekiem hasła, aby zapobiec błędom uwierzytelniania podczas uzyskiwania dostępu do udziałów plików platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [Aktualizowanie hasła konta USŁUGI AD.](#update-ad-account-password)

Zachowaj identyfikator SID nowo utworzonego konta, będziesz go potrzebować do następnego kroku. Tożsamość usługi AD, która została właśnie utworzona, która reprezentuje konto magazynu, nie musi być synchronizowana z usługą Azure AD.

##### <a name="c-enable-the-feature-on-your-storage-account"></a>d. Włączanie funkcji na koncie magazynu

Skrypt włączy tę funkcję na koncie magazynu. Aby wykonać tę konfigurację ręcznie, podaj szczegóły konfiguracji właściwości domeny w następującym poleceniu, a następnie uruchom je. Identyfikator SID konta magazynu wymagany w następującym poleceniu to identyfikator SID tożsamości utworzonej w udziale w udziale AD (sekcja b powyżej).

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```


### <a name="3-confirm-that-the-feature-is-enabled"></a>3. Upewnij się, że funkcja jest włączona

Możesz sprawdzić, czy funkcja jest włączona na koncie magazynu, możesz użyć następującego skryptu:

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

Funkcja została pomyślnie włączona na koncie magazynu. Mimo że funkcja jest włączona, nadal trzeba wykonać dodatkowe akcje, aby móc prawidłowo korzystać z tej funkcji.

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Teraz pomyślnie włączono uwierzytelnianie usługi AD za pomocą SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure z tożsamością usługi AD. Aby udzielić dodatkowym użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami w sekcji [Przypisywanie uprawnień dostępu](#assign-access-permissions-to-an-identity) do używania tożsamości i [konfigurowania uprawnień NTFS za pomocą sekcji SMB.](#configure-ntfs-permissions-over-smb)

## <a name="update-ad-account-password"></a>Aktualizowanie hasła do konta usługi AD

Jeśli użytkownik zarejestrował tożsamość/konto usługi AD reprezentujące konto magazynu w ramach usługi organizacyjnej wymuszaej czas wygaśnięcia hasła, należy obrócić hasło przed osiągnięciem maksymalnego wieku hasła. Nie można zaktualizować hasła konta usługi AD spowoduje błędy uwierzytelniania dostępu do udziałów plików platformy Azure.  

Aby wyzwolić obrót hasła, `Update-AzStorageAccountADObjectPassword` można uruchomić polecenie z modułu AzFilesHybrid. Polecenie cmdlet wykonuje akcje podobne do rotacji klucza konta magazynu. Pobiera drugi klucz Protokołu Kerberos konta magazynu i używa go do aktualizacji hasła zarejestrowanego konta w usłudze AD. Następnie ponownie generuje docelowy klucz Protokołu Kerberos konta magazynu i aktualizuje hasło zarejestrowanego konta w u. To polecenie cmdlet należy uruchomić w środowisku przyłączanym do domeny usługi AD.

```PowerShell
#Update the password of the AD account registered for the storage account
Update-AzStorageAccountADObjectPassword `
        -RotateToKerbKey kerb2 `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -StorageAccountName "<your-storage-account-name-here>"
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usług Azure Files i sposobu korzystania z usługi AD za pomocą SMB, zobacz następujące zasoby:

- [Omówienie obsługi uwierzytelniania opartego na tożsamościach usługi Azure Files dla dostępu do SMB](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
