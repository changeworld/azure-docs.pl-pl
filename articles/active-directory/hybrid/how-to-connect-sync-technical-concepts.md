---
title: 'Synchronizacja programu Azure AD Connect: Zagadnienia techniczne | Dokumentacja firmy Microsoft'
description: Zawiera wyjaśnienie założeń techniczne synchronizacji programu Azure AD Connect.
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
origin.date: 01/15/2018
ms.date: 11/12/2018
ms.component: hybrid
ms.author: v-junlch
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60347592"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizacja programu Azure AD Connect: Zagadnienia techniczne
W tym artykule przedstawiono podsumowanie tego tematu [opis architektury](how-to-connect-sync-technical-concepts.md).

Synchronizacja programu Azure AD Connect opiera się na platformie solid metakatalogowego synchronizacji.
Poniższe sekcje wprowadzenie pojęcia metakatalogowego synchronizacji.
Rozwijając MIIS ILM i FIM, usługi Azure Active Directory Sync Services zapewnia dalej platformy łączenie ze źródłami danych, synchronizowanie danych między źródłami danych, a także aprowizacji i anulowania obsługi tożsamości.

![Zagadnienia techniczne](./media/how-to-connect-sync-technical-concepts/scenario.png)

Poniższe sekcje zawierają więcej szczegółów na temat następujące aspekty FIM Synchronization Service:

- Łącznik
- Przepływ atrybutu
- Obszaru łącznika
- Metaverse
- Inicjowanie obsługi

## <a name="connector"></a>Łącznik
Moduły kodu, które są używane do komunikacji z połączonego katalogu są nazywane łączników (wcześniej znane jako agenci zarządzania (MAs)).

Są one instalowane na komputerze z programem Azure AD Connect sync. Łączniki umożliwiają bez wykorzystania agentów do konwersacji za pomocą protokołów systemu zdalnego zamiast polegania na wdrożenia specjalistycznych agentów. Oznacza to, zmniejszenia ryzyka i czas wdrożenia, szczególnie gdy chodzi o krytycznych aplikacji i systemów.

Na powyższym rysunku łącznika jest równoznaczny z obszaru łącznika, ale obejmuje cała komunikacja z systemem zewnętrznym.

Łącznika jest odpowiedzialny za wszystkie importowania i eksportowania funkcji do systemu i ułatwia deweloperom z listy zrozumieć, jak połączyć się z każdego systemu natywnie korzystając aprowizacja deklaratywna Dostosowywanie przekształcenia danych.

Przywozu i wywozu tylko wówczas, gdy zaplanowane, co umożliwia dalsze izolację od zmian w ramach systemu, ponieważ zmiany nie są automatycznie propagowane do połączonego źródła danych. Ponadto deweloperzy mogą również utworzyć własne łączniki do łączenia się z praktycznie dowolnego źródła danych.

## <a name="attribute-flow"></a>Przepływ atrybutu
Metaverse jest skonsolidowanego widoku wszystkich dołączonym do tożsamości sąsiadujące łącznika miejsca do magazynowania. Na powyższej ilustracji przepływu atrybutu jest przedstawiony za wiersze ze strzałkami dla przepływu ruchu przychodzącego i wychodzącego. Przepływ atrybutu to proces kopiowania lub przekształcania danych z jednego systemu i wszystkich atrybutów przepływów (przychodzący lub wychodzący).

Przepływ atrybutu odbywa się między obszar łączników i metaverse dwukierunkowo podczas operacji synchronizacji (pełny lub różnicowe) są planowane do uruchomienia.

Przepływ atrybutu występuje tylko w przypadku, gdy te synchronizacje są uruchamiane. Przepływy atrybutów są definiowane w reguły synchronizacji. Mogą to być dla ruchu przychodzącego (ISR na powyższym rysunku) lub ruchu wychodzącego (OSR na powyższym rysunku).

## <a name="connected-system"></a>Połączonego systemu
Połączonego systemu (zwane również połączonego katalogu) odnoszące się do usługi Azure AD Connect sync połączył się z systemu zdalnego i odczytuje i zapisuje dane tożsamości do i z.

## <a name="connector-space"></a>Obszaru łącznika
Każdego połączonego źródła danych jest przedstawiana jako podzbiór filtrowanych obiektów i atrybutów w przestrzeni łącznika.
Umożliwia usłudze synchronizacji działa lokalnie, bez potrzeby skontaktuj się z systemu zdalnego, podczas synchronizowania obiektów i ogranicza interakcji do importuje i eksportuje tylko.

Gdy źródła danych i łącznika ma możliwość wyświetlania listy zmian (import zmian), następnie wydajność operacyjną gwałtowny wzrost jako tylko zmiany od ostatniego cyklu sondowania są wymieniane. Połączonego źródła danych przed zmianami propagowanie automatycznie wymaga podania, harmonogram łącznik importuje i eksportuje i powoduje, że przestrzeni łącznika. Dodano ubezpieczenia daje poczucie spokoju podczas testowania, przeglądania i Potwierdzanie następnej aktualizacji.

## <a name="metaverse"></a>Metaverse
Metaverse jest skonsolidowanego widoku wszystkich dołączonym do tożsamości sąsiadujące łącznika miejsca do magazynowania.

Tożsamości są ze sobą powiązane i urząd przypisano do różnych atrybutów za pomocą mapowania przepływu importu, obiektu metaverse centralnej rozpoczyna agregują informacje z wielu systemów. Z tego przepływu atrybutu obiektu mapowania zawierają informacje do systemu wychodzącego.

Obiekty są tworzone, gdy autorytatywne system projektów je do obiektu metaverse. Zaraz po usunięciu wszystkich połączeń obiekt magazynu metaverse zostanie usunięty.

Nie można bezpośrednio edytować obiekty w magazynie metaverse. Wszystkie dane w obiekcie musi zamieszczanie za pośrednictwem przepływu atrybutu. Metaverse obsługuje trwałe łączniki z każdego miejsca łącznika. Te łączniki nie wymagają ponownej oceny dla każdego przebiegu synchronizacji. Oznacza to, synchronizacja programu Azure AD Connect nie musi zlokalizować każdorazowo pasującego obiektu zdalnego. Umożliwia to uniknięcie konieczności kosztownych agentów uniemożliwić zmiany atrybutów, które normalnie będzie odpowiedzialne za korelacji obiektów.

Podczas odnajdywania nowych źródeł danych, które mogą mieć istniejących obiektów, które muszą być zarządzane, synchronizacja programu Azure AD Connect używa w procesie zwanym regułę dołączania do oceny potencjalnych kandydatów, z którą chcesz ustanowić łącze.
Po utworzeniu połączenia tej oceny powtarzał i przepływu normalnego atrybutów może wystąpić między zdalnym połączonego źródła danych i obiektu metaverse.

## <a name="provisioning"></a>Inicjowanie obsługi
Gdy projektów jako autorytatywne źródło w innym łącznikiem reprezentującą podrzędne połączonego źródła danych można utworzyć nowy obiekt w obiekcie metaverse nowego obiektu przestrzeni łącznika.

To z natury ustanawia łącze, a przepływ atrybutów można kontynuować dwukierunkowo.

Zawsze, gdy zasada ustala, że nowego obiektu przestrzeni łącznika musi zostać utworzona, jest on nazywany inicjowania obsługi administracyjnej. Jednak ponieważ ta operacja tylko odbywa się w przestrzeni łącznika, go nie jest przenoszone do połączonego źródła danych do momentu eksportu jest wykonywane.

## <a name="additional-resources"></a>Dodatkowe zasoby
- [Synchronizacja programu Azure AD Connect: Dostosowywanie opcji synchronizacji](how-to-connect-sync-whatis.md)
- [Integrowanie tożsamości lokalnych z usługą Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png

