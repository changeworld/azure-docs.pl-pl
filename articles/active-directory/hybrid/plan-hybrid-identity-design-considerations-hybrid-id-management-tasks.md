---
title: Zadania zarządzania projektowania tożsamości hybrydowej — Azure | Dokumentacja firmy Microsoft
description: Kontrola dostępu warunkowego usługi Azure Active Directory sprawdza, czy określonych warunków, wybranego podczas uwierzytelniania użytkownika, a jeśli tak, to przed zezwoleniem na dostęp do aplikacji. Po spełnieniu tych warunków, użytkownik jest uwierzytelniony i zezwolenie na dostęp do aplikacji.
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
ms.openlocfilehash: 16d20e9ea2203c788a2b7b5eb0507b357350407d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918494"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Planowanie cyklu życia tożsamości hybrydowej
Tożsamość jest jedną z podstaw enterprise mobility i aplikacji strategii dostępu. Czy logujesz się Twoje urządzenie przenośne lub aplikacji SaaS, swoją tożsamość jest kluczem do uzyskania dostępu do wszystkich. Na najwyższym poziomie rozwiązanie do zarządzania tożsamościami obejmuje, ujednolicając naukę i synchronizacja między repozytoriami tożsamości w tym automatyzacji i centralizowanie proces aprowizacji zasobów. Rozwiązanie tożsamości powinien być scentralizowane tożsamości lokalnych i w chmurze i również użyć jakiegoś Federację tożsamości do utrzymywania scentralizowanego uwierzytelniania i bezpiecznego udostępniania i współpracować z zewnętrznych użytkowników i firm. Zasoby do zakresu od systemów operacyjnych i aplikacji do osób w lub powiązane z organizacji. Może się zmienić strukturę organizacyjną aby pomieścić inicjowania obsługi zasad i procedur.

Jest również posiadanie rozwiązanie tożsamości zapewniające obsługę wysokowydajnych zwiększać możliwości użytkowników przez zapewnienie im z samoobsługi środowiska, aby zachować produktywność. Rozwiązanie tożsamości jest bardziej niezawodne, jeśli włączenie logowania jednokrotnego dla użytkowników wszystkich zasobów potrzebują dostępu. Administratorzy na wszystkich poziomach standardowe procedury służą do zarządzania poświadczeniami użytkownika. Niektóre poziomy administracji można zredukować lub usunięte, w zależności od zakresu inicjowania obsługi administracyjnej rozwiązania do zarządzania. Ponadto można bezpiecznie rozpowszechniać możliwości administrowania ręcznie lub automatycznie wśród różnych organizacji. Na przykład administrator domeny może obsługiwać tylko osoby i zasobów w tej domenie. Ten użytkownik może wykonywać zadania administracyjne i inicjowania obsługi administracyjnej, ale nie ma uprawnień do wykonywania zadań konfiguracji, takich jak tworzenie przepływów pracy.

## <a name="determine-hybrid-identity-management-tasks"></a>Określić zadania związane z zarządzaniem tożsamości hybrydowej
Przesyłanie zadań administracyjnych w Twojej organizacji zwiększa dokładność i efektywności administracji i poprawia Równoważenie obciążenia organizacji. Poniżej przedstawiono tabele przestawne, które zdefiniować systemu zarządzania tożsamościami niezawodne.

 ![zagadnienia dotyczące zarządzania tożsamościami](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Aby zdefiniować zadania związane z zarządzaniem tożsamościami hybrydowe, musisz poznać niektóre podstawowe właściwości organizacji, która zostanie przyjęty tożsamości hybrydowej. Należy zrozumieć bieżące repozytoriów, używanych na potrzeby źródła tożsamości. Znajomość tych podstawowych elementów, można będzie miał podstawowe wymagania i na podstawie należy zadać bardziej szczegółowe pytania, które prowadzą do lepszego decyzja projektowa rozwiązania tożsamości.  

Podczas definiowania tych wymagań, upewnij się, że co najmniej odpowiedzi na następujące pytania

* Opcje inicjowania obsługi: 
  
  * Rozwiązania z tożsamością hybrydową obsługuje niezawodne konta zarządzania dostępem i aprowizacji systemu?
  * Jak są użytkowników, grup i haseł, które mają być zarządzane?
  * Zarządzanie cyklem życia tożsamości jest elastyczny? 
    * Jak zawieszenie konta aktualizacji hasła długo?
* Zarządzanie licencjami: 
  
  * Czy hybrydowe rozwiązanie obsługuje licencji Zarządzanie tożsamościami?
    * Jeśli tak, jakie funkcje są dostępne?
* Rozwiązanie obsługuje zarządzanie licencjami na podstawie grupy? 
  
      - Jeśli tak, czy jest możliwe do przypisania grupy zabezpieczeń? 
       - Jeśli tak, katalog w chmurze automatycznie przypisze licencji wszystkich członków grupy? 
        - Co się stanie, jeśli użytkownik jest następnie dodawane do lub usunięte z grupy, będzie licencji można automatycznie przypisywane lub usuwane odpowiednio? 
* Integracja z innymi dostawcami tożsamości innych firm:
* Można zintegrować to rozwiązanie hybrydowego za pomocą dostawców tożsamości innych firm do implementowania logowania jednokrotnego?
* Jest możliwe, o ujednolicenie wszystkich dostawców innej tożsamości w systemie spójnych tożsamości?
* Jeśli tak, jak i które są one i jakie funkcje są dostępne?

## <a name="synchronization-management"></a>Zarządzanie synchronizacji
Jednym z celów programu identity manager, aby można było przenieść wszystkich dostawców tożsamości i przechowywać je zsynchronizować. Zachowaj dane synchronizowane oparte na dostawcy tożsamości wzorca autorytatywne. Scenariusza z tożsamością hybrydową, przy użyciu modelu zsynchronizowane zarządzania służy do zarządzania wszystkie tożsamości użytkowników i urządzeń na serwerze lokalnym i zsynchronizować konta i, opcjonalnie, hasła do chmury. Użytkownik wprowadza tego samego hasła lokalnie, jak robią w chmurze, a przy logowaniu, hasło jest weryfikowane przez rozwiązanie tożsamości. Ten model wykorzystuje narzędzia do synchronizacji katalogów.

![Synchronizacja katalogów](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) odpowiedniego projektu synchronizacji rozwiązania z tożsamością hybrydową upewnij się, że należy odpowiedzieć na następujące pytania: • co to są dostępne dla rozwiązania tożsamości hybrydowej rozwiązania synchronizacji?
• Jakie są możliwości logowania jednokrotnego?
• Jakie są opcje dla Federację tożsamości między B2B i B2C?

## <a name="next-steps"></a>Kolejne kroki
[Określenia strategii wdrażania zarządzania tożsamości hybrydowej](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

