---
title: Co to jest usługa Azure Scheduler? | Microsoft Docs
description: Usługa Azure Scheduler umożliwia opisywanie w sposób deklaratywny akcji do wykonania w chmurze. Usługa następnie planuje i uruchamia te akcje automatycznie.
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 52aa6ae1-4c3d-43fb-81b0-6792c84bcfae
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/18/2016
ms.author: deli
ms.openlocfilehash: a3bf1aacd6978499d7ef77cbcb451a06b857ac38
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "22715118"
---
# <a name="what-is-azure-scheduler"></a>Co to jest usługa Azure Scheduler?
Usługa Azure Scheduler umożliwia opisywanie w sposób deklaratywny akcji do wykonania w chmurze. Usługa następnie planuje i uruchamia te akcje automatycznie.  Usługa Scheduler korzysta w tym celu z witryny [Azure Portal](scheduler-get-started-portal.md), kodu, [interfejsu API REST](https://msdn.microsoft.com/library/mt629143.aspx) lub programu Azure PowerShell.

Usługa Scheduler tworzy, obsługuje i wywołuje zaplanowane prace.  Usługa Scheduler nie hostuje żadnych obciążeń ani nie uruchamia żadnego kodu. *Wywołuje* ona jedynie kod hostowany gdzie indziej — na platformie Azure, lokalnie lub przez innego dostawcę. Wywoływanie następuje z użyciem protokołu HTTP, HTTPS, kolejki magazynu, kolejki magistrali usług lub tematu magistrali usług.

Usługa Scheduler planuje [zadania](scheduler-concepts-terms.md), przechowuje i udostępnia do przeglądu historię wyników wykonania zadania, a także jednoznacznie i niezawodnie planuje uruchamianie obciążeń. Zadania WebJob Azure (element funkcji Web Apps w Usłudze aplikacji Azure) i inne funkcje planowania platformy Azure wykorzystują usługę Scheduler w tle. [Interfejs API REST usługi Scheduler](https://msdn.microsoft.com/library/mt629143.aspx) ułatwia zarządzanie komunikacją niezbędną do wykonywania tych akcji. W tym kontekście usługa Scheduler umożliwia prostą obsługę [złożonych harmonogramów i zaawansowanych cykli](scheduler-advanced-complexity.md).

Istnieje kilka scenariuszy, które nadają się do użycia z usługą Scheduler. Na przykład:

* *Cykliczne akcje aplikacji:* okresowe gromadzenie danych z serwisu Twitter w ramach źródła danych.
* *Codzienna konserwacja:* codzienne oczyszczanie dzienników, tworzenie kopii zapasowych i wykonywanie innych zadań konserwacji. Administrator może na przykład zdecydować, że tworzenie kopii zapasowej bazy danych będzie wykonywane codziennie o godzinie 01:00 przez następne dziewięć miesięcy.

Usługa Scheduler umożliwia programowe tworzenie, aktualizowanie, usuwanie i wyświetlanie zadań i [kolekcji zadań](scheduler-concepts-terms.md) oraz zarządzanie nimi za pomocą skryptów oraz w portalu.

## <a name="see-also"></a>Zobacz też
 [Pojęcia i terminologia dotyczące usługi Azure Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Rozpoczynanie pracy z usługą Scheduler w witrynie Azure Portal](scheduler-get-started-portal.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Tworzenie złożonych harmonogramów i zaawansowanych cykli z użyciem usługi Azure Scheduler](scheduler-advanced-complexity.md)

 [Dokumentacja interfejsu API REST usługi Azure Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Azure Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Azure Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Azure Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Azure Scheduler](scheduler-outbound-authentication.md)

