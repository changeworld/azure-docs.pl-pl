---
title: Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Analytics
description: Dowiedz się, jak zaplanować odzyskiwanie po awarii na kontach usługi Azure Data Lake Analytics.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73889758"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Wskazówki dotyczące odzyskiwania po awarii dla usługi Azure Data Lake Analytics

Usługa Azure Data Lake Analytics jest usługą zadań analizy na żądanie, która pozwala uprościć analizowanie danych big data. Zamiast wdrażać, konfigurować i dostosowywać sprzęt, możesz pisać zapytania umożliwiające przekształcanie danych i wyodrębniać wartościowe informacje. Usługa analizy może od razu rozpocząć obsługę zadań w dowolnej skali — należy wybrać ustawienie wskazujące potrzebną moc. Płacisz tylko za uruchomione zadanie, dzięki czemu oszczędzasz pieniądze. Ten artykuł zawiera wskazówki dotyczące ochrony zadań przed rzadkimi awariami w całym regionie lub przypadkowymi usunięciami.

## <a name="disaster-recovery-guidance"></a>Wskazówki dotyczące odzyskiwania po awarii

Podczas korzystania z usługi Azure Data Lake Analytics, jest ważne, aby przygotować własny plan odzyskiwania po awarii. Ten artykuł pomaga pokierować do tworzenia planu odzyskiwania po awarii. Istnieją dodatkowe zasoby, które mogą pomóc w utworzeniu własnego planu:
- [Failure and disaster recovery for Azure applications (Awarie i odzyskiwanie po awariach w aplikacjach platformy Azure)](/azure/architecture/reliability/disaster-recovery)
- [Odporność platformy Azure — wskazówki techniczne](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>Najważniejsze wskazówki i wskazówki dotyczące scenariuszy

Cykliczne zadanie U-SQL można uruchomić na koncie ADLA w regionie, który odczytuje i zapisuje tabele U-SQL, a także dane nieustrukturyzowane.  Przygotuj się na katastrofę, podejmując następujące kroki:

1. Utwórz konta ADLA i ADLS w regionie pomocniczym, które będą używane podczas awarii.

   > [!NOTE]
   > Ponieważ nazwy kont są unikatowe globalnie, należy użyć spójnego schematu nazewnictwa, który wskazuje, które konto jest pomocnicze.

2. W przypadku danych nieustrukturyzowanych odnośnik [wskazówki dotyczące odzyskiwania po awarii dotyczące danych w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)

3. W przypadku danych strukturalnych przechowywanych w tabelach i bazach danych usługi ADLA należy utworzyć kopie artefaktów metadanych, takich jak bazy danych, tabele, funkcje i zestawy o wartości tabeli. Należy okresowo ponownie zsynchronizować te artefakty, gdy nastąpią zmiany w produkcji. Na przykład nowo wstawione dane muszą być replikowane do regionu pomocniczego przez skopiowanie danych i wstawienie do tabeli pomocniczej.

   > [!NOTE]
   > Te nazwy obiektów są ograniczone do konta pomocniczego i nie są unikatowe globalnie, więc mogą mieć takie same nazwy jak na podstawowym koncie produkcyjnym.

Podczas awarii należy zaktualizować skrypty, aby ścieżki wejściowe wskazywały pomocniczy punkt końcowy. Następnie użytkownicy przesyłają swoje zadania do konta ADLA w regionie pomocniczym. Dane wyjściowe zadania zostaną zapisane na koncie ADLA i ADLS w regionie pomocniczym.

## <a name="next-steps"></a>Następne kroki

[Wskazówki dotyczące odzyskiwania danych w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
