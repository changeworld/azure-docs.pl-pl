---
title: Kopia zapasowa udziału plików platformy Azure – informacje
description: Dowiedz się, jak zrobić zapasowy udziałów plików platformy Azure w magazynie usług odzyskiwania
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: fd5bb51b2c7b5c09e9d859b69c3094eb50c205b5
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396284"
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

## <a name="backup-costs"></a>Koszty tworzenia kopii zapasowych

Kopia zapasowa udziału plików Azure jest rozwiązaniem opartym na migawkach, a opłaty za magazyn za migawki są naliczane wraz z użyciem plików platformy Azure zgodnie ze szczegółami cen wymienionymi [tutaj.](https://azure.microsoft.com/pricing/details/storage/files/)

Jednak opłata za chronione wystąpienie za wykorzystanie rozwiązania do tworzenia kopii zapasowych jest zgodna z modelem cenowym opisanym w sekcji [Kopia zapasowa dla plików platformy Azure.](https://azure.microsoft.com/pricing/details/backup/) Obecnie rzeczywista cena została zaktualizowana tylko dla zachodnio-środkowych stanów USA. W przypadku innych regionów dokładne ceny zostaną wkrótce zaktualizowane o pewne różnice regionalne, ale przy użyciu tego samego modelu cenowego.

>[!NOTE]
>Podczas podglądu nie ma "opłaty za chronione wystąpienie" i opłata będzie pobierana tylko za migawki zgodnie z cenami wymienionymi [tutaj](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak zrobić [zapasowy udziałów plików platformy Azure](backup-afs.md)
* Znajdowanie odpowiedzi na [pytania dotyczące tworzenia kopii zapasowych plików platformy Azure](backup-azure-files-faq.md)
