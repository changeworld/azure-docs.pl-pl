---
title: Rozpoczynanie pracy z usługą Azure Scheduler w witrynie Azure Portal | Microsoft Docs
description: Rozpoczynanie pracy z usługą Azure Scheduler w portalu Azure
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/10/2016
ms.author: deli
ms.openlocfilehash: f03ddb475835b30e9b931b7f057c062b57ac45f3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31413434"
---
# <a name="get-started-with-azure-scheduler-in-azure-portal"></a>Rozpoczynanie pracy z usługą Azure Scheduler w portalu Azure
Azure Scheduler umożliwia łatwe tworzenie zaplanowanych zadań. Niniejszy samouczek zawiera informacje na temat tworzenia zadania. Są w nim zawarte także informacje na temat możliwości monitorowania oraz zarządzania, jakie oferuje usługa Scheduler.

## <a name="create-a-job"></a>Tworzenie zadania
1. Zaloguj się w [portalu Azure](https://portal.azure.com/).  
2. Kliknij przycisk **+ Nowy** > wpisz w polu wyszukiwania *Harmonogram* > wybierz z wyników **Harmonogram** > kliknij **Utwórz**.
   
    ![][marketplace-create]
3. Utwórzmy zadanie, które po prostu przesyła żądanie GET do witryny http://www.microsoft.com/. Na ekranie **Zadanie usługi Scheduler** wprowadź następujące informacje:
   
   1. **Nazwa:** `getmicrosoft`  
   2. **Subskrypcja:** subskrypcja usługi Azure użytkownika   
   3. **Kolekcja zadań:** wybierz istniejącą kolekcję zadań lub kliknij przycisk **Utwórz nową** > wprowadź nazwę.
4. Następnie określ w obszarze **Ustawienia akcji** następujące wartości:
   
   1. **Typ akcji:** ` HTTP`  
   2. **Metoda:** `GET`  
   3. **Adres URL:** ` http://www.microsoft.com`  
      
      ![][action-settings]
5. Ostatnią czynnością jest zdefiniowanie harmonogramu. To zadanie można ustawić jako jednorazowe, my skorzystamy jednak z możliwości wybrania harmonogramu cyklu:
   
   1. **Cykl**: `Recurring`
   2. **Uruchom**: dzisiejsza data
   3. **Powtarzaj co**: `12 Hours`
   4. **Zakończ**: dwa dni od dnia dzisiejszego  
      
      ![][recurrence-schedule]
6. Kliknij przycisk **Utwórz**

## <a name="manage-and-monitor-jobs"></a>Zarządzanie i monitorowanie zadań
Utworzone zadanie zostaje wyświetlone na głównym pulpicie nawigacyjnym Azure. Kliknij zadanie. Zostanie otwarte nowe okno z następującymi kartami:

1. Właściwości  
2. Ustawienia akcji  
3. Harmonogram  
4. Historia
5. Użytkownicy
   
   ![][job-overview]

### <a name="properties"></a>Właściwości
Te właściwości tylko do odczytu opisują metadane dotyczące zarządzania odnoszące się do zadania usługi Scheduler.

   ![][job-properties]

### <a name="action-settings"></a>Ustawienia akcji
Kliknięcie zadania na ekranie **Zadania** umożliwia jego konfigurowanie. Dzięki temu można skonfigurować ustawienia zaawansowane, które nie zostały skonfigurowane za pomocą kreatora szybkiego tworzenia.

W przypadku wszystkich typów akcji istnieje możliwość zmiany zasad ponawiania i akcji błędu.

Dla typów akcji zadań HTTP i HTTPS można zmienić metodę na dowolne dozwolone zlecenie HTTP. Można również dodawać, usuwać lub zmieniać nagłówki i podstawowe informacje uwierzytelnienia.

W przypadku typów akcji kolejki magazynu można zmienić konto magazynu, nazwę kolejki, token SAS oraz treść.

W przypadku typów akcji magistrali usług można zmieniać przestrzenie nazw, ścieżki tematu/kolejki, ustawienia uwierzytelniania, typ transportu, właściwości wiadomości oraz treść wiadomości.

   ![][job-action-settings]

### <a name="schedule"></a>Harmonogram
Tu można ponownie skonfigurować harmonogram utworzony z użyciem kreatora szybkiego tworzenia i wprowadzić w nim zmiany.

Zapewnia to możliwość tworzenia [złożonych harmonogramów i zaawansowanych cykli w ramach zadania](scheduler-advanced-complexity.md)

Możesz zmienić datę i godzinę rozpoczęcia, harmonogram cyklu oraz datę i godzinę zakończenia (jeśli zadanie ma charakter cykliczny).

   ![][job-schedule]

### <a name="history"></a>Historia
Na karcie **Historia** są wyświetlane wybrane metryki odnoszące się do każdego wykonania wybranego zadania w systemie. Metryki te przedstawiają wartości dotyczące stanu harmonogramu w czasie rzeczywistym:

1. Stan  
2. Szczegóły  
3. Liczba ponownych prób
4. Wystąpienie: 1., 2., 3. itp.
5. Godzina rozpoczęcia wykonywania  
6. Godzina zakończenia wykonywania
   
   ![][job-history]

Możesz kliknąć przebieg, aby wyświetlić odnoszące się do niego dane z sekcji **Szczegóły historii**, łącznie z całością odpowiedzi dla każdego wykonania. To okno dialogowe umożliwia także skopiowanie odpowiedzi do schowka.

   ![][job-history-details]

### <a name="users"></a>Użytkownicy
Kontrola dostępu oparta na rolach (Role-Based Access Control, RBAC) na platformie Azure umożliwia precyzyjne zarządzanie dostępem dla usługi Azure Scheduler. Aby uzyskać informacje o sposobie korzystania z karty Użytkownicy, należy zapoznać się z tematem [Azure Role-Based Access Control](../role-based-access-control/role-assignments-portal.md) (Kontrola dostępu na podstawie ról na platformie Azure).

## <a name="see-also"></a>Zobacz też
 [Co to jest Scheduler?](scheduler-intro.md)

 [Pojęcia i terminologia dotyczące usługi Scheduler oraz hierarchia jednostek](scheduler-concepts-terms.md)

 [Plany i rozliczenia w usłudze Azure Scheduler](scheduler-plans-billing.md)

 [Tworzenie złożonych harmonogramów i zaawansowanych cykli z użyciem usługi Azure Scheduler](scheduler-advanced-complexity.md)

 [Dokumentacja interfejsu API REST usługi Scheduler](https://msdn.microsoft.com/library/mt629143)

 [Dokumentacja poleceń cmdlet programu PowerShell dla usługi Scheduler](scheduler-powershell-reference.md)

 [Wysoka dostępność i niezawodność usługi Scheduler](scheduler-high-availability-reliability.md)

 [Limity, wartości domyślne i kody błędów usługi Scheduler](scheduler-limits-defaults-errors.md)

 [Uwierzytelnianie połączeń wychodzących usługi Scheduler](scheduler-outbound-authentication.md)

[marketplace-create]: ./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png
[action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png
[recurrence-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png
[job-properties]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png
[job-overview]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png
[job-action-settings]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png
[job-schedule]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png
[job-history]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png
[job-history-details]: ./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png


[1]: ./media/scheduler-get-started-portal/scheduler-get-started-portal001.png
[2]: ./media/scheduler-get-started-portal/scheduler-get-started-portal002.png
[3]: ./media/scheduler-get-started-portal/scheduler-get-started-portal003.png
[4]: ./media/scheduler-get-started-portal/scheduler-get-started-portal004.png
[5]: ./media/scheduler-get-started-portal/scheduler-get-started-portal005.png
[6]: ./media/scheduler-get-started-portal/scheduler-get-started-portal006.png
[7]: ./media/scheduler-get-started-portal/scheduler-get-started-portal007.png
[8]: ./media/scheduler-get-started-portal/scheduler-get-started-portal008.png
[9]: ./media/scheduler-get-started-portal/scheduler-get-started-portal009.png
[10]: ./media/scheduler-get-started-portal/scheduler-get-started-portal010.png
[11]: ./media/scheduler-get-started-portal/scheduler-get-started-portal011.png
[12]: ./media/scheduler-get-started-portal/scheduler-get-started-portal012.png
[13]: ./media/scheduler-get-started-portal/scheduler-get-started-portal013.png
[14]: ./media/scheduler-get-started-portal/scheduler-get-started-portal014.png
[15]: ./media/scheduler-get-started-portal/scheduler-get-started-portal015.png
