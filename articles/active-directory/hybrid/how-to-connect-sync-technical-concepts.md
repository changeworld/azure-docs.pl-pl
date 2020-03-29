---
title: 'Synchronizacja usługi Azure AD Connect: pojęcia techniczne | Dokumenty firmy Microsoft'
description: W tym artykule wyjaśniono pojęcia techniczne synchronizacji usługi Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347592"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizacja programu Azure AD Connect: zagadnienia techniczne
Ten artykuł jest podsumowaniem tematu [Opis architektury](how-to-connect-sync-technical-concepts.md).

Synchronizacja usługi Azure AD Connect opiera się na platformie synchronizacji metadirectory stałe.
W poniższych sekcjach przedstawiono pojęcia dotyczące synchronizacji metadirectory.
Opierając się na MIIS, ILM i FIM, usługi synchronizacji usługi Active Directory Azure zapewnia następną platformę do łączenia się ze źródłami danych, synchronizowania danych między źródłami danych, a także inicjowania obsługi administracyjnej i anulowania obsługi administracyjnej tożsamości.

![Zagadnienia techniczne](./media/how-to-connect-sync-technical-concepts/scenario.png)

Poniższe sekcje zawierają więcej szczegółów na temat następujących aspektów usługi synchronizacji FIM:

* Łącznik
* Przepływ atrybutów
* Miejsce na złącze
* Metaverse
* Inicjowanie obsługi

## <a name="connector"></a>Łącznik
Moduły kodu, które są używane do komunikowania się z połączonym katalogiem są nazywane łącznikami (wcześniej nazywanymi agentami zarządzania (MA)).

Są one instalowane na komputerze z synchronizacją usługi Azure AD Connect. Łączniki zapewniają bezagentowe możliwości rozmawiania przy użyciu zdalnych protokołów systemowych zamiast polegania na wdrażaniu wyspecjalizowanych agentów. Oznacza to skrócenie ryzyka i czasu wdrażania, szczególnie w przypadku krytycznych aplikacji i systemów.

Na powyższym rysunku złącze jest synonimem przestrzeni złącza, ale obejmuje całą komunikację z systemem zewnętrznym.

Łącznik jest odpowiedzialny za wszystkie funkcje importu i eksportu do systemu i zwalnia deweloperów z konieczności zrozumienia, jak połączyć się z każdym systemem natywnie podczas korzystania z deklaratywnych inicjowania obsługi administracyjnej w celu dostosowania przekształceń danych.

Import i eksport występują tylko wtedy, gdy zaplanowano, co pozwala na dalszą izolację od zmian zachodzących w systemie, ponieważ zmiany nie są automatycznie propagowane do podłączonego źródła danych. Ponadto deweloperzy mogą również tworzyć własne łączniki do łączenia się z praktycznie dowolnym źródłem danych.

## <a name="attribute-flow"></a>Przepływ atrybutów
Metaverse jest skonsolidowanym widokiem wszystkich połączonych tożsamości z sąsiednich przestrzeni łączników. Na powyższym rysunku przepływ atrybutów jest przedstawiany przez linie z grotami strzałek dla przepływu przychodzącego i wychodzącego. Przepływ atrybutów to proces kopiowania lub przekształcania danych z jednego systemu do drugiego i wszystkich przepływów atrybutów (przychodzących lub wychodzących).

Przepływ atrybutów występuje między przestrzeni łącznika i metaverse dwukierunkowe, gdy synchronizacja (pełna lub delta) operacje są zaplanowane do uruchomienia.

Przepływ atrybutów występuje tylko wtedy, gdy te synchronizacje są uruchamiane. Przepływy atrybutów są definiowane w regułach synchronizacji. Mogą to być przychodzące (ISR na zdjęciu powyżej) lub wychodzące (OSR na zdjęciu powyżej).

## <a name="connected-system"></a>Podłączony system
Połączony system (aka podłączony katalog) odnosi się do zdalnego systemu synchronizacja usługi Azure AD Connect ma połączenie z i odczytywania i zapisywania danych tożsamości do iz.

## <a name="connector-space"></a>Miejsce na złącze
Każde połączone źródło danych jest reprezentowane jako filtrowany podzbiór obiektów i atrybutów w przestrzeni łącznika.
Dzięki temu usługa synchronizacji działa lokalnie bez konieczności kontaktowania się z systemem zdalnym podczas synchronizowania obiektów i ogranicza interakcję tylko do importu i eksportu.

Gdy źródło danych i łącznik mają możliwość zapewnienia listy zmian (import delta), a następnie wydajność operacyjna wzrasta dramatycznie, jak tylko zmiany od ostatniego cyklu sondowania są wymieniane. Przestrzeń łącznika izoluje podłączone źródło danych od zmian propagujących się automatycznie, wymagając, aby łącznik zaplanował importowanie i eksportowanie. To dodatkowe ubezpieczenie zapewnia spokój podczas testowania, podglądu lub potwierdzania następnej aktualizacji.

## <a name="metaverse"></a>Metaverse
Metaverse jest skonsolidowanym widokiem wszystkich połączonych tożsamości z sąsiednich przestrzeni łączników.

Ponieważ tożsamości są ze sobą połączone, a uprawnienia są przypisywane dla różnych atrybutów za pomocą mapowań przepływu importu, centralny obiekt metaverse zaczyna agregować informacje z wielu systemów. Z tego przepływu atrybutów obiektu mapowania przenoszą informacje do systemów wychodzących.

Obiekty są tworzone, gdy autorytatywny system projektuje je do metaverse. Po usunięciu wszystkich połączeń obiekt metaverse zostanie usunięty.

Obiekty w metaverse nie mogą być edytowane bezpośrednio. Wszystkie dane w obiekcie muszą być przekazywane za pośrednictwem przepływu atrybutów. Metaverse utrzymuje łączniki trwałe z każdym miejscem łącznika. Te łączniki nie wymagają ponownej oceny dla każdego uruchomienia synchronizacji. Oznacza to, że synchronizacja usługi Azure AD Connect nie musi za każdym razem znajdować pasującego obiektu zdalnego. Pozwala to uniknąć konieczności kosztownych agentów, aby zapobiec zmianom atrybutów, które normalnie byłyby odpowiedzialne za korelację obiektów.

Podczas odnajdywania nowych źródeł danych, które mogą mieć wcześniej istniejących obiektów, które muszą być zarządzane, synchronizacja usługi Azure AD Connect używa procesu o nazwie reguły sprzężenia do oceny potencjalnych kandydatów, z którymi można ustanowić łącze.
Po ustanowieniu łącza ta ocena nie wystąpi ponownie i może wystąpić normalny przepływ atrybutów między zdalnie połączonym źródłem danych a metaverse.

## <a name="provisioning"></a>Inicjowanie obsługi
Gdy autorytatywne źródło projektuje nowy obiekt do metaverse nowy obiekt przestrzeni łącznika można utworzyć w innym łączniku reprezentującego źródło danych połączonych poniżej.

To z natury ustanawia łącze, a przepływ atrybutów może przebiegać dwukierunkowo.

Za każdym razem, gdy reguła określa, że nowy obiekt obszaru łącznika musi zostać utworzony, jest nazywany inicjowania obsługi administracyjnej. Jednak ponieważ ta operacja odbywa się tylko w przestrzeni łącznika, nie przenosi się do połączonego źródła danych, dopóki nie zostanie przeprowadzony eksport.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Usługa Azure AD Connect Sync: dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
