---
title: 'Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono, jak działa synchronizacja usługi Azure AD Connect i jak dostosować.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2017
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e3b87f40d75d4045155e7dd953dc76ffd9de2b34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60348743"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizacja usługi Azure AD Connect: zrozumienie i dostosowywanie synchronizacji
Usługi synchronizacji usługi Azure Active Directory Connect (synchronizacja usługi Azure AD Connect) są głównym składnikiem usługi Azure AD Connect. Zajmuje się wszystkie operacje, które są związane z synchronizacji danych tożsamości między środowiskiem lokalnym i usługi Azure AD. Synchronizacja usługi Azure AD Connect jest następcą programu DirSync, Usługi Azure AD Sync i menedżera tożsamości forefront z skonfigurowanym łącznikiem usługi Azure Active Directory.

Ten temat jest domem dla **synchronizacji usługi Azure AD Connect** (nazywany także **aparatem synchronizacji)** i zawiera listę łączy do wszystkich innych tematów z nim związanych. Aby uzyskać łącza do usługi Azure AD Connect, zobacz [Integrowanie tożsamości lokalnych z usługą Azure Active Directory.](whatis-hybrid-identity.md)

Usługa synchronizacji składa się z dwóch składników, lokalnego składnika **synchronizacji usługi Azure AD Connect** i strony usługi w usłudze Azure AD o nazwie usługa **synchronizacji usługi Azure AD Connect.**

## <a name="azure-ad-connect-sync-topics"></a>Tematy synchronizacji usługi Azure AD Connect
| Temat | Co obejmuje i kiedy czytać |
| --- | --- |
| **Podstawy synchronizacji usługi Azure AD Connect** | |
| [Opis architektury](concept-azure-ad-connect-sync-architecture.md) |Dla tych z Was, którzy są nowicjuszami w silniku synchronizacji i chcą dowiedzieć się o architekturze i używanych terminach. |
| [Koncepcje techniczne](how-to-connect-sync-technical-concepts.md) |Krótka wersja tematu architektury i pokrótce wyjaśnia użyte terminy. |
| [Topologie obsługiwane w programie Azure AD Connect](plan-connect-topologies.md) |W tym artykule opisano różne topologie i scenariusze obsługuje aparat synchronizacji. |
| **Konfiguracja niestandardowa** | |
| [Ponowne uruchamianie kreatora instalacji](how-to-connect-installation-wizard.md) |W tym artykule wyjaśniono, jakie opcje są dostępne po ponownym uruchomieniu kreatora instalacji usługi Azure AD Connect. |
| [Omówienie aprowizacji deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning.md) |Opisuje model konfiguracji o nazwie deklaratywny inicjowania obsługi administracyjnej. |
| [Opis wyrażenia inicjowania obsługi administracyjnej deklaratywnej](concept-azure-ad-connect-sync-declarative-provisioning-expressions.md) |Opisuje składnię języka wyrażeń używanego w deklaratywnym inicjowaniu obsługi administracyjnej. |
| [Opis konfiguracji domyślnej](concept-azure-ad-connect-sync-default-configuration.md) |W tym artykule opisano reguły gotowe do użycia i konfigurację domyślną. Opisano również, jak reguły współpracują ze sobą dla scenariuszy gotowych do pracy. |
| [Opis użytkowników i kontaktów](concept-azure-ad-connect-sync-user-and-contacts.md) |Kontynuuje poprzedni temat i opisuje, jak konfiguracja dla użytkowników i kontaktów działa razem, w szczególności w środowisku wielolesowym. |
| [Jak wprowadzić zmiany w konfiguracji domyślnej](how-to-connect-sync-change-the-configuration.md) |Przeprowadzi Cię przez jak dokonać typowej zmiany konfiguracji do przepływów atrybutów. |
| [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](how-to-connect-sync-best-practices-changing-default-configuration.md) |Ograniczenia obsługi i wprowadzania zmian w konfiguracji out-of-box. |
| [Konfigurowanie filtrowania](how-to-connect-sync-configure-filtering.md) |W tym artykule opisano różne opcje ograniczania, które obiekty są synchronizowane z usługą Azure AD i krok po kroku, jak skonfigurować te opcje. |
| **Funkcje i scenariusze** | |
| [Zapobieganie przypadkowemu usuwaniu](how-to-connect-sync-feature-prevent-accidental-deletes.md) |W tym artykule opisano *funkcję zapobiegania przypadkowym usuwaniom* i sposób jej konfigurowania. |
| [Scheduler](how-to-connect-sync-feature-scheduler.md) |Opisuje wbudowany harmonogram, który importuje, synchronizuje i eksportuje dane. |
| [Implementowanie synchronizacji skrótów haseł](how-to-connect-password-hash-synchronization.md) |W tym artykule opisano, jak działa synchronizacja haseł, jak zaimplementować oraz jak obsługiwać i rozwiązywać problemy. |
| [Zapisywanie zwrotne urządzeń](how-to-connect-device-writeback.md) |W tym artykule opisano, jak działa stornia zwrotnego urządzenia w usłudze Azure AD Connect. |
| [Rozszerzenia katalogów](how-to-connect-sync-feature-directory-extensions.md) |W tym artykule opisano sposób rozszerzania schematu usługi Azure AD o własne atrybuty niestandardowe. |
| [Office 365 PreferredDataLocation](how-to-connect-sync-feature-preferreddatalocation.md) |W tym artykule opisano sposób umieszczania zasobów usługi Office 365 użytkownika w tym samym regionie co użytkownik. |
| **Usługa synchronizacji** | |
| [Funkcje usługi synchronizacji programu Azure AD Connect](how-to-connect-syncservice-features.md) |Zawiera opis strony usługi synchronizacji i jak zmienić ustawienia synchronizacji w usłudze Azure AD. |
| [Odporność atrybutów duplikatów](how-to-connect-syncservice-duplicate-attribute-resiliency.md) |W tym artykule opisano, jak włączyć i używać **userPrincipalName** i **proxyAddresses** zduplikowane wartości atrybutów odporności. |
| **Operacje i interfejs użytkownika** | |
| [Synchronization Service Manager](how-to-connect-sync-service-manager-ui.md) |Zawiera opis interfejsu użytkownika menedżera usług synchronizacji, w tym [karty Operacje,](how-to-connect-sync-service-manager-ui-operations.md) [Łączniki,](how-to-connect-sync-service-manager-ui-connectors.md) [Projektant metaverse](how-to-connect-sync-service-manager-ui-mvdesigner.md)i [Wyszukiwanie metaverse.](how-to-connect-sync-service-manager-ui-mvsearch.md) |
| [Zadania operacyjne i zagadnienia](how-to-connect-sync-operations.md) |W tym artykule opisano problemy operacyjne, takie jak odzyskiwanie po awarii. |
| **Jak...** | |
| [Resetowanie konta usługi Azure AD](how-to-connect-azureadaccount.md) |Jak zresetować poświadczenia konta usługi używanego do łączenia się z synchronizacji usługi Azure AD Connect z usługą Azure AD. |
| **Więcej informacji i referencje** | |
| [Porty](reference-connect-ports.md) |Wyświetla listę portów, które należy otworzyć między aparatem synchronizacji a katalogami lokalnymi i usługą Azure AD. |
| [Atrybuty synchronizowane z usługą Azure Active Directory](reference-connect-sync-attributes-synchronized.md) |Wyświetla listę wszystkich atrybutów synchronizowanych między lokalną usługą AD i usługą Azure AD. |
| [Informacje ogólne o funkcjach](reference-connect-sync-functions-reference.md) |Wyświetla listę wszystkich funkcji dostępnych w deklaratywnej inicjowania obsługi administracyjnej. |

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)
