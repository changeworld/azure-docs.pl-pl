---
title: Wprowadzenie do usługi Azure stosu Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: W tym artykule opisano, co to jest ASDK i typowe przypadki użycia do oceny programu Microsoft Azure stosu.
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
ms.date: 06/07/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 951cd1adc09373b9af560097b088fd740ceb51a8
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/07/2018
ms.locfileid: "34850631"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Co to jest zestaw deweloperski stosu Azure?
[Microsoft Azure stosu zintegrowane systemy](.\.\azure-stack-poc.md) należeć do zakresu o rozmiarze z 4-12 węzłów i wspólnie są obsługiwane przez partnera sprzętu i firmy Microsoft. Użyj stosu Azure zintegrowanych systemów, aby włączyć nowe scenariusze dla obciążeń produkcyjnych. Jeśli operator Azure stosu, który zarządza zintegrowanych systemów infrastruktury i oferuje usługi, zobacz nasze [dokumentacji operator](https://docs.microsoft.com/azure/azure-stack).

Azure stosu Development Kit (ASDK) to wdrożenie jednowęzłowej Azure stosu, który można pobrać i użyć **bezpłatnie**. Wszystkie składniki ASDK są instalowane na maszynach wirtualnych uruchomioną na komputerze serwera jednego hosta, które muszą spełniać lub przekracza [minimalne wymagania sprzętowe](asdk-deploy-considerations.md#hardware). ASDK ma zapewnić środowisko, można obliczyć stosu Azure i opracowywanie nowoczesnych aplikacji przy użyciu interfejsów API i narzędzi zgodne z platformy Azure w *nieprodukcyjnych* środowiska. 

> [!IMPORTANT]
> ASDK nie jest przeznaczony do użycia lub obsługiwane w środowisku produkcyjnym.

Ponieważ wszystkie składniki ASDK są wdrażane jednego zestawu hosta komputera, są ograniczone zasoby fizyczne dostępne. Z wdrożeniami ASDK zarówno stosu Azure VMs infrastruktury i dzierżawcy maszyn wirtualnych współistnieją na tym samym serwerze. Ta konfiguracja nie jest przeznaczone do oceny skali lub wydajności.

ASDK ma na celu zapewnienie Azure spójne środowisko chmury hybrydowej dla:
- **Administratorzy** (operatory stosu Azure). ASDK jest doskonałą do oceny i Dowiedz się więcej o dostępnych usług Azure stosu.
- **Deweloperzy**. ASDK może służyć do rozwoju hybrydowego lub nowoczesnych aplikacji lokalnych (środowiska i testowania). Zapewnia to powtarzalność środowisko programistyczne przed lub obok wdrożeń produkcyjnych Azure stosu. 

Obejrzyj ten krótki film, aby dowiedzieć się więcej na temat ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Różnice Azure stosu ASDK i wieloma węzłami.
Wdrożenia w pojedynczym węźle ASDK różnią się od wdrożeniach z wieloma węzłami stosu Azure na kilka sposobów ważne, które należy zwrócić uwagę.

|Opis|ASDK|Stos Azure wieloma węzłami.|
|-----|-----|-----|
|**Skalowanie**|Wszystkie składniki są instalowane na komputerze serwera z jednym węzłem.|Można dostosować w zakresie rozmiaru z 4-12 węzłów.|
|**Odporność**|Konfiguracja pojedynczego węzła nie ma wysokiej dostępności|[O wysokiej dostępności](.\.\azure-stack-key-features.md#high-availability-for-azure-stack) możliwości są obsługiwane.|
|**Sieć**|Aby rozsyłać cały ruch sieciowy ASDK ASDK korzysta z maszyny Wirtualnej o nazwie AzS BGPNAT01. Nie ma żadnych wymagań dodatkowych przełącznika.|Maszyna wirtualna AzS BGPNAT01 nie istnieje we wdrożeniach z wieloma węzłami. Bardziej złożone [infrastrukturę routingu sieci](.\.\azure-stack-network.md#network-infrastructure) jest niezbędne, łącznie z górnej półki (TOR), kontrolera zarządzania płytą główną (BMC) i przełączniki obramowania (sieci centrum danych).|
|**Proces poprawek i aktualizacji**|Aby przejść do nowej wersji ASDK, należy ponownie wdrożyć ASDK na komputerze hosta development kit.|[Poprawka i zaktualizuj](.\.\azure-stack-updates.md) procesu używane do aktualizowania zainstalowana wersja Azure stosu.|
|**Pomoc techniczna**|Forum MSDN Azure stosu. Pomoc techniczna firmy Microsoft i obsługuje (CSS) jest *nie* dostępne w środowiskach nieprodukcyjnych.|[Forum MSDN Azure stosu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) i obsługuje pełne CSS.|
| | |

## <a name="learn-about-available-services"></a>Dowiedz się więcej o dostępnych usług
Jako Operator stosu Azure musisz wiedzieć, usług, które można udostępnić użytkownikom. Stos Azure obsługuje podzbiór usług platformy Azure i na liście obsługiwanych usług będzie rozwijać wraz z upływem czasu.

### <a name="foundational-services"></a>Podstawowych usług
Domyślnie stosu Azure zawiera następujące "podstawowych usługi" podczas wdrażania ASDK:
- Wystąpienia obliczeniowe
- Magazyn
- Networking
- Usługa Key Vault

Z tych podstawowych usług może oferować użytkownikom z minimalną konfiguracją infrastruktury jako — usługa (IaaS).

### <a name="additional-services"></a>Usługi dodatkowe
Obecnie są obsługiwane następujące dodatkowe usługi platformy jako — usługa (PaaS):
- App Service
- Azure Functions
- Bazy danych SQL i MySQL

> [!NOTE]
> Te usługi wymagają dodatkowej konfiguracji przed można udostępnić je użytkownikom i nie są domyślnie dostępne po zainstalowaniu ASDK.

## <a name="service-roadmap"></a>Plan usługi
Stos Azure będą w dalszym ciągu obsługę dodatkowych usług Azure. Aby dowiedzieć się o nadchodzących zmianach dalej stosu Azure, zobacz [plan stosu Azure](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Kolejne kroki
Aby rozpocząć, obliczenia stosu Azure, należy przygotować hosta zestawu programowanie komputera serwera, a następnie [zainstalować ASDK](asdk-install.md). Po wykonaniu tej można Zaloguj się do portali administratorów i użytkowników na rozpoczęcie korzystania z usługi Azure stosu.