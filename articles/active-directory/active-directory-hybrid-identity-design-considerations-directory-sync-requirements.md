---
title: Tożsamość hybrydowym - wymagań synchronizacji katalogu Azure | Dokumentacja firmy Microsoft
description: Określenie, jakie wymagania niezbędne do synchronizowania wszystkich użytkowników między on = lokalnych i w chmurze dla przedsiębiorstw.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: 475b35c767fcc9c42b828dd2219a14890e05f779
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2018
---
# <a name="determine-directory-synchronization-requirements"></a>Określenie wymagań synchronizacji katalogu
Synchronizacja jest udostępnianie tożsamości w chmurze na podstawie ich tożsamości lokalnych użytkowników. Czy zsynchronizowane konta będzie używany do uwierzytelniania lub uwierzytelnianie federacyjne, użytkownicy będą nadal musi być tożsamości w chmurze.  Ta tożsamość należy utrzymanie i okresowo aktualizowane.  Aktualizacje mogą mieć wiele form, od zmian tytuł do zmiany hasła.  

Rozpocznij od szacowania organizacji lokalnej tożsamości rozwiązanie i użytkownika wymagań. Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące sposobu tworzenia i obsługiwane w chmurze tożsamości użytkowników.  W przypadku większości organizacji usługi Active Directory działa lokalnie i będą katalog lokalny, który użytkownicy będą przez synchronizowane z, jednak w niektórych przypadkach nie będzie wielkość liter.  

Upewnij się, że należy odpowiedzieć na następujące pytania:

* Masz jeden las usługi AD, wielu lub Brak?
  
  * Ile katalogów usługi Azure AD będzie można być synchronizowanie?
    
    1. Używasz filtrowania?
    2. Masz wiele serwerów usługi Azure AD Connect planowana?
* Obecnie masz synchronizacji narzędzia lokalnej?
  
  * Jeśli tak, czy użytkownicy, jeśli użytkownicy mają wirtualnego katalogu/integracji tożsamości?
* Masz wszelkie inne katalogu lokalnego, które mają być synchronizowane (np. katalogu LDAP, bazy danych działu HR, itp.)?
  * Zamierzasz realizacji dowolnej usługi GALSync?
  * Co to jest bieżący stan nazwy UPN w organizacji? 
  * Masz innego katalogu, który uwierzytelniania użytkowników?
  * Firma używa programu Microsoft Exchange?
    * Są one plan o wdrożeniu hybrydowym programu exchange?

Teraz, gdy masz pomysł o wymaganiach synchronizacji, należy określić, które narzędzie jest poprawne tych wymagań.  Firma Microsoft udostępnia kilka narzędzi integracji katalogów i synchronizacji.  Zobacz [Tabela porównawcza narzędzi integracji katalogu tożsamości hybrydowej](active-directory-hybrid-identity-design-considerations-tools-comparison.md) Aby uzyskać więcej informacji. 

Teraz, gdy masz wymagań synchronizacji i narzędzie, które będą w tym celu w firmie, należy ocenić aplikacje, które używają tych usług katalogowych. Ocena ważne jest, aby zdefiniować wymagania techniczne do integrowania tych aplikacji w chmurze. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Zostaną przeniesione następujące aplikacje w chmurze i użyć katalogu?
* Czy istnieją specjalne atrybuty, które muszą zostać zsynchronizowane w chmurze, więc tych aplikacji można używać ich pomyślnie?
* Te aplikacje będą potrzebne do zapisania ponownie korzystać z uwierzytelniania w chmurze?
* Te aplikacje będzie live lokalnego, podczas gdy użytkownicy uzyskują dostęp do ich przy użyciu tożsamości chmury?

Należy również określić synchronizacji katalogów wymagań i ograniczeń zabezpieczeń. Ocena ważne jest, aby uzyskać listę wymagań, które będą potrzebne w celu tworzenia i zarządzania tożsamościami użytkowników w chmurze. Upewnij się, że należy odpowiedzieć na następujące pytania:

* Której zostaną umieszczone serwera synchronizacji?
* Będzie on zostać przyłączone do domeny?
* Serwer zostanie umieszczony w sieci z ograniczeniami za zaporą, takich jak sieć obwodowa?
  * Użytkownik będzie mógł otworzyć porty zapory wymagane do obsługi synchronizacji?
* Czy organizacja ma plan odzyskiwania po awarii dla serwera synchronizacji?
* Masz konto z odpowiednie uprawnienia do wszystkich lasów, które chcesz zsynchronizować z?
  * Jeśli firma nie znasz odpowiedzi na to pytanie, zapoznaj się z sekcją "Uprawnień do synchronizacji haseł" w artykule [zainstalować usługi Azure do synchronizacji usługi Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) i ustalić, jeśli masz już konto z odpowiednimi uprawnieniami lub jeśli chcesz utworzyć.
* Jeśli masz synchronizacji mutli lasu jest serwer synchronizacji można uzyskać w każdym lesie?

> [!NOTE]
> Upewnij się zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określenie wymagań dotyczących odpowiedzi na zdarzenia](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) będą przekazywane dostępnych opcji. Po udzieleniu odpowiedzi na te pytania można wybrać opcji najlepiej pasujące do firmy wymaga.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określić wymagania dotyczące uwierzytelniania wieloskładnikowego](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](active-directory-hybrid-identity-design-considerations-overview.md)

