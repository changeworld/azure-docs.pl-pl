---
title: Wprowadzenie do usługi Azure Stack Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co to jest ASDK i typowe przypadki użycia usługi obliczenia Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 54eb2ff43a5f36999294b8d0c580bc425ab65b28
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58629071"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Co to jest Azure Stack Development Kit?
[Zintegrowane systemy usługi Microsoft Azure Stack](../azure-stack-poc.md) w zakresie rozmiaru, od 4 do 16 węzłach i są wspólnie objęte partnera sprzętu i Microsoft. Użyj usługi Azure Stack zintegrowane systemy, aby włączyć nowe scenariusze dla obciążeń produkcyjnych. Jeśli jesteś operatorów usługi Azure Stack, który zarządza infrastrukturą zintegrowane systemy i oferuje usługi, zobacz nasze [dokumentacja operatora](https://docs.microsoft.com/azure/azure-stack).

Azure Stack Development Kit (ASDK) jest wdrożenia z pojedynczym węzłem usługi Azure Stack, który można pobrać do użycia **za darmo**. Wszystkie składniki ASDK są zainstalowane w uruchomionej na komputerze serwera jednego hosta maszyn wirtualnych, które muszą spełniać lub przekraczać [minimalne wymagania sprzętowe](asdk-deploy-considerations.md#hardware). Ma zapewnić środowisko można ocenić usługę Azure Stack i tworzenie nowoczesnych aplikacji przy użyciu interfejsów API i narzędzia spójne z platformą Azure w ASDK *nieprodukcyjnych* środowiska. 

> [!IMPORTANT]
> ASDK nie jest przeznaczona do użycia lub obsługiwane w środowisku produkcyjnym.

Wszystkie składniki ASDK są wdrażane z komputerem hosta rozwój jednego zestawu, dlatego są ograniczone zasoby fizyczne dostępne. W przypadku wdrożeń ASDK maszyn wirtualnych infrastruktury Azure Stack i dzierżawy maszyn wirtualnych współistnieć na tym samym komputerze serwera. Ta konfiguracja nie jest przeznaczona do oceny skalę i wydajność.

ASDK zaprojektowano w celu zapewnienia spójnych z platformą Azure środowiska chmury hybrydowej dla:
- **Administratorzy** (operatorzy usługi Azure Stack). ASDK jest doskonałą do oceny i Dowiedz się więcej o dostępnych usług Azure Stack.
- **Deweloperzy**. ASDK może służyć do tworzenia hybrydowych i nowoczesnych aplikacji w środowisku lokalnym (w środowisku deweloperskim/testowym). Zapewnia to powtarzalności środowisko programistyczne przed lub obok wdrożeń produkcyjnych w usłudze Azure Stack. 

Obejrzyj ten krótki film wideo, aby dowiedzieć się więcej na temat ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK i wieloma węzłami różnice usługi Azure Stack
Wdrożenia w pojedynczym węźle ASDK różnią się od wdrożeniach usługi Azure Stack z wieloma węzłami na kilka sposobów ważne, które należy wiedzieć.

|Opis|ASDK|Usługa Azure Stack wielowęzłowego|
|-----|-----|-----|
|**Skalowanie**|Wszystkie składniki są zainstalowane na komputerze serwera z jednym węzłem.|Można dostosować w zakresie rozmiaru, od 4 do 16 węzłów.|
|**Odporność na błędy**|Konfiguracja pojedynczego węzła nie zapewnia wysoką dostępność|[Wysoka dostępność](../azure-stack-overview.md#providing-high-availability) funkcje są obsługiwane.|
|**Sieć**|ASDK host rozsyła cały ruch sieciowy ASDK. Nie ma żadnych wymagań dodatkowych przełącznika.|Bardziej złożone [infrastrukturę routingu sieci](../azure-stack-network.md#network-infrastructure) we wdrożeniach z wieloma węzłami niezbędne jest tym Top-Of-Rack (TOR), kontrolera zarządzania płytą główną (BMC) i przełączniki obramowania (sieci centrum danych).|
|**Proces poprawek i aktualizacji**|Aby przejść do nowej wersji ASDK, należy ponownie wdrożyć ASDK na komputerze-hoście development kit.|[Stosowanie poprawek i aktualizacji](../azure-stack-updates.md) proces używany do aktualizacji zainstalowanej wersji usługi Azure Stack.|
|**Pomoc techniczna**|Forum MSDN usługi Azure Stack. Pomoc techniczna firmy Microsoft dział obsługi klienta i pomocy technicznej (CSS) jest *nie* dostępne dla środowisk nieprodukcyjnych.|[Forum MSDN usługi Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) i obsługują pełne CSS.|
| | |

## <a name="learn-about-available-services"></a>Dowiedz się więcej o dostępnych usług
Operator usługi Azure Stack musisz wiedzieć, usług, które można udostępnić użytkownikom. Usługa Azure Stack obsługuje podzbiór usług platformy Azure i listę obsługiwanych usług będą w dalszym ciągu ewoluować wraz z upływem czasu.

### <a name="foundational-services"></a>Podstawowe usługi
Domyślnie usługi Azure Stack obejmuje następujące "podstawowe usługi" podczas wdrażania ASDK:
- Wystąpienia obliczeniowe
- Magazyn
- Networking
- Usługa Key Vault

W przypadku tych fundamentalnych usług możesz zaoferować Infrastructure-as-a-Service (IaaS) do użytkowników z minimalną konfiguracją.

### <a name="additional-services"></a>Usługi dodatkowe
Obecnie obsługiwane są następujące dodatkowe usługi Platform-as-a-Service (PaaS):
- App Service
- Azure Functions
- Bazy danych SQL i MySQL

> [!NOTE]
> Te usługi wymagają dodatkowej konfiguracji przed można udostępnić je użytkownikom i nie są domyślnie dostępne, po zainstalowaniu ASDK.

## <a name="service-roadmap"></a>Plan usługi
Usługa Azure Stack będą w dalszym ciągu dodawać obsługę dodatkowych usług systemu Azure. Aby dowiedzieć się, co będzie dalej z platformą Azure Stack, zobacz [plan usługi Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć pracę, oceny usługi Azure Stack, trzeba wcześniej [Pobierz najnowsze ASDK](asdk-download.md) i przygotuj komputer-host ASDK. Po przygotowaniu hosta kit rozwoju, możesz zainstalować ASDK i zaloguj się do portali administratora i użytkownika, aby rozpocząć korzystanie z usługi Azure Stack.