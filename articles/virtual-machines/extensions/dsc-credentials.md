---
title: Przekazywanie poświadczeń na platformę Azure przy użyciu konfiguracji żądanego stanu
description: Dowiedz się, jak bezpiecznie przekazywać poświadczenia do maszyn wirtualnych platformy Azure przy użyciu konfiguracji żądanego stanu programu PowerShell (DSC).
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: ea76b7e8-b576-445a-8107-88ea2f3876b9
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: f7edbd0fd8791829a2d9ffaa4e7c0ee0e561cc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73748971"
---
# <a name="pass-credentials-to-the-azure-dscextension-handler"></a>Przekazywanie poświadczeń do programu obsługi usługi Azure DSCExtension

W tym artykule omówiono rozszerzenie konfiguracji żądanego stanu (DSC) dla platformy Azure. Aby zapoznać się z omówieniem programu obsługi rozszerzeń DSC, zobacz [Wprowadzenie do programu obsługi rozszerzenia konfiguracji żądanego stanu platformy Azure.](dsc-overview.md)

 

## <a name="pass-in-credentials"></a>Przekazywanie poświadczeń

W ramach procesu konfiguracji może być konieczne skonfigurowanie kont użytkowników, dostęp do usług lub zainstalowanie programu w kontekście użytkownika. Aby wykonać te czynności, należy podać poświadczenia.

Za pomocą dsc można skonfigurować konfiguracje sparametryzowane. W konfiguracji sparametryzowanej poświadczenia są przekazywane do konfiguracji i bezpiecznie przechowywane w plikach .mof. Program obsługi rozszerzeń platformy Azure upraszcza zarządzanie poświadczeniami, zapewniając automatyczne zarządzanie certyfikatami.

Następujący skrypt konfiguracji DSC tworzy lokalne konto użytkownika z określonym hasłem:

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

Ważne jest, aby uwzględnić **localhost węzła** jako część konfiguracji. Program obsługi rozszerzenia w szczególności wyszukuje **localhost węzła** instrukcji. Jeśli brakuje tej instrukcji, następujące kroki nie działają. Ważne jest również, aby uwzględnić typecast **[PsCredential]**. Ten określony typ wyzwala rozszerzenie do szyfrowania poświadczeń.

Aby opublikować ten skrypt w magazynie obiektów blob platformy Azure:

`Publish-AzVMDscConfiguration -ConfigurationPath .\user_configuration.ps1`

Aby ustawić rozszerzenie usługi Azure DSC i podać poświadczenia:

```powershell
$configurationName = 'Main'
$configurationArguments = @{ Credential = Get-Credential }
$configurationArchive = 'user_configuration.ps1.zip'
$vm = Get-AzVM -Name 'example-1'

$vm = Set-AzVMDscExtension -VMName $vm -ConfigurationArchive $configurationArchive -ConfigurationName $configurationName -ConfigurationArgument @configurationArguments

$vm | Update-AzVM
```

## <a name="how-a-credential-is-secured"></a>Jak zabezpieczono poświadczenie

Uruchomienie tego kodu monituje o poświadczenie. Po poświadczeniu jest ono krótko przechowywane w pamięci. Po opublikowaniu poświadczenia przy użyciu polecenia cmdlet **Set-AzVMDscExtension** poświadczenie jest przesyłane za pośrednictwem protokołu HTTPS do maszyny Wirtualnej. Na maszynie Wirtualnej platforma Azure przechowuje poświadczenia zaszyfrowane na dysku przy użyciu lokalnego certyfikatu maszyny Wirtualnej. Poświadczenia są na krótko odszyfrowywane w pamięci, a następnie są ponownie szyfrowane w celu przekazania go do dsc.

Ten proces różni się od [korzystania z bezpiecznych konfiguracji bez obsługi rozszerzeń](/powershell/scripting/dsc/pull-server/securemof). Środowisko platformy Azure umożliwia bezpieczne przesyłanie danych konfiguracji za pośrednictwem certyfikatów. Korzystając z programu obsługi rozszerzeń DSC, nie trzeba podawać **$CertificatePath** lub **$CertificateID**/ **$Thumbprint** wpisu w **ConfigurationData**.

## <a name="next-steps"></a>Następne kroki

- Pobierz [wprowadzenie do programu obsługi rozszerzeń usługi Azure DSC.](dsc-overview.md)
- Sprawdź [szablon usługi Azure Resource Manager dla rozszerzenia DSC](dsc-template.md).
- Aby uzyskać więcej informacji na temat programu PowerShell DSC, przejdź do centrum dokumentacji programu [PowerShell](/powershell/scripting/dsc/overview/overview).
- Aby uzyskać więcej funkcji, którymi można zarządzać za pomocą programu PowerShell DSC, a aby uzyskać więcej zasobów DSC, przejrzyj [galerię programu PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).