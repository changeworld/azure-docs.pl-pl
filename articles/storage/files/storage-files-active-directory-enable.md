---
title: Użyj Azure AD Domain Services, aby autoryzować dostęp do danych plików za pośrednictwem protokołu SMB
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamościach za pośrednictwem bloku komunikatów serwera (SMB) dla Azure Files za pośrednictwem Azure Active Directory Domain Services. Przyłączone do domeny maszyny wirtualne z systemem Windows mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fd42a6ffa6ea46d49df673cde617c70ce7425d91
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460377"
---
# <a name="enable-azure-active-directory-domain-services-authentication-over-smb-for-azure-files"></a>Włącz uwierzytelnianie Azure Active Directory Domain Services za pośrednictwem protokołu SMB dla Azure Files

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Omówienie uwierzytelniania usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files można znaleźć w temacie [Omówienie uwierzytelniania Azure Active Directory za pośrednictwem protokołu SMB dla Azure Files](storage-files-active-directory-overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview-of-the-workflow"></a>Przegląd przepływu pracy

Przed włączeniem uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla Azure Files upewnij się, że środowiska usługi Azure AD i Azure Storage zostały prawidłowo skonfigurowane. Zalecamy zapoznanie się z wymaganiami [wstępnymi](#prerequisites) , aby upewnić się, że zostały wykonane wszystkie wymagane kroki.

Następnie Udziel dostępu do zasobów Azure Files przy użyciu poświadczeń usługi Azure AD, wykonując następujące czynności: 

1. Włącz uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB dla konta magazynu, aby zarejestrować konto magazynu przy użyciu skojarzonego wdrożenia usługi Azure AD DS.
2. Przypisywanie uprawnień dostępu dla udziału do tożsamości usługi Azure AD (użytkownika, grupy lub nazwy głównej usługi).
3. Skonfiguruj uprawnienia systemu plików NTFS za pośrednictwem protokołu SMB dla katalogów i plików.
4. Zainstaluj udział plików platformy Azure z maszyny wirtualnej przyłączonej do domeny.

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy służący do włączania uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla Azure Files.

![Diagram przedstawiający przepływ pracy usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed włączeniem usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files upewnij się, że zostały spełnione następujące wymagania wstępne:

1.  **Wybierz lub Utwórz dzierżawę usługi Azure AD.**

    Możesz użyć nowej lub istniejącej dzierżawy do uwierzytelniania w usłudze Azure AD za pośrednictwem protokołu SMB. Dzierżawa i udział plików, do których chcesz uzyskać dostęp, muszą być skojarzone z tą samą subskrypcją.

    Aby utworzyć nową dzierżawę usługi Azure AD, możesz [dodać dzierżawę usługi Azure AD i subskrypcję usługi Azure AD](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription). Jeśli masz istniejącą dzierżawę usługi Azure AD, ale chcesz utworzyć nową dzierżawę do użycia z Azure Files, zobacz [Tworzenie dzierżawy Azure Active Directory](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant).

2.  **Włącz Azure AD Domain Services w dzierżawie usługi Azure AD.**

    Aby zapewnić obsługę uwierzytelniania przy użyciu poświadczeń usługi Azure AD, musisz włączyć Azure AD Domain Services dla dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [włączyć Azure Active Directory Domain Services przy użyciu Azure Portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Ukończenie wdrożenia usługi Azure AD DS zwykle trwa około 15 minut. Przed przejściem do następnego kroku Sprawdź, czy stan kondycji usługi Azure AD DS jest **uruchomiony**z włączoną funkcją synchronizacji skrótów haseł.

3.  **Przyłączanie domeny do maszyny wirtualnej platformy Azure przy użyciu usługi Azure AD DS.**

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi Azure AD z maszyny wirtualnej, maszyna wirtualna musi być przyłączona do domeny do usługi Azure AD DS. Aby uzyskać więcej informacji na temat dołączania do maszyny wirtualnej, zobacz [dołączanie maszyny wirtualnej z systemem Windows Server do domeny zarządzanej](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB z usługą Azure Files jest obsługiwane tylko na maszynach wirtualnych platformy Azure działających w systemach operacyjnych starszych niż Windows 7 lub Windows Server 2008 R2.

4.  **Wybierz lub Utwórz udział plików platformy Azure.**

    Wybierz nowy lub istniejący udział plików skojarzony z tą samą subskrypcją co dzierżawy usługi Azure AD. Aby uzyskać informacje na temat tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w Azure Files](storage-how-to-create-file-share.md). 
    W celu uzyskania optymalnej wydajności zalecamy, aby udział plików znajdował się w tym samym regionie co maszyna wirtualna, z której planujesz uzyskać dostęp do udziału.

5.  **Sprawdź łączność Azure Files, instalując udziały plików platformy Azure przy użyciu klucza konta magazynu.**

    Aby sprawdzić, czy maszyna wirtualna i udział plików są prawidłowo skonfigurowane, spróbuj zainstalować udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows](storage-how-to-use-files-windows.md).

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Włącz uwierzytelnianie AD DS platformy Azure dla Twojego konta

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB dla Azure Files, można ustawić właściwość kont magazynu utworzonych po 24 września 2018 przy użyciu Azure Portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Ustawienie tej właściwości rejestruje konto magazynu ze skojarzonym wdrożeniem usługi Azure AD DS. Uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB jest następnie włączone dla wszystkich nowych i istniejących udziałów plików na koncie magazynu.

Pamiętaj, że możesz włączyć uwierzytelnianie na platformie Azure AD DS za pośrednictwem protokołu SMB dopiero po pomyślnym wdrożeniu usługi Azure AD DS w dzierżawie usługi Azure AD. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

### <a name="azure-portal"></a>Portal Azure

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB przy użyciu [Azure Portal](https://portal.azure.com), wykonaj następujące czynności:

1. W Azure Portal przejdź do istniejącego konta magazynu lub [Utwórz konto magazynu](../common/storage-quickstart-create-account.md).
2. W sekcji **Ustawienia** wybierz pozycję **Konfiguracja**.
3. Wybierz pozycję **Azure Active Directory Domain Services (Azure AD DS)** z listy rozwijanej **usługi katalogowej opartej na tożsamościach na potrzeby uwierzytelniania plików platformy Azure** .

Na poniższej ilustracji przedstawiono sposób włączania uwierzytelniania AD DS platformy Azure za pośrednictwem protokołu SMB dla konta magazynu.

![Włącz uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB w Azure Portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)
  
### <a name="powershell"></a>PowerShell  

Aby włączyć uwierzytelnianie AD DS platformy Azure za pośrednictwem protokołu SMB z Azure PowerShell, zainstaluj najnowszą wersję AZ module (2,4 lub nowszą) lub moduł AZ. Storage (1,5 lub nowszy). Aby uzyskać więcej informacji na temat instalowania programu PowerShell, zobacz [Install Azure PowerShell in Windows with PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Aby utworzyć nowe konto magazynu, wywołaj polecenie [New-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), a następnie ustaw dla parametru EnableAzureActiveDirectoryDomainServicesForFile **wartość true**. W poniższym przykładzie Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. (Jeśli używasz poprzedniego modułu w wersji zapoznawczej, parametr do włączenia funkcji jest **EnableAzureFilesAadIntegrationForSMB**).

```powershell
# Create a new storage account
New-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -Location "<azure-region>" `
    -SkuName Standard_LRS `
    -Kind StorageV2 `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```

Aby włączyć tę funkcję na istniejących kontach magazynu, użyj następującego polecenia:

```powershell
# Update a storage account
Set-AzStorageAccount -ResourceGroupName "<resource-group-name>" `
    -Name "<storage-account-name>" `
    -EnableAzureActiveDirectoryDomainServicesForFile $true
```


### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Aby włączyć uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB przy użyciu interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję interfejsu wiersza polecenia (w wersji 2.0.70 lub nowszej). Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby utworzyć nowe konto magazynu, wywołaj polecenie[AZ Storage account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)i ustaw właściwość `--enable-files-aadds` na **wartość true**. W poniższym przykładzie Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. (Jeśli korzystasz z poprzedniego modułu w wersji zapoznawczej, parametr do włączenia funkcji to **File-AAD**).

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Aby włączyć tę funkcję na istniejących kontach magazynu, użyj następującego polecenia:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```


## <a name="assign-access-permissions-to-an-identity"></a>Przypisywanie uprawnień dostępu do tożsamości

Aby uzyskać dostęp do zasobów Azure Files przy użyciu poświadczeń usługi Azure AD, tożsamość (nazwa użytkownika, Grupa lub nazwa główna usługi) musi mieć odpowiednie uprawnienia na poziomie udziału. Ten proces jest podobny do określania uprawnień do udziału w systemie Windows, w którym można określić typ dostępu określonego użytkownika do udziału plików. Wskazówki zawarte w tej sekcji przedstawiają sposób przypisywania uprawnień do odczytu, zapisu lub usuwania udziału plików do tożsamości.

Wprowadziliśmy dwie wbudowane role platformy Azure na potrzeby udzielania użytkownikom uprawnień na poziomie udziału:

- **Czytnik udziałów plików dla plików magazynu (SMB** ) umożliwia dostęp do odczytu w udziałach pliku usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautor udostępniania danych plików magazynu SMB** umożliwia dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.
- **Współautorzy udziałów SMB danych plików magazynu** umożliwia odczyt, zapis, usuwanie i modyfikowanie uprawnień NTFS w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB.

> [!IMPORTANT]
> Pełna kontrola administracyjna udziału plików, w tym możliwość przypisania roli do tożsamości, wymaga użycia klucza konta magazynu. Kontrolka administracyjna nie jest obsługiwana w przypadku poświadczeń usługi Azure AD.

Korzystając z Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, można przypisać wbudowane role do tożsamości usługi Azure AD użytkownika w celu udzielenia uprawnień na poziomie udziału.

#### <a name="azure-portal"></a>Portal Azure
Aby przypisać rolę RBAC do tożsamości usługi Azure AD przy użyciu [Azure Portal](https://portal.azure.com), wykonaj następujące czynności:

1. W Azure Portal przejdź do udziału plików lub [Utwórz udział plików w Azure Files](storage-how-to-create-file-share.md).
2. Wybierz **kontrola dostępu (IAM)** .
3. Wybierz pozycję **Dodaj przypisanie roli**
4. W bloku **Dodaj przypisanie roli** wybierz odpowiednią rolę wbudowaną (plik magazynu: czytnik udziałów SMB, współautor udziału danych plików magazynu) z listy **rola** . Pozostaw ustawienie domyślne opcji **Przypisz dostęp do** : **użytkownik, Grupa lub nazwa główna usługi Azure AD**. Wybierz docelową tożsamość usługi Azure AD według nazwy lub adresu e-mail.
5. Wybierz pozycję **Zapisz** , aby ukończyć operację przypisywania roli.

#### <a name="powershell"></a>PowerShell

Następujące polecenie programu PowerShell pokazuje, jak przypisać rolę RBAC do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC przy użyciu programu PowerShell, zobacz [Zarządzanie dostępem przy użyciu RBAC i Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Przed uruchomieniem następującego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasów, z własnymi wartościami.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

#### <a name="cli"></a>Interfejs CLI
  
Następujące polecenie interfejsu wiersza polecenia 2,0 pokazuje, jak przypisać rolę RBAC do tożsamości usługi Azure AD na podstawie nazwy logowania. Aby uzyskać więcej informacji na temat przypisywania ról RBAC przy użyciu interfejsu wiersza polecenia platformy Azure, zobacz [Zarządzanie dostępem przy użyciu RBAC i interfejsu wiersza polecenia platformy Azure](../../role-based-access-control/role-assignments-cli.md). 

Przed uruchomieniem następującego przykładowego skryptu pamiętaj, aby zastąpić wartości symboli zastępczych, w tym nawiasów, z własnymi wartościami.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```

## <a name="configure-ntfs-permissions-over-smb"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu protokołu SMB 
Po przypisaniu uprawnień na poziomie udziałów przy użyciu RBAC należy przypisać odpowiednie uprawnienia systemu plików NTFS do poziomu głównego, katalogu lub pliku. Należy traktować uprawnienia na poziomie udziałów jako strażnika wysokiego poziomu, który określa, czy użytkownik może uzyskać dostęp do udziału. Uprawnienia NTFS działają na bardziej szczegółowym poziomie, aby określić, jakie operacje może wykonać użytkownik na poziomie katalogu lub pliku.

Azure Files obsługuje pełny zestaw uprawnień systemu plików NTFS podstawowych i zaawansowanych. Uprawnienia NTFS można wyświetlać i konfigurować dla katalogów i plików w udziale plików platformy Azure, instalując udział, a następnie używając Eksploratora plików systemu Windows lub uruchamiając polecenie [icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls) lub [Set-ACL](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-acl) . 

Aby skonfigurować system plików NTFS z uprawnieniami administratora, należy zainstalować udział przy użyciu klucza konta magazynu z maszyny wirtualnej przyłączonej do domeny. Postępuj zgodnie z instrukcjami w następnej sekcji, aby zainstalować udział plików platformy Azure z wiersza polecenia i odpowiednio skonfigurować uprawnienia systemu plików NTFS.

W katalogu głównym udziału plików są obsługiwane następujące zestawy uprawnień:

- Builtin\administratorzy: (OI) (CI) (F)
- NT NT\SYSTEM: (OI) (CI) (CI) (F)
- BUILTIN\Users: (RX)
- BUILTIN\Users: (OI) (CI) (we/wy) (GR, GE)
- Użytkownicy NT AUTHORITY\Authenticated: (OI) (CI) (M)
- ZARZĄDZANIE NT\SYSTEM: (F)
- TWÓRCA-WŁAŚCICIEL: (OI) (WE/WY) (IO) (F)

### <a name="mount-a-file-share-from-the-command-prompt"></a>Instalowanie udziału plików z wiersza polecenia

Użyj polecenia Windows **net use** , aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości symboli zastępczych w poniższym przykładzie własnymi wartościami. Aby uzyskać więcej informacji na temat instalowania udziałów plików, zobacz [Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows](storage-how-to-use-files-windows.md).

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name> <storage-account-key> /user:Azure\<storage-account-name>
```
### <a name="configure-ntfs-permissions-with-windows-file-explorer"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu Eksploratora plików systemu Windows
Użyj Eksploratora plików systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego.

1. Otwórz Eksploratora plików systemu Windows i kliknij prawym przyciskiem myszy plik/katalog i wybierz polecenie **Właściwości**
2. Kliknij kartę **zabezpieczenia**
3. Kliknij pozycję **Edytuj..** . przycisk zmiany uprawnień
4. Możesz zmienić uprawnienia istniejących użytkowników lub kliknąć przycisk **Dodaj...** , aby przyznać uprawnienia nowym użytkownikom
5. W oknie monitu do dodawania nowych użytkowników wprowadź nazwę użytkownika docelowego, do którego chcesz udzielić uprawnień w polu **Wprowadź nazwy obiektów do wybrania** , a następnie kliknij przycisk **Sprawdź nazwy** , aby znaleźć pełną nazwę UPN użytkownika docelowego.
7.  Kliknij przycisk **OK** .
8.  Na karcie Zabezpieczenia wybierz pozycję wszystkie uprawnienia, które chcesz udzielić nowo dodawanemu użytkownikowi.
9.  Kliknij przycisk **Zastosuj**

### <a name="configure-ntfs-permissions-with-icacls"></a>Konfigurowanie uprawnień systemu plików NTFS przy użyciu icacls
Użyj następującego polecenia systemu Windows, aby przyznać pełne uprawnienia do wszystkich katalogów i plików w udziale plików, w tym katalogu głównego. Pamiętaj, aby zastąpić wartości symboli zastępczych w przykładzie własnymi wartościami.

```
icacls <mounted-drive-letter>: /grant <user-email>:(f)
```

Aby uzyskać więcej informacji na temat używania icacls do ustawiania uprawnień systemu plików NTFS i różnych typów obsługiwanych uprawnień, zobacz [informacje dotyczące wiersza polecenia dla icacls](https://docs.microsoft.com/windows-server/administration/windows-commands/icacls).

## <a name="mount-a-file-share-from-a-domain-joined-vm"></a>Instalowanie udziału plików z maszyny wirtualnej przyłączonej do domeny

Następujący proces sprawdza, czy poświadczenia usługi Azure AD zostały prawidłowo skonfigurowane i czy można uzyskać dostęp do udziału plików platformy Azure z maszyny wirtualnej przyłączonej do domeny: 

Zaloguj się do maszyny wirtualnej przy użyciu tożsamości usługi Azure AD, do której udzielono uprawnień, jak pokazano na poniższej ilustracji.

![Zrzut ekranu przedstawiający ekran logowania do usługi Azure AD na potrzeby uwierzytelniania użytkowników](media/storage-files-active-directory-enable/azure-active-directory-authentication-dialog.png)

Użyj poniższego polecenia, aby zainstalować udział plików platformy Azure. Pamiętaj, aby zastąpić wartości zastępcze własnymi wartościami. Ponieważ użytkownik został już uwierzytelniony, nie trzeba podawać klucza konta magazynu ani nazwy użytkownika i hasła usługi Azure AD. Usługa Azure AD za pośrednictwem protokołu SMB obsługuje logowanie jednokrotne przy użyciu poświadczeń usługi Azure AD.

```
net use <desired-drive-letter>: \\<storage-account-name>.file.core.windows.net\<share-name>
```

Pomyślnie włączono uwierzytelnianie usługi Azure AD za pośrednictwem protokołu SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure za pomocą tożsamości usługi Azure AD. Aby udzielić dodatkowych użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami podanymi w sekcji [przypisywanie uprawnień dostępu do tożsamości](#assign-access-permissions-to-an-identity) i [Konfigurowanie uprawnień NTFS za pośrednictwem protokołu SMB](#configure-ntfs-permissions-over-smb) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Files i sposobu korzystania z usługi Azure AD za pośrednictwem protokołu SMB, zobacz następujące zasoby:

- [Wprowadzenie do Azure Files](storage-files-introduction.md)
- [Omówienie uwierzytelniania usługi Azure Active Directory za pośrednictwem protokołu SMB dla usługi Azure Files](storage-files-active-directory-overview.md)
- [Często zadawane pytania](storage-files-faq.md)
