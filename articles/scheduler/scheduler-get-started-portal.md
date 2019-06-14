---
title: Tworzenie zaplanowanych zadań przy użyciu usługi Azure Scheduler — Azure Portal | Microsoft Docs
description: Dowiedz się, jak tworzyć, planować i uruchamiać swoje pierwsze zautomatyzowane zadanie przy użyciu usługi Azure Scheduler w witrynie Azure Portal
services: scheduler
ms.service: scheduler
ms.suite: infrastructure-services
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: e69542ec-d10f-4f17-9b7a-2ee441ee7d68
ms.topic: conceptual
ms.date: 09/17/2018
ms.openlocfilehash: 3b2cfc932c6322df8237ec7cdf820fc4242bfa72
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60532010"
---
# <a name="create-and-schedule-your-first-job-with-azure-scheduler---azure-portal"></a>Tworzenie i planowanie swojego pierwszego zadania przy użyciu usługi Azure Scheduler — Azure Portal

> [!IMPORTANT]
> Usługa [Azure Logic Apps](../logic-apps/logic-apps-overview.md) zastępuje usługę Azure Scheduler, która zostanie wycofana. Zamiast niej [spróbuj używać usługi Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) do planowania zadań. 

Ten samouczek pokazuje, jak łatwo można utworzyć i zaplanować zadanie, a następnie monitorować je i zarządzać nim. 

Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

## <a name="create-job"></a>Tworzenie zadania

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).  

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź „scheduler”. Wybierz z listy wyników **Scheduler**, a następnie wybierz **Utwórz**.

   ![Tworzenie zasobu usługi Scheduler](./media/scheduler-get-started-portal/scheduler-v2-portal-marketplace-create.png)

   Teraz utwórz zadanie, które wysyła żądanie GET do tego adresu URL: `https://www.microsoft.com/` 

1. W obszarze **Zadanie usługi Scheduler** wprowadź następujące informacje:

   | Właściwość | Przykładowa wartość | Opis |
   |----------|---------------|-------------| 
   | **Nazwa** | getMicrosoft | Nazwa zadania | 
   | **Kolekcja zadań** | <*job-collection-name*> | Utwórz kolekcję zadań lub wybierz istniejącą kolekcję. | 
   | **Subskrypcja** | <*Azure-subscription-name*> | Nazwa subskrypcji platformy Azure | 
   |||| 

1. Wybierz **Ustawienia akcji — Konfiguruj**, podaj te informacje, a następnie wybierz **OK** po wykonaniu tych czynności:

   | Właściwość | Przykładowa wartość | Opis |
   |----------|---------------|-------------| 
   | **Akcja** | **HTTP** | Typ akcji do uruchomienia | 
   | **Metoda** | **Get** | Metoda do wywołania | 
   | **Adres URL** | **https://www.microsoft.com** | Docelowy adres URL | 
   |||| 
   
   ![Definiowanie zadania](./media/scheduler-get-started-portal/scheduler-v2-portal-action-settings.png)

1. Wybierz **Harmonogram — Konfiguruj**, zdefiniuj harmonogram, a następnie wybierz **OK** po wykonaniu tych czynności:

   Chociaż można utworzyć zadanie jednorazowe, w tym przykładzie zostanie skonfigurowany harmonogram cyklu.

   | Właściwość | Przykładowa wartość | Opis |
   |----------|---------------|-------------| 
   | **Cykl** | **Cykliczny** | Zadanie jednorazowe lub cykliczne | 
   | **Rozpocznij w dniu** | <*dzisiejsza data*> | Data rozpoczęcia zadania | 
   | **Powtarzaj co** | **1 godz.** | Interwał i częstotliwość cyklu | 
   | **Zakończenie** | **Zakończ do**: dwa dni od dnia dzisiejszego | Data zakończenia zadania | 
   | **Przesunięcie względem czasu UTC** | **UTC +08:00** | Różnica czasu między uniwersalnym czasem koordynowanym (UTC) a czasem obowiązującym w lokalizacji | 
   |||| 

   ![Definiowanie harmonogramu](./media/scheduler-get-started-portal/scheduler-v2-portal-recurrence-schedule.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   Po utworzeniu zadania platforma Azure wdroży zadanie widoczne na pulpicie nawigacyjnym platformy Azure. 

1. Gdy platforma Azure wyświetli powiadomienie o pomyślnym wdrożeniu, wybierz polecenie **Przypnij do pulpitu nawigacyjnego**. W przeciwnym razie wybierz ikonę **Powiadomienia** (dzwonek) na pasku narzędzi platformy Azure, a następnie wybierz **Przypnij do pulpitu nawigacyjnego**.

## <a name="monitor-and-manage-jobs"></a>Monitorowanie zadań i zarządzanie nimi

Aby przejrzeć i monitorować zadanie oraz zarządzać nim, wybierz zadanie na pulpicie nawigacyjnym platformy Azure. W obszarze **Ustawienia** znajdują się obszary do przeglądania i zarządzania dotyczące zadania:

![Ustawienia zadania](./media/scheduler-get-started-portal/scheduler-v2-portal-job-overview-1.png)

Aby uzyskać więcej informacji na temat tych obszarów wybierz obszar:

* [**Właściwości**](#properties)
* [**Ustawienia akcji**](#action-settings)
* [**Harmonogram**](#schedule)
* [**Historia**](#history)
* [**Użytkownicy**](#users)

<a name="properties"></a>

### <a name="properties"></a>Właściwości

Aby wyświetlić właściwości tylko do odczytu, które opisują metadane dotyczące zarządzania zadaniem, wybierz pozycję **Właściwości**.

![Wyświetlanie właściwości zadania](./media/scheduler-get-started-portal/scheduler-v2-portal-job-properties.png)

<a name="action-settings"></a>

### <a name="action-settings"></a>Ustawienia akcji

Aby zmienić zaawansowane ustawienia zadania, wybierz pozycję **Ustawienia akcji**. 

![Przeglądanie ustawień akcji](./media/scheduler-get-started-portal/scheduler-v2-portal-job-action-settings.png)

| Typ akcji | Opis | 
|-------------|-------------| 
| Wszystkie typy | Możesz zmienić ustawienia **Zasady ponawiania** i **Akcja błędu**. | 
| HTTP i HTTPS | Ustawienie **Metoda** można zmienić na dowolną dozwoloną metodę. Można również dodawać, usuwać lub zmieniać nagłówki i informacje uwierzytelniania podstawowego. | 
| Kolejka magazynu| Można zmienić konto magazynu, nazwę kolejki, token SAS i treść. | 
| Service Bus | Można zmienić przestrzeń nazw, ścieżkę tematu lub kolejki, ustawienia uwierzytelniania, typ transportu, właściwości wiadomości oraz treść wiadomości. | 
||| 

<a name="schedule"></a>

### <a name="schedule"></a>Harmonogram

Po skonfigurowaniu harmonogramu za pomocą kreatora zadania można zmienić ten harmonogram, np. datę i godzinę rozpoczęcia, harmonogram cyklu oraz datę i godzinę zakończenia dla zadań cyklicznych.
Możesz także tworzyć bardziej [złożone harmonogramy i zaawansowane cykle](scheduler-advanced-complexity.md).

Aby zmienić widok lub zmienić harmonogram zadania, wybierz **Harmonogram**:

![Wyświetlanie harmonogramu zadania](./media/scheduler-get-started-portal/scheduler-v2-portal-job-schedule.png)

<a name="history"></a>

### <a name="history"></a>Historia

Aby wyświetlić metryki dotyczące każdego uruchomienia wybranego zadania, wybierz pozycję **Historia**. Metryki te przedstawiają wartości w czasie rzeczywistym o kondycji zadania, takie jak stan, liczba ponownych prób, liczba wystąpień, czas rozpoczęcia i czas zakończenia.

![Wyświetlanie historii i metryk zadań](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history.png)

Aby wyświetlić szczegóły historii dla każdego uruchomienia, np. pełną odpowiedź dotyczącą uruchomienia, w obszarze **Historia** wybierz poszczególne uruchomienia. 

![Wyświetlanie historii zadań](./media/scheduler-get-started-portal/scheduler-v2-portal-job-history-details.png)

<a name="users"></a>

### <a name="users"></a>Użytkownicy

Możesz zarządzać dostępem do usługi Azure Scheduler dla każdego użytkownika na poziomie szczegółowym za pomocą kontroli dostępu na podstawie ról (RBAC) platformy Azure. Aby dowiedzieć się, jak skonfigurować dostęp oparty na rolach, zobacz [Zarządzanie dostępem przy użyciu kontroli RBAC](../role-based-access-control/role-assignments-portal.md)

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o [pojęciach, terminologii i hierarchii jednostek](scheduler-concepts-terms.md)
* [Tworzenie złożonych i zaawansowanych harmonogramów cyklicznych](scheduler-advanced-complexity.md)
* Dowiedz się więcej o [wysokiej dostępności i niezawodności usługi Scheduler](scheduler-high-availability-reliability.md)
* Dowiedz się więcej o [limitach, limitach przydziału, wartościach domyślnych i kodach błędów](scheduler-limits-defaults-errors.md)
