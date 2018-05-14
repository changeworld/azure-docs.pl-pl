---
title: Kopia zapasowa i odzyskiwanie danych dla stosu Azure w usłudze Kopia zapasowa infrastruktury | Dokumentacja firmy Microsoft
description: Można utworzyć kopię zapasową i przywrócić konfigurację i dane usługi przy użyciu infrastruktury usługi tworzenia kopii zapasowej.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 9B51A3FB-EEFC-4CD8-84A8-38C52CFAD2E4
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/20/2017
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 8c8037fe3936485082299250e603b2f3ea3859b9
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2018
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Kopia zapasowa i odzyskiwanie danych dla stosu Azure w usłudze Kopia zapasowa infrastruktury

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Można utworzyć kopię zapasową i przywrócić konfigurację i dane usługi przy użyciu infrastruktury usługi tworzenia kopii zapasowej. Każda instalacja stosu Azure zawiera wystąpienia usługi. Kopie zapasowe utworzone przez usługę do ponownego rozmieszczenia stosu chmury Azure można użyć do przywrócenia tożsamości, zabezpieczeń i danych usługi Azure Resource Manager.

Gdy wszystko będzie gotowe do umieszczenia w środowisku produkcyjnym chmury można włączyć kopii zapasowej. Nie należy włączać kopii zapasowej, jeśli planujesz do testowania i weryfikacji przez długi czas.

Przed włączeniem usługi tworzenia kopii zapasowej upewnij się, że [wymagania w miejscu](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Infrastruktura usługa kopii zapasowej nie zawiera danych użytkownika i aplikacji. Zobacz następujące artykuły, aby uzyskać instrukcje dotyczące wykonywania kopii zapasowych i przywracania [usługi aplikacji](https://aka.ms/azure-stack-app-service), [SQL](https://aka.ms/azure-stack-ms-sql), i [MySQL](https://aka.ms/azure-stack-mysql) dostawców zasobów i danych skojarzonego użytkownika.

## <a name="the-infrastructure-backup-service"></a>Usługa tworzenia kopii zapasowej infrastruktury

Usług zawiera następujące funkcje.

| Cecha                                            | Opis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usługi tworzenia kopii zapasowej infrastruktury                     | Koordynować kopii zapasowej w podzbiór usługi infrastruktury platformy Azure stosu. W przypadku awarii, dane można przywracać w ramach ponownego wdrażania. |
| Kompresji i szyfrowania wyeksportowane dane kopii zapasowej | Dane kopii zapasowej jest skompresowany i szyfrowane przez system, przed wyeksportowaniem go do lokalizacji magazynu zewnętrznego dostarczonego przez administratora.                |
| Zadanie tworzenia kopii zapasowej monitorowania                              | System powiadamia o kroków zakończy się niepowodzeniem i korygowania zadania tworzenia kopii zapasowej.                                                                                                |
| Środowisko zarządzania kopiami zapasowymi                       | RP kopii zapasowej obsługuje włączenie kopii zapasowej.                                                                                                                         |
| Chmura odzyskiwania                                     | W przypadku utraty danych w wyniku katastrofy kopii zapasowych może służyć do przywrócenia podstawowe informacje stosu Azure jako część wdrożenia.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Sprawdź wymagania dotyczące usługi Kopia zapasowa infrastruktury

- **Lokalizacja magazynu**  
  Należy udział plików dostępny z stosu Azure, który może zawierać siedem kopii zapasowych. Każdej kopii zapasowej wynosi około 10 GB. Na udział powinno być możliwe do przechowywania 140 GB kopii zapasowych. Aby uzyskać więcej informacji o wybieraniu lokalizacji magazynu dla usługi Kopia zapasowa Azure stosu infrastruktury, zobacz [wymagania dotyczące tworzenia kopii zapasowej kontrolera](azure-stack-backup-reference.md#backup-controller-requirements).
- **Poświadczenia**  
  Wymagane konto użytkownika domeny i poświadczenia, na przykład może użyć poświadczeń administratora platformy Azure stosu.
- **Klucz szyfrowania**  
  Pliki kopii zapasowej są szyfrowane za pomocą tego klucza. Upewnij się, że przechowywanie tego klucza w bezpiecznej lokalizacji. Po ustawić ten klucz po raz pierwszy lub Obróć klucz w przyszłości, nie można wyświetlić tego klucza z tego interfejsu. Więcej instrukcji do wygenerowania klucza wstępnego, wykonaj skrypty w [włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell](http://azure-stack-backup-enable-backup-powershell.md).

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się, jak [włączenia kopii zapasowej Azure stosu z portalu administracyjnego](azure-stack-backup-enable-backup-console.md).
- Dowiedz się, jak [włączenia kopii zapasowej Azure stosu przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).
- Dowiedz się, jak [kopia zapasowa Azure stosu](azure-stack-backup-back-up-azure-stack.md )
- Dowiedz się, jak [odzyskać przed utratą danych w wyniku katastrofy](azure-stack-backup-recover-data.md)
