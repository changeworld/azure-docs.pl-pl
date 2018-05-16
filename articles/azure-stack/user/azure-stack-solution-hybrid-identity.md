---
title: Konfiguruj tożsamość w chmurze hybrydowej z aplikacjami platformy Azure i stosu Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować tożsamość w chmurze hybrydowej z aplikacjami platformy Azure i stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5224fe4682509f92e1f18d865e5cc5afb1218ad1
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="tutorial-configure-hybrid-cloud-identity-with-azure-and-azure-stack-applications"></a>Samouczek: Skonfiguruj hybrydowych tożsamość w chmurze z aplikacjami platformy Azure i stosu Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Dostępne są dwie opcje w celu udzielenia dostępu do aplikacji Azure globalnych i stosu Azure. Kiedy stos Azure ma ciągłych połączenia z Internetem, można użyć usługi Azure Active Directory (Azure AD). Gdy stos Azure jest odłączony od Internetu, można użyć usług federacyjnych Azure Directory (AD FS). Nazwy główne usług umożliwia udzielenie dostępu do aplikacji w stosie Azure w celu wdrażania i konfiguracji za pomocą Menedżera zasobów Azure w stosie Azure. 

W tym samouczku utworzysz środowisku próbki do:

> [!div class="checklist"]
> * Ustanowienia tożsamość hybrydowa w globalnej Azure i stosu Azure
> * Pobieranie tokenu na potrzeby dostępu interfejsu API stosu Azure.

Te kroki wymagają, czy masz uprawnienia operatora stosu Azure.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Tworzenie nazwy głównej usługi dla usługi Azure AD w portalu

Jeśli została wdrożona przy użyciu usługi Azure AD jako magazynu tożsamości stosu Azure, można utworzyć nazwy główne usług, podobnie jak w przypadku usługi Azure. [Ten dokument](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) pokazuje, jak wykonać kroki za pośrednictwem portalu. Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) przed rozpoczęciem.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Tworzenie nazwy głównej usługi dla usług AD FS przy użyciu programu PowerShell

Jeśli wdrożono stosu Azure z usługami AD FS, można użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się z programu PowerShell przy użyciu tej tożsamości. [Ten dokument](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) pokazuje, jak wykonać czynności przy użyciu programu PowerShell.

## <a name="using-the-azure-stack-api"></a>Przy użyciu interfejsu API Azure stosu

[W tym samouczku](https://docs.microsoft.com/en-us/azure/azure-stack/user/azure-stack-rest-api-use) przeprowadzi Cię przez proces pobierania tokenu na potrzeby dostępu interfejsu API stosu Azure.

## <a name="connect-to-azure-stack-using-powershell"></a>Połącz stos Azure przy użyciu programu Powershell

Poniżej przedstawiono przykładowy skrypt za pomocą pojęcia nauczanych w tym dokumencie podłączania do stosu Azure.

### <a name="prerequisites"></a>Wymagania wstępne

Instalacja stosu Azure połączona z usługą Azure Active Directory z subskrypcją, w których masz dostęp. Jeśli nie masz instalacji stosu Azure, można użyć tych instrukcji do konfigurowania [Azure stosu Development Kit](https://docs.microsoft.com/en-us/azure/azure-stack/asdk/asdk-deploy).

[W tym samouczku](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-powershell-configure-quickstart) przeprowadzi Cię przez kroki niezbędne do zainstalowania programu Azure PowerShell, a następnie połączyć się z instalacją programu Azure stosu.

#### <a name="connect-to-azure-stack-using-code"></a>Połącz stos Azure przy użyciu kodu

Aby połączyć się przy użyciu kodu stosu Azure, umożliwia punkty końcowe interfejsu API usługi Azure Resource Manager Przełącz uwierzytelniania i wykres punkty końcowe instalację stosu Azure, a następnie uwierzytelniania za pomocą żądań REST. Przykładową aplikację można znaleźć [tutaj](https://github.com/shriramnat/HybridARMApplication).

> [!Note]  
Jeśli zestaw Azure SDK dla języka wyboru nie obsługuje profile interfejsu API platformy Azure, zestaw SDK może nie działać z stosu Azure. Aby dowiedzieć się więcej na temat profilów interfejsu API platformy Azure, przejdź [tutaj](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles).

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat sposobu obsługi tożsamości w stosie Azure, zobacz [architektura tożsamości stosu Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).  
 - Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
