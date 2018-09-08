---
title: Tworzenie jednostki usługi dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak utworzyć nową jednostkę usługi, który może służyć przy użyciu kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2018
ms.author: sethm
ms.openlocfilehash: 65fa9593b35af45ee9b8568bac5e4886909314e1
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092548"
---
# <a name="provide-applications-access-to-azure-stack"></a>Zapewnianie aplikacjom dostępu do usługi Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

Gdy aplikacja potrzebuje dostępu do wdrożenia i konfigurować zasoby za pomocą usługi Azure Resource Manager w usłudze Azure Stack, można utworzyć jednostki usługi, czyli poświadczenia dla aplikacji. Następnie można oddelegować uprawnienia niezbędne do tego obiektu głównego usługi.  

Na przykład może być narzędziem do zarządzania konfiguracji, który używa usługi Azure Resource Manager do spisu zasobów platformy Azure. W tym scenariuszu może utworzyć nazwę główną usługi, rola Czytelnik należy udzielić tego obiektu głównego usługi i ograniczyć zarządzanie za pomocą narzędzia konfiguracji na dostęp tylko do odczytu. 

Nazwy główne usług są preferowane w porównaniu do uruchamiania aplikacji z poświadczeniami użytkownika, ponieważ:

* Możesz przypisywać uprawnienia do jednostki, które różnią się od uprawnień konta usługi. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.
* Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany Twoje obowiązki.
* Certyfikat można użyć do zautomatyzowania uwierzytelniania podczas wykonywania skryptu instalacji nienadzorowanej.  

## <a name="getting-started"></a>Wprowadzenie

W zależności od tego, jak zostały wdrożone usługi Azure Stack należy rozpocząć od tworzenia jednostki usługi. W tym dokumencie opisano tworzenie nazwy głównej usługi dla obu [usługi Azure Active Directory (Azure AD)](#create-service-principal-for-azure-ad) i [Active Directory Federation Services(AD FS)](#create-service-principal-for-ad-fs). Po utworzeniu nazwy głównej usługi zestaw typowych kroków do usług AD FS i Azure Active Directory są używane do [delegować uprawnienia](#assign-role-to-service-principal) do roli.     

## <a name="create-service-principal-for-azure-ad"></a>Tworzenie jednostki usługi dla usługi Azure AD

Jeśli udało Ci się wdrożyć usługę Azure Stack przy użyciu usługi Azure AD jako magazynu tożsamości, można utworzyć jednostki usługi, podobnie jak w przypadku platformy Azure. W tej sekcji dowiesz się, jak wykonać czynności opisane w portalu. Upewnij się, że masz [wymagane uprawnienia usługi Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) przed rozpoczęciem.

### <a name="create-service-principal"></a>Tworzenie jednostki usługi
W tej sekcji utworzysz aplikację (nazwy głównej usługi) w usłudze Azure AD, która reprezentuje aplikację.

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**   
3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnych** dla typu aplikacji, którą chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

Utworzono nazwę główną usługi dla swojej aplikacji.

### <a name="get-credentials"></a>Pobierz poświadczenia
Programowe zalogować, użyj Identyfikatora aplikacji i aplikacji sieci Web / interfejs API, klucz uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Z **rejestracje aplikacji** w usłudze Active Directory, wybierz swoją aplikację.

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Aplikacje w [przykładowe aplikacje](#sample-applications) sekcji odnoszą się do tej wartości jako identyfikatora klienta.

     ![identyfikator klienta](./media/azure-stack-create-service-principal/image12.png)
3. Aby wygenerować klucz uwierzytelniania dla aplikacji sieci Web / interfejs API, wybierz **ustawienia** > **klucze**. 

4. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość do Notatnika lub innej tymczasowej lokalizacji, ponieważ nie można pobrać klucza później. Możesz podać wartość klucza z Identyfikatorem aplikacji do podpisania aplikacji. Store wartość klucza w miejscu, gdzie aplikacja będzie mogła ją pobrać.

![Zapisany klucz](./media/azure-stack-create-service-principal/image15.png)

Po wykonaniu tych czynności, przejdź do [przypisanie roli aplikacji](#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Tworzenie jednostki usługi dla usług AD FS
Jeśli wdrożono usługę Azure Stack z usługami AD FS, można użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się za pomocą programu PowerShell przy użyciu tej tożsamości.

Skrypt jest uruchamiany z uprzywilejowanym punktu końcowego na maszynie wirtualnej ERCS.

Wymagania:
- Wymagany jest certyfikat.

#### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:


|Parametr|Opis|Przykład|
|---------|---------|---------|
|Name (Nazwa)|Nazwę SPN konta|MyAPP|
|ClientCertificates|Tablica obiektów certyfikatu|X509 certyfikatu|
|ClientRedirectUris<br>(Opcjonalnie)|Identyfikator URI przekierowania aplikacji|-|

#### <a name="example"></a>Przykład

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia:

   > [!NOTE]
   > Ten przykład tworzy certyfikat z podpisem własnym. Po uruchomieniu tych poleceń wdrażania w środowisku produkcyjnym, należy użyć [Get-Certificate](/powershell/module/pkiclient/get-certificate) można pobrać obiektu certyfikatu dla certyfikatu, którego chcesz użyć.

   ```PowerShell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $creds

    # This produces a self signed cert for testing purposes. It is prefered to use a managed certificate for this.
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange

    $ServicePrincipal = Invoke-Command -Session $session -ScriptBlock { New-GraphApplication -Name '<yourappname>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $session -ScriptBlock { get-azurestackstampinformation }
    $session|remove-pssession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "azurestackuser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```

2. Po zakończeniu pracy automatyzacji przedstawia wymagane szczegóły, aby użyć nazwy SPN. 

   Na przykład:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="assign-a-role"></a>Przypisywanie roli
Po utworzeniu nazwy głównej usługi, należy najpierw [przypisać rolę](#assign-role-to-service-principal).

### <a name="sign-in-through-powershell"></a>Zaloguj się przy użyciu programu PowerShell
Po przypisaniu roli, należy zalogować się do usługi Azure Stack przy użyciu jednostki usługi przy użyciu następującego polecenia:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ClientId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Przypisywanie roli do jednostki usługi
Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która reprezentuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: Built in Roles](../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów oznacza, że może odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. W portalu Azure Stack przejdź do poziomu zakresu, którą chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego można wybrać grupy zasobów lub zasobu.

2. Umożliwia określonej subskrypcji (grupy zasobów lub zasobu), aby przypisać tę aplikację.

     ![Wybierz subskrypcję do przypisania](./media/azure-stack-create-service-principal/image16.png)

3. Wybierz **kontrola dostępu (IAM)**.

     ![Wybierz dostępu](./media/azure-stack-create-service-principal/image17.png)

4. Wybierz pozycję **Dodaj**.

5. Wybierz rolę, którą chcesz przypisać do aplikacji.

6. Wyszukaj aplikację, a następnie wybierz ją.

7. Wybierz **OK** zakończenie przypisanie roli. Zostanie wyświetlona aplikacja na liście Użytkownicy przypisani do roli dla tego zakresu.

Teraz, po utworzeniu nazwy głównej usługi i przypisaną rolę, możesz rozpocząć za pomocą tego poziomu aplikacji dostęp do zasobów usługi Azure Stack.  

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie użytkowników do usług AD FS](azure-stack-add-users-adfs.md)
[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
