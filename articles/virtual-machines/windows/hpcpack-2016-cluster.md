---
title: Klaster HPC Pack 2016 na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć klaster HPC Pack 2016 na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3dde6a68-e4a6-4054-8b67-d6a90fdc5e3f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 03/09/2018
ms.author: danlep
ms.openlocfilehash: 91f067de33d1ff4bc272773e3db49de47fac2feb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="deploy-an-hpc-pack-2016-cluster-in-azure"></a>Wdrożenie klastra HPC Pack 2016 na platformie Azure

Wykonaj kroki opisane w tym artykule, aby wdrożyć [Microsoft HPC Pack 2016 Update 1](https://technet.microsoft.com/library/cc514029) klastra w maszynach wirtualnych platformy Azure. HPC Pack jest bezpłatna rozwiązania HPC firmy Microsoft, oparty na technologii Microsoft Azure i Windows Server i obsługuje obciążeń szeroki zakres HPC.

Użyj jednej z [szablonów usługi Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) do wdrożenia klastra HPC Pack 2016. Masz kilka opcji Topologia klastra z różną liczbę i typy klastra węzły główne i węzły obliczeniowe.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="pfx-certificate"></a>Certyfikatu PFX

W klastrze Microsoft HPC Pack 2016 wymaga certyfikat wymiany informacji osobistych (PFX) do zabezpieczania komunikacji między węzłami klastra HPC. Certyfikat musi spełniać następujące wymagania:

* Musi mieć klucz prywatny zdolny do wymiany klucza
* Użycie klucza zawiera podpis cyfrowy i szyfrowanie klucza
* Obejmuje ulepszonego użycia klucza uwierzytelniania serwera i uwierzytelnianie klienta

Jeśli nie masz jeszcze certyfikatu, który spełnia te wymagania, należy zażądać certyfikatu od urzędu certyfikacji. Można również użyć następujących poleceń, aby wygenerować certyfikat z podpisem własnym systemem operacyjnym, na którym uruchomiono polecenie. Następnie należy wyeksportować certyfikat jako plik PFX chroniony hasłem z kluczem prywatnym.

* **Dla systemu Windows 10 lub Windows Server 2016**, uruchom wbudowane **SelfSignedCertificate nowy** polecenia cmdlet programu PowerShell w następujący sposób:

  ```PowerShell
  New-SelfSignedCertificate -Subject "CN=HPC Pack 2016 Communication" -KeySpec KeyExchange -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.1,1.3.6.1.5.5.7.3.2") -CertStoreLocation cert:\CurrentUser\My -KeyExportPolicy Exportable -NotAfter (Get-Date).AddYears(5)
  ```
* **W systemach operacyjnych starszych niż Windows 10 lub Windows Server 2016**, Pobierz [generator certyfikatu z podpisem własnym](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z Microsoft Script Center. Wyodrębnij jego zawartość, a następnie uruchom następujące polecenia w wierszu polecenia programu PowerShell:

    ```PowerShell 
    Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
  
    New-SelfSignedCertificateEx -Subject "CN=HPC Pack 2016 Communication" -KeySpec Exchange -KeyUsage "DigitalSignature,KeyEncipherment" -EnhancedKeyUsage "Server Authentication","Client Authentication" -StoreLocation CurrentUser -Exportable -NotAfter (Get-Date).AddYears(5)
    ```

Po utworzeniu certyfikatu w magazynie bieżącego użytkownika, użyj przystawki Certyfikaty można wyeksportować certyfikat jako plik PFX chroniony hasłem z kluczem prywatnym. Możesz też wyeksportować certyfikat przy użyciu [Pfxcertificate eksportu](/powershell/module/pkiclient/export-pfxcertificate?view=win10-ps) polecenia cmdlet programu PowerShell.

### <a name="upload-certificate-to-an-azure-key-vault"></a>Przekaż certyfikat do magazynu kluczy Azure

Przed wdrożeniem klastra HPC, Przekaż certyfikat PFX do [usługi Azure key vault](../../key-vault/index.yml) jako klucz tajny i rekord następujące informacje do użycia podczas wdrażania: **nazwę magazynu**, **magazynu Grupa zasobów**, **adres URL certyfikatu**, i **odcisk palca certyfikatu**.

Przykładowy skrypt programu PowerShell, Przekaż certyfikat, utwórz magazyn kluczy i wygenerować wymaganych informacji jest zgodna. Aby uzyskać więcej informacji na temat przekazywania certyfikatu do usługi Azure key vault, zobacz [wprowadzenie do usługi Azure Key Vault](../../key-vault/key-vault-get-started.md).

```powershell
#Give the following values
$VaultName = "mytestvault"
$SecretName = "hpcpfxcert"
$VaultRG = "myresourcegroup"
$location = "westus"
$PfxFile = "c:\Temp\mytest.pfx"
$Password = "yourpfxkeyprotectionpassword"
#Validate the pfx file
try {
    $pfxCert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList $PfxFile, $Password
}
catch [System.Management.Automation.MethodInvocationException]
{
    throw $_.Exception.InnerException
}
$thumbprint = $pfxCert.Thumbprint
$pfxCert.Dispose()
# Create and encode the JSON object
$pfxContentBytes = Get-Content $PfxFile -Encoding Byte
$pfxContentEncoded = [System.Convert]::ToBase64String($pfxContentBytes)
$jsonObject = @"
{
"data": "$pfxContentEncoded",
"dataType": "pfx",
"password": "$Password"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
#Create an Azure key vault and upload the certificate as a secret
$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
$rg = Get-AzureRmResourceGroup -Name $VaultRG -Location $location -ErrorAction SilentlyContinue
if($null -eq $rg)
{
    $rg = New-AzureRmResourceGroup -Name $VaultRG -Location $location
}
$hpcKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultRG -Location $location -EnabledForDeployment -EnabledForTemplateDeployment
$hpcSecret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $SecretName -SecretValue $secret
"The following Information will be used in the deployment template"
"Vault Name             :   $VaultName"
"Vault Resource Group   :   $VaultRG"
"Certificate URL        :   $($hpcSecret.Id)"
"Certificate Thumbprint :   $thumbprint"

```


## <a name="supported-topologies"></a>Obsługiwane topologie

Wybierz jedną z [szablonów usługi Azure Resource Manager](https://github.com/MsHpcPack/HPCPack2016) do wdrożenia klastra HPC Pack 2016. Poniżej przedstawiono trzy przykładowe topologie klastra architektury wysokiego poziomu. Topologie wysokiej dostępności obejmują wiele głównymi węzłami klastra.

1. Klastra o wysokiej dostępności z domeną usługi Active Directory

    ![Klastra HA w domenie AD](./media/hpcpack-2016-cluster/haad.png)


2. Wysoka dostępność klastra bez domeny usługi Active Directory

    ![Klastra HA bez domeny usługi AD](./media/hpcpack-2016-cluster/hanoad.png)

3. Klaster z jednym węzłem głównym

   ![Klaster z jednym węzłem głównym](./media/hpcpack-2016-cluster/singlehn.png)


## <a name="deploy-a-cluster"></a>Wdrażanie klastra

Aby utworzyć klaster, wybierz szablon, a następnie kliknij przycisk **wdrażanie na platformie Azure**. W [portalu Azure](https://portal.azure.com), określ parametry szablonu, zgodnie z opisem w poniższych krokach. Każdy szablon tworzy wszystkich zasobów systemu Azure wymaganych przez infrastruktura klastra HPC. Zasoby obejmują sieci wirtualnej platformy Azure, publicznego adresu IP adres usługi równoważenia obciążenia (tylko w przypadku klastra o wysokiej dostępności), interfejsów sieciowych, zestawów dostępności, kont magazynu i maszyn wirtualnych.

### <a name="step-1-select-the-subscription-location-and-resource-group"></a>Krok 1: Wybierz subskrypcję, lokalizację i grupę zasobów

**Subskrypcji** i **lokalizacji** musi być taka sama, określona po przekazaniu certyfikatu PFX (patrz wymagania wstępne). Zaleca się utworzenie innego **grupy zasobów** dla wdrożenia.

### <a name="step-2-specify-the-parameter-settings"></a>Krok 2: Określ ustawienia parametru

Wprowadź lub zmień wartości parametrów szablonu. Kliknij ikonę obok każdego parametru, aby uzyskać pomoc. Zobacz też wskazówki dotyczące [dostępne rozmiary maszyny Wirtualnej](sizes.md).

Określ wartości zanotowanych w wymaganiach wstępnych dotyczących następujących parametrów: **nazwę magazynu**, **grupy zasobów magazynu**, **adres URL certyfikatu**, i  **Odcisk palca certyfikatu**.

### <a name="step-3-review-terms-and-create"></a>Krok 3. Przejrzyj postanowienia i utworzyć
Zapoznaj się z warunkami skojarzony z szablonem. Jeśli akceptujesz, kliknij przycisk **zakupu** do wdrożenia.

W zależności od topologii klastra wdrażania może potrwać 30 minut lub dłużej.

## <a name="connect-to-the-cluster"></a>Łączenie z klastrem
1. Po wdrożeniu klastra HPC Pack, przejdź do [portalu Azure](https://portal.azure.com). Kliknij przycisk **grup zasobów**i Znajdź grupę zasobów, w którym została wdrożona klastra. Można znaleźć węzła głównego maszyn wirtualnych.

    ![Głównymi węzłami klastra w portalu](./media/hpcpack-2016-cluster/clusterhns.png)

2. Kliknij przycisk jednego węzła głównego (w klastrze wysokiej dostępności kliknij dowolny z węzłów głównych). W **omówienie**, można znaleźć publicznego adresu IP lub pełną nazwę DNS klastra.

    ![Ustawienia połączenia klastra](./media/hpcpack-2016-cluster/clusterconnect.png)

3. Kliknij przycisk **Connect** aby zalogować się do żadnego z węzłów głównych przy użyciu pulpitu zdalnego z nazwą użytkownika administratora. W przypadku klastra została wdrożona w domenie usługi Active Directory, nazwa użytkownika jest w formie \<privateDomainName >\\\<adminUsername > (na przykład hpc.local\hpcadmin).

## <a name="next-steps"></a>Kolejne kroki
* Przesyłanie zadań do klastra. Zobacz [przesyłania zadań HPC HPC Pack klastra w systemie Azure](hpcpack-cluster-submit-jobs.md) i [Zarządzanie klastra HPC Pack 2016 na platformie Azure przy użyciu usługi Azure Active Directory](hpcpack-cluster-active-directory.md).

