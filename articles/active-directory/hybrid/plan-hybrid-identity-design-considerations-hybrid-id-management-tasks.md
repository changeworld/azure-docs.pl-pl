---
title: Projekt tożsamości hybrydowej — zadania zarządzania Azure | Dokumenty firmy Microsoft
description: Dzięki kontroli dostępu warunkowego usługa Azure Active Directory sprawdza określone warunki, które można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków użytkownik jest uwierzytelniony i ma dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109382"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planowanie cyklu życia tożsamości hybrydowej
Tożsamość jest jednym z fundamentów strategii mobilności i dostępu do aplikacji w przedsiębiorstwie. Niezależnie od tego, czy logujesz się na urządzeniu przenośnym, czy w aplikacji SaaS, Twoja tożsamość jest kluczem do uzyskania dostępu do wszystkiego. Na najwyższym poziomie rozwiązanie do zarządzania tożsamościami obejmuje ujednolicenie i synchronizację między repozytoriami tożsamości, co obejmuje automatyzację i centralizację procesu inicjowania obsługi administracyjnej zasobów. Rozwiązanie tożsamości powinno być scentralizowaną tożsamością w środowisku lokalnym i w chmurze, a także używać jakiejś formy federacji tożsamości do obsługi scentralizowanego uwierzytelniania i bezpiecznego udostępniania i współpracy z zewnętrznymi użytkownikami i firmami. Zasoby obejmują systemy operacyjne i aplikacje po osoby w organizacji lub z nią powiązane. Struktura organizacyjna może być zmieniona w celu uwzględnienia zasad i procedur inicjowania obsługi administracyjnej.

Ważne jest również, aby mieć rozwiązanie tożsamości ukierunkowane na wzmocnienie pozycji użytkowników, zapewniając im samoobsługowe doświadczenia, aby zapewnić im produktywność. Rozwiązanie tożsamości jest bardziej niezawodne, jeśli umożliwia logowanie jednokrotne dla użytkowników we wszystkich zasobach, do których mają dostęp. Administratorzy na wszystkich poziomach mogą używać standardowych procedur zarządzania poświadczeniami użytkownika. Niektóre poziomy administracji można zmniejszyć lub wyeliminować, w zależności od zakresu rozwiązania do zarządzania inicjowania obsługi administracyjnej. Ponadto można bezpiecznie dystrybuować możliwości administracyjne, ręcznie lub automatycznie, między różnymi organizacjami. Na przykład administrator domeny może obsługiwać tylko osoby i zasoby w tej domenie. Ten użytkownik może wykonywać zadania administracyjne i inicjowania obsługi administracyjnej, ale nie jest autoryzowany do wykonywania zadań konfiguracyjnych, takich jak tworzenie przepływów pracy.

## <a name="determine-hybrid-identity-management-tasks"></a>Określanie zadań zarządzania tożsamościami hybrydowymi
Dystrybucja zadań administracyjnych w organizacji zwiększa dokładność i skuteczność administracji oraz poprawia równowagę obciążenia organizacji. Poniżej znajdują się przestawne, które definiują niezawodny system zarządzania tożsamościami.

 ![zagadnienia związane z zarządzaniem tożsamościami](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Aby zdefiniować zadania zarządzania tożsamościami hybrydowymi, należy zrozumieć niektóre podstawowe cechy organizacji, która będzie przyjmować tożsamość hybrydową. Ważne jest, aby zrozumieć bieżące repozytoria używane dla źródeł tożsamości. Znając te podstawowe elementy, będziesz mieć podstawowe wymagania i na podstawie tego trzeba będzie zadać bardziej szczegółowe pytania, które doprowadzą Cię do lepszej decyzji projektowej dla rozwiązania tożsamości.  

Definiując te wymogi, należy zapewnić, aby co najmniej następujące pytania były

* Opcje inicjowania obsługi administracyjnej: 
  
  * Czy rozwiązanie tożsamości hybrydowej obsługuje niezawodny system zarządzania dostępem do konta i inicjowania obsługi administracyjnej?
  * W jaki sposób będą zarządzane użytkownicy, grupy i hasła?
  * Czy zarządzanie cyklem życia tożsamości jest responsywne? 
    * Jak długo trwa zawieszenie konta aktualizacji haseł?
* Zarządzanie licencjami: 
  
  * Czy rozwiązanie tożsamości hybrydowej obsługuje zarządzanie licencjami?
    * Jeśli tak, jakie możliwości są dostępne?
  * Czy rozwiązanie obsługuje zarządzanie licencjami opartymi na grupach? 
  
    * Jeśli tak, czy można przypisać do niej grupę zabezpieczeń? 
    * Jeśli tak, czy katalog w chmurze automatycznie przypisze licencje do wszystkich członków grupy? 
    * Co się stanie, jeśli użytkownik zostanie następnie dodany lub usunięty z grupy, licencja zostanie automatycznie przypisana lub usunięta w razie potrzeby? 
* Integracja z innymi zewnętrznymi dostawcami tożsamości:
  * Czy to rozwiązanie hybrydowe można zintegrować z zewnętrznymi dostawcami tożsamości w celu wdrożenia logowania jednokrotnego?
  * Czy możliwe jest ujednolicenie wszystkich różnych dostawców tożsamości w spójny system tożsamości?
  * Jeśli tak, to jak i jakie są i jakie możliwości są dostępne?

## <a name="synchronization-management"></a>Zarządzanie synchronizacją
Jednym z celów menedżera tożsamości, aby móc przynieść wszystkich dostawców tożsamości i zachować je zsynchronizowane. Dane są synchronizowane na podstawie autorytatywnego dostawcy tożsamości wzorca. W scenariuszu tożsamości hybrydowej z zsynchronizowanym modelem zarządzania można zarządzać wszystkimi tożsamościami użytkowników i urządzeń na serwerze lokalnym i synchronizować konta i opcjonalnie hasła do chmury. Użytkownik wprowadza to samo hasło lokalnie, co w chmurze, a podczas logowania hasło jest weryfikowane przez rozwiązanie tożsamości. Ten model używa narzędzia do synchronizacji katalogów.

![synchronizacja](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) katalogów Aby prawidłowo zaprojektować synchronizację rozwiązania tożsamości hybrydowej, należy odpowiedzieć na następujące pytania:
*    Jakie rozwiązania do synchronizacji są dostępne dla rozwiązania tożsamości hybrydowej?
*    Jakie są możliwości logowania jednokrotnego?
*    Jakie są opcje federacji tożsamości między B2B i B2C?

## <a name="next-steps"></a>Następne kroki
[Określanie strategii wdrażania zarządzania tożsamościami hybrydowymi](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

