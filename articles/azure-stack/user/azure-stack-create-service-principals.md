---
title: Tworzenie nazwy głównej usługi Azure stosu | Dokumentacja firmy Microsoft
description: Opisuje sposób tworzenia nowej nazwy głównej usługi, który może służyć z kontroli dostępu opartej na rolach w usłudze Azure Resource Manager do zarządzania dostępem do zasobów.
services: azure-resource-manager
documentationcenter: na
author: mattbriggs
manager: femila
ms.assetid: 7068617b-ac5e-47b3-a1de-a18c918297b6
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/28/2018
ms.author: mabrigg
ms.openlocfilehash: 0517c85c62aaffd1055206120281c7b7de31ad82
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="provide-applications-access-to-azure-stack"></a>Zapewnianie dostępu aplikacji do stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Gdy aplikacja potrzebuje dostępu do wdrażania lub skonfigurować zasoby za pośrednictwem usługi Azure Resource Manager w stosie Azure, można utworzyć nazwy głównej usługi, który jest poświadczenia dla aplikacji.  Następnie można oddelegować uprawnienia niezbędne do tej nazwy głównej usługi.  

Na przykład może być narzędziem do zarządzania konfiguracji korzystającą z usługi Azure Resource Manager w celu spisu zasobów platformy Azure.  W tym scenariuszu można utworzyć nazwy głównej usługi, przyznaj rolę czytelnika do tej nazwy głównej usługi i ograniczyć zarządzania za pomocą narzędzia konfiguracji dostępu tylko do odczytu. 

Nazwy główne usług są preferowane w porównaniu do uruchamiania aplikacji z poświadczeniami użytkownika, ponieważ:

* Można przypisać uprawnienia do podmiotu zabezpieczeń, które są inne niż uprawnień konta usługi. Zazwyczaj te uprawnienia są ograniczone do dokładnie co aplikacja powinna wykonać.
* Nie masz umożliwia zmianę poświadczeń aplikacji Zmień Twoje obowiązki.
* Certyfikat służy do automatyzacji procesu uwierzytelniania podczas wykonywania skryptu instalacji nienadzorowanej.  

## <a name="getting-started"></a>Wprowadzenie

W zależności od tego, jak zostały wdrożone stosu Azure możesz uruchomić tworzenie główną usługi.  W tym dokumencie opisano przez proces tworzenia nazwy głównej usługi dla obu [usługi Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad) i [Active Directory Federation Services(AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).  Po utworzeniu nazwy głównej usługi zestaw wspólnych kroków do usług AD FS i usługi Azure Active Directory są używane do [delegować uprawnienia](azure-stack-create-service-principals.md#assign-role-to-service-principal) do roli.     

## <a name="create-service-principal-for-azure-ad"></a>Tworzenie nazwy głównej usługi dla usługi Azure AD

Jeśli została wdrożona przy użyciu usługi Azure AD jako magazynu tożsamości stosu Azure, można utworzyć nazwy główne usług, podobnie jak w przypadku usługi Azure.  W tej sekcji przedstawiono sposób wykonywania kroków za pośrednictwem portalu.  Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions) przed rozpoczęciem.

### <a name="create-service-principal"></a>Tworzenie jednostki usługi
W tej sekcji utworzysz aplikację (nazwy głównej usługi) w usłudze Azure AD, która będzie reprezentowała aplikacji.

1. Zaloguj się do konta platformy Azure za pośrednictwem [portalu Azure](https://portal.azure.com).
2. Wybierz **usługi Azure Active Directory** > **rejestracji aplikacji** > **Dodaj**   
3. Podaj nazwę i adres URL aplikacji. Wybierz opcję **aplikacji sieci Web / interfejs API** lub **natywnego** dla typu aplikacji, w którym chcesz utworzyć. Po ustawieniu wartości, wybierz **Utwórz**.

Utworzono nazwy głównej usługi dla aplikacji.

### <a name="get-credentials"></a>Pobieranie poświadczeń
Podczas logowania programowo, można użyć Identyfikatora aplikacji i klucz uwierzytelniania. Aby uzyskać te wartości, wykonaj następujące kroki:

1. Z **rejestracji aplikacji** w usłudze Active Directory, wybierz aplikację.

2. Skopiuj **identyfikator aplikacji** i zapisz go w kodzie aplikacji. Aplikacje w [przykładowe aplikacje](#sample-applications) sekcji odnoszą się do tej wartości jako identyfikator klienta.

     ![identyfikator klienta](./media/azure-stack-create-service-principal/image12.png)
3. Aby wygenerować klucz uwierzytelniania, wybierz pozycję **Klucze**.

4. Podaj opis i czas trwania klucza. Po zakończeniu wybierz pozycję **Zapisz**.

Po zapisaniu klucza zostanie wyświetlona jego wartość. Skopiuj tę wartość, ponieważ później nie będzie można pobrać klucza. Musisz podać wartość tego klucza z Identyfikatorem aplikacji do podpisania co aplikacja. Zapisz wartość klucza w miejscu, z którego aplikacja będzie mogła ją pobrać.

![Zapisany klucz](./media/azure-stack-create-service-principal/image15.png)


Po wykonaniu tych czynności, przejdź do [przypisywanie roli aplikacji](azure-stack-create-service-principals.md#assign-role-to-service-principal).

## <a name="create-service-principal-for-ad-fs"></a>Tworzenie nazwy głównej usługi dla usług AD FS
Jeśli wdrożono stosu Azure z usługami AD FS, można użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się z programu PowerShell przy użyciu tej tożsamości.

### <a name="before-you-begin"></a>Przed rozpoczęciem

[Pobierz narzędzia niezbędne do pracy z stosu Azure na komputerze lokalnym.](azure-stack-powershell-download.md)

### <a name="import-the-identity-powershell-module"></a>Zaimportuj moduł programu PowerShell tożsamości
Po pobraniu narzędzia, przejdź do folderu, pobranych i zaimportować moduł programu PowerShell tożsamości za pomocą następującego polecenia:

```PowerShell
Import-Module .\Identity\AzureStack.Identity.psm1
```

Podczas importowania modułu może zostać wyświetlony komunikat o błędzie informujący "AzureStack.Connect.psm1 nie jest podpisany cyfrowo. Skrypt nie zostanie wykonany w systemie". Aby rozwiązać ten problem, można ustawić zasady wykonywania, aby umożliwić uruchomienie skryptu za pomocą następującego polecenia w sesji programu PowerShell z podwyższonym poziomem uprawnień:

```PowerShell
Set-ExecutionPolicy Unrestricted
```

### <a name="create-the-service-principal"></a>Tworzenie jednostki usługi
Można utworzyć nazwy głównej usługi, wykonując następujące polecenie, upewniając się zaktualizować *DisplayName* parametru:
```powershell
$servicePrincipal = New-AzSADGraphServicePrincipal `
 -DisplayName "<YourServicePrincipalName>" `
 -AdminCredential $(Get-Credential) `
 -AdfsMachineName "AZS-ADFS01" `
 -Verbose
```
### <a name="assign-a-role"></a>Przypisywanie roli
Po utworzeniu nazwy głównej usługi, należy najpierw [przypisać rolę](azure-stack-create-service-principals.md#assign-role-to-service-principal)

### <a name="sign-in-through-powershell"></a>Zaloguj się za pomocą programu PowerShell
Gdy rola jest przypisywana, można logowania się do stosu Azure przy użyciu nazwy głównej usługi za pomocą następującego polecenia:

```powershell
Add-AzureRmAccount -EnvironmentName "<AzureStackEnvironmentName>" `
 -ServicePrincipal `
 -CertificateThumbprint $servicePrincipal.Thumbprint `
 -ApplicationId $servicePrincipal.ApplicationId ` 
 -TenantId $directoryTenantId
```

## <a name="assign-role-to-service-principal"></a>Przypisz rolę do nazwy głównej usługi
Aby uzyskać dostęp do zasobów w ramach subskrypcji, należy przypisać aplikacji do roli. Zdecyduj, które roli reprezentuje odpowiednich uprawnień dla aplikacji. Aby dowiedzieć się więcej o dostępnych ról, zobacz [RBAC: Built in Roles](../../role-based-access-control/built-in-roles.md).

Na poziomie subskrypcji, grupy zasobów lub zasobów można ustawić zakresu. Uprawnienia są dziedziczone na niższe poziomy zakresu. Na przykład dodawanie aplikacji do roli czytnik dla grupy zasobów oznacza, że mogą odczytywać, grupy zasobów i wszystkie zasoby, które zawiera.

1. W portalu Azure stosu przejdź na poziom zakresu, który chcesz przypisać aplikację do. Na przykład, aby przypisać rolę w zakresie subskrypcji, wybierz **subskrypcje**. Zamiast tego możesz określić, grupy zasobów lub zasobu.

2. Umożliwia określonej subskrypcji (grupy zasobów lub zasobów), aby przypisać tę aplikację.

     ![Wybierz subskrypcję do przypisania](./media/azure-stack-create-service-principal/image16.png)

3. Wybierz **(IAM) kontroli dostępu**.

     ![Wybierz dostępu](./media/azure-stack-create-service-principal/image17.png)

4. Wybierz pozycję **Dodaj**.

5. Wybierz rolę, którą chcesz przypisać do aplikacji.

6. Wyszukaj aplikację i zaznacz go.

7. Wybierz **OK** na zakończenie przypisanie roli. Zostanie wyświetlona aplikacja w listy użytkowników przypisanych do roli dla tego zakresu.

Teraz, utworzeniu nazwy głównej usługi i przypisaną rolę, możesz rozpocząć za pomocą tej w aplikacji dostęp do zasobów Azure stosu.  

## <a name="next-steps"></a>Kolejne kroki

[Zarządzanie uprawnieniami użytkowników](azure-stack-manage-permissions.md)
