---
title: 'Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji | Dokumentacja firmy Microsoft'
description: Wyjaśnia, jak usługa Azure AD Connect Synchronizuj działania i dostosowywania.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: ee4bf802-045b-4da0-986e-90aba2de58d6
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 247b7bb5b5dbce94c8f8339a6f06c8ae5dab75d9
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2018
ms.locfileid: "37919371"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji
Usługi synchronizacji usługi Azure Active Directory Connect (Azure AD Connect sync) jest głównym składnikiem programu Azure AD Connect. Ta odpowiada za wszystkie operacje, które odnoszą się do synchronizowania danych tożsamości między środowiskiem lokalnym i usługi Azure AD. Synchronizacja programu Azure AD Connect jest następcą narzędzia DirSync, Azure AD Sync i Forefront Identity Manager za pomocą usługi Azure łącznika usługi Active Directory skonfigurowane.

Ten temat stanowi stronę główną dla **synchronizacji programu Azure AD Connect** (nazywane również **aparatu synchronizacji**) i zawiera także łącza do innych tematów odnoszących się do niego. Aby uzyskać łącza do programu Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

Usługa synchronizacji zawiera dwa składniki, lokalne **synchronizacji programu Azure AD Connect** składnika i po stronie usługi w usłudze Azure AD o nazwie **Usługa synchronizacji Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Usługa Azure AD Connect sync tematy
| Temat | Co obejmuje i kiedy do odczytu |
| --- | --- |
| **Podstawy usługi Azure AD Connect sync** | |
| [Omówienie architektury](active-directory-aadconnectsync-understanding-architecture.md) |Dla osób, które są nowe w aparacie synchronizacji i chcesz dowiedzieć się więcej na temat architektury i terminy używane. |
| [Zagadnienia techniczne](active-directory-aadconnectsync-technical-concepts.md) |Skróconej wersji tego tematu architektury i krótko opisano terminy używane. |
| [Topologie obsługiwane w programie Azure AD Connect](active-directory-aadconnect-topologies.md) |W tym artykule opisano różne topologie i scenariusze, które obsługuje aparat synchronizacji. |
| **Konfiguracja niestandardowa** | |
| [Ponownie uruchom Kreatora instalacji](active-directory-aadconnectsync-installation-wizard.md) |Wyjaśnia, jakie opcje mają dostępne, gdy ponownie uruchom Kreatora instalacji usługi Azure AD Connect. |
| [Opis Aprowizacja Deklaratywna](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |W tym artykule opisano model konfiguracji o nazwie aprowizacja deklaratywna. |
| [Opis wyrażeń związanych z Aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |W tym artykule opisano składnię języka wyrażeń używane w aprowizacja deklaratywna. |
| [Opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md) |W tym artykule opisano zasady out-of-box i konfiguracja domyślna. Opisano również, jak zasady współpracują ze sobą w scenariuszach out-of-box do pracy. |
| [Opis użytkowników i kontaktów](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Jest kontynuowane od poprzedniego tematu, a w tym artykule opisano, jak konfiguracja dla użytkowników i kontaktów współpracuje ze sobą, w szczególności w środowisku wielu lasów. |
| [Sposób wprowadzania zmian w domyślnej konfiguracji](active-directory-aadconnectsync-change-the-configuration.md) |Przedstawiono sposób wprowadzania Typowa konfiguracja zmień przepływy atrybutów. |
| [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Ograniczenia dotyczące obsługi i wprowadzania zmian w konfiguracji out-of-box. |
| [Konfigurowanie filtrowania](active-directory-aadconnectsync-configure-filtering.md) |W tym artykule opisano różne opcje dotyczące ograniczania obiekty, które są synchronizowane z usługą Azure AD i instrukcje krok po kroku sposób konfigurowania tych opcji. |
| **Funkcje i scenariusze** | |
| [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |W tym artykule opisano *Zapobieganie przypadkowemu usuwaniu* funkcji i sposobie konfigurowania go. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |W tym artykule opisano wbudowanych harmonogram, który jest importowania, synchronizacji i eksportowania danych. |
| [Implementowanie synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |W tym artykule opisano opis działania synchronizacji haseł, jak wdrożyć i sposobu działania i rozwiązywać problemy. |
| [Zapisywanie zwrotne urządzeń](active-directory-aadconnect-feature-device-writeback.md) |W tym artykule opisano, jak działa zapisywania zwrotnego urządzeń w usłudze Azure AD Connect. |
| [Rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md) |Opisuje sposób rozszerzyć schemat usługi Azure AD za pomocą niestandardowych atrybutów. |
| [PreferredDataLocation usługi Office 365](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Informacje dotyczące użytkownika usługi Office 365 zasoby należy umieścić w tym samym regionie, co użytkownik. |
| **Usługa synchronizacji** | |
| [Funkcji usługi synchronizacji programu Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Opisuje po stronie usługi synchronizacji oraz zmienić ustawienia synchronizacji w usłudze Azure AD. |
| [Odporność na duplikowanie atrybutów](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Opisuje sposób włączenia i używania **userPrincipalName** i **proxyAddresses** odporność na zduplikowane atrybuty wartości. |
| **Operacje i interfejsu użytkownika** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |W tym artykule opisano interfejs użytkownika Menedżera usługi synchronizacji, w tym [operacji](active-directory-aadconnectsync-service-manager-ui-operations.md), [łączników](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Projektant magazynu Metaverse](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), i [wyszukiwanie magazynu Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) karty. |
| [Zagadnienia i zadania operacyjne](active-directory-aadconnectsync-operations.md) |W tym artykule opisano problemy operacyjne, takie jak odzyskiwanie po awarii. |
| **Jak...** | |
| [Resetuj konta usługi Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Jak zresetować poświadczenia konta usługi używane do łączenia z synchronizacji programu Azure AD Connect do usługi Azure AD. |
| **Więcej informacji i materiały referencyjne** | |
| [Porty](active-directory-aadconnect-ports.md) |Wyświetla listę które porty należy otworzyć między aparatem synchronizacji i w katalogach lokalnych i usługi Azure AD. |
| [Atrybuty synchronizowane z usługą Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Wyświetla listę wszystkich atrybutów, które są synchronizowane między lokalnej usługi AD i Azure AD. |
| [Informacje ogólne o funkcjach](active-directory-aadconnectsync-functions-reference.md) |Wyświetla listę wszystkich funkcji dostępnych w aprowizacja deklaratywna. |

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
