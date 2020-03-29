---
title: Projekt tożsamości hybrydowej — wymagania dotyczące synchronizacji katalogów Platformy Azure | Dokumenty firmy Microsoft
description: Określ, jakie wymagania są potrzebne do synchronizacji wszystkich użytkowników między on=lokalnie i chmury dla przedsiębiorstwa.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381165"
---
# <a name="determine-directory-synchronization-requirements"></a>Określanie wymagań dotyczących synchronizacji katalogów
Synchronizacja polega na dostarczaniu użytkownikom tożsamości w chmurze na podstawie ich tożsamości lokalnej. Niezależnie od tego, czy będą używać zsynchronizowanego konta do uwierzytelniania lub uwierzytelniania federacyjnego, użytkownicy nadal będą musieli mieć tożsamość w chmurze.  Ta tożsamość będzie musiała być utrzymywana i okresowo aktualizowana.  Aktualizacje mogą przybierać wiele formularzy, od zmian tytułu po zmiany hasła.  

Zacznij od oceny lokalnych rozwiązań tożsamości organizacji i wymagań użytkownika. Ta ocena jest ważne, aby zdefiniować wymagania techniczne dotyczące sposobu tworzenia i obsługi tożsamości użytkowników w chmurze.  W większości organizacji usługa Active Directory jest lokalna i będzie katalogiem lokalnym, z których użytkownicy będą synchronizowani, jednak w niektórych przypadkach tak nie będzie.  

Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy masz jeden las AD, wiele lub żaden?
  
  * Ile katalogów usługi Azure AD będzie synchronizować?
    
    1. Czy korzystasz z filtrowania?
    2. Czy planowane jest wiele serwerów usługi Azure AD Connect?
* Czy obecnie masz narzędzie do synchronizacji lokalnie?
  
  * Jeśli tak, czy użytkownicy, jeśli użytkownicy mają katalog wirtualny/integrację tożsamości?
* Czy masz inny katalog lokalny, który chcesz zsynchronizować (np. katalog LDAP, baza danych HR itp.)?
  * Czy zamierzasz robić jakieś GALSync?
  * Jaki jest aktualny stan sieci UPN w organizacji? 
  * Czy masz inny katalog, na który użytkownicy uwierzytelniają?
  * Czy Twoja firma korzysta z programu Microsoft Exchange?
    * Czy planują wdrożenie wymiany hybrydowej?

Teraz, gdy masz pomysł na temat wymagań synchronizacji, należy określić, które narzędzie jest poprawne, aby spełnić te wymagania.  Firma Microsoft udostępnia kilka narzędzi do wykonywania integracji i synchronizacji katalogów.  Zobacz [tabelę porównawczą narzędzi integracji katalogów tożsamości hybrydowej,](plan-hybrid-identity-design-considerations-tools-comparison.md) aby uzyskać więcej informacji. 

Teraz, gdy masz wymagania dotyczące synchronizacji i narzędzie, które osiągnie to dla twojej firmy, musisz ocenić aplikacje korzystające z tych usług katalogowych. Ta ocena jest ważne, aby zdefiniować wymagania techniczne, aby zintegrować te aplikacje do chmury. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Czy te aplikacje zostaną przeniesione do chmury i będą korzystać z katalogu?
* Czy istnieją specjalne atrybuty, które muszą być zsynchronizowane z chmurą, aby te aplikacje mogły z nich korzystać pomyślnie?
* Czy te aplikacje muszą być ponownie napisane, aby skorzystać z auth chmury?
* Czy te aplikacje będą nadal występować lokalnie, podczas gdy użytkownicy uzyskują do nich dostęp przy użyciu tożsamości w chmurze?

Należy również określić wymagania dotyczące zabezpieczeń i ograniczenia synchronizacji katalogów. Ta ocena jest ważne, aby uzyskać listę wymagań, które będą potrzebne w celu utworzenia i utrzymania tożsamości użytkownika w chmurze. Pamiętaj, aby odpowiedzieć na następujące pytania:

* Gdzie będzie znajdować się serwer synchronizacji?
* Czy zostanie przyłączona do domeny?
* Czy serwer będzie zlokalizowany w sieci z ograniczeniami za zaporą, takiej jak strefa DMZ?
  * Czy będzie można otworzyć wymagane porty zapory do obsługi synchronizacji?
* Czy masz plan odzyskiwania po awarii dla serwera synchronizacji?
* Czy masz konto z odpowiednimi uprawnieniami dla wszystkich lasów, z których chcesz zsynchronizować?
  * Jeśli Twoja firma nie zna odpowiedzi na to pytanie, przejrzyj sekcję "Uprawnienia do synchronizacji haseł" w artykule [Instalowanie usługi synchronizacji usługi Azure Active Directory](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) i określ, czy masz już konto z tymi uprawnieniami lub jeśli musisz je utworzyć.
* Jeśli masz mutli-forest sync jest serwer synchronizacji w stanie dostać się do każdego lasu?

> [!NOTE]
> Pamiętaj, aby zanotować wszystkie odpowiedzi i zrozumieć ich uzasadnienie. [Określ wymagania dotyczące reagowania na zdarzenia](plan-hybrid-identity-design-considerations-incident-response-requirements.md) zostaną przejmą dostępne opcje. Odpowiadając na te pytania, wybierzesz opcję, która najlepiej odpowiada Twoim potrzebom biznesowym.
> 
> 

## <a name="next-steps"></a>Następne kroki
[Określanie wymagań dotyczących uwierzytelniania wieloskładnikowego](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Zobacz też
[Omówienie zagadnień projektowych](plan-hybrid-identity-design-considerations-overview.md)

