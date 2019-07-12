---
title: Włącz uwierzytelnianie usługi Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza) — usługi Azure Storage
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamości za pośrednictwem protokołu SMB (Server Message Block) (wersja zapoznawcza) dla usługi Azure Files za pomocą usług domenowych Azure Active Directory (Azure AD). Przyłączone do domeny Windows maszyn wirtualnych (VM) mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/05/2019
ms.author: rogarana
ms.openlocfilehash: cd4c952caa336f2602d3c30e0db3e10ebee59cb9
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67696120"
---
# <a name="enable-azure-active-directory-domain-service-authentication-over-smb-for-azure-files-preview"></a>Włącz uwierzytelnianie usługi Azure Active Directory domeny za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)
[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby uzyskać omówienie uwierzytelniania usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, zobacz [uwierzytelniania Omówienie programu Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Omówienie przepływu pracy
Zanim włączysz uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB dla usługi Azure Files, upewnij się, że usługi Azure AD i środowisk Azure Storage są poprawnie skonfigurowane. Zalecane jest, zapoznaj się z za pośrednictwem [wymagania wstępne](#prerequisites) aby upewnić się, że zostało wykonane wszystkie wymagane kroki. 

Następnie udzielać dostępu do zasobów usługi Azure Files przy użyciu poświadczeń usługi Azure AD, wykonaj następujące czynności: 

1. Włącz uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB dla konta magazynu do rejestrowania konta magazynu przy użyciu powiązanego wdrożenia usług domenowych Azure AD.
2. Przypisz uprawnienia dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub jednostki usługi).
3. Skonfiguruj uprawnienia NTFS za pośrednictwem protokołu SMB plików i katalogów.
4. Instalowanie udziału plików platformy Azure z maszyn wirtualnych przyłączonych do domeny.

Poniższy diagram przedstawia przepływ pracy end-to-end włączania uwierzytelniania usługi Azure AD DS za pośrednictwem protokołu SMB dla usługi Azure Files. 

![Diagram przedstawiający usługi Azure AD przy użyciu protokołu SMB dla przepływu pracy z usługą Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Wymagania wstępne 

Przed włączeniem usługi Azure AD przy użyciu protokołu SMB dla usługi Azure Files, upewnij się, że zostały wykonane następujące wymagania wstępne:

1.  **Wybierz lub Utwórz dzierżawę usługi Azure AD.**

    Nowych i istniejących dzierżawców można użyć uwierzytelniania usługi Azure AD przy użyciu protokołu SMB. Dzierżawy i udział plików, który chcesz uzyskać dostęp do musi być skojarzony z tą samą subskrypcją.

    Aby utworzyć nową dzierżawę usługi Azure AD możesz [Dodawanie dzierżawy usługi Azure AD i subskrypcję usługi Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Jeśli masz istniejącą dzierżawę usługi Azure AD, ale chcesz utworzyć nową dzierżawę do użycia z usługą Azure Files, zobacz [tworzenie dzierżawy usługi Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Włączanie usług domenowych Azure AD dla dzierżawy usługi Azure AD.**

    Aby zapewnić obsługę uwierzytelniania przy użyciu poświadczeń usługi Azure AD, należy włączyć usługi Azure AD Domain Services dla dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie z szczegółowymi wskazówkami dotyczącymi [włączyć usługi Azure Active Directory Domain Services w witrynie Azure portal](../../active-directory-domain-services/create-instance.md).

    Zwykle trwa około 15 minut wdrożenia usług domenowych Azure AD zakończyć. Sprawdź, czy stan kondycji usługi domenowe Azure AD zawiera **systemem**, z włączoną synchronizacją skrótów haseł, przed przejściem do następnego kroku.

3.  **Przyłączanie do domeny maszyny Wirtualnej platformy Azure za pomocą usługi Azure AD Domain Services.**

    Dostępu do udziału plików przy użyciu poświadczeń usługi Azure AD z maszyny Wirtualnej, maszyna wirtualna musi być przyłączony do domeny usług domenowych Azure AD. Aby uzyskać więcej informacji na temat przyłączania do domeny maszyny Wirtualnej, zobacz [dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB za pomocą usługi Azure Files jest obsługiwana tylko na platformie Azure maszyn wirtualnych uruchomionych na wersje systemów operacyjnych Windows 7 lub Windows Server 2008 R2.

4.  **Wybierz lub Utwórz udział plików platformy Azure.**

    Wybierz udział plików nowej lub istniejącej, która jest skojarzona z tej samej subskrypcji co dzierżawy usługi Azure AD. Aby uzyskać informacje dotyczące tworzenia nowego udziału plików, zobacz [Utwórz udział plików w usłudze Azure Files](storage-how-to-create-file-share.md). 
    Aby uzyskać optymalną wydajność firma Microsoft zaleca, że udział plików znajduje się w tym samym regionie co maszyna wirtualna, z którego planujesz uzyskiwać dostęp do udziału.

5.  **Sprawdź łączność z usługą Azure Files przy instalowanie udziałów plików platformy Azure przy użyciu klucza konta magazynu.**

    Aby sprawdzić, czy sieci maszyny Wirtualnej i udział plików są prawidłowo skonfigurowane, spróbuj zainstalowanie udziału plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Włącz uwierzytelnianie usługi Azure AD DS dla swojego konta

Aby włączyć uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB dla usługi Azure Files, można ustawić właściwości dla kont magazynu utworzonych po 24 września 2018 roku przy użyciu witryny Azure portal, programu Azure PowerShell lub wiersza polecenia platformy Azure. Ustawienie tej właściwości rejestruje konta magazynu skojarzone wdrożenie usług domenowych Azure AD. Uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB następnie jest włączona dla wszystkich udziałów plików nowych i istniejących na koncie magazynu. 

Należy pamiętać, że można włączyć uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB tylko wtedy, gdy została pomyślnie wdrożona usług domenowych Azure AD dla Twojej dzierżawy usługi Azure AD. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

### <a name="azure-portal"></a>Azure Portal

Aby włączyć uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB za pomocą [witryny Azure portal](https://portal.azure.com), wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do istniejącego konta magazynu lub [Tworzenie konta magazynu](../common/storage-quickstart-create-account.md).
2. W **ustawienia** zaznacz **konfiguracji**.
3. Włącz **Azure uwierzytelniania usługi Active Directory dla usługi Azure Files (wersja zapoznawcza)** .

Na poniższej ilustracji przedstawiono sposób włączania uwierzytelniania usługi Azure AD przy użyciu protokołu SMB dla konta magazynu.

![Włącz uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB w witrynie Azure portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Aby włączyć uwierzytelnianie usługi Azure AD DS za pośrednictwem protokołu SMB przy użyciu programu Azure PowerShell: Najpierw zainstaluj najnowszy moduł Az (2.4 lub nowszej) lub moduł Az.Storage (w wersji 1.5 lub nowszej). Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Instalowanie programu Azure PowerShell na Windows przy użyciu funkcji PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps):

```powershell
Install-Module -Name Az.Storage -AllowPrerelease -Force -AllowClobber
```

Następnie utwórz nowy magazyn konta, a następnie wywołaj [AzStorageAccount zestaw](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) i ustaw **EnableAzureActiveDirectoryDomainServicesForFile** parametr **true**. W poniższym przykładzie Pamiętaj, aby zastąpić symbole zastępcze własnymi wartościami. (Jeśli była używana poprzedni moduł (wersja zapoznawcza), parametr do włączenia funkcji jest **EnableAzureFilesAadIntegrationForSMB**.)

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```
Aby włączyć tę funkcję na istniejących kont magazynu, użyj następującego polecenia:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB z interfejsu wiersza polecenia platformy Azure w wersji 2.0, należy najpierw zainstalować `storage-preview` rozszerzenia:

```cli-interactive
az extension add --name storage-preview
```
  
Następnie utwórz nowy magazyn konta, a następnie wywołaj [aktualizacja konta magazynu az](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) i ustaw `--file-aad` właściwości **true**. W poniższym przykładzie Pamiętaj, aby zastąpić symbole zastępcze własnymi wartościami.

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --file-aad true
```

## <a name="assign-access-permissions-to-an-identity"></a>Przypisanie uprawnień dostępu do tożsamości usługi 

Aby uzyskać dostęp do zasobów usługi Azure Files przy użyciu poświadczeń usługi Azure AD, tożsamości (użytkownika, grupy lub jednostki usługi) musi mieć odpowiednie uprawnienia na poziomie udziału. Ten proces jest podobny do określania uprawnień udostępniania Windows, gdzie Określ typ dostępu do udziału plików danego użytkownika. Wskazówki zawarte w tej sekcji pokazano, jak przypisać odczytu, zapisu lub usunąć uprawnienia dla udziału plików do tożsamości usługi.

Wprowadziliśmy dwie Azure wbudowane role, do przyznawania uprawnień na poziomie udziału dla użytkowników: Czytnik udziału SMB dla pliku danych, magazynu i współautorem udziału SMB dla magazynu plików. 

- **Czytnik udziału SMB dla pliku danych, magazynu** umożliwia dostęp do odczytu w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB
- **Współautor udziału SMB dla pliku danych, magazynu** umożliwia odczytu, zapisu i usuwania dostępu w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB

> [!IMPORTANT]
> Pełną kontrolę administracyjną nad udział plików, w tym możliwość przypisania roli do tożsamości usługi wymaga przy użyciu klucza konta magazynu. Kontrola administracyjna nie jest obsługiwana przy użyciu poświadczeń usługi Azure AD. 

Można przypisać wbudowane role do tożsamości usługi Azure AD dla użytkownika, przyznawania uprawnień na poziomie udziału, można użyć witryny Azure portal, programu PowerShell lub wiersza polecenia platformy Azure. 

#### <a name="azure-portal"></a>Azure Portal
Aby przypisać rolę RBAC do tożsamości usługi Azure AD przy użyciu [witryny Azure portal](https://portal.azure.com), wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do udziału plików, lub [Utwórz udział plików w usłudze Azure Files](storage-how-to-create-file-share.md).
2. Wybierz **kontrola dostępu (IAM)** .
3. Wybierz **Dodaj przypisanie roli**
4. W **Dodaj przypisanie roli** bloku, wybierz odpowiednią rolę wbudowaną (czytnik udziału SMB dla pliku danych, magazynu, współautor udziału SMB dla pliku danych, magazynu) z **roli** listy rozwijanej. Zachowaj **Przypisz dostęp do** jako domyślny: "Użytkownik usługi azure AD, grupy lub jednostki usługi" i wybierz docelową tożsamości usługi Azure AD według nazwy lub adresu e-mail. 
5. Na koniec wybierz **Zapisz** można ukończyć operacji przypisania roli.

#### <a name="powershell"></a>PowerShell

Następujące polecenie programu PowerShell pokazuje, jak przypisać rolę RBAC do tożsamości usługi Azure AD, w oparciu o nazwę logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC przy użyciu programu PowerShell, zobacz [zarządzanie dostępem przy użyciu RBAC i programu Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Podczas uruchamiania następującego skryptu przykładowego, pamiętaj, aby zastąpić wartości symboli zastępczych, razem z nawiasami własnymi wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interfejs wiersza polecenia
  
Następujące polecenie interfejsu wiersza polecenia 2.0 pokazuje, jak przypisać rolę RBAC do tożsamości usługi Azure AD, w oparciu o nazwę logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC przy użyciu wiersza polecenia platformy Azure, zobacz [zarządzanie dostępem przy użyciu RBAC i wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

Podczas uruchamiania następującego skryptu przykładowego, pamiętaj, aby zastąpić wartości symboli zastępczych, razem z nawiasami własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshare/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Skonfiguruj uprawnienia NTFS za pośrednictwem protokołu SMB 
Po przypisaniu uprawnienia na poziomie udziału za pomocą funkcji RBAC, należy przypisać odpowiednie uprawnienia systemu plików NTFS w poziomie głównym, katalogu lub pliku. Pomyśl o uprawnienia na poziomie udziału, jak strażnik wysokiego poziomu, która określa, czy użytkownik może uzyskiwać dostęp udziału, a uprawnień NTFS działania na bardziej szczegółowym poziomie, aby określić, jakie operacje użytkownik może wykonywać na poziomie pliku lub katalogu. 

Usługa pliki systemu Azure obsługuje pełny zestaw podstawowych i zaawansowanych uprawnienia systemu plików NTFS. Można wyświetlać i konfigurować uprawnienia systemu plików NTFS dla katalogów i plików w udziale plików platformy Azure przez zainstalowanie udziału, a następnie uruchamiając Windows [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) lub [listy ACL zestaw](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) polecenia. 

> [!NOTE]
> Wersja zapoznawcza obsługuje wyświetlanie za pomocą Eksploratora plików Windows tylko uprawnienia. Edytowanie uprawnień nie jest jeszcze obsługiwane.

Aby skonfigurować uprawnienia systemu plików NTFS z uprawnieniami administratora, musi zainstalowania udziału za pomocą klucza konta magazynu z maszyny Wirtualnej z przyłączonym do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z poziomu wiersza polecenia i odpowiednio skonfigurować uprawnienia systemu plików NTFS.

Następujące zestawy uprawnień są obsługiwane w katalogu głównym udziału plików:

- BUILTIN\Administrators:(oi)(ci)(F)
- NT AUTHORITY\SYSTEM:(OI)(CI)(F)
- BUILTIN\Users:(RX)
- BUILTIN\Users:(oi)(ci)(IO)(GR,GE)
- Users:(OI)(CI)(M) grupę Zarządzanie NT\Użytkownicy NT
- NT AUTHORITY\SYSTEM:(F)
- TWÓRCA OWNER:(OI)(CI)(IO)(F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Instalowanie udziału plików z poziomu wiersza polecenia

Użyj Windows **net użyj** polecenie, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić symbole zastępcze w przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat Instalowanie udziałów plików, zobacz [instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```

### <a name="configure-ntfs-permissions-with-icacls"></a>Skonfiguruj uprawnienia NTFS z icacls
Użyj następującego polecenia Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego. Pamiętaj, aby zastąpić symbole zastępcze w nawiasie w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat korzystania icacls, aby ustawić uprawnienia systemu plików NTFS i na różnych typów uprawnień obsługiwane, zobacz [wiersza polecenia dla icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Instalowanie udziału plików z maszyny Wirtualnej przyłączone do domeny 

Teraz jesteś gotowy zweryfikować, że powyższe kroki została ukończona pomyślnie za pomocą swoje poświadczenia usługi Azure AD, aby uzyskać dostęp do pliku platformy Azure będzie udostępniać z przyłączonym do domeny maszyny Wirtualnej. Najpierw zaloguj się do maszyny Wirtualnej przy użyciu tożsamości usługi Azure AD, do którego mają przyznane uprawnienia, jak pokazano na poniższej ilustracji.

![Zrzut ekranu przedstawiający usługi Azure AD ekran logowania do uwierzytelniania użytkownika](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Następnie użyj następującego polecenia, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić symbole zastępcze własnymi wartościami. Ponieważ należy już został uwierzytelniony, nie należy podać klucz konta magazynu lub usługi Azure AD, nazwę użytkownika i hasło. Usługa Azure AD przy użyciu protokołu SMB obsługuje logowanie jednokrotne przy użyciu poświadczeń usługi Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Teraz pomyślnie włączone uwierzytelnianie usługi Azure AD przy użyciu protokołu SMB i przypisać rolę niestandardową, która zapewnia dostęp do udziału plików do tożsamości usługi Azure AD. Aby udzielić dostępu do udziału plików do dodatkowych użytkowników, postępuj zgodnie z instrukcjami w kroku 2 i 3.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Files i używanie programu Azure AD przy użyciu protokołu SMB zobacz następujące zasoby:

- [Wprowadzenie do usługi Azure Files](storage-files-introduction.md)
- [Omówienie uwierzytelniania usługi Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files (wersja zapoznawcza)](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
