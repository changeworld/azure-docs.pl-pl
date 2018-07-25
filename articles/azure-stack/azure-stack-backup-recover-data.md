---
title: Odzyskiwanie z utratą danych w wyniku katastrofy w usłudze Azure Stack przy użyciu usługi Kopia zapasowa infrastruktury | Dokumentacja firmy Microsoft
description: Po poważnej awarii powoduje, że usługi Azure Stack zakończyć się niepowodzeniem, możesz przywracania danych infrastruktury, podczas której wdrożenia usługi Azure Stack.
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
ms.openlocfilehash: f1582efa7d357f6f535c562a656ec17024357320
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242873"
---
# <a name="recover-from-catastrophic-data-loss"></a>Odzyskiwanie z utratą danych w wyniku katastrofy

*Dotyczy: zintegrowane systemy usługi Azure Stack.*

Usługa Azure Stack jest uruchamiany usług platformy Azure w centrum danych. Usługa Azure Stack można uruchamiać w środowiskach tak małej, jak czterech węzłów zainstalowane w jednym stojaku. Z kolei platforma Azure działa w ponad 40 regionach w wielu centrach danych i wielu stref w każdym regionie. Zasoby użytkownika może obejmować wiele serwerów, stojaki, centrach danych i regionach. Dzięki usłudze Azure Stack aktualnie masz możliwość wdrażania całej chmury na jednym stojaku. To udostępnia Twoja chmura istnieje wtedy ryzyko katastrofą w centrum danych lub w błędy z powodu błędów produktu. W przypadku awarii, wystąpienie usługi Azure Stack przejdzie do trybu offline. Wszystkie dane jest potencjalnie nieodwracalny.

W zależności od przyczyny utraty danych może być konieczne napraw usługę jednolita infrastruktura lub Przywróć całe wystąpienie usługi Azure Stack. Może być konieczne nawet Przywróć na inny komputer w tej samej lokalizacji lub w innej lokalizacji.

Ta eliminuje scenariusz odzyskanie całej instalacji w przypadku awarii sprzętu i ponowne wdrożenie chmury prywatnej.

| Scenariusz                                                           | Utrata danych                            | Zagadnienia do rozważenia                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Odzyskiwanie z utratą danych w wyniku katastrofy z powodu usterki po awarii lub produktu | Wszystkie dane infrastruktury i użytkownik i aplikacja | Użytkowników aplikacji i dane są chronione oddzielnie od infrastruktury danych |

## <a name="workflows"></a>Przepływy

Podróż ochrony Uruchom Azure rozpoczyna się od tworzenia kopii zapasowej danych infrastruktury i aplikacji/dzierżawy osobno. W tym dokumencie opisano, jak chronić infrastrukturę. 

![Początkowe wdrożenie usługi Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow1.png)

W najgorszego przypadku gdzie wszystkie dane zostaną utracone odzyskiwanie usługi Azure Stack jest proces przywracania unikatowe dane infrastruktury do wdrożenia w usłudze Azure Stack i wszystkie dane użytkownika. 

![Ponowne wdrażanie usługi Azure Stack](media\azure-stack-backup\azure-stack-backup-workflow2.png)

## <a name="restore"></a>Przywracanie

Jeśli następuje utrata danych w wyniku katastrofy, ale sprzęt jest nadal można używać, ponowne wdrażanie usługi Azure Stack jest wymagana. Podczas ponownego wdrożenia można określić miejsce przechowywania i poświadczeń wymaganych do korzystania z kopii zapasowych. W tym trybie nie ma potrzeby określić usługi, które mają być przywracane. Infrastruktura kopii zapasowej kontrolera wprowadza stan płaszczyzna formantu jako część przepływu pracy wdrażania.

W przypadku awarii renderujący bezużyteczne sprzętu, ponownego wdrażania jest możliwe tylko na nowym sprzęcie. Ponowne wdrożenie może potrwać kilka tygodni, gdy sprzęt zastępczy jest uporządkowana i umieszczane w centrum danych. Przywracanie danych płaszczyzna kontroli jest możliwe w dowolnym momencie. Jednakże Przywracanie nie jest obsługiwane, jeśli wersja ponownie wdrożonym wystąpieniu jest więcej niż jedna wersja jest nowsza niż wersja używana w ostatniej kopii zapasowej. 

| Tryb wdrożenia | Punkt początkowy | Punkt końcowy                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Czysta instalacja   | Kompilacja linii bazowej | OEM służy do wdrażania usługi Azure Stack i aktualizacji do najnowszej obsługiwanej wersji.                                                                                                                                          |
| Tryb odzyskiwania   | Kompilacja linii bazowej | OEM wdraża usługi Azure Stack w trybie odzyskiwania i obsługuje wersję dopasowania wymogi oparte na najnowszej dostępnej kopii zapasowej. Producent OEM kończy wdrożenia przez aktualizację do najnowszej obsługiwanej wersji. |

## <a name="data-in-backups"></a>Dane kopii zapasowych

Usługa Azure Stack obsługuje typ wdrożenia o nazwie tryb odzyskiwania w chmurze. Ten tryb jest używany tylko wtedy, gdy użytkownik chce odzyskać usługi Azure Stack po awarii lub błędu w produkcie renderowane rozwiązania nieodwracalny. Ten tryb wdrożenia nie może odzyskać dane użytkownika przechowywane w rozwiązaniu. Zakres tego trybu wdrożenia jest ograniczona do przywracania następujące dane:

 - Danych wejściowych wdrożenia
 - Systemów wewnętrznych tożsamości
 - Federacyjna zidentyfikować konfiguracji (odłączonego wdrożenia)
 - Certyfikaty główne używane przez urząd certyfikacji wewnętrznego
 - Azure danych użytkownika konfiguracji Menedżera zasobów, takich jak subskrypcji, planów, ofert i przydziałów w celu skorzystania z magazynu, sieci i zasoby obliczeniowe
 - Wpisów tajnych usługi KeyVault i magazynów
 - Przypisania zasad RBAC i przypisań ról 

Brak użytkownika infrastruktura jako usługa (IaaS) lub platforma jako usługa (PaaS) zasoby są odzyskiwane podczas wdrażania. Oznacza to maszyny wirtualne IaaS, kont magazynu, obiekty BLOB, tabel, konfiguracja sieci i tak dalej, zostaną utracone. Cel odzyskiwania w chmurze jest zapewnienie Twoje operatory i użytkownicy mogą zalogować się do portalu, po zakończeniu wdrożenia. Ponowne zalogowanie użytkownicy nie będą widzieć swoje zasoby. Użytkownicy mają swoje subskrypcje przywrócona i wraz z, oryginalnym planów i oferuje zasady zdefiniowane przez administratora. Użytkownicy logujący się do systemu działa w ramach tego samego ograniczenia w oryginalnym rozwiązaniu przed po awarii. Po ukończeniu odzyskiwania w chmurze, operator można przywrócić ręcznie Dodaj wartość i innych firm RPs i powiązane dane.

## <a name="next-steps"></a>Kolejne kroki

 - Więcej informacji na temat najlepszych rozwiązań dotyczących [przy użyciu usługi Kopia zapasowa infrastruktury](azure-stack-backup-best-practices.md).