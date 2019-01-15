---
title: Konfigurowanie tożsamości chmury hybrydowej przy użyciu aplikacji Azure i usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować tożsamość w chmurze hybrydowej z aplikacjami platformy Azure i usługi Azure Stack.
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
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.openlocfilehash: 7bca71f03ae820107ea0a004b4b96d53f07909ae
ms.sourcegitcommit: 70471c4febc7835e643207420e515b6436235d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2019
ms.locfileid: "54303075"
---
# <a name="tutorial-configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Samouczek: Konfiguruj tożsamość w chmurze hybrydowej dla aplikacji platformy Azure i usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Dowiedz się, jak skonfigurować tożsamość w chmurze hybrydowej dla aplikacji platformy Azure i usługi Azure Stack.

Dostępne są dwie opcje do przyznawania dostępu do aplikacji sieci globalnej platformy Azure i usługi Azure Stack.

 * Gdy usługi Azure Stack ma stałego połączenia z Internetem, można użyć usługi Azure Active Directory (Azure AD).
 * W przypadku usługi Azure Stack jest odłączony od Internetu, można użyć usług federacyjnych Azure Directory (AD FS).

Aby udzielić dostępu do aplikacji usługi Azure Stack na potrzeby wdrożenia lub konfiguracji za pomocą usługi Azure Resource Manager w usłudze Azure Stack jest użycie jednostki usługi.

W ramach tego samouczka utworzysz przykładowe środowisku:

> [!div class="checklist"]
> - Ustalenia tożsamości hybrydowej w globalnej platformy Azure i usługi Azure Stack
> - Pobieranie tokenu dostępu do interfejsu API usługi Azure Stack.

Musi mieć uprawnienia operatora usługi Azure Stack, aby uzyskać instrukcje, w tym samouczku.

> [!Tip]  
> ![pillars.png hybrydowe](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack to rozszerzenie platformy Azure. Usługa Azure Stack zapewnia elastyczność i innowacyjność chmury obliczeniowej w środowiska lokalne i włączanie jedyną chmurę hybrydową, która pozwala na tworzenie i wdrażanie aplikacji hybrydowych w dowolnym miejscu.  
> 
> Oficjalny dokument [zagadnień projektowych dotyczących aplikacji hybrydowych](https://aka.ms/hybrid-cloud-applications-pillars) przeglądy filary jakości oprogramowania (umieszczania, skalowalności, dostępności, odporności, możliwości zarządzania i zabezpieczeń) dla projektowania, wdrażania i obsługi hybrydowej aplikacje. Zagadnienia dotyczące projektowania pomaga w optymalizacji projekt aplikacji hybrydowych, minimalizując wyzwania w środowiskach produkcyjnych.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Tworzenie jednostki usługi dla usługi Azure AD w portalu

Jeśli udało Ci się wdrożyć usługę Azure Stack przy użyciu usługi Azure AD jako magazynu tożsamości, można utworzyć jednostki usługi, podobnie jak w przypadku platformy Azure. [Tworzenie jednostek usługi](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-azure-ad) artykułu dowiesz się, jak wykonać czynności opisane w portalu. Sprawdź, czy masz [wymagane uprawnienia usługi Azure AD](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) przed rozpoczęciem.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Tworzenie jednostki usługi dla usług AD FS przy użyciu programu PowerShell

Jeśli wdrożono usługę Azure Stack z usługami AD FS, można użyć programu PowerShell do tworzenia nazwy głównej usługi, Przypisz rolę dostępu i zaloguj się za pomocą programu PowerShell przy użyciu tej tożsamości. [Tworzenie jednostki usługi dla usług AD FS](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-create-service-principals#create-service-principal-for-ad-fs) dowiesz się, jak wykonać kroki wymagane przy użyciu programu PowerShell.

## <a name="using-the-azure-stack-api"></a>Za pomocą usługi Azure Stack interfejsu API

[Interfejsu API usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-rest-api-use) samouczek przeprowadzi Cię przez proces pobierania tokenu dostępu do interfejsu API usługi Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Nawiązywanie połączenia przy użyciu programu Powershell w usłudze Azure Stack

Samouczek Szybki Start [na rozpoczęcie pracy przy użyciu programu PowerShell w usłudze Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart) przeprowadzi Cię przez kroki niezbędne do zainstalowania programu Azure PowerShell, a następnie nawiązać połączenie z instalacji programu Azure Stack.

### <a name="prerequisites"></a>Wymagania wstępne

Instalacja usługi Azure Stack jest podłączony do usługi Azure Active Directory z subskrypcją, w których masz dostęp. Jeśli nie masz instalacji usługi Azure Stack, można użyć tych instrukcji do skonfigurowania [Azure Stack Development Kit](https://docs.microsoft.com/azure/azure-stack/asdk/asdk-deploy).

#### <a name="connect-to-azure-stack-using-code"></a>Nawiązywanie połączenia usługi Azure Stack przy użyciu kodu

Aby połączyć z usługą Azure Stack przy użyciu kodu, umożliwia punkty końcowe interfejsu API usługi Azure Resource Manager uzyskać uwierzytelnianie i punkty końcowe programu graph dla tej instalacji usługi Azure Stack, a następnie uwierzytelniania za pomocą żądania REST. Przykładowa aplikacja kliencka można znaleźć na [GitHub](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Jeśli zestaw Azure SDK dla języka wybranego nie obsługuje profilami interfejsu API platformy Azure, zestawu SDK mogą nie działać z usługą Azure Stack. Aby dowiedzieć się więcej o profilach interfejsu API platformy Azure, zobacz [Zarządzanie profilami wersji interfejsu API](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-version-profiles) artykułu.

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat sposobu obsługi tożsamości w usłudze Azure Stack, zobacz [architektura tożsamości dla usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-identity-architecture).
 - Aby dowiedzieć się więcej na temat wzorców chmury platformy Azure, zobacz [wzorców projektowych chmury](https://docs.microsoft.com/azure/architecture/patterns).
