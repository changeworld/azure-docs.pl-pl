---
title: Kopia zapasowa i odzyskiwanie danych dla usługi Azure Stack przy użyciu infrastruktury usługi Backup | Dokumentacja firmy Microsoft
description: Można utworzyć kopię zapasową i przywrócić konfigurację i dane usługi przy użyciu usługi Kopia zapasowa infrastruktury.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/25/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: da1ae76925ffeba7a1df57b4121f8cfe20b2887f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58882110"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Kopia zapasowa i odzyskiwanie danych dla usługi Azure Stack przy użyciu infrastruktury usługi Backup

*Dotyczy Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

Można utworzyć kopię zapasową i przywrócić konfigurację i dane usługi przy użyciu usługi Kopia zapasowa infrastruktury. Każda instalacja usługi Azure Stack zawiera wystąpienie usługi. Kopie zapasowe utworzone przez usługę do ponownego wdrożenia chmury Azure Stack można użyć do przywrócenia tożsamości, zabezpieczeń i danych usługi Azure Resource Manager. 

Można włączyć kopii zapasowej, gdy jesteś gotowy do ponownego uruchomienia chmury w środowisku produkcyjnym. Nie należy włączać kopii zapasowej, jeśli planujesz przeprowadzić testowanie i sprawdzanie poprawności przez długi czas.

Przed włączeniem usługi tworzenia kopii zapasowej upewnij się, że masz [wymagania w miejscu](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> Usługa Backup infrastruktury nie ma danych użytkownika i aplikacji. Zapoznaj się [ochrony maszyn wirtualnych wdrożonych w usłudze Azure Stack](user/azure-stack-manage-vm-protect.md) Aby uzyskać więcej informacji na temat ochrony maszyn wirtualnych IaaS na podstawie aplikacji. Kompleksową wiedzę na temat sposobu ochrony aplikacji w usłudze Azure Stack, można znaleźć [informacje dotyczące usługi Azure Stack firm ciągłość działalności biznesowej i odzyskiwanie po awarii odzyskiwania oficjalny dokument dotyczący](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Usługa kopii zapasowej infrastruktury

Usługi zawiera następujące funkcje.

| Cecha                                            | Opis                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Usługi infrastruktury kopii zapasowych                     | Tworzenie kopii zapasowej współrzędnych dla podzestawu usług infrastruktury w usłudze Azure Stack. W przypadku awarii, można przywrócić dane w ramach ponownego wdrażania. |
| Kompresja i szyfrowanie wyeksportowane dane kopii zapasowej | Dane kopii zapasowej jest kompresowane i szyfrowane przez system, zanim jest eksportowana do lokalizacji magazynu zewnętrznego, dostarczone przez administratora.                |
| Monitorowanie zadania tworzenia kopii zapasowej                              | System powiadamia, gdy kopia zapasowa zadań zakończy się niepowodzeniem i korygowanie kroki.                                                                                                |
| Środowisko zarządzania kopiami zapasowymi                       | RP kopii zapasowej obsługuje opcja włączania kopii zapasowych.                                                                                                                         |
| Odzyskiwania w chmurze                                     | W przypadku utraty danych w wyniku katastrofy kopii zapasowych może służyć do przywrócenia podstawowych informacji o usłudze Azure Stack jako część wdrożenia.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Sprawdź wymagania dotyczące infrastruktury usługi Backup

- **Lokalizacja magazynu**  
  Udział plików należy dostępne z usługi Azure Stack może zawierać siedem kopii zapasowych. Każda kopia zapasowa wynosi około 10 GB. Swój udział powinno być możliwe do przechowywania kopii zapasowych 140 GB. Aby uzyskać więcej informacji o wybieraniu lokalizacji magazynu dla usługi Azure Stack infrastruktury kopii zapasowych, zobacz [wymagania dotyczące kontrolera kopii zapasowej](azure-stack-backup-reference.md#backup-controller-requirements).
- **Poświadczenia**  
  Potrzebujesz konta użytkownika domeny i poświadczenia, na przykład, może użyć poświadczeń administratora usługi Azure Stack.
- **Certyfikat szyfrowania**  
  Pliki kopii zapasowej są szyfrowane za pomocą klucza publicznego w certyfikacie. Upewnij się, że ten certyfikat są przechowywane w bezpiecznej lokalizacji. 


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack z portalu administratora](azure-stack-backup-enable-backup-console.md).

Dowiedz się, jak [Włącz wykonywanie kopii zapasowej dla usługi Azure Stack przy użyciu programu PowerShell](azure-stack-backup-enable-backup-powershell.md).

Dowiedz się, jak [wykonywanie kopii zapasowych usługi Azure Stack](azure-stack-backup-back-up-azure-stack.md )

Dowiedz się, jak [sprawności po utracie danych w wyniku katastrofy](azure-stack-backup-recover-data.md)
