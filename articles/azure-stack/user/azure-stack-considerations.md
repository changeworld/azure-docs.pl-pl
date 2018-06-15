---
title: Zrozumienie podstawowych różnic między Azure i stosu Azure, korzystając z usług i tworzenie aplikacji | Dokumentacja firmy Microsoft
description: Co należy wiedzieć, aby używać usług lub tworzenie aplikacji dla platformy Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/16/2018
ms.author: mabrigg
ms.openlocfilehash: eab208175f7eb3b761ec7266483a7cd5268198e8
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
ms.locfileid: "31512932"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Kluczowe zagadnienia dotyczące: za pomocą usług lub tworzenia aplikacji dla platformy Azure stosu

Przed użyciem usługi lub tworzyć aplikacje dla stosu Azure, trzeba poznać różnice między stosu Azure i usługi Azure. W tym artykule identyfikuje Najważniejsze kwestie, podczas używania stosu Azure jako środowiska deweloperskiego chmury hybrydowej.

## <a name="overview"></a>Przegląd

Usługa Azure Stack to platforma chmury hybrydowej, która umożliwia korzystanie z usług platformy Azure z centrum danych Twojej firmy lub dostawcy usługi. Można utworzyć aplikację na stosie Azure i wdrożyć ją do stosu Azure, Azure lub chmury hybrydowe platformy Azure.

Operatorem stosu Azure poinformuje użytkownika usługi, które są dostępne do użycia i jak uzyskać pomoc techniczną. Oferują one te usługi za pośrednictwem ich niestandardowych planów i oferty.

Azure zawartości technicznej przyjęto założenie, że aplikacje są opracowywanych dla usługi Azure zamiast stosu Azure. Podczas tworzenia i wdrażania aplikacji na stosie Azure, należy poznać niektóre podstawowe różnice:

* Stos Azure oferuje podzbiór usługi i funkcje, które są dostępne w systemie Azure.
* Twój dostawca firmy lub usługi można wybrać usługi, które chcą oferować. Dostępne opcje mogą obejmować dostosowane usług lub aplikacji. Mogą one oferować własnych dostosowanych dokumentacji.
* Należy użyć odpowiedniego punkty końcowe platformy Azure dotyczące stosu (na przykład adresy URL portalu adres i punktu końcowego usługi Azure Resource Manager).
* Należy użyć wersji programu PowerShell i interfejs API, które są obsługiwane przez stos Azure. Korzystanie z obsługiwanych wersji zapewnia, czy aplikacje będą działać w stosie Azure i usługi Azure.

## <a name="cheat-sheet-high-level-differences"></a>Ściągawka: Ogólne różnice

W poniższej tabeli opisano ogólne różnice między stosu Azure i usługi Azure. Zachowaj te różnice pamiętać podczas opracowywania stosu Azure lub korzystania z usług Azure stosu.
*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

| Obszar | Azure (globalna) | Azure Stack |
| -------- | ------------- | ----------|
| Kto działa? | Microsoft | Twój dostawca organizacji lub usługi.|
| Który zamierzasz zgłosić obsługi? | Microsoft | Dla zintegrowany system skontaktuj się z operatorem stosu Azure (w dostawcy organizacji lub usługi) dla pomocy technicznej.<br><br>Azure stosu Development Kit pomocy technicznej, odwiedź stronę [fora Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Ponieważ zestaw deweloperski środowiska do oceny, nie jest oficjalną obsługiwane oferowane za pośrednictwem usług obsługi klienta firmy Microsoft (CSS).
| Dostępne usługi | Lista [produkty Azure](https://azure.microsoft.com/services/?b=17.04b). Dostępne usługi zależą od regionu systemu Azure. | Stos Azure obsługuje usług platformy Azure. Rzeczywiste usługi różni się zależnie od dostawcy organizacji lub usługa wybierze na ofertę.
| Usługa Azure Resource Manager punktu końcowego * | https://management.azure.com | Dla systemu Azure stosu zintegrowane użycia punktu końcowego zapewnianej przez operatora sieci Azure stosu.<br><br>Dla zestawu SDK należy użyć: https://management.local.azurestack.external
| Portal adresu URL * | [https://portal.azure.com](https://portal.azure.com) | System Azure stosu zintegrowane przejdź do adresu URL, zapewnianej przez operatora sieci Azure stosu.<br><br>Dla zestawu SDK należy użyć: https://portal.local.azurestack.external
| Region | Możesz wybrać regionu, którego chcesz wdrożyć. | System Azure stosu zintegrowane Użyj regionu, która jest dostępna w systemie.<br><br>Dla zestawu SDK, zawsze będą region **lokalnego**.
| Grupy zasobów | Grupa zasobów może obejmować regionów. | Dla zintegrowanych systemów i zestaw deweloperski istnieje tylko jeden region.
|Obsługiwanych przestrzeniach nazw, typów zasobów i wersje interfejsu API | Najnowsze (i jego wcześniejsze wersje, które nie są jeszcze przestarzałe). | Stos Azure obsługuje określonych wersji. Zobacz sekcję "Wymagania dotyczące wersji" tego artykułu.
| | |

* Jeśli jesteś operator stosu Azure, zobacz [za pomocą portalu administratora](../azure-stack-manage-portals.md) i [podstawy administracji](../azure-stack-manage-basics.md) Aby uzyskać więcej informacji.

## <a name="helpful-tools-and-best-practices"></a>Przydatne narzędzia i najlepsze rozwiązania
 
 Firma Microsoft zapewnia kilka narzędzi i wskazówek, która ułatwia tworzenie stosu Azure.

| Zalecenie | Dokumentacja | 
| -------- | ------------- | 
| Zainstaluj poprawne narzędzia na stacji roboczej developer. | - [Instalowanie programu PowerShell](azure-stack-powershell-install.md)<br>- [Pobierz narzędzia](azure-stack-powershell-download.md)<br>- [Konfigurowanie programu PowerShell](azure-stack-powershell-configure-user.md)<br>- [Zainstaluj program Visual Studio](azure-stack-install-visual-studio.md) 
| Przejrzyj informacje dotyczące następujących elementów:<br>-Zagadnienia dotyczące szablonów azure Resource Manager<br>-Porady szablonów Szybki Start<br>— Umożliwia modułu zasad pomagają w używaniu platformy Azure na stosie Azure | [Opracowywanie zawartości dla usługi Azure Stack](azure-stack-developer.md) | 
| Przejrzyj i stosuj najlepsze rozwiązania dla szablonów. | [Szablony szybkiego startu usługi Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Wymagania dotyczące wersji

Stos Azure obsługuje określonych wersji programu Azure PowerShell i interfejsów API usługi Azure. Obsługiwane wersje umożliwia upewnij się, że aplikację można wdrożyć zarówno stos Azure i na platformie Azure.

Aby upewnić się, że używasz poprawnej wersji programu Azure PowerShell, użyj [profile w wersji interfejsu API](azure-stack-version-profiles.md). Aby sprawdzić najnowsze profil wersji interfejsu API, który można użyć, Dowiedz się kompilacji stosu Azure używasz. Te informacje można uzyskać od administratora stosu Azure.

>[!NOTE]
 Jeśli używasz Development Kit stosu Azure i mają dostęp administracyjny, zobacz sekcję "Sprawdzić bieżącą wersję" [zarządzanie aktualizacjami](https://docs.microsoft.com/azure/azure-stack/azure-stack-updates#determine-the-current-version) ustalenie kompilacji stosu Azure.

Dla innych interfejsów API uruchom następujące polecenie programu PowerShell, aby dane wyjściowe obszary nazw, typów zasobów i wersji interfejsu API, które są obsługiwane w ramach subskrypcji Azure stosu. Warto zauważyć, że może nadal być różnice na poziomie właściwości. (To polecenie mogło działać, musisz mieć już [zainstalowane](azure-stack-powershell-install.md) i [skonfigurowane](azure-stack-powershell-configure-user.md) środowiska PowerShell dla środowiska Azure stosu. Użytkownik musi mieć również subskrypcji z ofertą Azure stosu.)

 ```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Przykład danych wyjściowych (obcięty): ![przykładowe dane wyjściowe polecenia Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać bardziej szczegółowe informacje na temat różnic na poziomie usługi zobacz:

* [Zagadnienia dotyczące maszyn wirtualnych Azure stosu](azure-stack-vm-considerations.md)
* [Zagadnienia dotyczące magazynu w stosie Azure](azure-stack-acs-differences.md)
* [Zagadnienia dotyczące sieci Azure stosu](azure-stack-network-differences.md)
