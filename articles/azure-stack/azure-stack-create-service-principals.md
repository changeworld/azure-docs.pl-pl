---
title: Zarządzanie nazwy głównej usługi dla usługi Azure Stack | Dokumentacja firmy Microsoft
description: W tym artykule opisano, jak zarządzać nową jednostkę usługi, który może służyć przy użyciu kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 0f5a4dc76830740d69547a01ce40b5e10cf4a74b
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2019
ms.locfileid: "58499412"
---
# <a name="provide-applications-access-to-azure-stack"></a>Zapewnianie aplikacjom dostępu do usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Gdy aplikacja potrzebuje dostępu do wdrożenia i konfigurować zasoby za pomocą usługi Azure Resource Manager w usłudze Azure Stack, można utworzyć jednostki usługi, czyli poświadczenia dla aplikacji. Następnie można oddelegować uprawnienia niezbędne do tego obiektu głównego usługi.  

Na przykład może być narzędziem do zarządzania konfiguracji, który używa usługi Azure Resource Manager do spisu zasobów platformy Azure. W tym scenariuszu może utworzyć nazwę główną usługi, rola Czytelnik należy udzielić tego obiektu głównego usługi i ograniczyć zarządzanie za pomocą narzędzia konfiguracji na dostęp tylko do odczytu. 

Nazwy główne usług są preferowane w porównaniu do uruchamiania aplikacji z poświadczeniami użytkownika, ponieważ:

 - Możesz przypisywać uprawnienia do jednostki, które różnią się od uprawnień konta usługi. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.
 - Nie trzeba zmienić poświadczenia aplikacji, w przypadku zmiany Twoje obowiązki.
 - Certyfikat można użyć do zautomatyzowania uwierzytelniania podczas wykonywania skryptu instalacji nienadzorowanej.  

## <a name="getting-started"></a>Wprowadzenie

W zależności od tego, jak zostały wdrożone usługi Azure Stack należy rozpocząć od tworzenia jednostki usługi. W tym dokumencie opisano tworzenie usługi podmiotu zabezpieczeń dla:

- Azure Active Directory (Azure AD). Usługa Azure AD to wielodostępna, oparta na chmurze katalogu, a usługa zarządzania tożsamościami. Za pomocą usługi Azure AD połączonej usługi Azure Stack.
- Active Directory Federation Services (AD FS). Usługi AD FS zapewniają uproszczoną, zabezpieczoną Federację tożsamości i możliwości logowania jednokrotnego (SSO) dla sieci Web. Za pomocą usług AD FS zarówno połączonych i niepołączonych wystąpień usługi Azure Stack.

Po utworzeniu nazwy głównej usługi zestaw typowych kroków do usług AD FS i Azure Active Directory umożliwiają delegować uprawnienia do roli.

## <a name="manage-service-principal-for-azure-ad"></a>Zarządzanie nazwy głównej usługi dla usługi Azure AD

Jeśli zostały wdrożone usługi Azure Stack w usłudze Azure Active Directory (Azure AD), co usługa zarządzania tożsamościami, można utworzyć jednostki usługi, podobnie jak w przypadku platformy Azure. W tej sekcji dowiesz się, jak wykonać czynności opisane w portalu. Upewnij się, że masz [wymagane uprawnienia usługi Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) przed rozpoczęciem.

### <a name="create-service-principal"></a>Tworzenie jednostki usługi

W tej sekcji utworzysz aplikację (nazwy głównej usługi) w usłudze Azure AD, która reprezentuje aplikację.

1. Zaloguj się do konta platformy Azure za pośrednictwem [witryny Azure portal](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **rejestracje aplikacji** > **rejestrowanie nowej aplikacji**
3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnych** dla typu aplikacji, którą chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

Utworzono nazwę główną usługi dla swojej aplikacji.

### <a name="get-credentials"></a>Pobieranie poświadczeń

Programowe zalogować, użyj Identyfikatora aplikacji i aplikacji sieci Web / interfejs API, klucz uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Z **rejestracje aplikacji** w usłudze Active Directory, wybierz swoją aplikację.

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Aplikacje w sekcji aplikacje przykładowe odwoływać się do tej wartości jako identyfikatora klienta.

     ![Identyfikator klienta](./media/azure-stack-create-service-principal/image12.png)
3. Aby wygenerować klucz uwierzytelniania dla aplikacji sieci Web / interfejs API, wybierz **ustawienia** > **klucze**. 

4. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość do Notatnika lub innej tymczasowej lokalizacji, ponieważ nie można pobrać klucza później. Możesz podać wartość klucza z Identyfikatorem aplikacji do podpisania aplikacji. Store wartość klucza w miejscu, gdzie aplikacja będzie mogła ją pobrać.

![zapisany klucz](./media/azure-stack-create-service-principal/image15.png)

Po wykonaniu tych czynności, możesz przypisać aplikację rolę.

## <a name="manage-service-principal-for-ad-fs"></a>Zarządzanie nazwy głównej usługi dla usług AD FS

Jeśli zostały wdrożone usługi Azure Stack przy użyciu usługi Active Directory Federation Services (AD FS), co usługa zarządzania tożsamościami, należy użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się przy użyciu tej tożsamości.

Aby utworzyć jednostkę usługi usługi z usługami AD FS, można użyć jednej z dwóch metod. Możesz:
 - [Tworzenie jednostki usługi przy użyciu certyfikatu](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Tworzenie jednostki usługi przy użyciu klucz tajny klienta](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Jednostki usług zadania zarządzania usług AD FS.

| Type | Akcja |
| --- | --- |
| Certyfikat usług AD FS | [Tworzenie](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certyfikat usług AD FS | [Aktualizacja](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certyfikat usług AD FS | [Usuń](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Klucz tajny programu AD FS klienta | [Tworzenie](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Klucz tajny programu AD FS klienta | [Aktualizacja](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Klucz tajny programu AD FS klienta | [Usuń](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Tworzenie jednostki usługi przy użyciu certyfikatu

Podczas tworzenia nazwy głównej usługi podczas korzystania z usług AD FS tożsamości, można użyć certyfikatu.

#### <a name="certificate"></a>Certyfikat

Wymagany jest certyfikat.

**Wymagania dotyczące certyfikatów**

 - Dostawca usług kryptograficznych (CSP) musi być starszej wersji dostawcy kluczy.
 - Format certyfikatu musi być w pliku PFX, jak klucze publiczne i prywatne są wymagane. Serwery Windows używają, pliki PFX, które zawierają plik klucza publicznego (plik certyfikatu protokołu SSL) i skojarzony plik klucza prywatnego.
 - W środowisku produkcyjnym certyfikat musi być wystawiony z wewnętrznego urzędu certyfikacji lub publicznego urzędu certyfikacji. Jeśli korzystasz z publicznego urzędu certyfikacji, należy objęte urzędowi podstawowy obrazu systemu operacyjnego w ramach programu Microsoft zaufanego głównego urzędu. Można znaleźć pełną listę w [programu zaufanych certyfikatów głównych firmy Microsoft: Uczestnicy](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Infrastruktury Azure Stack musi mieć dostęp do sieci do lokalizacji listy odwołania certyfikatów (CRL) urzędu certyfikacji, opublikowane w certyfikacie. Tę listę CRL musi być punkt końcowy HTTP.

#### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

|Parametr|Opis|Przykład|
|---------|---------|---------|
|Name (Nazwa)|Nazwę SPN konta|MyAPP|
|ClientCertificates|Tablica obiektów certyfikatu|X509 certyfikatu|
|ClientRedirectUris<br>(Opcjonalnie)|Identyfikator URI przekierowania aplikacji|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Tworzenie jednostki usługi przy użyciu programu PowerShell

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

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

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Do celów sprawdzania poprawności, można utworzyć certyfikatu z podpisem własnym za pomocą poniższym przykładzie:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Po zakończeniu pracy automatyzacji przedstawia wymagane szczegóły, aby użyć nazwy SPN. Zalecane jest przechowywanie danych wyjściowych do późniejszego użycia.

   Na przykład:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Aktualizuj certyfikat dla nazwy głównej usługi dla usług AD FS

Jeśli wdrożono usługę Azure Stack z usługami AD FS można użyć programu PowerShell, aby zaktualizować klucz tajny dla nazwy głównej usługi.

Skrypt jest uruchamiany z uprzywilejowanym punktu końcowego na maszynie wirtualnej ERCS.

#### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

|Parametr|Opis|Przykład|
|---------|---------|---------|
|Name (Nazwa)|Nazwę SPN konta|MyAPP|
|ApplicationIdentifier|Unikatowy identyfikator|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Tablica obiektów certyfikatu|X509 certyfikatu|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Przykład aktualizowanie nazwy głównej usługi dla usług AD FS

Ten przykład tworzy certyfikat z podpisem własnym. Po uruchomieniu polecenia cmdlet wdrażania w środowisku produkcyjnym, należy użyć [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) można pobrać obiektu certyfikatu dla certyfikatu, którego chcesz użyć.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Po zakończeniu pracy automatyzacji, wyświetlana jest wartość odcisku palca zaktualizowane, wymaganych do uwierzytelniania nazwy SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Tworzenie jednostki usługi przy użyciu klucz tajny klienta

Podczas tworzenia nazwy głównej usługi podczas korzystania z usług AD FS tożsamości, można użyć certyfikatu. Punkt końcowy uprzywilejowanych użyje uruchamiać polecenia cmdlet.

Te skrypty są uruchamiane z uprzywilejowanym punktu końcowego na maszynie wirtualnej ERCS. Aby uzyskać więcej informacji na temat uprzywilejowanych punktu końcowego zobacz [przy użyciu uprzywilejowanych punktu końcowego w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

#### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

| Parametr | Opis | Przykład |
|----------------------|--------------------------|---------|
| Name (Nazwa) | Nazwę SPN konta | MyAPP |
| GenerateClientSecret | Tworzenie wpisu tajnego |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Użyj ERCS PrivilegedEndpoint, aby utworzyć nazwę główną usługi

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Po uruchomieniu polecenia cmdlet powłoki przedstawia wymagane szczegóły, aby użyć nazwy SPN. Upewnij się, że klucz tajny klienta są przechowywane.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Zaktualizuj klucz tajny klienta jednostki usługi dla usług AD FS

Nowy wpis tajny klienta jest automatycznie generowany przez polecenie cmdlet programu PowerShell.

Skrypt jest uruchamiany z uprzywilejowanym punktu końcowego na maszynie wirtualnej ERCS.

##### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

| Parametr | Opis | Przykład |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Unikatowy identyfikator. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Zmienia wpis tajny klienta z okresem przerzucania 2880 minut, w której stary klucz tajny jest nadal ważny. |  |
| ResetClientSecret | Zmień klucz tajny klienta natychmiast |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Przykład aktualizowanie klucz tajny klienta usług AD FS

W przykładzie użyto **ResetClientSecret** parametr, który natychmiast zmienia się klucz tajny klienta.

1. Otwórz sesję środowiska Windows PowerShell z podwyższonym poziomem uprawnień i uruchom następujące polecenia cmdlet:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Po zakończeniu pracy automatyzacji, wyświetla nowo wygenerowane klucz tajny, wymaganych do uwierzytelniania nazwy SPN. Upewnij się, że nowy wpis tajny klienta są przechowywane.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Usuń nazwę główną usługi dla usług AD FS

Jeśli wdrożono usługę Azure Stack z usługami AD FS, można użyć programu PowerShell, można usunąć jednostki usługi.

Skrypt jest uruchamiany z uprzywilejowanym punktu końcowego na maszynie wirtualnej ERCS.

#### <a name="parameters"></a>Parametry

Wymagane są następujące informacje jako dane wejściowe dla parametrów usługi automation:

|Parametr|Opis|Przykład|
|---------|---------|---------|
| Parametr | Opis | Przykład |
| ApplicationIdentifier | Unikatowy identyfikator | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Aby wyświetlić listę wszystkich istniejących jednostek usługi i ich identyfikatora aplikacji, służy polecenie get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Przykład usuwania jednostki usługi dla usług AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Przypisywanie roli

Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, rolę, która reprezentuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [RBAC: wbudowane role](../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone na niższych poziomach zakresu. Na przykład dodanie aplikacji do roli Czytelnik dla grupy zasobów oznacza, że może odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. W portalu Azure Stack przejdź do poziomu zakresu, którą chcesz przypisać aplikację. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego można wybrać grupy zasobów lub zasobu.

2. Umożliwia określonej subskrypcji (grupy zasobów lub zasobu), aby przypisać tę aplikację.

     ![Wybierz subskrypcję do przypisania](./media/azure-stack-create-service-principal/image16.png)

3. Wybierz **kontrola dostępu (IAM)**.

     ![Wybierz dostępu](./media/azure-stack-create-service-principal/image17.png)

4. Wybierz **Dodaj przypisanie roli**.

5. Wybierz rolę, którą chcesz przypisać do aplikacji.

6. Wyszukaj aplikację, a następnie wybierz ją.

7. Wybierz **OK** zakończenie przypisanie roli. Zostanie wyświetlona aplikacja na liście Użytkownicy przypisani do roli dla tego zakresu.

Teraz, po utworzeniu nazwy głównej usługi i przypisaną rolę, możesz rozpocząć za pomocą tego poziomu aplikacji dostęp do zasobów usługi Azure Stack.  

## <a name="next-steps"></a>Kolejne kroki

[Dodawanie użytkowników do usług AD FS](azure-stack-add-users-adfs.md)  
[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)  
[Dokumentacja usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Usługi Active Directory Federation Services](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
