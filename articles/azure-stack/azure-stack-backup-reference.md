---
title: Azure odwołania do usługi Kopia zapasowa infrastruktury stosu | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera materiałów referencyjnych dla usługi Kopia zapasowa Azure stosu infrastruktury.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: D6EC0224-97EA-446C-BC95-A3D32F668E2C
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 608f3043e0e4b851820274ca743cbc44d1c8c0f1
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="infrastructure-backup-service-reference"></a>Dokumentacja usługi Kopia zapasowa

## <a name="azure-backup-infrastructure"></a>Infrastruktura kopii zapasowej platformy Azure

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Stos Azure składa się z wielu usług wchodzących w skład portalu usługi Azure Resource Manager i infrastruktury zarządzania. Możliwości zarządzania podobne urządzenia stosu Azure koncentruje się na zmniejsza się złożoność widoczne dla operatora rozwiązania.

Infrastruktura kopii zapasowej jest przeznaczony do internalize złożoność wykonywanie kopii zapasowych i przywracanie danych usług infrastruktury, zapewniając operatory można skoncentrować się na utrzymywania umowy SLA dla użytkowników i zarządzania nimi rozwiązania.

Eksportowanie danych kopii zapasowej do udziału zewnętrznego jest wymagana w celu uniknięcia przechowywania kopii zapasowych na tym samym systemie. Wymagających udziału zewnętrznego zapewnia elastyczność, aby określić miejsce przechowywania danych na podstawie istniejących zasad BC i odzyskiwania po awarii firmy przez administratora. 

### <a name="infrastructure-backup-components"></a>Infrastruktura kopii zapasowej składników

Tworzenie kopii zapasowej infrastruktura obejmuje następujące składniki:

 - **Infrastruktura kopii zapasowej kontrolera**  
 Kontroler kopii zapasowej infrastruktury zostanie uruchomiony z i znajduje się w każdej chmurze stosu platformy Azure.
 - **Dostawca zasobów kopii zapasowej**  
 Użytkownika interfejsu i aplikacji programu interfejsów (API) s udostępnia podstawowe funkcje tworzenia kopii zapasowej Azure stosu infrastruktury składa się kopii zapasowej dostawcy zasobów (RP kopii zapasowej).

#### <a name="infrastructure-backup-controller"></a>Infrastruktura kopii zapasowej kontrolera

Kontroler kopii zapasowej infrastruktury jest usługi sieć szkieletowa usług pobiera wystąpienia chmury stosu Azure. Zasoby kopii zapasowej są tworzone w regionalnych poziomu i przechwytywania określonego regionu dane usługi z Menedżera zasobów Azure AD, urząd certyfikacji, CRP, zasad ograniczeń oprogramowania, NRP, KeyVault, RBAC. 

### <a name="backup-resource-provider"></a>Dostawca zasobów kopii zapasowej

Interfejs użytkownika w portalu Azure stosu dla konfiguracji podstawowej oraz listę zasobów kopii zapasowej przedstawia informacje o kopii zapasowej dostawcy zasobów. Operator może wykonywać następujące operacje w interfejsie użytkownika:

 - Włączenia kopii zapasowej po raz pierwszy, zapewniając lokalizacji magazynu zewnętrznego, poświadczeń i klucza szyfrowania
 - Wyświetl zakończone utworzone zasoby kopii zapasowej i stan zasobów w ramach tworzenia
 - Zmodyfikuj lokalizację magazynu, gdzie kopii zapasowej kontrolera umieszcza dane kopii zapasowej
 - Modyfikowanie poświadczenia używane przez program kopii zapasowej kontrolera dostępu do lokalizacji magazynu zewnętrznego
 - Zmodyfikuj klucz szyfrowania, który używa kopii zapasowej kontrolera do szyfrowania kopii zapasowych 


## <a name="backup-controller-requirements"></a>Wymagania dotyczące tworzenia kopii zapasowej kontrolera

W tej sekcji opisano ważne wymagania dotyczące infrastruktury kopii zapasowych. Firma Microsoft zaleca, aby zapoznać się z informacjami dokładnie przed włączenia kopii zapasowej dla swojego wystąpienia usługi Azure stosu, a następnie odwołują się do niego w razie potrzeby podczas wdrażania kolejnych operacji.

Dostępne są następujące wymagania:

  - **Wymagania dotyczące oprogramowania** — w tym artykule opisano wskazówki zmiany rozmiaru i lokalizacje przechowywania. 
  - **Wymagania dotyczące sieciowej** — w tym artykule opisano wymagania sieciowe do różnych magazynów.  

### <a name="software-requirements"></a>Wymagania dotyczące oprogramowania

#### <a name="supported-storage-locations"></a>Lokalizacje przechowywania

| Lokalizacja magazynu                                                                 | Szczegóły                                                                                                                                                  |
|----------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| Udziału plików SMB hostowanych na urządzeniu magazynującym w środowisku zaufanej sieci | Udział SMB, w tym samym centrum danych, którym jest wdrażany stosu Azure lub w różnych centrach danych. Wiele wystąpień stosu Azure można używać tego samego udziału plików. |
| Udziału plików SMB na platformie Azure                                                          | Nie są obecnie obsługiwane.                                                                                                                                 |
| Magazyn obiektów blob Azure                                                            | Nie są obecnie obsługiwane.                                                                                                                                 |

#### <a name="supported-smb-versions"></a>Obsługiwane wersje protokołu SMB

| SMB | Wersja |
|-----|---------|
| SMB | 3.x     |

#### <a name="storage-location-sizing"></a>Zmiana rozmiaru lokalizacji magazynu 

Infrastruktura kopii zapasowej kontrolera wykona kopię zapasową danych na żądanie. Zalecane jest, aby utworzyć kopię zapasową co najmniej dwa razy dziennie i Zachowaj kopii zapasowych co najwyżej siedem dni. 

| Środowisko skali | Przewidywany stopień rozmiar kopii zapasowej | Całkowita ilość miejsca wymaganego |
|-------------------|--------------------------|--------------------------------|
| węzły 4-12        | 10 GB                     | 140 GB                          |

### <a name="network-requirements"></a>Wymagania dotyczące sieci
| Lokalizacja magazynu                                                                 | Szczegóły                                                                                                                                                                                 |
|----------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Udziału plików SMB hostowanych na urządzeniu magazynującym w środowisku zaufanej sieci | Port 445 jest wymagany, jeśli wystąpienie stosu Azure znajduje się w środowisku chronione zaporą. Infrastruktura kopii zapasowej kontrolera zainicjuje połączenie z serwerem plików SMB za pośrednictwem portu 445. |
| Aby użyć nazwy FQDN serwera plików, nazwa musi być rozpoznawana z program ten             |                                                                                                                                                                                         |

> [!Note]  
> Nie portów przychodzących muszą być otwarte.


## <a name="infrastructure-backup-limits"></a>Limity kopii zapasowej infrastruktury

Te limity wziąć pod uwagę planowania, wdrażania i obsługi programu Microsoft Azure stosu wystąpień. W poniższej tabeli opisano te limity.

### <a name="infrastructure-backup-limits"></a>Ogranicza infrastruktura kopii zapasowej
| Identyfikator limitu                                                 | Limit        | Komentarze                                                                                                                                    |
|------------------------------------------------------------------|--------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Typ kopii zapasowej                                                      | Tylko Pełna    | Infrastruktura kopii zapasowej kontroler obsługuje tylko pełne kopie zapasowe. Przyrostowe kopie zapasowe nie są obsługiwane.                                          |
| Zaplanowane kopie zapasowe                                                | Tylko ręczne  | Kopii zapasowej kontrolera aktualnie obsługuje tylko tworzenie kopii zapasowych na żądanie                                                                                 |
| Maksymalna równoczesnych zadań tworzenia kopii zapasowej                                   | 1            | Tylko jedno aktywne zadanie tworzenia kopii zapasowej jest obsługiwana dla każdego wystąpienia kopii zapasowej kontrolera.                                                                  |
| Konfiguracja przełącznika sieci                                     | Nie znajduje się w zakresie | Administrator musi utworzyć kopię zapasową konfiguracji przełącznika sieci przy użyciu narzędzi OEM. Zapoznaj się dokumentacją dotyczącą stosu Azure dostarczonego przez każdego producenta OEM. |
| Host cyklu życia sprzętu                                          | Nie znajduje się w zakresie | Administrator musi utworzyć kopię zapasową sprzętu cyklu życia hosta za pomocą narzędzi OEM. Zapoznaj się dokumentacją dotyczącą stosu Azure dostarczonego przez każdego producenta OEM.      |
| Maksymalna liczba udziałów plików                                    | 1            | Tylko jeden plik udział może służyć do przechowywania danych kopii zapasowej                                                                                        |
| Kopia zapasowa usługi aplikacji, funkcje, SQL i mysql zasobów dostawcy danych | Nie znajduje się w zakresie | Odwołuje się do wskazówek opublikowane do wdrażania i zarządzania nimi wartość — Dodaj RPs utworzone przez firmę Microsoft.                                                  |
| Dostawcy zasobów usługi tworzenia kopii zapasowych innych firm                              | Nie znajduje się w zakresie | Odwołuje się do wskazówek opublikowane do wdrażania i zarządzania nimi wartość — Dodaj RPs utworzonych przez dostawców innych firm.                                          |

## <a name="next-steps"></a>Kolejne kroki

 - Aby dowiedzieć się więcej na temat infrastruktury usługi Kopia zapasowa, zobacz [kopii zapasowej i odzyskiwanie danych dla stosu Azure w usłudze Kopia zapasowa infrastruktury](azure-stack-backup-infrastructure-backup.md).