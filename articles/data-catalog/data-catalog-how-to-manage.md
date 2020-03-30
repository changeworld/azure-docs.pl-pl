---
title: Zarządzanie zasobami danych w wykazie danych platformy Azure
description: W tym artykule przedstawiono sposób kontrolowania widoczności i własności zasobów danych zarejestrowanych w usłudze Azure Data Catalog.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 9905ed72ec54304bbdb0f7ee607cbb013fc645bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68736343"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Zarządzanie zasobami danych w wykazie danych platformy Azure
## <a name="introduction"></a>Wprowadzenie
Usługa Azure Data Catalog jest przeznaczona do odnajdywania źródeł danych, dzięki czemu można łatwo odnajdywać i rozumieć źródła danych potrzebne do przeprowadzania analiz i podejmowania decyzji. Te funkcje odnajdywania mają największy wpływ, gdy ty i inni użytkownicy możecie znaleźć i zrozumieć najszerszy zakres dostępnych źródeł danych. Mając na uwadze te elementy, domyślne zachowanie wykazu danych jest dla wszystkich zarejestrowanych źródeł danych, które mają być widoczne i wykrywalne przez wszystkich użytkowników katalogu.

Wykaz danych nie daje dostępu do samych danych. Dostęp do danych jest kontrolowany przez właściciela źródła danych. Za pomocą wykazu danych można odnajdywać źródła danych i wyświetlać metadane związane ze źródłami zarejestrowanymi w katalogu.

Mogą jednak wystąpić sytuacje, w których źródła danych powinny być widoczne tylko dla określonych użytkowników lub członków określonych grup. W takich scenariuszach użytkownicy mogą przejąć na własność zarejestrowanych zasobów danych w katalogu, a następnie kontrolować widoczność zasobów, których są własnością.

> [!NOTE]
> Funkcja opisana w tym artykule jest dostępna tylko w wersji standardowej usługi Azure Data Catalog. Wersja bezpłatna nie zapewnia możliwości własności i ograniczania widoczności zasobów danych.
>
>

## <a name="manage-ownership-of-data-assets"></a>Zarządzanie własnością zasobów danych
Domyślnie zasoby danych zarejestrowane w wykazie danych nie są własnością. Każdy użytkownik z uprawnieniami dostępu do katalogu może odnajdywać i dodawać adnotacje do tych zasobów. Użytkownicy mogą przejąć na własność nieuwęzłych zasobów danych, a następnie ograniczyć widoczność posiadanych zasobów.

Gdy zasób danych w wykazie danych jest własnością, tylko użytkownicy, którzy są autoryzowani przez właścicieli, mogą odnajdować zasób i wyświetlić jego metadane, a tylko właściciele mogą usunąć zasób z katalogu.

> [!NOTE]
> Własność w wykazie danych ma wpływ tylko na metadane, które są przechowywane w katalogu. Własność nie nadaje żadnych uprawnień do źródłowego źródła danych.
>
>

### <a name="take-ownership"></a>Przejęcie na własność
Użytkownicy mogą przejąć na własność zasoby danych, wybierając opcję **Przejmij na własność** w portalu wykazu danych. Żadne specjalne uprawnienia nie są wymagane do przejęcia na własność nieuwęzowego zasobu danych. Każdy użytkownik może przejąć na własność nieuwznany zasób danych.

### <a name="add-owners-and-co-owners"></a>Dodaj właścicieli i współwłaścicieli
Jeśli zasób danych jest już własnością, inni użytkownicy nie mogą po prostu przejąć na własność. Muszą one zostać dodane jako współwłaściciele przez istniejącego właściciela. Każdy właściciel może dodać dodatkowych użytkowników lub grupy zabezpieczeń jako współwłaścicieli.

> [!NOTE]
> Najlepszym rozwiązaniem jest mieć co najmniej dwie osoby jako właścicieli dla dowolnego zasobu danych będących własnością.
>
>

### <a name="remove-owners"></a>Usuń właścicieli
Tak jak każdy właściciel zasobu może dodawać współwłaścicieli, każdy właściciel zasobu może usunąć dowolnego współwłaściciela.

Właściciel zasobu, który usuwa się jako właściciel, nie może już zarządzać zasobem. Jeśli właściciel zasobu usuwa się jako właściciel i nie ma innych współwłaścicieli, zasób powraca do stanu nieuwznanego.

## <a name="control-visibility"></a>Kontroluj widoczność
Właściciele zasobów danych mogą kontrolować widoczność zasobów danych, których są właścicielami. Aby ograniczyć widoczność jako domyślną, gdzie wszyscy użytkownicy wykazu danych mogą wykrywać i wyświetlać zasób danych, właściciel zasobu może przełączać ustawienie widoczności z **Wszyscy** na **Właściciele & Ci użytkownicy** we właściwościach zasobu. Właściciele mogą następnie dodawać określonych użytkowników i grupy zabezpieczeń.

> [!NOTE]
> W miarę możliwości uprawnienia własności i widoczności zasobów powinny być przypisywane do grup zabezpieczeń, a nie do poszczególnych użytkowników.
>
>

## <a name="catalog-administrators"></a>Administratorzy katalogu
Administratorzy wykazu danych są niejawnie współwłaścicielami wszystkich zasobów w katalogu. Właściciele zasobów nie mogą usuwać widoczności administratorów, a administratorzy mogą zarządzać własnością i widocznością wszystkich zasobów danych w katalogu.

## <a name="summary"></a>Podsumowanie
Model crowdsourcingu wykazu danych do metadanych i odnajdowania zasobów danych umożliwia wszystkim użytkownikom katalogu współtworzenie i odnajdowanie. Standardowa edycja wykazu danych jest przeznaczona do własności i zarządzania w celu ograniczenia widoczności i wykorzystania określonych zasobów danych.
