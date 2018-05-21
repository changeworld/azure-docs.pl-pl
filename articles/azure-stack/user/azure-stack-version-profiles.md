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
ms.openlocfilehash: e568ffd2c3adb97ed0b727b85e7888fb797db1f9
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2018
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Zarządzanie profilami wersji interfejsu API Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Profile interfejsu API Określ dostawcy zasobów platformy Azure i wersja interfejsu API Azure REST punktów końcowych. Można tworzyć niestandardowe klientów w różnych językach przy użyciu interfejsu API profilów. Każdy klient używa profil interfejsu API można skontaktować się z dostawcy zasobów poprawne i wersja interfejsu API Azure stosu.

Możesz utworzyć aplikację do pracy z dostawców zasobów platformy Azure, bez konieczności przystąpić dokładnie wersji każdego z dostawców zasobów interfejsu API jest zgodny z stosu Azure. Po prostu Dopasuj aplikacji do profilu; zestaw SDK umożliwia przywrócenie do prawidłowej wersji interfejsu API.

Ten temat ułatwia:

 - Zrozumienie profile interfejsu API Azure stosu.
 - Dowiedz się, jak można użyć interfejsu API profile do opracowywania rozwiązań.
 - Zobacz, gdzie można znaleźć wskazówki dotyczące kodu.

## <a name="summary-of-api-profiles"></a>Podsumowanie profilów interfejsu API

- Profile interfejsu API są używane do reprezentowania zestaw dostawców zasobów platformy Azure i ich wersje interfejsu API.
- Profile interfejsu API zostały utworzone dla deweloperów, aby mogli tworzyć szablony między wieloma chmury Azure. Profile są przeznaczone do spełnia potrzeby interfejs zgodny i stabilna.
- Profile są wydawane cztery razy w roku.
- Używane są trzy profile konwencji nazewnictwa:
    - **latest**  
        Zawiera najnowsze wersje interfejsu API wydane w ramach globalnego Azure.
    - **yyyy-mm-dd-hybrid**  
    Wydawane w okresach organizowanych, to wydanie koncentruje się na spójność i stabilność przez wiele chmur. Optymalne zgodności stosu Azure jest przeznaczony dla tego profilu.
    - **rrrr mm-dd profilu** znajduje się pomiędzy optymalną stabilności i najnowszych funkcji.

### <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Profile interfejsu API platformy Azure i zgodności Azure stosu

Najnowsze profile interfejsu API Azure nie są zgodne z stosu Azure. Można użyć następujących konwencji nazewnictwa do identyfikowania profile dla rozwiązań stosu Azure.

**najnowsze**  
Ten profil jest najbardziej aktualne wersje interfejsu API w Azure globalne, które nie będą działać w stosie Azure. **Najnowsze** ma największą liczbę zmian, które psuły. Profil umieszcza Odłóż stabilności i zgodności z innych chmur. Jeśli użytkownik próbuje użyć najbardziej aktualne wersje interfejsu API, **najnowsze** jest profil, należy użyć.

**Rrrr mm-dd hybrydowego**  
Ten profil jest zwalniany marca i września co roku. Ten profil ma optymalną stabilności i zgodności z różnych chmury. **Rrrr mm-dd hybrydowego** zaprojektowano pod kątem globalne Azure i stosu Azure. Wersje interfejsu API Azure wymienione w tym profilu będą takie same jak te, które są wyświetlane na stosie Azure. Tworzenie kodu dla hybrydowych rozwiązań w chmurze, można użyć tego profilu.

**rrrr mm-dd profilu**  
Ten profil jest wydane dla globalnych Azure w czerwcu i grudnia. Ten profil nie będzie działać względem stosu Azure; Zazwyczaj będzie wiele zmian podziału. Mimo że znajduje się pomiędzy optymalną stabilności i najnowszych funkcji, to różnica między **najnowsze** i ten profil jest to, że **najnowsze** zawsze składają się z najnowszej wersji interfejsu API, niezależnie od tego, kiedy interfejsu API została wydana. Na przykład, jeśli nowa wersja interfejsu API jest tworzony dla interfejsu API obliczeniowe jutro, ta wersja interfejsu API będzie wyświetlane w w **najnowsze**, ale nie **rrrr mm-dd profilu** ponieważ istnieje już w tym profilu.  **rrrr mm-dd profilu** obejmuje najnowsze wersje wydane przed czerwca lub przed grudnia.

## <a name="azure-resource-manager-api-profiles"></a>Profile interfejs API Menedżera zasobów Azure

Azure stosu nie używać najnowszej wersji wersji interfejsu API w globalnej Azure. Podczas tworzenia rozwiązania, należy znaleźć wersji interfejsu API dla każdego dostawcy zasobów platformy Azure zgodnego z stosu Azure.

Zamiast niż badania każdy dostawca zasobów i obsługiwane przez stos Azure wersji, można użyć profilu interfejsu API. Profil określa zestaw dostawców zasobów i wersje interfejsu API. Zestaw SDK lub skompilowane przy użyciu zestawu SDK narzędzia zostaną przywrócone do wersji interfejsu api docelowej określona w profilu. Przy użyciu profilów interfejsu API można określić wersji profilu, która ma zastosowanie do całego szablonu, a następnie w czasie wykonywania, usługi Azure Resource Manager wybierze właściwej wersji zasobu.

Interfejs API profile pracować z narzędziami, które używają usługi Azure Resource Manager, takich jak programu PowerShell, interfejsu wiersza polecenia Azure, kod w zestawie SDK i programu Microsoft Visual Studio. Profile można użyć narzędzia i zestawy SDK można odczytać wersji modułów i biblioteki do uwzględnienia podczas tworzenia aplikacji.

**Programowanie scenariusz użycia profilu**  
Załóżmy, że używasz programu PowerShell, aby utworzyć:

* Konto magazynu, który używa **Microsoft.Storage** dostawcy zasobów, która obsługuje interfejs api-version 2016-03-30.
* Maszyna wirtualna, która używa **Microsoft.Compute** dostawcy zasobów, która obsługuje interfejs api-version 2015-12-01.

Zamiast Znajdowanie i instalowanie modułów programu PowerShell, które obsługuje wersje interfejsu api dla magazynu i zasobów obliczeniowych, mogą używać profilu. Użyj polecenia cmdlet ** instalacji profilu * profilename *** i programu PowerShell ładuje poprawną wersję modułów.

Podobnie jeśli zestaw SDK Python używany do tworzenia aplikacji opartych na języku Python, mogą używać profilu. Zestaw SDK ładuje poprawne modułów dla dostawców zasobów, które zostały określone w skrypcie.

Deweloperzy mogą skupić się na zapisywanie rozwiązania. Mogą używać profilu, wiedząc, że kod będzie działać we wszystkich chmur, które obsługują profilu.

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
