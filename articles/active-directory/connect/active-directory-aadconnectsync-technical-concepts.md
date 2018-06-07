---
title: 'Synchronizacja programu Azure AD Connect: zagadnienia techniczne | Dokumentacja firmy Microsoft'
description: Zawiera wyjaśnienie założeń techniczne synchronizacja programu Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: markvi;andkjell
ms.openlocfilehash: 591f67747316b950f32c5a113edda1080ed814a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592844"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizacja programu Azure AD Connect: zagadnienia techniczne
W tym artykule przedstawiono podsumowanie tematu [opis architektury](active-directory-aadconnectsync-technical-concepts.md).

Synchronizacja programu Azure AD Connect, bazując na platformie stałe metakatalogowego synchronizacji.
Poniższe sekcje wprowadzenie pojęcia metakatalogowego synchronizacji.
Kompilowanie od serwera MIIS, ILM i FIM, usług Azure do synchronizacji usługi Active Directory zapewnia platformę dalej do połączenia ze źródłami danych, synchronizowania danych między źródłami danych, jak również udostępnianie i anulowania obsługi tożsamości.

![Zagadnienia techniczne](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Poniższe sekcje zawierają szczegółowe informacje o następujących aspektów usługę synchronizacji programu FIM:

* Łącznik
* Przepływ atrybutów
* Obszar łączników
* Metaverse
* Inicjowanie obsługi

## <a name="connector"></a>Łącznik
Moduły kodu, które są używane do komunikacji z połączonego katalogu są nazywane łączników (wcześniej znane jako agenci zarządzania (MAs)).

Te pliki zostaną zainstalowane na komputerze z uruchomionym synchronizacja programu Azure AD Connect. Łączniki umożliwiają bez wykorzystania agentów konwersacji przy użyciu protokołów systemu zdalnego, zdejmując to zadanie wdrażania agentów specjalne. Oznacza to zmniejszyć ryzyko i czas wdrażania, szczególnie w przypadku zajmujących się kluczowych aplikacji i systemów.

W powyższym rysunku łącznika jest synonimem przestrzeni łącznika, ale obejmuje całą komunikację z systemu zewnętrznego.

Łącznik jest odpowiedzialny za wszystkie importowania i eksportowania funkcji do systemu i zwalnia deweloperzy z konieczności zrozumieć, jak połączyć się z każdego systemu natywnie korzystając z aprowizacją deklaratywną dostosować przekształcenia danych.

Importu i eksportu tylko wówczas, gdy zaplanowane, co pozwala na dalsze izolacji od zmian w ramach systemu, ponieważ zmiany nie są automatycznie propagowane do połączonego źródła danych. Ponadto deweloperzy mogą również utworzyć własne łączniki do nawiązywania połączenia z niemal dowolnego źródła danych.

## <a name="attribute-flow"></a>Przepływ atrybutów
Metaverse jest skonsolidowanego widoku wszystkich tożsamości dołączonego do sąsiadujących — obszary łączników. Na powyższej ilustracji przedstawiono przepływ atrybutów liniami z strzałek dla przepływu ruchu przychodzącego i wychodzącego. Przepływ atrybutu to proces kopiowania lub Przekształcanie danych z jednego systemu i wszystkich atrybutów przepływów (przychodzący lub wychodzący).

Przepływ atrybutu odbywa się między obszar łączników i metaverse dwukierunkowo podczas operacji synchronizacji (pełna lub różnicowa) są zaplanowane do uruchomienia.

Przepływ atrybutów jest używane tylko w przypadku tych synchronizacje są uruchamiane. Przepływy atrybutów są definiowane w reguły synchronizacji. Mogą to być przychodzącego (ISR na powyższym rysunku) lub wychodzący (OSR na powyższym rysunku).

## <a name="connected-system"></a>System połączony
System połączony (alias połączonego katalogu) odwołujące się do systemu zdalnego synchronizacji ma podłączony do usługi Azure AD Connect i odczytuje i zapisuje dane tożsamości do i z.

## <a name="connector-space"></a>Obszar łączników
Każdego połączonego źródła danych jest reprezentowany jako podzbiór filtrowanych obiektów i atrybutów w przestrzeni łącznika.
Umożliwia do działania lokalnie, bez konieczności skontaktuj się z systemu zdalnego, podczas synchronizowania obiektów usługi synchronizacji i ogranicza interakcję w celu importowania i eksportuje tylko.

Jeśli źródło danych i łącznika ma możliwość wyświetlania listy zmian (import zmian), następnie wydajność operacyjną gwałtowny wzrost jako tylko zmiany od ostatniego cyklu sondowania są wymieniane. Obszar łączników powoduje połączonego źródła danych z propagowanie automatycznie przez wymaganie, że harmonogram łącznik importuje i eksportuje zmiany. Dodano ubezpieczenia przyznaje spokój umysłu podczas testowania, przeglądania i potwierdzenie następnej aktualizacji.

## <a name="metaverse"></a>Metaverse
Metaverse jest skonsolidowanego widoku wszystkich tożsamości dołączonego do sąsiadujących — obszary łączników.

Tożsamości są połączone ze sobą i uprawnienia przypisano dla różnych atrybutów w mapowaniach przepływu importu, obiektu metaverse centralnej zaczyna agregują informacje z wielu systemów. Z tego przepływu atrybutu obiektu mapowania przenoszenia informacje do systemów ruchu wychodzącego.

Obiekty są tworzone podczas autorytatywne systemu projektów je do środowiska metaverse. Po usunięciu wszystkich połączeń obiektu metaverse jest usuwać.

Nie można bezpośrednio edytować obiektów w magazynie metaverse. Wszystkie dane w obiekcie musi się odbywać za pośrednictwem przepływ atrybutów. Metaverse obsługuje trwałe łączniki z każdego miejsca łącznika. Te łączniki nie wymagają ponownej oceny dla każdej synchronizacji, uruchom. Oznacza to, że synchronizacja programu Azure AD Connect nie ma można odnaleźć zgodnego obiektu zdalnego zawsze. Dzięki temu można uniknąć konieczności kosztowne agentów uniemożliwić zmiany atrybutów, które zazwyczaj będzie odpowiedzialny za korelowanie obiektów.

Podczas rozpoznawania nowych źródeł danych, które mogą mieć istniejących obiektów, które muszą być zarządzane, synchronizacja programu Azure AD Connect korzysta w procesie nazywanym reguły sprzężenia oceniania potencjalnych kandydatów umożliwiające nawiązanie połączenia.
Po utworzeniu połączenia tej oceny powtarzał i przepływu normalnego atrybutów może wystąpić między zdalnego połączonego źródła danych i metaverse.

## <a name="provisioning"></a>Inicjowanie obsługi
Gdy projekty wiarygodne źródło w innym łącznikiem reprezentującą podrzędne połączonego źródła danych można tworzyć nowy obiekt do środowiska metaverse nowy obiekt przestrzeni łącznika.

To z założenia stanowi połączenie, a przepływ atrybutów można kontynuować dwukierunkowo.

Zawsze, gdy reguła określa nowy obiekt miejsca łącznik musi zostać utworzona, jest on nazywany inicjowania obsługi administracyjnej. Jednak ponieważ tej operacji tylko odbywa się w obrębie przestrzeni łącznika, go nie jest przenoszone do połączonego źródła danych do czasu eksportu jest wykonywane.

## <a name="additional-resources"></a>Dodatkowe zasoby
* [Azure AD Connect Sync: Dostosowywanie opcji synchronizacji](active-directory-aadconnectsync-whatis.md)
* [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
