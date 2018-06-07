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
ms.date: 05/22/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: a57afb4a90da5877879afddc35545e0bfef622a7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808166"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Samouczek: Skonfiguruj hybrydowych tożsamość w chmurze dla platformy Azure i Azure stosu aplikacji

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Dowiedz się, jak skonfigurować tożsamość w chmurze hybrydowej aplikacji Azure i stosu Azure.

Masz dwie opcje za udzielanie dostępu do aplikacji w Azure globalnych i stosu Azure.

 * Kiedy stos Azure ma stałego połączenia z Internetem, można użyć usługi Azure Active Directory (Azure AD).
 * Gdy stos Azure jest odłączony od Internetu, można użyć usług federacyjnych Azure Directory (AD FS).

Nazwy główne usług umożliwia udzielenie dostępu do aplikacji stosu Azure na potrzeby wdrażania lub konfiguracji przy użyciu usługi Azure Resource Manager w stosie Azure.

W tym samouczku utworzysz środowisku próbki do:

> [!div class="checklist"]
> * Ustanowienia tożsamość hybrydowa w globalnej Azure i stosu Azure
> * Pobierz token, aby uzyskać dostępu do interfejsu API Azure stosu.

Musi mieć uprawnienia operatora stosu Azure instrukcje w tym samouczku.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Tworzenie nazwy głównej usługi dla usługi Azure AD w portalu

Jeśli została wdrożona przy użyciu usługi Azure AD jako magazynu tożsamości stosu Azure, można utworzyć nazwy główne usług, podobnie jak w przypadku usługi Azure. [Tworzenia nazwy główne usług](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artykule opisano, jak wykonać kroki za pośrednictwem portalu. Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) przed rozpoczęciem.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Tworzenie nazwy głównej usługi dla usług AD FS przy użyciu programu PowerShell

Jeśli wdrożono stosu Azure z usługami AD FS, można użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się z programu PowerShell przy użyciu tej tożsamości. [Tworzenie nazwy głównej usługi dla usług AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) pokazuje, jak wykonać kroki wymagane przy użyciu programu PowerShell.

## <a name="using-the-azure-stack-api"></a>Przy użyciu interfejsu API Azure stosu

[Interfejsu API Azure stosu](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) samouczek przeprowadzi Cię przez proces pobierania tokenu na potrzeby dostępu interfejsu API stosu Azure.

## <a name="connect-to-azure-stack-using-powershell"></a>Połącz stos Azure przy użyciu programu Powershell

Procedury szybkiego startu [można pobrać i uruchomić przy użyciu programu PowerShell w stosie Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) przeprowadzi Cię przez kroki niezbędne do zainstalowania programu Azure PowerShell, a następnie połączyć się z instalacją programu Azure stosu.

### <a name="prerequisites"></a>Wymagania wstępne

Instalacja stosu Azure połączona z usługą Azure Active Directory z subskrypcją, w których masz dostęp. Jeśli nie masz instalacji stosu Azure, można użyć tych instrukcji do konfigurowania [Azure stosu Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Połącz stos Azure przy użyciu kodu

Aby połączyć się przy użyciu kodu stosu Azure, umożliwia punkty końcowe interfejsu API usługi Azure Resource Manager Przełącz uwierzytelniania i wykres punkty końcowe instalację stosu Azure, a następnie uwierzytelniania za pomocą żądań REST. Przykładowa aplikacja klienta można znaleźć na [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Jeśli zestaw Azure SDK dla języka wyboru nie obsługuje profile interfejsu API platformy Azure, zestaw SDK może nie działać z stosu Azure. Aby dowiedzieć się więcej na temat profilów interfejsu API platformy Azure, zobacz [zarządzania profilami wersji interfejsu API](https://docs.microsoft.com/da-dk/azure/azure-stack/user/azure-stack-version-profiles) artykułu.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat sposobu obsługi tożsamości w stosie Azure, zobacz [architektura tożsamości stosu Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Aby dowiedzieć się więcej na temat wzorców chmury Azure, zobacz [wzorcach projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
