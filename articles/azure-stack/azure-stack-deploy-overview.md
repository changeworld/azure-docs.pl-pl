---
title: Ocena usługi Azure Stack Development Kit | Dokumentacja firmy Microsoft
description: Informacje o sposobie wdrażania usługi Azure Stack Development Kit do celów oceny.
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
ms.date: 11/05/2018
ms.author: jeffgilb
ms.custom: mvc
ms.reviewer: misainat
ms.openlocfilehash: 44fed3311234e1a64cb46c3403f39a9e269d189b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52956930"
---
# <a name="quickstart-evaluate-the-azure-stack-development-kit"></a>Szybki Start: oceny usługi Azure Stack Development Kit

[Usługi Azure Stack Development Kit (ASDK)](./asdk/asdk-what-is.md) jest środowiskiem środowisk testowych i programistycznych, które można wdrożyć do oceny i zademonstrowania funkcji usługi Azure Stack i usług. Aby rozpocząć pracę z ASDK, należy przygotować hosta sprzęt komputerowy, a następnie uruchom kilka skryptów (instalacja zajmuje kilka godzin). Po tym można Zaloguj się do portali administratora lub użytkownika Aby rozpocząć korzystanie z usługi Azure Stack.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="asdk-host-computer-requirements"></a>Wymagania dotyczące komputera hosta ASDK

Przed zainstalowaniem ASDK, należy przygotować komputer, który będzie hostem deweloperski. Na komputerze deweloperskim zestaw host musi spełniać sprzętu, oprogramowania i wymagania dotyczące sieci opisane w  **[przejrzyj zagadnienia dotyczące planowania wdrożenia ASDK](./asdk/asdk-deploy-considerations.md)**.

> [!TIP]
> Możesz użyć [wymagania dotyczące wdrażania usługi Azure Stack Sprawdź narzędzie](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) po zainstalowaniu systemu operacyjnego na komputerze deweloperskim zestaw hosta aby upewnić się, że sprzęt spełnia wszystkie wymagania.

### <a name="account-requirements"></a>Wymagania dotyczące konta

Należy również wybrać między za pomocą usługi Azure Active Directory (Azure AD) lub usługi Active Directory Federation Services (AD FS) jako rozwiązanie tożsamości dla danego wdrożenia. Przejrzyj wymagania dotyczące konta w  **[zagadnienia dotyczące wdrażania](./asdk/asdk-deploy-considerations.md#account-requirements)**

## <a name="download-and-extract-the-deployment-package"></a>Pobierz i Wyodrębnij pakiet wdrożeniowy

Po przygotowaniu komputera-hosta kit programowania, Pobierz i Wyodrębnij pakiet wdrożeniowy ASDK. Pakiet wdrożeniowy zawiera pliki instalacyjne usługi Azure Stack i pliku Cloudbuilder.vhdx, czyli wirtualnego dysku twardego (VHD) z rozruchowego systemu operacyjnego.

Możesz pobrać pakiet wdrożeniowy na hoście kit rozwoju lub na innym komputerze. Pliki wdrożenia wyodrębnione potrwać 60 GB wolnego miejsca na dysku, dzięki czemu za pomocą innego komputera może pomóc zmniejszyć wymagania dotyczące magazynu na hoście development kit.

**[Pobierz i Wyodrębnij usługi Azure Stack Development Kit (ASDK)](./asdk/asdk-download.md)**

## <a name="prepare-the-host-computer"></a>Przygotuj komputer-host

Przed zainstalowaniem ASDK należy przygotować środowisko hosta, a system jest skonfigurowany do uruchamiania z zestawu SDK wirtualnego dysku twardego. Po ponownym uruchomieniu hosta uruchamia się z CloudBuilder.vhdx i możesz przystąpić do wdrażania ASDK.

**[Przygotuj komputer-host ASDK](./asdk/asdk-prepare-host.md)**

## <a name="install-the-asdk-on-the-host-computer"></a>Zainstaluj ASDK na komputerze-hoście

Po rozruchu komputera hosta z wirtualnego dysku twardego można wdrożyć zestaw development kit Cloudbuilder środowiska wirtualnego. Możesz wdrożyć ASDK przy użyciu graficznego interfejsu użytkownika (GUI), pod warunkiem, uruchamiając skrypt programu PowerShell asdk installer.ps1 lub z [wiersza polecenia programu PowerShell](./asdk/asdk-deploy-powershell.md)

> [!NOTE]
> Po uruchomieniu hosta za pomocą obrazu Cloudbuilder.vhdx, istnieje możliwość konfigurowania [ustawieniami telemetrii usługi Azure Stack](./asdk/asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *przed* instalowanie ASDK.

**[Zainstaluj zestaw Azure Stack Development Kit (ASDK)](./asdk/asdk-install.md)**

## <a name="perform-post-deployment-configurations"></a>Przeprowadź konfiguracje po wdrożeniu

Po zainstalowaniu ASDK, istnieje kilka zalecanych kontroli po instalacji i zmian konfiguracji, które powinny być.

**Narzędzia**

Instalowanie narzędzi usługi Azure Stack PowerShell i serwisu GitHub i sprawdź powodzenia instalacji przy użyciu polecenia cmdlet test-AzureStack.

**Rozwiązania z tożsamością**

Dla wdrożenia, które używa usługi Azure AD możesz aktywować zarówno usługi Azure Stack administratora i dzierżawy portali. Aktywacja wyraża zgodę dające w portalu Azure Stack i Azure Resource Manager odpowiednie uprawnienia (na liście na stronie zgoda) do wszystkich użytkowników katalogu.

**Wygaśnięcie hasła**

Należy zresetować zasady wygasania haseł, aby upewnić się, że hasło dla hosta kit rozwoju nie wygasa przed zakończeniem okresu Twojej oceny.

> [!NOTE]
> Istnieje również możliwość skonfigurowania [ustawieniami telemetrii usługi Azure Stack](./asdk/asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *po* instalowanie ASDK.

**[Wpis ASDK zadania związane z wdrażaniem](./asdk/asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Rejestrowania na platformie Azure

Należy zarejestrować usługi Azure Stack przy użyciu platformy Azure, aby można było [pobieranie elementów portalu Azure marketplace](./asdk/asdk-marketplace-item.md) do usługi Azure Stack.

**[Rejestrowania usługi Azure Stack na platformie Azure](./asdk/asdk-register.md)**

## <a name="next-steps"></a>Kolejne kroki

Gratulacje! Wykonując czynności opisane w tym przewodniku Szybki Start masz środowisko ASDK z [administratora](https://adminportal.local.azurestack.external) portalu i [użytkownika](https://portal.local.azurestack.external) portalu.
