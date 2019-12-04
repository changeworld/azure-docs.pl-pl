---
title: Azure AD Connect — często zadawane pytania dotyczące aprowizacji w chmurze
description: Ten dokument zawiera opis często zadawanych pytań dotyczących aprowizacji w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93c88b167884c455ffb995f35356b121bce8a207
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74793556"
---
# <a name="azure-active-directory-connect-faq"></a>Azure Active Directory Connect — często zadawane pytania

Zapoznaj się z często zadawanymi pytaniami dotyczącymi usługi Azure Active Directory (Azure AD) łączenie z obsługą chmury.

## <a name="general-installation"></a>Instalacja ogólna

**P: jak często jest przeprowadzane Inicjowanie obsługi w chmurze?**

Zaplanowano uruchomienie aprowizacji w chmurze co 2 minuty. Co 2 minuty wszystkie zmiany skrótu użytkownika, grupy i hasła będą obsługiwane w usłudze Azure AD.

**P: widzi błędy synchronizacji skrótów haseł podczas pierwszego uruchomienia. Zalet?**

Jest to oczekiwane. Błędy są spowodowane nieobecnością obiektu użytkownika w usłudze Azure AD. Gdy użytkownik zostanie zainicjowany do usługi Azure AD, skróty haseł powinny być inicjowane w kolejnym przebiegu. Zaczekaj na kilka przebiegów i upewnij się, że synchronizacja skrótów haseł nie ma już błędów.

**P: Jaka jest różnica między synchronizacją Azure AD Connect a obsługą chmury?**

W przypadku synchronizacji Azure AD Connect obsługa jest uruchamiana na lokalnym serwerze synchronizacji. Konfiguracja jest przechowywana na lokalnym serwerze synchronizacji. W przypadku Azure AD Connect aprowizacji w chmurze konfiguracja aprowizacji jest przechowywana w chmurze i uruchamiana w chmurze w ramach usługi Azure AD Provisioning. 

**P: Czy można użyć aprowizacji w chmurze do synchronizowania z wielu Active Directory lasów?**

Tak. Inicjowanie obsługi administracyjnej w chmurze może służyć do synchronizowania z wieloma lasami Active Directory. W środowisku z obsługą kilku lasów wszystkie odwołania (np. Menedżer) muszą znajdować się w domenie.  

**P: jak jest aktualizowany Agent?**

Agenci są autouaktualniani przez firmę Microsoft. Zmniejsza to obciążenie w celu przetestowania i zweryfikowania nowych wersji agenta. 

**P: Czy można wyłączyć Autouaktualnianie?**

Nie ma żadnego obsługiwanego sposobu wyłączenia autouaktualniania.

**P: Czy można zmienić kotwicę źródłową na potrzeby aprowizacji w chmurze?**

Domyślnie Inicjowanie obsługi w chmurze używa metody MS-ds-Consistency-GUID z alternatywą dla ObjectGUID jako kotwicą źródła. Nie ma żadnego obsługiwanego sposobu zmiany kotwicy źródłowej.

**P: widzę nowe jednostki usługi z nazwami domen usługi AD podczas korzystania z aprowizacji w chmurze. Czy jest to oczekiwane?**

Tak, Inicjowanie obsługi w chmurze tworzy jednostkę usługi dla konfiguracji aprowizacji z nazwą domeny jako główną nazwą usługi. Nie wprowadzaj żadnych zmian w konfiguracji głównej usługi.

**P: co się stanie, gdy synchronizowany użytkownik musi zmienić hasło przy następnym logowaniu?**

Jeśli synchronizacja skrótów haseł jest włączona w ramach aprowizacji w chmurze, a synchronizowany użytkownik musi zmienić hasło przy następnym logowaniu w lokalnej usłudze AD, Inicjowanie obsługi administracyjnej w chmurze nie jest obsługiwane w przypadku zmiany skrótu hasła do usługi Azure AD. Gdy użytkownik zmieni hasło, skrót hasła użytkownika jest inicjowany z usługi AD do usługi Azure AD.

**P: czy usługa Cloud Provisioning obsługuje zapisywanie zwrotne MS-ds-consistencyGUID dla dowolnego obiektu?**

Nie, Inicjowanie obsługi administracyjnej nie obsługuje zapisywania zwrotnego MS-ds-consistencyGUID dla żadnego obiektu (w tym obiektów użytkownika). 

**Pytanie: jestem użytkownikom aprowizacji przy użyciu aprowizacji w chmurze. Usunięto konfigurację. Dlaczego nadal widzę stare zsynchronizowane obiekty w usłudze Azure AD?** 

Po usunięciu konfiguracji Inicjowanie obsługi administracyjnej nie będzie oczyszczać zsynchronizowanych obiektów w usłudze Azure AD. Aby upewnić się, że nie masz starych obiektów, Zmień zakres konfiguracji na pustą grupę lub jednostki organizacyjne. Po uruchomieniu aprowizacji i oczyszczeniu obiektów należy wyłączyć i usunąć konfigurację. 

## <a name="next-steps"></a>Następne kroki 

- [Co to jest inicjowanie obsługi?](what-is-provisioning.md)
- [Co to jest Azure AD Connect aprowizacji w chmurze?](what-is-cloud-provisioning.md)
