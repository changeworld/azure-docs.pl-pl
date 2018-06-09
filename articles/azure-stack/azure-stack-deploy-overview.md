---
title: Ocena Azure stosu Development Kit | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć Azure stosu Development Kit do celów oceny.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/04/2018
ms.author: jeffgilb
ms.custom: mvc
ms.openlocfilehash: a0e742ab3ac43cc7977761dd94c9689e3a7c2e0b
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235189"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Szybki Start: ocena Azure stosu Development Kit

[Azure stosu Development Kit (ASDK)](.\asdk\asdk-what-is.md) jest środowiskiem badań i rozwoju, które można wdrożyć do oceny i Wykaż stosu Azure funkcji i usług. Aby rozpocząć pracę z ASDK, należy przygotować hosta komputera, a następnie uruchom niektóre skrypty (instalacja zajmuje kilka godzin). Po wykonaniu tej można Zaloguj się do portali administratora lub użytkownika na rozpoczęcie korzystania z usługi Azure stosu.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="asdk-host-computer-requirements"></a>Wymagania dotyczące komputera hosta ASDK

Przed zainstalowaniem ASDK, należy przygotować komputer, który będzie hostem zestaw deweloperski. Na komputerze deweloperskim zestaw host musi spełniać sprzętu, oprogramowania i wymagania dotyczące sieci opisanych w  **[przejrzyj zagadnienia związane z planowaniem wdrożenia ASDK](.\asdk\asdk-deploy-considerations.md)**.

> [!TIP]
> Można użyć [wymagania dotyczące wdrażania stosu Azure Sprawdź narzędzie](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po zainstalowaniu systemu operacyjnego na komputerze hosta programowanie zestawu, aby upewnić się, że sprzęt spełnia wszystkie wymagania.

### <a name="account-requirements"></a>Wymagania dotyczące konta

Należy również wybrać za pomocą usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) jako rozwiązanie tożsamości dla danego wdrożenia. Przejrzyj wymagania dotyczące konta w  **[zagadnienia dotyczące wdrażania](.\asdk\asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Pobierać i wyodrębniać pakiet wdrożeniowy

Po przygotowaniu komputera development kit hosta, pobierać i wyodrębniać ASDK pakietu wdrożeniowego. Pakiet wdrażania zawiera pliki instalacyjne stosu Azure i pliku Cloudbuilder.vhdx, czyli wirtualnego dysku twardego (VHD) z systemem operacyjnym rozruchowego.

Możesz pobrać pakiet wdrożeniowy hosta zestawu rozwoju lub do innego komputera. Pliki wyodrębnionego wdrożenia podjąć 60 GB wolnego miejsca na dysku, więc za pomocą innego komputera może pomóc zmniejszyć wymagania dotyczące magazynu na hoście development kit.

**[Pobierać i wyodrębniać Azure stosu Development Kit (ASDK)](.\asdk\asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Przygotuj komputer-host

Przed zainstalowaniem ASDK, należy przygotować środowisko hosta, a system jest skonfigurowany do uruchamiania z zestaw deweloperski wirtualnego dysku twardego. Po ponownym uruchomieniu hosta rozruchu z CloudBuilder.vhdx i możesz przystąpić do wdrażania ASDK.

**[Przygotuj komputer-host ASDK](.\asdk\asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Zainstaluj ASDK na komputerze hosta

Po uruchomieniu komputera hosta z dysku VHD, można wdrożyć zestaw deweloperski Cloudbuilder środowiska wirtualnego. Można wdrożyć ASDK przy użyciu graficznego interfejsu użytkownika (GUI), uruchamiając skrypt programu PowerShell asdk installer.ps1 lub z [wiersza polecenia programu PowerShell](.\asdk\asdk-deploy-powershell.md)

> [!NOTE]
> Po uruchomieniu hosta z obrazu Cloudbuilder.vhdx, istnieje możliwość konfigurowania [ustawieniami telemetrii stosu Azure](.\asdk\asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *przed* instalowanie ASDK.

**[Zainstaluj zestaw Azure stosu Development Kit (ASDK)](.\asdk\asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Przeprowadź konfiguracje po wdrożeniu

Po zainstalowaniu ASDK, istnieje kilka zalecanych kontroli po instalacji i zmian konfiguracji, które należy utworzyć.

**Narzędzia**

Instalowanie narzędzi Azure PowerShell stosu i GitHub i sprawdzić, powodzenia instalacji przy użyciu polecenia cmdlet test-AzureStack.

**Rozwiązania z tożsamością**

Do wdrożenia, który używa usługi Azure AD musisz aktywować zarówno stosu Azure administratora i dzierżawcy portali. Aktywacja zgadza się na zapewnieniu stosu Azure portalu i usługi Azure Resource Manager odpowiednie uprawnienia (wyświetlane na stronie zgoda) dla wszystkich użytkowników z katalogu.

**Wygaśnięcie hasła**

Należy zresetować zasady wygasania haseł, aby upewnić się, że hasło dla hosta development kit nie wygasa przed zakończeniem okresu oceny.

> [!NOTE]
> Istnieje również możliwość skonfigurowania [ustawieniami telemetrii stosu Azure](.\asdk\asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalowanie ASDK.

**[Zadania wdrażania POST ASDK](.\asdk\asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Zarejestrować w usłudze Azure

Należy zarejestrować stosu Azure przy użyciu platformy Azure, co pozwala [pobieranie elementów witrynę Azure marketplace](.\asdk\asdk-marketplace-item.md) stos Azure.

**[Zarejestruj stosu Azure przy użyciu platformy Azure](.\asdk\asdk-register.md)**

## <a name="next-steps"></a>Kolejne kroki

Gratulacje! Wykonując kroki tego przewodnika Szybki Start masz środowisko ASDK z [administratora](https://adminportal.local.azurestack.external) portalu i [użytkownika](https://portal.local.azurestack.external) portalu.
