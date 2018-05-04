---
title: Zarządzanie profilami wersji interfejsu API w stosie Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat profilów wersji interfejsu API Azure stosu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: mabrigg
ms.reviewer: sijuman
ms.openlocfilehash: 1ea65c9c1f69c8eec77eb498a5963b0d77ce57f1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Zarządzanie profilami wersji interfejsu API Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Profile interfejsu API Określ dostawcy zasobów platformy Azure i wersja interfejsu API Azure REST punktów końcowych. Można tworzyć niestandardowe klientów w różnych językach przy użyciu interfejsu API profilów. Każdy klient używa profil interfejsu API można skontaktować się z dostawcy zasobów prawo i wersja interfejsu API Azure stosu. 

Możesz utworzyć aplikację do pracy z dostawców zasobów platformy Azure, bez konieczności przystąpić dokładnie wersji każdego z dostawców zasobów interfejsu API jest zgodny z stosu Azure. Po prostu Dopasuj aplikacji do profilu; zestaw SDK wraca do prawej wersji interfejsu API.


Ten temat ułatwia:
 - Zrozumienie profile interfejsu API Azure stosu.
 - Jak można użyć interfejsu API profile do opracowywania rozwiązań.
 - Gdzie szukać wskazówki dotyczące kodu.

## <a name="summary-of-api-profiles"></a>Podsumowanie profilów interfejsu API

- Profile interfejsu API są używane do reprezentowania zestaw dostawców zasobów platformy Azure i ich wersje interfejsu API.
- Profile interfejsu API zostały utworzone dla deweloperów do tworzenia szablonów w wielu chmury Azure. Są one przeznaczone do spełnienia potrzeb zgodny i stabilnego interfejsu.
- Profile są wydawane cztery razy w roku.
- Są trzy profile konwencji nazewnictwa:
    - **latest**  
        Najnowsze wersje interfejsu API wydane na platformie Azure.
    - **yyyy-mm-dd-hybrid**  
    Wydawane w okresach organizowanych, to wydanie koncentruje się na spójność i stabilność przez wiele chmur. Optymalne zgodności stosu Azure jest przeznaczony dla tego profilu. 
    - **rrrr mm-dd profilu**  
    Znajduje się pomiędzy optymalną stabilności i najnowszych funkcji.

### <a name="api-profiles-and-azure-stack-compatibility"></a>Profile interfejsu API i zgodności platformy Azure stosu

Najnowsze profile interfejsu API nie są zgodne z stosu Azure. Konwencje nazewnictwa pomoże zidentyfikować profile do użycia w rozwiązań stosu Azure.

**najnowsze**  
Ten profil jest najbardziej aktualne wersje interfejsu API w Azure globalne, które nie będzie działać w stosie Azure. Ten profil ma największą liczbę zmian, które psuły. Profil umieszcza Odłóż stabilności i zgodności z innych chmur. Jeśli chcesz korzystać z najbardziej aktualne wersje interfejsu API, jest profil, które powinny być używane.

**Rrrr mm-dd hybrydowego**  
Ten profil jest wydany marca i września co roku. Ten profil ma optymalną stabilności i zgodności z różnych chmury. Ten profil zaprojektowano pod kątem Azure globalnych i stosu Azure. Wersje interfejsu API Azure wymienione w tym profilu będą takie same jak te, które są wyświetlane na stosie Azure. Tworzenie kodu dla hybrydowych rozwiązań w chmurze, można użyć tego profilu.

**rrrr mm-dd profilu**  
Ten profil jest wydane dla globalnych Azure w czerwcu i grudnia. Ten profil nie będzie działać względem stosu Azure; będzie wiele zmian podziału. Znajduje się on za optymalne stabilności i najnowszych funkcji, różnica między r i ten profil jest czy najnowsza zawsze będzie składać się z najnowszej wersji interfejsu API, niezależnie od tego, kiedy został zwolniony interfejsu API. Nowa wersja interfejsu API jest tworzony jutro obliczeniowe interfejsu API, tej wersji interfejsu API będzie wyświetlane w najnowszej profilu, ale nie znajduje się w profilu rrrr mm-dd profilu jako ten profil zostanie nawiązane wcześniej. Obejmuje ona najbardziej aktualne wersje wydanych przed czerwca lub grudnia.

## <a name="azure-resource-manager-api-profiles"></a>Profile interfejs API Menedżera zasobów Azure

Azure stosu nie używać najnowszej wersji wersji interfejsu API w globalnej Azure. Podczas tworzenia własnych rozwiązania, należy znaleźć wersja interfejsu API dla każdego dostawcy zasobów platformy Azure zgodnego z stosu Azure.

Zamiast niż badania każdy dostawca zasobów i obsługiwane przez stos Azure wersji, można użyć profilu interfejsu API. Profil określa zestaw dostawców zasobów i wersje interfejsu API. Zestaw SDK lub skompilowane przy użyciu zestawu SDK narzędzia zostaną przywrócone do wersji interfejsu api docelowej określona w profilu. Przy użyciu profilów interfejsu API można określić wersji profilu, która ma zastosowanie do całego szablonu, a następnie w czasie wykonywania, usługi Azure Resource Manager wybierze właściwej wersji zasobu.

Interfejs API profile pracować z narzędziami, które używają usługi Azure Resource Manager, takich jak programu PowerShell, interfejsu wiersza polecenia Azure, kod w zestawie SDK i programu Microsoft Visual Studio. Profile można użyć narzędzia i zestawy SDK można odczytać wersji modułów i biblioteki do uwzględnienia podczas tworzenia aplikacji.

Na przykład użyj programu PowerShell do utworzenia magazynu konta przy użyciu **Microsoft.Storage** dostawcy zasobów, która obsługuje interfejs api-version 2016-03-30 i maszyny Wirtualnej za pomocą dostawcy zasobów Microsoft.Compute z interfejsu api-version 2015-12-01 , należy wyszukać obsługujący moduł PowerShell 2016-03-30 dla magazynu i który moduł obsługuje 2015-02-01 obliczania i zainstaluj je. Zamiast tego można użyć profilu. Użyj polecenia cmdlet ** instalacji profilu * profilename *** i programu PowerShell ładuje właściwej wersji modułów.

Podobnie podczas tworzenia aplikacji opartych na języku Python za pomocą zestawu SDK Python, można określić profil. Zestaw SDK ładuje prawo modułów dla dostawców zasobów, określonych w skrypcie.

Deweloperzy mogą skupić się na zapisywanie rozwiązania. Zamiast badanie, której wersji interfejsu api, dostawca zasobów i które chmurze współpracuje ze sobą, Użyj profilu i dowiedzieć się, że kod będzie działać we wszystkich chmur, które obsługują tego profilu.

## <a name="api-profile-code-samples"></a>Przykłady kodu interfejsu API profilu

Przykłady kodu, aby pomóc w zintegrowaniu rozwiązania w języku preferowanym stosu Azure przy użyciu profilów można znaleźć. Obecnie wskazówki i przykłady można znaleźć w następujących językach:

- **Program PowerShell**  
Można użyć **AzureRM.Bootstrapper** modułu dostępne za pośrednictwem galerii programu PowerShell, można pobrać poleceń cmdlet programu PowerShell wymaganych do pracy z profilami wersji interfejsu API. Aby uzyskać informacje, zobacz [profile w wersji Użyj interfejsu API środowiska PowerShell](azure-stack-version-profiles-powershell.md).
- **Interfejs wiersza polecenia platformy Azure 2.0**  
Można aktualizować konfiguracji środowiska, aby użyć określonego profilu wersji interfejsu API Azure stosu. Aby uzyskać informacje, zobacz [wersji profilów Użyj interfejsu API Azure CLI 2.0](azure-stack-version-profiles-azurecli2.md).
- **GO**  
W zestawie SDK Przejdź profil jest kombinacją różnych typów zasobów z różnymi wersjami z różnych usług. Profile są dostępne w obszarze Profile / ścieżki z ich wersji w **RRRR-MM-DD** format. Aby uzyskać informacje, zobacz [Użyj interfejsu API w wersji profilów dla Przejdź](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby zestawu SDK dla usługi Azure stosu Resource Manager udostępnia narzędzia umożliwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci wirtualnych i magazynu w języku Ruby. Aby uzyskać informacje, zobacz [profile w wersji interfejsu API używany z Ruby](azure-stack-version-profiles-ruby.md)

## <a name="next-steps"></a>Kolejne kroki
* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika Azure stosu](azure-stack-powershell-configure-user.md)
* [Szczegółowe informacje o wersji interfejsu API dostawcy zasobów obsługiwanych przez profile](azure-stack-profiles-azure-resource-manager-versions.md).
