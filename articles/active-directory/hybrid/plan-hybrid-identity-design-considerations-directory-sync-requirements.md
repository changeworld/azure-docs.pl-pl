---
title: Projektowania tożsamości hybrydowej — wymagania dotyczące synchronizacji katalogu usługi Azure | Dokumentacja firmy Microsoft
description: Zidentyfikuj, jakie wymagania są wymagane do zsynchronizowania wszystkich użytkowników między on = lokalnie i w chmurze dla przedsiębiorstw.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381165"
---
# <a name="determine-directory-synchronization-requirements"></a>Określić wymagania dotyczące synchronizacji katalogu
Synchronizacja jest udostępnianie użytkownikom tożsamość w chmurze, na podstawie ich tożsamości w środowisku lokalnym. Czy używają zsynchronizowane konta do uwierzytelniania lub uwierzytelnianie federacyjne, użytkownicy nadal będą musieli miał tożsamość w chmurze.  Tej tożsamości, musisz być utrzymywane i okresowo aktualizowana.  Aktualizacje mogą mieć wiele form, od zmian tytuł do zmiany hasła.  

Rozpocznij od oceny organizacje lokalne wymagania dotyczące rozwiązania i użytkownika tożsamości. Ocena ważne jest, aby zdefiniować wymagania techniczne dotyczące sposobu tworzenia i przechowywane w chmurze tożsamości użytkowników.  W przypadku większości organizacji usługi Active Directory działa lokalnie i będą katalogu lokalnego, który użytkownicy będą przez synchronizowane z, jednak w niektórych przypadkach nie będzie tak.  

Pamiętaj odpowiedzieć na następujące pytania:

* Masz jeden las usługi AD, wiele lub Brak?
  
  * Jak wiele katalogów usługi Azure AD będzie można być synchronizowany z?
    
    1. Używasz filtrowania?
    2. Masz wiele serwerów usługi Azure AD Connect planowana?
* Obecnie masz synchronizacji narzędzia w środowisku lokalnym?
  
  * Jeśli tak, czy użytkownicy, jeśli użytkownicy mają wirtualnego katalogu/integracja tożsamości?
* Masz żadnych innych katalogu lokalnego, które chcesz synchronizować (np. katalogu LDAP, bazy danych działu HR, itp.)?
  * Czy będą wykonywać czynności wszelkie GALSync?
  * Co to jest bieżący stan nazwy UPN w swojej organizacji? 
  * Czy masz inny katalog, który użytkownicy są uwierzytelniani względem?
  * Firma używa programu Microsoft Exchange?
    * Czy planują wdrożenie hybrydowe programu exchange o?

Teraz, gdy masz pomysł o wymaganiach dotyczących usługi synchronizacji, należy określić, które narzędzie jest właściwy, aby spełnić te wymagania.  Firma Microsoft udostępnia kilka narzędzi, aby osiągnąć Integracja katalogu i synchronizacji.  Zobacz [Tabela porównawcza narzędzi integracji katalogu tożsamości hybrydowej](plan-hybrid-identity-design-considerations-tools-comparison.md) Aby uzyskać więcej informacji. 

Teraz, gdy masz wymagań synchronizacji i narzędzia które będzie to zrobić dla Twojej firmy, należy ocenić aplikacje, które używają tych usług katalogowych. Ocena ważne jest, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje do chmury. Pamiętaj odpowiedzieć na następujące pytania:

* Zostaną przeniesione te aplikacje do chmury i korzystają z katalogu?
* Istnieją specjalne atrybuty, które muszą zostać zsynchronizowane z chmurą, więc aplikacje te można ich używać?
* Te aplikacje będą ponownego zapisania może korzystać z uwierzytelniania w chmurze?
* Te aplikacje będą na żywo w środowisku lokalnym, podczas gdy użytkownicy uzyskiwać do nich dostęp przy użyciu tożsamości chmury?

Należy również określić synchronizacji katalogów wymagań i ograniczeń zabezpieczeń. Ocena ważne jest, aby uzyskać listę wymagań, które będą potrzebne w celu tworzenia i zarządzania tożsamościami użytkowników w chmurze. Pamiętaj odpowiedzieć na następujące pytania:

* Której zostaną umieszczone serwera synchronizacji?
* Czy będzie przyłączony do domeny?
* Serwer zostanie umieszczony w sieci z ograniczeniami za zaporą, takich jak sieć obwodowa?
  * Użytkownik będzie mógł otworzyć porty zapory wymagane, aby możliwa była synchronizacja?
* Czy masz plan odzyskiwania po awarii dla serwera synchronizacji?
* Czy masz konto z odpowiednimi uprawnieniami dla wszystkich lasów, chcesz zsynchronizować z?
  * Jeśli Twoja firma nie znasz odpowiedzi na to pytanie, zapoznaj się z sekcją "Uprawnienia do synchronizacji haseł" w artykule [Instalowanie usługi Azure Active Directory Sync](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) i określić, jeśli masz już konto za pomocą tych uprawnień, czy muszę ją utworzyć.
* Jeśli masz lasu odpowiednie synchronizacja jest serwer synchronizacji mogła uzyskać dostęp do wszystkich lasów?

> [!NOTE]
> Pamiętaj zanotować wszystkie odpowiedzi i zrozumieć uzasadnienie. [Określanie wymagań dotyczących odpowiedzi na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zostanie umieszczona nad dostępnych opcji. Po udzieleniu odpowiedzi na te pytania można wybrać opcję, która najlepiej pasujące do działalności potrzebuje.
> 
> 

## <a name="next-steps"></a>Kolejne kroki
[Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zobacz także
[Omówienie zagadnień dotyczących projektowania](plan-hybrid-identity-design-considerations-overview.md)

