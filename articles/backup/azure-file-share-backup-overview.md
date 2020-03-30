---
title: Kopia zapasowa udziału plików platformy Azure – informacje
description: Dowiedz się, jak zrobić zapasowy udziałów plików platformy Azure w magazynie usług odzyskiwania
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 0e17b05a3febaa673fb29d45c2bcef25e2996df8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78386722"
---
# <a name="about-azure-file-share-backup"></a>Kopia zapasowa udziału plików platformy Azure – informacje

Tworzenie kopii zapasowej udziału plików platformy Azure to natywne rozwiązanie do tworzenia kopii zapasowych oparte na chmurze, które chroni dane w chmurze i eliminuje dodatkowe koszty konserwacji związane z lokalnymi rozwiązaniami do tworzenia kopii zapasowych. Usługa Azure Backup płynnie integruje się z synchronizacją plików platformy Azure i umożliwia scentralizowanie danych udziału plików oraz kopii zapasowych. To proste, niezawodne i bezpieczne rozwiązanie umożliwia skonfigurowanie ochrony udziałów plików przedsiębiorstwa w kilku prostych krokach z zapewnieniem, że można odzyskać dane w przypadku awarii.

## <a name="key-benefits-of-azure-file-share-backup"></a>Najważniejsze zalety tworzenia kopii zapasowej udziału plików platformy Azure

* Infrastruktura zero: nie jest potrzebne wdrożenie do konfigurowania ochrony dla udziałów plików.
* Wbudowane funkcje zarządzania: Można zaplanować tworzenie kopii zapasowych i określić żądany okres przechowywania bez dodatkowych narzutów przycinania danych.
* Natychmiastowe przywracanie: kopia zapasowa udziału plików platformy Azure używa migawek udziału plików, dzięki czemu można wybrać tylko pliki, które chcesz natychmiast przywrócić.
* Alerty i raportowanie: można skonfigurować alerty dla błędów tworzenia kopii zapasowych i przywracania i korzystać z rozwiązania raportowania dostarczonego przez usługę Azure Backup, aby uzyskać szczegółowe informacje na temat kopii zapasowych w udziałach plików.

## <a name="architecture"></a>Architektura

![Architektura kopii zapasowej udziału plików platformy Azure](./media/azure-file-share-backup-overview/azure-file-shares-backup-architecture.png)

## <a name="how-the-backup-process-works"></a>Jak działa proces tworzenia kopii zapasowej

1. Pierwszym krokiem w konfigurowaniu kopii zapasowej dla udziałów plików platformy Azure jest utworzenie magazynu usług odzyskiwania. Magazyn zapewnia skonsolidowany widok kopii zapasowych skonfigurowanych w różnych obciążeniach.

2. Po utworzeniu magazynu usługa Azure Backup odnajduje konta magazynu, które można zarejestrować w przechowalni. Możesz wybrać konto magazynu obsługujące udziały plików, które chcesz chronić.

3. Po wybraniu konta magazynu usługa Azure Backup wyświetla zestaw udziałów plików obecnych na koncie magazynu i przechowuje ich nazwy w katalogu warstw zarządzania.

4. Następnie należy skonfigurować zasady tworzenia kopii zapasowych (harmonogram i przechowywanie) zgodnie z wymaganiami i wybrać udziały plików do utworzenia kopii zapasowej. Usługa Azure Backup rejestruje harmonogramy na płaszczyźnie kontrolnej do wykonywania zaplanowanych kopii zapasowych.

5. Na podstawie określonych zasad harmonogram usługi Azure Backup wyzwala kopie zapasowe w zaplanowanym czasie. W ramach tego zadania migawka udziału plików jest tworzona przy użyciu interfejsu API udziału plików. Tylko adres URL migawki jest przechowywany w magazynie metadanych.

    >[!NOTE]
    >Dane udziału plików nie są przesyłane do usługi Kopia zapasowa, ponieważ usługa Kopia zapasowa tworzy migawki należące do konta magazynu i zarządza nimi.

6. Zawartość udziału plików platformy Azure (pojedyncze pliki lub pełny udział) można przywrócić z migawek dostępnych w udziale pliku źródłowego. Po wyzwoleniu operacji adres URL migawki jest pobierany z magazynu metadanych, a dane są wyświetlane i przesyłane z migawki źródłowej do wybranego docelowego udziału plików.

7. Dane monitorowania zadań kopii zapasowej i przywracania są wypychane do usługi Monitorowania kopii zapasowych platformy Azure. Dzięki temu można monitorować kopie zapasowe w chmurze dla udziałów plików w jednym pulpicie nawigacyjnym. Ponadto można również skonfigurować alerty lub powiadomienia e-mail, gdy ma to wpływ na kondycję kopii zapasowej. Wiadomości e-mail są wysyłane za pośrednictwem usługi poczty e-mail platformy Azure.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak zrobić [zapasowy udziałów plików platformy Azure](backup-afs.md)
* Znajdowanie odpowiedzi na [pytania dotyczące tworzenia kopii zapasowych plików platformy Azure](backup-azure-files-faq.md)
