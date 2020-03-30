---
title: Często zadawane pytania dotyczące aprowizacji w chmurze programu Azure AD Connect
description: W tym dokumencie opisano często zadawane pytania dotyczące inicjowania obsługi administracyjnej w chmurze.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fbc1baa86bb81c8975587e84427a72ccc044805e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77916578"
---
# <a name="azure-active-directory-connect-faq"></a>Często zadawane pytania dotyczące połączenia usługi Azure Active Directory

Przeczytaj o często zadawanych pytaniach dotyczących inicjowania obsługi administracyjnej usługi Azure Active Directory (Azure AD) Connect.

## <a name="general-installation"></a>Instalacja ogólna

**Pyt.: Jak często jest uruchamiana inicjowanie obsługi administracyjnej w chmurze?**

Inicjowanie obsługi administracyjnej w chmurze jest zaplanowane do uruchomienia co 2 minuty. Co 2 minuty wszystkie zmiany skrótu użytkownika, grupy i hasła będą aprowizować na platformie Azure AD.

**P: Wyświetlanie błędów synchronizacji skrótu hasła w pierwszym uruchomieniu. Dlaczego?**

Jest to oczekiwane. Błędy są spowodowane obiektem użytkownika, który nie jest obecny w usłudze Azure AD. Gdy użytkownik jest aprowizowana do usługi Azure AD, skróty haseł należy inicjowania obsługi administracyjnej w kolejnym uruchomieniu. Poczekaj na kilka uruchomień i upewnij się, że synchronizacja skrótów haseł nie ma już błędów.

**Pyt.: Co się stanie, jeśli wystąpienie usługi Active Directory ma atrybuty, które nie są obsługiwane przez provisoning chmury (na przykład rozszerzenia katalogów)?**

Aprowizacji w chmurze zostanie uruchomiony i aprowizować obsługiwane atrybuty. Nieobsługiwały atrybuty nie będą aprowizować na platformie Azure AD. Przejrzyj rozszerzenia katalogów w usłudze Active Directory i upewnij się, że nie potrzebujesz tych atrybutów do przepływu do usługi Azure AD. Jeśli wymagany jest co najmniej jeden atrybut, należy rozważyć użycie synchronizacji usługi Azure AD Connect lub przeniesienie wymaganych informacji do jednego z obsługiwanych atrybutów (na przykład atrybuty rozszerzenia 1-15).

**Pyt.: Jaka jest różnica między synchronizacją usługi Azure AD Connect a aprowewaniem w chmurze?**

Dzięki synchronizacji usługi Azure AD Connect inicjowanie obsługi administracyjnej jest uruchamiane na lokalnym serwerze synchronizacji. Konfiguracja jest przechowywana na lokalnym serwerze synchronizacji. Dzięki inicjowaniu obsługi administracyjnej w chmurze usługi Azure AD Connect konfiguracja inicjowania obsługi administracyjnej jest przechowywana w chmurze i uruchamiana w chmurze w ramach usługi inicjowania obsługi administracyjnej usługi Azure AD. 

**Pyt.: Czy można używać inicjowania obsługi administracyjnej w chmurze do synchronizacji z wielu lasów usługi Active Directory?**

Tak. Inicjowanie obsługi administracyjnej w chmurze może służyć do synchronizacji z wielu lasów usługi Active Directory. W środowisku wielolesowym wszystkie odwołania (przykład, menedżer) muszą znajdować się w domenie.  

**P: W jaki sposób agent jest aktualizowany?**

Agenci są automatycznie uaktualniane przez firmę Microsoft. Dla zespołu IT zmniejsza to obciążenie konieczności testowania i sprawdzania poprawności nowych wersji agenta. 

**P: Czy mogę wyłączyć automatyczne uaktualnianie?**

Nie ma obsługiwanego sposobu wyłączania automatycznego uaktualniania.

**Pyt.: Czy można zmienić kotwicę źródłowej do inicjowania obsługi administracyjnej w chmurze?**

Domyślnie inicjowanie obsługi administracyjnej w chmurze używa ms-ds-consistency-GUID z rezerwowym do ObjectGUID jako kotwicy źródłowej. Nie ma obsługiwanego sposobu zmiany kotwicy źródłowej.

**Pyt.: Widzę nowe jednostki usługi z nazwami domen usługi AD podczas korzystania z obsługi administracyjnej w chmurze. Czy jest to oczekiwane?**

Tak, inicjowanie obsługi administracyjnej w chmurze tworzy jednostkę usługi dla konfiguracji inicjowania obsługi administracyjnej o nazwie domeny jako głównej nazwy usługi. Nie należy wprowadzać żadnych zmian w konfiguracji jednostki usługi.

**Pyt.: Co się stanie, gdy zsynchronizowany użytkownik jest zobowiązany do zmiany hasła przy następnym logowaniu?**

Jeśli synchronizacja skrótów haseł jest włączona w inicjowaniu obsługi administracyjnej w chmurze, a zsynchronizowany użytkownik jest zobowiązany do zmiany hasła przy następnym logowaniu w lokalnej usłudze AD, inicjowanie obsługi administracyjnej w chmurze nie powoduje inicjowania obsługi administracyjnej skrótu hasła do usługi Azure AD. Gdy użytkownik zmieni hasło, skrót hasła użytkownika jest aprowizowana z usługi AD do usługi Azure AD.

**Pyt.: Czy inicjowanie obsługi administracyjnej chmury obsługuje zapisywanie zwrotne ms-ds-consistencyGUID dla dowolnego obiektu?**

Nie, inicjowanie obsługi administracyjnej w chmurze nie obsługuje storca z ms-ds-consistencyGUID dla dowolnego obiektu (w tym obiektów użytkownika). 

**P: Inicjuję inicjowanie obsługi administracyjnej użytkowników przy użyciu aprowizacji w chmurze. Usunąłem konfigurację. Dlaczego nadal widzę stare zsynchronizowane obiekty w usłudze Azure AD?** 

Po usunięciu konfiguracji inicjowanie obsługi administracyjnej w chmurze nie czyści zsynchronizowanych obiektów w usłudze Azure AD. Aby upewnić się, że nie masz starych obiektów, zmień zakres konfiguracji na pustą grupę lub jednostki organizacyjne. Po uruchomieniu inicjowania obsługi administracyjnej i czyści obiekty, wyłączyć i usunąć konfigurację. 

**Pyt.: Co to znaczy, że hybryda programu Exchange nie jest obsługiwana?**

Funkcja wdrożenia hybrydowego programu Exchange umożliwia jednoczesne istnienie skrzynek pocztowych programu Exchange lokalnie i w usłudze Office 365. Program Azure AD Connect synchronizuje określony zbiór atrybutów z usługi Azure AD z katalogiem lokalnym.  Agent inicjowania obsługi administracyjnej w chmurze obecnie nie synchronizuje tych atrybutów z powrotem do katalogu lokalnego i dlatego nie jest obsługiwany jako zamiennik dla usługi Azure AD Connect.

**Pyt.: Czy można zainstalować agenta inicjowania obsługi administracyjnej w chmurze w systemie Windows Server Core?**

Nie, instalowanie agenta na rdzeniu serwera nie jest obsługiwane.

## <a name="next-steps"></a>Następne kroki 

- [Co to jest aprowizacja?](what-is-provisioning.md)
- [Co to jest aprowizacja w chmurze programu Azure AD Connect?](what-is-cloud-provisioning.md)
