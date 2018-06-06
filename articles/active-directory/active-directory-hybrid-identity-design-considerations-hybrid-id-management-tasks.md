---
title: Zadania zarządzania projektowania tożsamości hybrydowej - Azure | Dokumentacja firmy Microsoft
description: Z kontroli dostępu warunkowego usługi Azure Active Directory sprawdza określonych warunków, można wybrać podczas uwierzytelniania użytkownika i przed zezwoleniem na dostęp do aplikacji. Gdy te warunki są spełnione, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 5fcb229690827c7bd508aa0d27dd68b169b4e25e
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801330"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plan cyklu życia tożsamości hybrydowej
Tożsamość jest jednym z kluczowych enterprise mobility i stosowania strategii dostępu. Czy są zalogować się do Twojego urządzenia przenośnego lub aplikacji SaaS, tożsamości jest klucz do uzyskania dostępu do wszystkich składników. Na najwyższym poziomie rozwiązania do zarządzania tożsamościami obejmuje połączenie i synchronizacja między repozytoriami tożsamości w tym automatyzacji oraz scentralizowany proces inicjowania obsługi administracyjnej zasobów. Rozwiązanie tożsamości powinny być scentralizowane tożsamości w ramach lokalnej i w chmurze i również formularz niektórych federacji tożsamości Obsługa scentralizowane uwierzytelnianie i bezpiecznie udostępnić i współpracę z użytkowników zewnętrznych i firm. Zasoby należeć do zakresu od systemów operacyjnych i aplikacji do osób w lub powiązane z organizacji. Struktura organizacyjna może się zmienić do inicjowania obsługi zasad i procedur.

Należy również jest przeznaczone dla użytkowników przez zapewnienie im z samoobsługi środowiska, aby zachować produktywność rozwiązanie tożsamości. Rozwiązania tożsamości jest bardziej niezawodną metodą, jeśli umożliwia logowanie jednokrotne użytkowników we wszystkich zasobów potrzebują dostępu. Administratorzy na wszystkich poziomach standardowe procedury służą do zarządzania poświadczeniami użytkownika. Niektóre poziomy administracyjnej można zredukować lub usunięte, w zależności od szerokości inicjowania obsługi administracyjnej rozwiązania do zarządzania. Ponadto można bezpiecznie rozpowszechniać możliwości administrowania ręcznie lub automatycznie, między różnymi organizacjami. Na przykład administrator domeny może obsługiwać tylko osoby i zasobów w tej domenie. Ten użytkownik może wykonywać zadania administracyjne i inicjowania obsługi administracyjnej, ale nie ma uprawnień do wykonywania zadań konfiguracji, takich jak tworzenie przepływów pracy.

## <a name="determine-hybrid-identity-management-tasks"></a>Określić zadań zarządzania tożsamości hybrydowej
Przesyłanie zadań administracyjnych w organizacji zwiększa dokładność i efektywności administracji i zwiększa równoważenia obciążenia w organizacji. Poniżej przedstawiono przestawień, definiujące tożsamości niezawodny system zarządzania.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)

Aby zdefiniować zadania zarządzania tożsamościami hybrydowe, należy zrozumieć pewne istotne cechy organizacji, która zostanie przyjęty tożsamość hybrydowa. Należy zrozumieć bieżące repozytoriów, używane dla tożsamości źródeł. Znajomość tych podstawowych elementów, możesz będzie miał podstawowych wymagań i na podstawie należy poprosić bardziej szczegółowe pytania, które prowadzić do lepszego decyzji projektowej, rozwiązania do tożsamości.  

Podczas definiowania tych wymagań, upewnij się, że co najmniej odpowiedzi na następujące pytania

* Opcje inicjowania obsługi: 
  
  * Rozwiązania z tożsamością hybrydową obsługuje niezawodny konta dostępu do zarządzania i inicjowania obsługi administracyjnej systemu?
  * W jaki sposób użytkowników, grup i haseł, które mają być zarządzane?
  * Zarządzanie cyklem życia tożsamości jest elastyczny? 
    * Jak długo zawieszenie konta aktualizacji hasła podąża?
* Zarządzanie licencjami: 
  
  * Obsługuje zarządzanie licencji uchwytów rozwiązania tożsamości hybrydowe?
    * Jeśli tak, jakie funkcje są dostępne?
* Rozwiązanie obsługuje zarządzanie licencjami na podstawie grupy? 
  
      - Jeśli tak, czy jest możliwe przypisać grupie zabezpieczeń? 
       - Jeśli tak, katalogiem chmury automatycznie przypisze licencji do wszystkich członków grupy? 
        - Co się stanie, jeśli użytkownik jest następnie dodane do lub usunięte z grupy, zostaną licencji automatycznie przypisane lub usunięte odpowiednio? 
* Integracja z innych dostawców tożsamości innych firm:
* To rozwiązanie hybrydowe można zintegrować z dostawców tożsamości innych firm do implementowania rejestracji jednokrotnej?
* Czy jest możliwe ujednolicenie wszystkich dostawców innej tożsamości w systemie spójnych tożsamości?
* Jeśli tak, jak i które są one i jakie funkcje są dostępne?

## <a name="synchronization-management"></a>Zarządzanie synchronizacji
Jednym z celów programu identity manager, aby można było przełączyć wszystkich dostawców tożsamości i przechowywać je zsynchronizować. Zachowaj synchronizowania danych oparte na dostawcy tożsamości wzorca autorytatywnych. W scenariuszu hybrydowym tożsamości, z modelem zarządzania zsynchronizowane wszystkich tożsamości użytkowników i urządzeń w lokalnym serwerze zarządzania i zsynchronizować konta i, opcjonalnie, hasła do chmury. Użytkownik wprowadza tego samego hasła lokalnie w chmurze, a podczas logowania, hasło jest weryfikowany przez rozwiązanie tożsamości. Ten model korzysta z narzędzia synchronizacji katalogów.

![](./media/hybrid-id-design-considerations/Directory_synchronization.png) Do prawidłowego projektu synchronizacji rozwiązania z tożsamością hybrydową upewnij się, że należy odpowiedzieć na następujące pytania: • jakie są dostępne dla rozwiązania z tożsamością hybrydową rozwiązania synchronizacji?
• Co to są dostępne możliwości logowania jednokrotnego?
• Jakie są opcje Federację tożsamości między B2B i B2C?

## <a name="next-steps"></a>Kolejne kroki
[Określić strategii wdrażania zarządzania tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

