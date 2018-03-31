---
title: Porównywanie z innymi usługami Azure podobne usługi Azure Mobile Engagement
description: Porównanie usługi Azure Mobile Engagement z innych podobnych usług Azure - HockeyApp, AppInsights, centra powiadomień
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 1f114775-3a9a-4dd4-8d59-b10d1da9a68b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 57be3b235c5f439741155a70df7ddbe8a80906f2
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Porównywanie z innymi usługami Azure podobne usługi Azure Mobile Engagement
> [!IMPORTANT]
> Usługa Azure Mobile Engagement wycofaniu na 3/31/2018. Ta strona zostanie usunięta wkrótce po.
> 

Kiedykolwiek rozszerza się na liście usług oferowanych przez Microsoft Azure i w czasie może zastanawiasz się, jak usługa Azure Mobile Engagement różni się od tej usługi, która jest tylko do odczytu lub otrzymywać informacje o. W tym artykule próbuje wyczyścić pomyłek i kieruje do wyboru usługi Azure Mobile Engagement, gdy jest najbardziej odpowiednie dla Ciebie. 

Usługa Azure Mobile Engagement to usługa przeznaczone specjalnie **dla cyfrowego marketingu/CMOs** , ale mogą być używane przez żadnego **właściciel aplikacji mobilnej lub wydawcy** kto chce zwiększyć obciążenie, przechowywania i dodatkowych ich aplikacji mobilnych. 

*Jest to platforma stopnia zaangażowania użytkowników oprogramowanie jako usługa (SaaS), która zapewnia oparte na danych wgląd w użycie aplikacji, w przypadku segmentacji użytkowników w czasie rzeczywistym oraz umożliwia wysyłanie powiadomień wypychanych kontekstowe i wiadomości w aplikacji.* 

Niszczy tej definicji, będziemy mieć następujące właściwości klucza, które również wyodrębnić jego unikatowe wartości oferty:

1. **Powiadomienia wypychane kontekstowe i funkcji komunikatów w aplikacji**
   
   To jest fokus core produktu — wykonać powiadomień wypychanych docelowych i spersonalizowane. I w tym celu możemy zbierać dane złożone analizy behawioralnej. 
2. **Oparte na danych wgląd w użycie aplikacji**
   
   Udostępniamy międzyplatformowego zestawów SDK, aby zbierać analizy behawioralnej użytkowników aplikacji. Uwaga analizy behawioralnej termin (osób analizy wydajności), ponieważ możemy skupić się na aplikacji korzystania przez użytkowników aplikacji. Zbierane są dane analizy wydajności podstawowe o błędy, awarii itp., ale oznacza to nie fokus core produktu. 
3. **W przypadku segmentacji użytkowników w czasie rzeczywistym**
   
   Po zebraniu danych analizy behawioralnej użytkowników aplikacji, firma Microsoft umożliwiają segmentów odbiorców na podstawie różnych parametrów i zebranych danych, aby możliwe było uruchamiane kampanie wypychania docelowych. 
4. **Oprogramowanie — jako — usługa (SaaS):**
   
   Mamy portalu niezależnie od portalu zarządzania Azure, który jest zoptymalizowany do interakcji i wyświetlanie sformatowanego analizy behawioralnej użytkowników aplikacji i uruchom marketingu kampanie wypychania. Produkt jest dostosowana do Rozpoczęcie w żadnym momencie!   

Z tego zestawu odmienny ręcznie Oto jak porównać możemy pozornie Azure podobnymi — należy pamiętać, że artykuł nie porównanie poziomu funkcji, ale przyjmuje więcej scenariusza na podstawie podejście do definiowania produkt, dla którego działa najlepiej:

1. [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) przenośne rozwiązanie opracowywania oprogramowania firmy Microsoft. Dzięki niemu można dystrybuować kompilacje do testerów wersji beta, zbierać dane dotyczące awarii i uzyskiwać opinie użytkowników. Integruje się również z programu Visual Studio Team Services umożliwiające łatwe kompilacji wdrożeń oraz integrację elementu roboczego. 
   
   W tym miejscu koncentruje się na DevOps i zbierania danych analizy wydajności dotyczące aplikacji mobilnych. Może nastąpić przejście z obu HockeyApps integracji i usługi Mobile Engagement w aplikacji, które nie będą nietypowe, ponieważ mimo że ma niektórych nakładają się na siebie w zebranych danych, różni się fokus core produktów i pomagają w realizacji celów różnych dla Ciebie.  
2. [Usługa Application Insights](../application-insights/app-insights-overview.md) Jeśli aplikacji mobilnej ma po stronie serwera, a następnie użyje usługi Application Insights do monitorowania po stronie serwera sieci web aplikacji, ale dla aplikacji mobilnych po stronie klienta należy używać HockeyApp. 
3. [Centra powiadomień](https://azure.microsoft.com/services/notification-hubs/) udostępnia usługi LDS w tym sensie, że nie ma potrzeby SDK zintegrowane w aplikacji mobilnej i może mieć pełną kontrolę nad aplikacji mobilnej i umożliwia wysyłanie powiadomień wypychanych przy użyciu podstawowych funkcji znakowania. Jest to doskonałe rozwiązanie dla właściciela żadnych aplikacji, który dba mniej dotyczące elementów docelowych i bardziej wysyłania komunikacji informacji natychmiast ich aplikacji użytkownikom (emisji do wypełniania duże). 
   
   Należy pamiętać, fokus w tym miejscu przy wysyłaniu ogromną segmentacji podstawowe możliwości szybkiego powiadomienia. 

Spójrzmy kilka scenariuszy:

1. Timowi wchodzi w skład zespołu cyfrowy w magazynie Adventure Works, która publikuje aplikacji mobilnych dla użytkowników. Timowi jego celem jest upewnij się, że użytkownicy, którzy Pobierz swoje aplikacje mobilne nadal z niego korzystać i często. Ta zwiększa się nie tylko brand dołączyć użytkowników aplikacji, ale także dodatkowych zwiększa gdy użytkownicy aplikacji dokonywanie zakupów przy użyciu aplikacji mobilnej. W tym Timowi należy wysłać skierowanych powiadomień do użytkowników aplikacji, które warto poprosić o Otwórz aplikację, a następnie wróć do niego często znajduje się. Jest to, gdzie Timowi będą przydatne Mobile Engagement. 
2. Joann jest częścią zespół deweloperów z aplikacji mobilnych w Adventure Works i szuka produktu zalogować się szczegółowe informacje o awarii (Crash), wyjątków i Pobierz dane telemetryczne wydajność aplikacji. Jest to, gdzie Joann będą przydatne HockeyApp. Joann można zintegrować zarówno HockeyApp dla jej fokus deweloperów i usługi Azure Mobile Engagement dla Timowi w tej samej aplikacji, aby uzyskać najlepsze cechy obu. 
3. Działanie okrężne jest częścią zespół deweloperów aplikacji mobilnych w sieci firmy Contoso wiadomości, a wszystkie chce się wysłać krytycznych alertów wiadomości dla wszystkich użytkowników bez dużo segmentacji zaraz po wyzwoleniu alertu wiadomości. Jest to, gdzie działania okrężnego będą przydatne usługi Notification Hubs. 
   W tym scenariuszu istnieje możliwość jednak miarę rozwoju aplikacji mobilnej, jest wymagane nie znacznie bardziej rozbudowane segmentacji i uzyskać szczegółowe informacje o zachowanie użytkownika aplikacji. W tej chwili działania okrężnego będzie musiał oceny usługi Azure Mobile Engagement. 

Aby recap, cel usługi Mobile Engagement nie jest tylko do zbierania analytics — nie jest "jeszcze innego analityka produktu firmy Microsoft". Chodzi o wysyłanie powiadomień wypychanych docelowych i przeznaczony do tego, możemy zbierać dane analizy behawioralnej, ale fokus na wysyłanie powiadomień wypychanych, które najbardziej odpowiednie dla użytkowników aplikacji tak, aby nie pochodzi jako wiadomości-śmieci. 

Więcej szczegółów - Przyjrzyjmy się to [szybkie wideo](mobile-engagement-overview.md) dotyczące usługi Mobile Engagement w nutshell. 

