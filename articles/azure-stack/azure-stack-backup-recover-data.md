---
title: Odzyskaj przed utratą danych w wyniku katastrofy w stosie Azure przy użyciu usługi Kopia zapasowa infrastruktury | Dokumentacja firmy Microsoft
description: Po poważnej awarii powoduje, że stos Azure niepowodzenie, można przywrócić dane infrastruktury, gdy aktualizujące wdrożenia stosu Azure.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 7ca3945dd3768ac71e0a962417f0a621aa83be1e
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="recover-from-catastrophic-data-loss"></a>Odzyskaj przed utratą danych w wyniku katastrofy

*Dotyczy: Azure stosu zintegrowanych systemów.*

Stos Azure działa usług platformy Azure w centrum danych. Stos Azure można uruchomić w środowiskach jak czterech węzłów zainstalowanych w jednym stojaku. Z kolei Azure działa w więcej niż 40 obszary wiele centrów danych i wiele stref w każdym regionie. Zasoby użytkownika mogą znajdować się na wielu serwerach, stojakami centrów danych i regionach. Stosu Azure obecnie tylko jest to możliwe do wdrożenia całego chmury w stojaku pojedynczego. Udostępnia to chmury ryzyko zdarzeń losowych w sieci centrum danych lub w przypadku awarii z powodu błędów produktu. Po awarii, lecz wystąpienia stosu Azure przejdzie do trybu offline. Wszystkie dane jest potencjalnie nieodwracalny.

W zależności od przyczyny utraty danych może być konieczne naprawienie usługi jednej infrastruktury lub przywrócenie całego wystąpienia stosu Azure. Nawet może być konieczne przywrócenie sprzętowe w tej samej lokalizacji lub w innej lokalizacji.

Adresy tego scenariusza odzyskiwanie całej instalacji w przypadku awarii sprzętu oraz ponowne wdrożenie chmury prywatnej.

| Scenariusz                                                           | Utrata danych                            | Zagadnienia do rozważenia                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Odzyskaj przed utratą danych w wyniku katastrofy z powodu błędu po awarii lub produktu | Wszystkie dane dotyczące infrastruktury i użytkownika i aplikacji | Użytkownik aplikacji i danych są chronione oddzielnie od infrastruktury danych |

## <a name="workflows"></a>Przepływy

Przebieg ochrony Azure Start rozpoczyna się od tworzenia kopii zapasowej danych infrastruktury i dzierżawcy/aplikacji oddzielnie. W tym dokumencie opisano, jak chronić infrastrukturę. 

![Początkowe wdrożenie Azure stosu](media\azure-stack-backup\azure-stack-backup-workflow1.png)

W najgorszym scenariuszy przypadków, gdy wszystkie dane zostaną utracone odzyskiwanie stosu Azure jest proces przywracania danych infrastruktury unikatowy dla tego wdrożenia stosu Azure i wszystkie dane użytkownika. 

![Należy ponownie wdrożyć Azure stosu](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Przywracanie

Jeśli brak utraty danych w wyniku katastrofy jednak sprzęt jest nadal używać ponownego wdrażania stosu Azure jest wymagany. Podczas ponownego wdrażania można określić miejsce przechowywania i poświadczenia wymagane do uzyskania dostępu kopii zapasowych. W tym trybie nie ma konieczności Określ usługi, które muszą zostać przywrócone. Infrastruktura kopii zapasowej kontrolera injects kontroli płaszczyzny stanu jako część przepływu pracy wdrażania.

W przypadku awarii renderujący bezużyteczne sprzętu, ponowne wdrożenie jest możliwe tylko na nowy sprzęt. Ponowne wdrożenie może potrwać kilka tygodni, podczas wymiany sprzętu porządkowania i umieszczane w centrum danych. Przywracanie danych płaszczyzny kontroli jest możliwe w dowolnym momencie. Jednak przywracania nie jest obsługiwana, jeśli wersja ponownie wdrożonym wystąpienia jest więcej niż jedna wersja nowsza niż wersja używana w ostatniej kopii zapasowej. 

| Tryb wdrożenia | Punkt początkowy | Punkt końcowy                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Czysta instalacja   | Kompilacja linii bazowej | OEM stosu Azure wdraża i aktualizuje najnowszą obsługiwaną wersję.                                                                                                                                          |
| Tryb odzyskiwania   | Kompilacja linii bazowej | OEM wdraża stosu Azure w trybie odzyskiwania i obsługuje wersję dopasowania wymogi oparte na najnowszej kopii zapasowej dostępne. Producent OEM kończy wdrożenia przez aktualizację do najnowszej wersji. |

## <a name="data-in-backups"></a>Dane w kopii zapasowych

Stos Azure obsługuje typ wdrożenia o nazwie w trybie odzyskiwania w chmurze. Ten tryb jest używany tylko wtedy, gdy wybrano odzyskiwanie po awarii stosu Azure lub błędu w produkcie renderowane rozwiązania nieodwracalny. W tym trybie wdrożenia nie odzyskać dane użytkownika przechowywane w rozwiązaniu. Zakres w tym trybie wdrożenia jest ograniczona do przywrócenia następujące dane:

 - Dane wejściowe wdrożenia
 - Systemów tożsamości wewnętrzny
 - Federacyjna zidentyfikować konfiguracji (bez połączenia wdrożenia)
 - Certyfikaty główne używane przez urząd certyfikacji wewnętrzny
 - Azure dane użytkownika konfiguracji Menedżera zasobów, takich jak subskrypcje, planów, ofertami i przydziały na potrzeby magazynu, sieci i zasoby obliczeniowe
 - KeyVault kluczy tajnych i magazynów
 - RBAC przypisania zasad i przypisań ról 

Brak użytkownika infrastruktura jako usługa (IaaS) lub platforma jako usługa (PaaS) zasoby są odzyskiwane podczas wdrażania. Oznacza to maszyn wirtualnych IaaS, kont magazynu obiektów blob, tabel, konfiguracji sieci i tak dalej, zostaną utracone. Chmura odzyskiwania służy do zapewnienia użytkownika operatory i użytkownicy mogą Zaloguj się do portalu po zakończeniu wdrożenia. Rejestrowanie zwrotnie w użytkowników nie będą widzieć ich zasobów. Użytkownicy mają swoje subskrypcje przywrócona i oraz że oryginalna planów i oferuje zasady zdefiniowane przez administratora. Logowania do systemu użytkowników działa w tym samym ograniczenia w oryginalnym rozwiązaniu przed awarią. Po ukończeniu odzyskiwania chmury operator można przywrócić ręcznie dodać wartość i RPs innej firmy, jak i skojarzone dane.

## <a name="next-steps"></a>Kolejne kroki

 - Więcej informacji na temat najlepszych rozwiązań dotyczących [przy użyciu usługi Kopia zapasowa infrastruktury](azure-stack-backup-best-practices.md).
