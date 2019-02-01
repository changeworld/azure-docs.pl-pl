---
title: Dokumentacja usługi Azure Stack infrastruktura kopii zapasowej usługa | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera dokumentacja dotycząca usługi Azure Stack infrastruktury kopii zapasowej.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 80cca629ea49d8843580c23f74d1d2076aa623e3
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246715"
---
# <a name="infrastructure-backup-service-reference"></a>Odwołanie do usługi Kopia zapasowa infrastruktury

## <a name="azure-backup-infrastructure"></a>Infrastruktura kopii zapasowej platformy Azure

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Usługa Azure Stack składa się z wielu usług, wchodzące w skład portalu usługi Azure Resource Manager, a środowisko zarządzania infrastrukturą. Możliwości zarządzania podobne urządzenia usługi Azure Stack koncentruje się na redukowania poziomu złożoności procesów udostępniane operatorowi rozwiązania.

Tworzenie kopii zapasowych jest przeznaczona do internalize złożoność tworzenia kopii zapasowych i przywracania danych dla usług infrastruktury, zapewniając operatory skupić się na utrzymywania umowy SLA dla użytkowników i zarządzania nimi rozwiązania.

Eksportowanie danych kopii zapasowej do udziału zewnętrznego jest wymagana, aby uniknąć przechowywania kopii zapasowych na tym samym systemie. Wymaganie udziału zewnętrznego zapewnia elastyczność do określenia miejsca przechowywania danych na podstawie istniejących zasad BC i odzyskiwania po awarii firmy przez administratora. 

### <a name="infrastructure-backup-components"></a>Infrastruktura kopii zapasowej składników

Tworzenie kopii zapasowych zawiera następujące składniki:

 - **Infrastruktura kopii zapasowej kontrolera**  
 Zapasowy kontroler infrastruktury jest utworzone za pomocą i znajduje się w każdej chmurze na platformie Azure Stack.
 - **Dostawca zasobów kopii zapasowej**  
 Kopii zapasowych dostawcy zasobów (RP kopii zapasowej) składa się z użytkownika interfejsu i aplikacji programu interfejsów (API) s udostępnia podstawowe funkcje tworzenia kopii zapasowych dla infrastruktury Azure Stack.

#### <a name="infrastructure-backup-controller"></a>Infrastruktura kopii zapasowej kontrolera

Kontroler kopii zapasowej infrastruktury jest usługa pobiera zrealizowana dla usługi Azure Stack chmury usługi Service Fabric. Zasoby kopii zapasowej są tworzone w regionalne poziomu i przechwytywania specyficzne dla regionu usługi dane z usługi AD, urząd certyfikacji, usługi Azure Resource Manager zasad SRP, NRP, magazynu kluczy, RBAC. 

### <a name="backup-resource-provider"></a>Dostawca zasobów kopii zapasowej

Kopii zapasowych dostawcy zasobów przedstawia interfejsu użytkownika w portalu usługi Azure Stack dla podstawowej konfiguracji i Lista zasobów kopii zapasowej. Operator może wykonywać następujące operacje w interfejsie użytkownika:

 - Włącz kopię zapasową po raz pierwszy, zapewniając lokalizacji magazynu zewnętrznego, poświadczenia i klucza szyfrowania
 - Wyświetl zakończone utworzone zasoby kopii zapasowej i stan zasobów w ramach tworzenia
 - Zmodyfikuj lokalizację magazynu, gdzie kopii zapasowej kontrolera umieszcza dane kopii zapasowej
 - Modyfikowanie poświadczenia, których kopia zapasowa kontroler używa uzyskiwać dostęp do lokalizacji magazynu zewnętrznego
 - Zmodyfikuj klucz szyfrowania kopii zapasowych kontroler jest używany do szyfrowania kopii zapasowych 


## <a name="backup-controller-requirements"></a>Wymagania dotyczące kontrolera kopii zapasowej

W tej sekcji opisano ważne wymagania dotyczące infrastruktury kopii zapasowej. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed Włącz wykonywanie kopii zapasowej dla swojego wystąpienia usługi Azure Stack, a następnie wrócić do niego zgodnie z potrzebami podczas wdrażania i kolejna operacja.

Wymagane jest miedzy innymi:

  - **Wymagania dotyczące oprogramowania** — w tym artykule opisano obsługiwane lokalizacje i wskazówki dotyczące ustalania rozmiaru. 
  - **Wymagania dotyczące sieciowej** — w tym artykule opisano wymagania dotyczące sieci dla lokalizacji innego magazynu.  

### <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

#### <a name="supported-storage-locations"></a>Obsługiwane lokalizacje

| Lokalizacja magazynu                                                                 | Szczegóły                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Udział plików SMB hostowanych na urządzeniu magazynującym, w tym środowisku zaufanych sieci | Udział SMB, w tym samym centrum danych, której wdrażana jest usługa Azure Stack lub w innym centrum danych. Wiele wystąpień usługi Azure Stack można użyć tego samego udziału plików. |
| Udział plików SMB na platformie Azure                                                          | Nie są obecnie obsługiwane.                                                                                                                                 |
| Magazyn obiektów blob na platformie Azure                                                            | Nie są obecnie obsługiwane.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Obsługiwane wersje protokołu SMB

| SMB | Wersja |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Ustalanie rozmiaru lokalizacja magazynu 

Infrastruktura kopii zapasowej kontrolera będzie kopię zapasową danych na żądanie. Zaleca się tworzenie kopii zapasowej ostatnio dwa razy dziennie i Kontynuuj, maksymalnie siedem dni kopii zapasowych. 

**1811 i nowszych**
| Skalowanie środowiska | Przewidywany rozmiar kopii zapasowej | Całkowita ilość miejsca wymaganego |
|-------------------|--------------------------|--------------------------------|
| 4 – 16 węzłów        | 20 GB                    | 280 GB                        |
| ASDK              | 10 GB                    | 140 GB                        |

**Wstępnie 1811**
| Skalowanie środowiska | Przewidywany rozmiar kopii zapasowej | Całkowita ilość miejsca wymaganego |
|-------------------|--------------------------|--------------------------------|
| 4 – 16 węzłach, ASDK  | 10 GB                     | 140 GB                        |

### <a name="network-requirements"></a>Wymagania dotyczące sieci
| Lokalizacja magazynu                                                                 | Szczegóły                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Udział plików SMB hostowanych na urządzeniu magazynującym, w tym środowisku zaufanych sieci | Port 445 jest wymagany, jeśli wystąpienie usługi Azure Stack, który znajduje się w środowisku chronionego. Infrastruktura kopii zapasowej kontrolera zainicjuje połączenie z serwerem plików SMB za pośrednictwem portu 445. |
| Aby użyć nazwy FQDN serwera plików, nazwa musi być rozpoznawalna z program ten             |                                                                                                                                                                                         |

> [!Note]  
> Nie porty wejściowe muszą być otwarte.


## <a name="infrastructure-backup-limits"></a>Limity kopii zapasowej infrastruktury

Te limity wziąć pod uwagę planowanie, wdrażanie i obsługiwanie swoich wystąpień usługi Microsoft Azure Stack. W poniższej tabeli opisano te limity.

### <a name="infrastructure-backup-limits"></a>Tworzenie kopii zapasowych ogranicza
| Identyfikator limitu                                                 | Limit        | Komentarze                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ kopii zapasowej                                                      | Tylko pełne    | Infrastruktura kopii zapasowej kontroler obsługuje tylko pełne kopie zapasowe. Przyrostowe kopie zapasowe nie są obsługiwane.                                          |
| Zaplanowane kopie zapasowe                                                | Zaplanowane i ręczne  | Kontroler tworzenia kopii zapasowej obsługuje tworzenie kopii zapasowych zaplanowanych, jak i na żądanie                                                                                 |
| Maksymalna liczba równoczesnych zadań tworzenia kopii zapasowej                                   | 1            | Tylko jedno aktywne zadanie tworzenia kopii zapasowych jest obsługiwana za wystąpienie usługi kopii zapasowej kontrolera.                                                                  |
| Konfiguracja przełącznika sieci                                     | Nie znajduje się w zakresie | Administrator musi utworzyć kopię zapasową przy użyciu narzędzi OEM konfiguracja przełącznika sieci. Zajrzyj do dokumentacji dla usługi Azure Stack dostarczonego przez każdego producenta OEM. |
| Host cyklu życia sprzętu                                          | Nie znajduje się w zakresie | Administrator musi utworzyć kopię zapasową sprzętu cyklu życia hosta, za pomocą narzędzi OEM. Zajrzyj do dokumentacji dla usługi Azure Stack dostarczonego przez każdego producenta OEM.      |
| Maksymalna liczba udziałów plików                                    | 1            | Tylko jednego udziału plików można przechowywać dane kopii zapasowej                                                                                        |
| Kopii zapasowych usługi dla aplikacji funkcji, SQL, dane dostawcy zasobów mysql | Nie znajduje się w zakresie | Zapoznaj się z wytycznych opublikowanych dla wdrażania i zarządzania nimi wartość dodaną RPs stworzone przez firmę Microsoft.                                                  |
| Dostawcy zasobów usługi tworzenia kopii zapasowych innych firm                              | Nie znajduje się w zakresie | Zapoznaj się z wytycznych opublikowanych dla wdrażania i zarządzania nimi wartość dodaną RPs utworzone przez innych dostawców.                                          |

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej o usłudze kopii zapasowej infrastruktury, zobacz [kopia zapasowa i odzyskiwanie danych dla usługi Azure Stack przy użyciu usługi Backup infrastruktury](azure-stack-backup-infrastructure-backup.md).
