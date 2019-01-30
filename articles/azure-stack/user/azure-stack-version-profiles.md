---
title: Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Zapoznaj się z profilami wersji interfejsu API w usłudze Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 28ff7c9f6ca5fc6365b3fe1b9a91d2159c8b3f48
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247616"
---
# <a name="manage-api-version-profiles-in-azure-stack"></a>Zarządzanie profilami wersji interfejsu API w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Profile interfejsu API Określ dostawca zasobów platformy Azure i wersja interfejsu API dla punkty końcowe REST platformy Azure. Można tworzyć niestandardowych klientów w różnych językach, przy użyciu profilów interfejsu API. Każdy klient korzysta z profilu usługi interfejsu API do kontaktowania się z dostawcy zasobu jest prawidłowy i wersja interfejsu API dla usługi Azure Stack.

Możesz utworzyć aplikację do pracy z dostawcami zasobów platformy Azure bez konieczności sortowania się dokładnie wersję każdy dostawca zasobów interfejsu API jest zgodna z usługą Azure Stack. Wyrównaj tylko aplikacji z profilem; zestaw SDK powraca do poprawnej wersji interfejsu API.

Ten temat ułatwia:

 - Dowiedz się, profilami interfejsu API dla usługi Azure Stack.
 - Dowiedz się, jak można użyć profilami interfejsu API do tworzenia rozwiązań usługi.
 - Zobacz, gdzie można znaleźć ze wskazówek specyficznych dla kodu.

## <a name="summary-of-api-profiles"></a>Podsumowanie profilami interfejsu API

- Profile interfejsu API są używane do reprezentowania zestawu dostawcy zasobów platformy Azure i ich wersje interfejsu API.
- Profile interfejsu API zostały utworzone w celu utworzenia szablonów dla wielu chmur platformy Azure. Profile udostępniają interfejs zgodny i stabilne.
- Profile są wydawane cztery razy w roku.
- Używane są trzy profile konwencji nazewnictwa:
    - **latest**  
        Zawiera najnowsze wersje interfejsu API, wydanej w globalnej platformy Azure.
    - **yyyy-mm-dd-hybrid**  
    Wydana dwa razy w roku, ta wersja koncentruje się na spójność i stabilność między wieloma chmurami. Ten profil jest przeznaczony dla optymalnej zgodności usługi Azure Stack.
    - **rrrr mm-dd-profile** optymalne stabilność i najnowszych funkcji.

## <a name="azure-api-profiles-and-azure-stack-compatibility"></a>Profilami interfejsu API platformy Azure i zgodności usługi Azure Stack

Najnowsze profile interfejsu API platformy Azure nie są zgodne z usługą Azure Stack. Można użyć następujących konwencji nazewnictwa do identyfikowania profile dla rozwiązań usługi Azure Stack.

**najnowsze**  
Ten profil jest najbardziej aktualne wersje interfejsu API w globalnej platformy Azure, które nie działają w usłudze Azure Stack. **Najnowsze** ma największą liczbę przełomowe zmiany. Profil, który umieszcza specjalnie stabilności i zgodności z innych chmur. Jeśli próbujesz użyć najbardziej aktualne wersje interfejsu API **najnowsze** jest profil, należy użyć.

**Yyyy-mm-dd-hybrid**  
Ten profil jest zwalniany marca i września co roku. Posiada optymalne stabilności i zgodności z różnych chmur. **Rrrr mm-dd hybrydowego** zaprojektowano pod kątem globalnej platformy Azure i usługi Azure Stack. Wersje interfejsu API platformy Azure wymienionych w tym profilu będą takie same jak te, które są wyświetlane w usłudze Azure Stack. Ten profil umożliwia opracowywanie kodu dla rozwiązań w chmurze hybrydowej.

**yyyy-mm-dd-profile**  
Ten profil jest zwalniany globalnej platformy Azure w czerwcu i grudnia. Nie działa z usługą Azure Stack; Zazwyczaj będzie istnieć wiele przełomowych zmianach. Mimo że bilansuje optymalne stabilności i najnowszych funkcji, a różnica między **najnowsze** i ten profil jest fakt, że **najnowsze** zawsze zawiera najnowsze wersje interfejsu API, niezależnie od tego, kiedy został interfejsu API wydana. Na przykład, jeśli nowa wersja interfejsu API jest tworzona dla interfejsu API obliczenia jutro, ta wersja interfejsu API znajduje się w **najnowsze**, ale nie **rrrr mm-dd-profile** profilu, ponieważ istnieje już w tym profilu. **rrrr mm-dd-profile** obejmuje najbardziej aktualne wersje wydane przed czerwca lub przed grudnia.

## <a name="azure-resource-manager-api-profiles"></a>Usługa Azure profilami interfejsu API usługi Resource Manager

Usługa Azure Stack nie używa najnowszą wersję wersji interfejsu API w globalnej platformy Azure. Podczas tworzenia rozwiązania, możesz znaleźć wersji interfejsu API dla każdego dostawcy zasobów platformy Azure, która jest zgodna z usługą Azure Stack.

Raczej niż badania, każdy dostawca zasobów i określonych wersji obsługiwanych przez usługę Azure Stack, możesz użyć profilu interfejsu API. Profil określa zestaw dostawców zasobów i wersje interfejsów API. Zestaw SDK lub narzędzia za pomocą zestawu SDK, zostaną przywrócone do obiektu docelowego `api-version` określone w profilu. Przy użyciu profilów interfejsu API możesz określić wersję profilu, który ma zastosowanie do całego szablonu i w czasie wykonywania, usługi Azure Resource Manager wybierze prawidłową wersję zasobu.

Profile interfejsu API Pracuj z narzędziami, które używają usługi Azure Resource Manager, takich jak program PowerShell, interfejsu wiersza polecenia platformy Azure, kodu podanego w zestaw SDK oraz programu Microsoft Visual Studio. Narzędzia i zestawy SDK Profile umożliwiają odczytu wersję moduły i biblioteki do uwzględnienia podczas tworzenia aplikacji.

Na przykład, jeśli używasz programu PowerShell do utworzenia konta magazynu przy użyciu **Microsoft.Storage** dostawcy zasobów, która obsługuje **parametru api-version** 2016-03-30 i maszynę Wirtualną przy użyciu  **Microsoft.Compute** dostawcy zasobów za pomocą **parametru api-version** 2015-12-01, który obsługuje moduł programu PowerShell 2016-03-30 należy wyszukać magazynu i modułu, którego obsługuje 2015-02-01 zasobów obliczeniowych, a następnie Zainstaluj je. Zamiast tego można użyć profilu. Użyj polecenia cmdlet `Install-Profile <profilename>`, i programu PowerShell ładuje właściwej wersji modułów.

Podobnie korzystając z zestawu SDK języka Python do tworzenia aplikacji opartych na języku Python, można określić profil. Zestaw SDK ładuje odpowiednie moduły dla dostawców zasobów, które zostały określone w skrypcie.

Deweloperzy mogą skoncentrować się na pisaniu rozwiązania. Zamiast badanie, które wersje interfejsu API, dostawcy zasobów i chmury współpracują ze sobą, można użyć profilu i dowiedzieć się, że Twój kod działa we wszystkich chmur, które obsługują ten profil.

## <a name="api-profile-code-samples"></a>Przykłady kodu profilu interfejsu API

Można znaleźć przykłady kodu, aby pomóc w zintegrowaniu rozwiązania przy użyciu preferowanego języka dzięki usłudze Azure Stack przy użyciu profilów. Obecnie wskazówki i przykłady można znaleźć w następujących językach:

- **.NET** może użyć profilu interfejsu API platformy .NET, aby pobrać najnowsze, najbardziej stabilny wersję każdego typu zasobu w pakiecie dostawcy zasobów. Aby uzyskać więcej informacji, zobacz [profilami wersji korzystanie z interfejsu API przy użyciu platformy .NET w usłudze Azure Stack](azure-stack-version-profiles-net.md).
- **Program PowerShell**  
Możesz użyć **AzureRM.Bootstrapper** modułu dostępnych za pośrednictwem galerii programu PowerShell, aby uzyskać polecenia cmdlet programu PowerShell, wymagane do pracy z profilami wersji interfejsu API. Aby uzyskać informacje, zobacz [profilami wersji korzystanie z interfejsu API środowiska PowerShell](azure-stack-version-profiles-powershell.md).
- **Interfejs wiersza polecenia platformy Azure**  
Można zaktualizować konfiguracji środowiska do korzystania z określonego profilu wersji interfejsu API usługi Azure Stack. Aby uzyskać informacje, zobacz [korzystanie z interfejsu API w wersji profilów dla wiersza polecenia platformy Azure](azure-stack-version-profiles-azurecli2.md).
- **Go**  
W zestawie SDK Przejdź profil jest kombinacją różnych typów zasobów z różnymi wersjami z różnymi usługami. Profile są dostępne w ścieżce profile /, mają wersję w **RRRR-MM-DD** formatu. Aby uzyskać informacje, zobacz [korzystanie z interfejsu API w wersji profilów dla języka Go](azure-stack-version-profiles-go.md).
- **Ruby**  
Ruby SDK dla usługi Azure Stack Resource Manager oferuje narzędzia ułatwiające tworzenie i zarządzanie infrastrukturą. Dostawcy zasobów w zestawie SDK obejmują obliczeniowych, sieci wirtualnych i magazynu przy użyciu języka Ruby. Aby uzyskać informacje, zobacz [profilami wersji korzystanie z interfejsu API za pomocą języka Ruby](azure-stack-version-profiles-ruby.md)
- **Python**  
Zestaw SDK języka Python obsługuje profilami wersji interfejsu API pod kątem platform inną chmurę, takich jak Azure Stack i globalnej platformy Azure. Za pomocą interfejsu API profile nad tworzeniem rozwiązań dla chmury hybrydowej. Aby uzyskać informacje, zobacz [profilami wersji korzystanie z interfejsu API za pomocą języka Python](azure-stack-version-profiles-python.md)

## <a name="next-steps"></a>Kolejne kroki

* [Install PowerShell for Azure Stack](azure-stack-powershell-install.md) (Instalowanie programu PowerShell dla usługi Azure Stack)
* [Konfigurowanie środowiska PowerShell użytkownika usługi Azure Stack](azure-stack-powershell-configure-user.md)
* [Szczegółowe informacje o wersji interfejsu API dostawcy zasobów obsługiwanych przez profile](azure-stack-profiles-azure-resource-manager-versions.md).
