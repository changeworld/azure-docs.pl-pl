---
title: 'Synchronizacja programu Azure AD Connect: zrozumienie i dostosować synchronizację | Dokumentacja firmy Microsoft'
description: Wyjaśniono, jak Azure AD Connect synchronizować działa i sposobu dostosowywania.
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
ms.author: markvi
ms.openlocfilehash: 6b2724f4c9511d606ab8eeac2dedea8759283883
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34595261"
---
# <a name="azure-ad-connect-sync-understand-and-customize-synchronization"></a>Synchronizacja programu Azure AD Connect: zrozumienie i dostosowywanie synchronizacji
Usługi synchronizacji usługi Azure Active Directory Connect (synchronizacja programu Azure AD Connect) jest głównym składnikiem programu Azure AD Connect. Odpowiada on za wszystkich operacji związanych z synchronizacją tożsamości między środowiskiem lokalnym i usługą Azure AD. Synchronizacja programu Azure AD Connect jest zastępuje narzędzie DirSync, Azure AD Sync i Forefront Identity Manager z usługi Azure Active Directory Connector skonfigurowany.

Ten temat jest stroną główną dla **synchronizacja programu Azure AD Connect** (nazywane również **aparatu synchronizacji**) i zawiera linki do innych tematów z nim związane. Łącza do usługi Azure AD Connect, zobacz [integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md).

Usługa synchronizacji zawiera dwa składniki, lokalną **synchronizacja programu Azure AD Connect** składnika i po stronie usługi w usłudze Azure AD o nazwie **usługi synchronizacji programu Azure AD Connect**.

## <a name="azure-ad-connect-sync-topics"></a>Tematy synchronizacji usługi Azure AD Connect
| Temat | Co obejmuje i kiedy do odczytu |
| --- | --- |
| **Podstawy synchronizacji usługi Azure AD Connect** | |
| [Omówienie architektury](active-directory-aadconnectsync-understanding-architecture.md) |Dla osób, które są nowe w aparacie synchronizacji, która chce uzyskać informacje o architekturze i używanymi pojęciami. |
| [Zagadnienia techniczne](active-directory-aadconnectsync-technical-concepts.md) |Skróconej wersji w temacie architektura i krótko opisano terminy używane. |
| [Topologie obsługiwane w programie Azure AD Connect](active-directory-aadconnect-topologies.md) |W tym artykule opisano różne topologie i scenariusze, które obsługuje aparatu synchronizacji. |
| **Konfiguracja niestandardowa** | |
| [Ponownie uruchom Kreatora instalacji](active-directory-aadconnectsync-installation-wizard.md) |Opisano opcje ma dostępne po uruchomieniu Kreatora instalacji usługi Azure AD Connect ponownie. |
| [Opis Aprowizacją deklaratywną](active-directory-aadconnectsync-understanding-declarative-provisioning.md) |W tym artykule opisano model konfiguracji o nazwie aprowizacją deklaratywną. |
| [Opis deklaratywne wyrażenia inicjowania obsługi administracyjnej](active-directory-aadconnectsync-understanding-declarative-provisioning-expressions.md) |W tym artykule opisano składnię języka wyrażenia używane w aprowizacją deklaratywną. |
| [Opis konfiguracji domyślnej](active-directory-aadconnectsync-understanding-default-configuration.md) |Opisuje zasady out-of-box i konfiguracja domyślna. Opisuje również, jak reguły współdziałać ze sobą w scenariuszach out-of-box do pracy. |
| [Opis użytkowników i kontaktów](active-directory-aadconnectsync-understanding-users-and-contacts.md) |Jest kontynuowane od poprzedniego tematu i opisuje sposób konfiguracji dla użytkowników i kontaktów działania, w szczególności w środowisku wielu lasów. |
| [Jak wprowadzić zmianę do konfiguracji domyślnej](active-directory-aadconnectsync-change-the-configuration.md) |Przedstawiono sposób wprowadzania zmiany do przepływów atrybutów typowych konfiguracji. |
| [Najlepsze rozwiązania dotyczące zmieniania konfiguracji domyślnej](active-directory-aadconnectsync-best-practices-changing-default-configuration.md) |Ograniczenia obsługi oraz wprowadzania zmian w konfiguracji out-of-box. |
| [Konfigurowanie filtrowania](active-directory-aadconnectsync-configure-filtering.md) |W tym artykule opisano różne opcje dotyczące sposobu ograniczyć obiekty, które są synchronizowane z usługą Azure AD i krok po kroku sposobu konfigurowania tych opcji. |
| **Scenariusze i funkcje** | |
| [Zapobieganie przypadkowemu usuwaniu](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) |W tym artykule opisano *Zapobieganie przypadkowemu usuwaniu* funkcji i sposobie konfigurowania go. |
| [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) |W tym artykule opisano wbudowanych harmonogramu, który importowania, synchronizowania i eksportowanie danych. |
| [Implementowanie synchronizacji skrótów haseł](active-directory-aadconnectsync-implement-password-hash-synchronization.md) |Opisuje opis działania synchronizacji haseł, jak wdrożyć i sposób obsługi i rozwiązywanie problemów z. |
| [Zapisywanie zwrotne urządzeń](active-directory-aadconnect-feature-device-writeback.md) |W tym artykule opisano, jak działa zapisu zwrotnego urządzeń w programie Azure AD Connect. |
| [Rozszerzenia katalogów](active-directory-aadconnectsync-feature-directory-extensions.md) |Opisuje sposób rozszerzyć schemat usługi Azure AD z własnych niestandardowych atrybutów. |
| [PreferredDataLocation usługi Office 365](active-directory-aadconnectsync-feature-preferreddatalocation.md) |Opisuje sposób umieść zasobów usługi Office 365 użytkownika w tym samym regionie co użytkownik. |
| **Usługa synchronizacji** | |
| [Funkcje usługi synchronizacji programu Azure AD Connect](active-directory-aadconnectsyncservice-features.md) |Opisuje po stronie usługi synchronizacji oraz zmienić ustawienia synchronizacji w usłudze Azure AD. |
| [Zduplikowany atrybut odporności](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) |Zawiera opis sposobu włączania i użyj **userPrincipalName** i **proxyAddresses** odporności wartości zduplikowany atrybut. |
| **Operacje i interfejsu użytkownika** | |
| [Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md) |Zawiera opis interfejsu użytkownika Menedżera usługi synchronizacji, w tym [operacji](active-directory-aadconnectsync-service-manager-ui-operations.md), [łączniki](active-directory-aadconnectsync-service-manager-ui-connectors.md), [Metaverse Designer](active-directory-aadconnectsync-service-manager-ui-mvdesigner.md), i [wyszukiwanie Metaverse](active-directory-aadconnectsync-service-manager-ui-mvsearch.md) karty. |
| [Zagadnienia i zadania operacyjne](active-directory-aadconnectsync-operations.md) |W tym artykule opisano problemy operacyjne, takie jak odzyskiwania po awarii. |
| **Jak...** | |
| [Resetuj konta usługi Azure AD](active-directory-aadconnectsync-howto-azureadaccount.md) |Jak można zresetować poświadczeń konta usługi używanego do łączenia z synchronizacji Azure AD Connect do usługi Azure AD. |
| **Więcej informacji i odwołań** | |
| [Porty](active-directory-aadconnect-ports.md) |Wyświetla listę portów, które należy otworzyć między aparatem synchronizacji oraz katalogów lokalnych i usługi Azure AD. |
| [Atrybuty synchronizowane z usługą Azure Active Directory](active-directory-aadconnectsync-attributes-synchronized.md) |Wyświetla listę wszystkich atrybutów synchronizacji między lokalnymi AD i Azure AD. |
| [Informacje ogólne o funkcjach](active-directory-aadconnectsync-functions-reference.md) |Wyświetla listę wszystkich funkcji dostępnych w aprowizacją deklaratywną. |

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)
