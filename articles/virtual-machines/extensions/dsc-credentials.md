---
title: Przekazywania poświadczeń do platformy Azure przy użyciu Desired State Configuration
description: Dowiedz się, jak bezpiecznie przekazywania poświadczeń do maszyn wirtualnych platformy Azure przy użyciu programu PowerShell Desired State Configuration (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 723d0cfe6e292c4b8013de4da55779a6c675d610
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64705928"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Przekazywania poświadczeń do obsługi DSCExtension platformy Azure

W tym artykule opisano rozszerzenie Desired State Configuration (DSC) dla platformy Azure. Aby uzyskać przegląd procedury obsługi rozszerzenia DSC, zobacz [wprowadzenie do procedury obsługi rozszerzenia Azure Desired State Configuration](dsc-overview.md).

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="pass-in-credentials"></a>Przekazywanie poświadczeń

W ramach procesu konfiguracji może być potrzebujesz skonfigurować konta użytkowników, uzyskiwanie dostępu do usług lub zainstaluj program w kontekście użytkownika. Aby wykonać te czynności, musisz podać poświadczenia.

DSC służy do ustawiania konfiguracji sparametryzowanych. W konfiguracji sparametryzowane poświadczenia są przekazywane do konfiguracji i bezpiecznie przechowywane w plikach MOF. Obsługa rozszerzenia Azure upraszcza zarządzanie poświadczeniami, zapewniając automatyczne zarządzanie certyfikatami.

Poniższy skrypt konfiguracji DSC tworzy konto użytkownika lokalnego przy użyciu podanego hasła:

```powershell
configuration Main
{
    param(
        [Parameter(Mandatory=$true)]
        [ValidateNotNullorEmpty()]
        [PSCredential]
        $Credential
    )
    Node localhost {
        User LocalUserAccount
        {
            Username = $Credential.UserName
            Password = $Credential
            Disabled = $false
            Ensure = "Present"
            FullName = "Local User Account"
            Description = "Local User Account"
            PasswordNeverExpires = $true
        }
    }
}
```

Jest ważne uwzględnić **localhost węzła** jako część konfiguracji. Procedury obsługi rozszerzenia specjalnie szuka **localhost węzła** instrukcji. Poniższe kroki nie działają, jeśli brakuje tej instrukcji. Warto również uwzględnić typecast **[PsCredential]** . Określonego typu, wyzwala rozszerzenie Aby zaszyfrować poświadczenia.

Aby opublikować ten skrypt do usługi Azure Blob storage:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Aby ustawić rozszerzenie DSC usługi Azure i podaj poświadczenia:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Jak są zabezpieczone poświadczenia

Uruchamiając ten kod wyświetla monit o podanie poświadczeń. Po poświadczenie jest warunkiem krótko jest przechowywany w pamięci. Po opublikowaniu przy użyciu poświadczeń **AzVMDscExtension zestaw** polecenia cmdlet, poświadczenia są przesyłane za pośrednictwem protokołu HTTPS do maszyny Wirtualnej. Na maszynie wirtualnej platformy Azure są przechowywane poświadczenia szyfrowane na dysku przy użyciu lokalnego certyfikatu maszyny Wirtualnej. Poświadczenie krótko zostaną odszyfrowane w pamięci, a następnie ponownie jest szyfrowany do przekazania go do DSC.

Ten proces różni się od [przy użyciu bezpiecznej konfiguracji bez obsługi rozszerzenia](/powershell/dsc/securemof). Środowisko platformy Azure zapewnia możliwość przesyłania danych konfiguracji w bezpieczny sposób za pomocą certyfikatów. Korzystając z obsługi rozszerzenia DSC, nie musisz podać **$CertificatePath** lub **$CertificateID**/  **$Thumbprint** wpis **ConfigurationData**.

## <a name="next-steps"></a>Kolejne kroki

- Pobierz [wprowadzenie do procedury obsługi rozszerzenia DSC usługi Azure](dsc-overview.md).
- Sprawdź [szablonu usługi Azure Resource Manager dla rozszerzenia DSC](dsc-template.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do [Centrum dokumentacji programu PowerShell](/powershell/dsc/overview).
- Przeglądaj więcej funkcji, w którym można zarządzać za pomocą programu PowerShell DSC, a inne zasoby DSC, [galerii programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).