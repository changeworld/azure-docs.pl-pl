---
title: Zrozumienie podstawowych różnic między platformą Azure i usługi Azure Stack, podczas korzystania z usług i tworzenie aplikacji | Dokumentacja firmy Microsoft
description: Co należy wiedzieć, aby używać usług lub tworzenie aplikacji dla usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 6e7d3ca2ac2d833c0a82a15aafaa3ee3e93097a2
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282597"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Najważniejsze kwestie: Za pomocą usług lub tworzenia aplikacji dla usługi Azure Stack

Przed użyciem usługi lub Twórz aplikacje dla usługi Azure Stack, ważne jest zrozumienie różnic między usługi Azure Stack i platformą Azure. W tym artykule identyfikuje Najważniejsze kwestie, korzystając z usługi Azure Stack jako środowiska programowania w chmurze hybrydowej.

## <a name="overview"></a>Przegląd

Usługa Azure Stack to hybrydowa platforma w chmurze, która pozwala na użycie usług platformy Azure z centrum danych dostawcy firmy lub usługi. Można utworzyć aplikację w usłudze Azure Stack i wdrożyć ją do usługi Azure Stack, Azure lub w chmurze hybrydowej platformy Azure.

Operator usługi Azure Stack zostanie wyświetlone powiadomienie, które usługi są dostępne do użycia i jak uzyskać pomoc techniczną. Oferują one tych usług za pośrednictwem ich dostosowane plany i oferty.

Azure zawartości technicznej przyjęto założenie, że aplikacje są opracowywane usługi platformy Azure, a nie usługi Azure Stack. Podczas tworzenia i wdrażania aplikacji do usługi Azure Stack, należy poznać niektóre podstawowe różnice:

* Usługa Azure Stack oferuje podzestaw usług i funkcji, które są dostępne na platformie Azure.
* Twój dostawca firmą lub usługą, można wybrać usługi, które mają do zaoferowania. Dostępne opcje mogą obejmować niestandardowych usług lub aplikacji. Mogą one oferować we własnej dokumentacji dostosowane.
* Należy użyć odpowiedniego Azure Stack punkty końcowe (na przykład adresy URL portalu adres i punktu końcowego usługi Azure Resource Manager).
* Należy użyć wersji programu PowerShell i interfejsu API, które są obsługiwane przez usługę Azure Stack. Korzystanie z obsługiwanych wersji zapewnia, że Twoje aplikacje działają zarówno w usłudze Azure Stack i Azure.

## <a name="cheat-sheet-high-level-differences"></a>Ściągawka: Ogólne różnice

W poniższej tabeli opisano ogólne różnice między usługi Azure Stack i platformą Azure. Zachowaj te różnice, należy pamiętać, podczas tworzenia usługi Azure Stack lub użyć usługi Azure Stack.

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

| Obszar | Azure (globalna) | Azure Stack |
| -------- | ------------- | ----------|
| Kto działa? | Microsoft | Twój dostawca organizacji lub usługi.|
| Z kim należy się skontaktować pomocy technicznej? | Microsoft | Zintegrowany system skontaktuj się z operatora infrastruktury Azure Stack (u Twojego dostawcy organizacji lub usługi) dla pomocy technicznej.<br><br>Azure Stack Development Kit pomocy technicznej, odwiedź stronę [fora firmy Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Ponieważ zestaw development kit jest środowisko wersji ewaluacyjnej, nie jest oficjalnym obsługiwane oferowane za pośrednictwem usług obsługi klienta firmy Microsoft (CSS).
| Dostępne usługi | Przejrzyj listę rzeczy, [produktów platformy Azure](https://azure.microsoft.com/services/?b=17.04b). Dostępne usługi różnić między regionami platformy Azure. | Usługa Azure Stack obsługuje podzbiór usług platformy Azure. Rzeczywiste usługi będzie zależeć od dostawcy organizacji lub usługa wybierze do zaoferowania.
| Usługa Azure Resource Manager endpoint * | https://management.azure.com | W przypadku to system zintegrowany z usługi Azure Stack Użyj punktu końcowego, który zawiera operator usługi Azure Stack.<br><br>W przypadku deweloperski, użyj: https://management.local.azurestack.external.
| Portal adresu URL * | [https://portal.azure.com](https://portal.azure.com) | System zintegrowany z usługi Azure Stack Użyj adresu URL, który zapewnia operator usługi Azure Stack.<br><br>W przypadku deweloperski, użyj: https://portal.local.azurestack.external.
| Region | Możesz wybrać region, które mają zostać wdrożone na. | To system zintegrowany z usługi Azure Stack można używać w regionie, który jest dostępny w systemie.<br><br>Dla zestawu SDK, region będzie zawsze **lokalnego**.
| Grupy zasobów | Grupa zasobów może obejmować regiony. | Zintegrowane systemy i zestaw development kit jest tylko jeden region.
|Obsługiwane przestrzenie nazw, typy zasobów i wersje interfejsu API | Najnowsze (i jego wcześniejsze wersje, które nie są jeszcze przestarzałe). | Usługa Azure Stack obsługuje określonych wersji. Zobacz [wymagania dotyczące wersji](#version-requirements) dalszej części tego artykułu.
| | |

* Jeśli masz operatorów usługi Azure Stack, zobacz [korzystanie z portalu administratora](../azure-stack-manage-portals.md) i [podstawy administracji](../azure-stack-manage-basics.md) Aby uzyskać więcej informacji.

## <a name="helpful-tools-and-best-practices"></a>Przydatnych narzędzi i najlepsze rozwiązania

Firma Microsoft zapewnia narzędzi i wskazówek ułatwiających tworzenie oprogramowania dla usługi Azure Stack.

| Zalecenie | Dokumentacja |
| -------- | ------------- |
| Zainstaluj odpowiednie narzędzia na stacji roboczej dewelopera. | - [Instalowanie programu PowerShell](azure-stack-powershell-install.md)<br>- [Pobierz narzędzia](azure-stack-powershell-download.md)<br>- [Konfigurowanie programu PowerShell](azure-stack-powershell-configure-user.md)<br>- [Zainstaluj program Visual Studio](azure-stack-install-visual-studio.md) 
| Przejrzyj informacje dotyczące następujących elementów:<br>— Zagadnienia dotyczące szablonów usługi Resource Manager platforma azure<br>-Jak znaleźć szablony szybkiego startu<br>-Użyj modułu zasad ułatwiają korzystanie z platformy Azure w celu projektowania dla usługi Azure Stack | [Opracowywanie zawartości dla usługi Azure Stack](azure-stack-developer.md) | 
| Przejrzyj i stosuj najlepsze rozwiązania dotyczące szablonów. | [Szablony szybkiego startu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Wymagania dotyczące wersji

Usługa Azure Stack obsługuje wersje programu Azure PowerShell i interfejsów API usług platformy Azure. Użyj obsługiwanych wersji, aby upewnić się, że aplikację można wdrożyć zarówno usługi Azure Stack i Azure.

Aby upewnić się, że używasz poprawnej wersji programu Azure PowerShell, należy użyć [profilami wersji interfejsu API](azure-stack-version-profiles.md). Aby określić najnowszą profilu wersji interfejsu API, którego można używać, Dowiedz się, kompilacja jest używany w usłudze Azure Stack. Te informacje można uzyskać od administratora usługi Azure Stack.

> [!NOTE]
> Jeśli używasz usługi Azure Stack Development Kit i mieć dostęp administracyjny, zobacz [ustalić bieżącą wersję](../azure-stack-updates.md#determine-the-current-version) sekcji, aby określić kompilację w usłudze Azure Stack.

Dla innych interfejsów API, uruchom następujące polecenie programu PowerShell służący do wypełniania wyjściowego przestrzenie nazw, typy zasobów i wersje interfejsu API, które są obsługiwane w ramach subskrypcji usługi Azure Stack. Uwaga może nadal być różnice na poziomie właściwości. To polecenie mogło działać, musisz mieć już [zainstalowane](azure-stack-powershell-install.md) i [skonfigurowane](azure-stack-powershell-configure-user.md) programu PowerShell dla środowiska Azure Stack. Należy również mieć subskrypcję oferty usługi Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Przykładowe dane wyjściowe (obcięty): ![Przykładowe dane wyjściowe polecenia Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowe informacje o różnice na poziomie usługi zobacz:

* [Uwagi dotyczące maszyn wirtualnych w usłudze Azure Stack](azure-stack-vm-considerations.md)
* [Zagadnienia dotyczące magazynu w usłudze Azure Stack](azure-stack-acs-differences.md)
* [Zagadnienia dotyczące sieci usługi Azure Stack](azure-stack-network-differences.md)
