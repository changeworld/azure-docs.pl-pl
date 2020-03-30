---
title: Autoryzowanie dostępu do danych plików za pomocą usług domenowych usługi Azure AD za pomocą usług domenowych usługi Azure AD
description: Dowiedz się, jak włączyć uwierzytelnianie oparte na tożsamości za pośrednictwem bloku komunikatów serwera (SMB) dla plików platformy Azure za pośrednictwem usług domenowych Active Directory platformy Azure. Maszyny wirtualne systemu Windows przyłączone do domeny mogą następnie uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e2e3c7763a13c8850554b079a426ed4172b74d28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77599277"
---
# <a name="enable-azure-active-directory-domain-services-authentication-on-azure-files"></a>Włączanie uwierzytelniania usług domenowych Active Directory platformy Azure w plikach platformy Azure

[!INCLUDE [storage-files-aad-auth-include](../../../includes/storage-files-aad-auth-include.md)]

Aby zapoznać się z omówieniem uwierzytelniania usługi Azure AD za pomocą smb dla udziałów plików platformy Azure, zobacz [Omówienie uwierzytelniania usługi Azure Active Directory za pomocą SMB dla plików Platformy Azure](storage-files-active-directory-overview.md). W tym artykule skupiono się na tym, jak włączyć uwierzytelnianie za pomocą usług domenowych Active Directory platformy Azure (Usługi Usługi Azure AD DS) w usługach Azure Files.

> [!NOTE]
> Usługa Azure Files obsługuje uwierzytelnianie Kerberos za pomocą usługi Azure AD DS z szyfrowaniem RC4-HMAC. Szyfrowanie Protokołu Kerberos AES nie jest jeszcze obsługiwane.

## <a name="prerequisites"></a>Wymagania wstępne

Przed włączeniem usługi Azure AD za pomocą smb dla udziałów plików platformy Azure upewnij się, że zostały ukończone następujące wymagania wstępne:

1.  **Wybierz lub utwórz dzierżawę usługi Azure AD.**

    Można użyć nowej lub istniejącej dzierżawy dla uwierzytelniania usługi Azure AD za pomocą SMB. Dzierżawy i udziału plików, które chcesz uzyskać dostęp musi być skojarzony z tej samej subskrypcji.

    Aby utworzyć nową dzierżawę usługi Azure AD, można [dodać dzierżawę usługi Azure AD i subskrypcję usługi Azure AD.](https://docs.microsoft.com/windows/client-management/mdm/add-an-azure-ad-tenant-and-azure-ad-subscription) Jeśli masz istniejącą dzierżawę usługi Azure AD, ale chcesz utworzyć nową dzierżawę do użytku z udziałami plików platformy Azure, zobacz [Tworzenie dzierżawy usługi Azure Active Directory.](https://docs.microsoft.com/rest/api/datacatalog/create-an-azure-active-directory-tenant)

1.  **Włącz usługi domenowe usługi azure ad w dzierżawie usługi Azure AD.**

    Aby obsługiwać uwierzytelnianie przy użyciu poświadczeń usługi Azure AD, należy włączyć usługi domenowe usługi Azure AD dla dzierżawy usługi Azure AD. Jeśli nie jesteś administratorem dzierżawy usługi Azure AD, skontaktuj się z administratorem i postępuj zgodnie ze wskazówkami krok po kroku, aby [włączyć usługi domenowe usługi platformy Azure Active Directory przy użyciu witryny Azure portal](../../active-directory-domain-services/tutorial-create-instance.md).

    Zazwyczaj trwa około 15 minut dla wdrożenia usług Ad DS do ukończenia. Sprawdź, czy stan kondycji usługi Azure AD DS pokazuje **uruchomione**, z włączoną synchronizacją skrótów haseł, przed przejściem do następnego kroku.

1.  **Dołącz do maszyny Wirtualnej platformy Azure za pomocą usług Azure AD DS.**

    Aby uzyskać dostęp do udziału plików przy użyciu poświadczeń usługi Azure AD z maszyny Wirtualnej, maszyna wirtualna musi być przyłączona do usługi Azure AD DS. Aby uzyskać więcej informacji na temat dołączania do maszyny wirtualnej w domenie, zobacz [Dołączanie maszyny wirtualnej systemu Windows Server do domeny zarządzanej](../../active-directory-domain-services/join-windows-vm.md).

    > [!NOTE]
    > Uwierzytelnianie usług Ad DS za pomocą usługi SMB z udziałami plików platformy Azure jest obsługiwane tylko na maszynach wirtualnych platformy Azure uruchomionych w wersjach systemu operacyjnego powyżej systemu Windows 7 lub Windows Server 2008 R2.

1.  **Wybierz lub utwórz udział plików platformy Azure.**

    Wybierz nowy lub istniejący udział plików, który jest skojarzony z tą samą subskrypcją co dzierżawa usługi Azure AD. Aby uzyskać informacje dotyczące tworzenia nowego udziału plików, zobacz [Tworzenie udziału plików w usłudze Azure Files](storage-how-to-create-file-share.md).
    Aby uzyskać optymalną wydajność, zaleca się, aby udział plików w tym samym regionie co maszyna wirtualna, z której planujesz uzyskać dostęp do udziału.

1.  **Sprawdź łączność usługi Azure Files, montując udziały plików platformy Azure przy użyciu klucza konta magazynu.**

    Aby sprawdzić, czy maszyna wirtualna i udział plików są poprawnie skonfigurowane, spróbuj złożyć udział plików przy użyciu klucza konta magazynu. Aby uzyskać więcej informacji, zobacz [Instalowanie udziału plików platformy Azure i uzyskiwanie dostępu do udziału w systemie Windows](storage-how-to-use-files-windows.md).

## <a name="overview-of-the-workflow"></a>Omówienie przepływu pracy

Przed włączeniem uwierzytelniania usług Ad DS platformy Azure za pomocą smb dla udziałów plików platformy Azure sprawdź, czy środowiska usługi Azure AD i usługi Azure Storage są poprawnie skonfigurowane. Zaleca się przejście przez [wymagania wstępne,](#prerequisites) aby upewnić się, że wykonano wszystkie wymagane kroki.

Następnie wykonaj następujące czynności, aby udzielić dostępu do zasobów usługi Azure Files przy użyciu poświadczeń usługi Azure AD:

- Włącz uwierzytelnianie usług Azure AD DS za pomocą usługi SMB dla konta magazynu, aby zarejestrować konto magazynu przy użyciu skojarzonego wdrożenia usług Azure AD DS.
- Przypisywanie uprawnień dostępu do udziału do tożsamości usługi Azure AD (użytkownika, grupy lub jednostki usługi).
- Konfigurowanie uprawnień NTFS za kątem SMB dla katalogów i plików.
- Zainstaluj udział plików platformy Azure z maszyny wirtualnej przyłączone do domeny.

Na poniższym diagramie przedstawiono kompleksowy przepływ pracy umożliwiający uwierzytelnianie usług Azure AD DS za pomocą smb dla usług Azure Files.

![Diagram przedstawiający usługę Azure AD za pomocą przestawu pracy SMB dla usług Azure Files](media/storage-files-active-directory-enable/azure-active-directory-over-smb-workflow.png)

## <a name="enable-azure-ad-ds-authentication-for-your-account"></a>Włączanie uwierzytelniania usług Ad DS dla swojego konta

Aby włączyć uwierzytelnianie usług Azure AD DS za pośrednictwem SMB dla usług Azure Files, można ustawić właściwość na kontach magazynu przy użyciu witryny Azure portal, Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Ustawienie tej właściwości niejawnie "domena łączy" konto magazynu ze skojarzonym wdrożeniem usług Azure AD DS. Uwierzytelnianie usług Ad DS platformy Azure za pomocą SMB jest następnie włączone dla wszystkich nowych i istniejących udziałów plików na koncie magazynu.

Należy pamiętać, że można włączyć uwierzytelnianie usług Ad DS platformy Azure za pomocą SMB tylko po pomyślnym wdrożeniu usług Azure AD DS w dzierżawie usługi Azure AD. Aby uzyskać więcej informacji, zobacz [wymagania wstępne](#prerequisites).

### <a name="azure-portal"></a>Portal Azure

Aby włączyć uwierzytelnianie usług Azure AD DS za pomocą usługi SMB w [witrynie Azure portal,](https://portal.azure.com)wykonaj następujące kroki:

1. W witrynie Azure portal przejdź do istniejącego konta magazynu lub [utwórz konto magazynu](../common/storage-account-create.md).
1. W sekcji **Ustawienia** wybierz pozycję **Konfiguracja**.
1. W obszarze **Dostęp oparty na tożsamości dla udziałów plików** przełącz przełącznik usługi **domenowej Usługi domenowej Active Directory platformy Azure (AAD DS)** na **Włączone**.
1. Wybierz **pozycję Zapisz**.

Na poniższej ilustracji pokazano, jak włączyć uwierzytelnianie usług Azure AD DS za pomocą SMB dla konta magazynu.

![Włączanie uwierzytelniania usług Ad DS za pomocą usługi SMB w witrynie Azure portal](media/storage-files-active-directory-enable/portal-enable-active-directory-over-smb.png)

### <a name="powershell"></a>PowerShell  

Aby włączyć uwierzytelnianie usług Azure AD DS za pomocą usługi SMB za pomocą programu Azure PowerShell, zainstaluj najnowszy moduł Az (2.4 lub nowszy) lub moduł Az.Storage (1.5 lub nowszy). Aby uzyskać więcej informacji dotyczących instalowania programu PowerShell, zobacz [Instalowanie programu Azure PowerShell w systemie Windows za pomocą programu PowerShellGet](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Aby utworzyć nowe konto magazynu, należy wywołać [new-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/New-azStorageAccount?view=azps-2.5.0), a następnie ustawić **parametr EnableAzureActiveDirectoryDomainServicesForFile** na **true**. W poniższym przykładzie należy pamiętać o zastąpieniu wartości zastępczych własnymi wartościami. (Jeśli używasz poprzedniego modułu podglądu, parametrem włączania funkcji jest **EnableAzureFilesAadIntegrationForSMB**.)

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

Aby włączyć uwierzytelnianie usługi Azure AD za pomocą usługi SMB za pomocą interfejsu wiersza polecenia platformy Azure, zainstaluj najnowszą wersję interfejsu wiersza polecenia (wersja 2.0.70 lub nowsza). Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Aby utworzyć nowe konto magazynu, wywołaj[konto magazynu az create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)i ustaw właściwość na `--enable-files-aadds` **true**. W poniższym przykładzie należy pamiętać o zastąpieniu wartości zastępczych własnymi wartościami. (Jeśli używasz poprzedniego modułu podglądu, parametrem włączania funkcji jest **file-aad**.)

```azurecli-interactive
# Create a new storage account
az storage account create -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

Aby włączyć tę funkcję na istniejących kontach magazynu, użyj następującego polecenia:

```azurecli-interactive
# Update a new storage account
az storage account update -n <storage-account-name> -g <resource-group-name> --enable-files-aadds $true
```

[!INCLUDE [storage-files-aad-permissions-and-mounting](../../../includes/storage-files-aad-permissions-and-mounting.md)]

Teraz pomyślnie włączono uwierzytelnianie usług Azure AD DS za pomocą usługi SMB i przypisano rolę niestandardową, która zapewnia dostęp do udziału plików platformy Azure za pomocą tożsamości usługi Azure AD. Aby udzielić dodatkowym użytkownikom dostępu do udziału plików, postępuj zgodnie z instrukcjami w sekcji [Przypisywanie uprawnień dostępu](#assign-access-permissions-to-an-identity) do używania tożsamości i [konfigurowania uprawnień NTFS za pomocą sekcji SMB](#configure-ntfs-permissions-over-smb).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usług Azure Files i sposobu korzystania z usługi Azure AD za pomocą usługi SMB, zobacz następujące zasoby:

- [Omówienie obsługi uwierzytelniania opartego na tożsamościach usługi Azure Files dla dostępu do SMB](storage-files-active-directory-overview.md)
- [Najczęściej zadawane pytania](storage-files-faq.md)
