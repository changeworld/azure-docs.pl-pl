---
title: Zarządzanie zasobami danych w usłudze Azure Data Catalog
description: Artykuł pokazuje, jak kontrolować widoczność i praw własności zasobów danych zarejestrowanych w usłudze Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 623f5ed4-8da7-48f5-943a-448d0b7cba69
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 407e25b7bb1a2220448c9701bbef208195c50b63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65953109"
---
# <a name="manage-data-assets-in-azure-data-catalog"></a>Zarządzanie zasobami danych w usłudze Azure Data Catalog
## <a name="introduction"></a>Wprowadzenie
Usługa Azure Data Catalog jest przeznaczona dla źródła danych odnajdywania, tak że można łatwo odnaleźć i zrozumieć użycie źródeł danych potrzebnych do wykonywania analiz i podejmowania decyzji. Te możliwości odnajdywania należy największy wpływ, gdy inni użytkownicy mogą odnaleźć i zrozumieć najszerszej gamy dostępnych źródeł danych. Z tych elementów należy pamiętać, domyślne zachowanie usługi Data Catalog jest dla wszystkich źródeł danych zarejestrowanych jako widoczny i wykrywane przez wszystkich użytkowników wykazu.

Data Catalog nie zapewnia dostęp do danych. Dostęp do danych jest kontrolowany przez właściciela źródła danych. Usługa Data Catalog możesz Odnajdywanie źródeł danych i wyświetlić metadane dotyczące źródeł, które są zarejestrowane w wykazie.

Może to być sytuacje, jednak gdy źródeł danych tylko powinny być widoczne na konkretnym użytkownikom lub członkom określonych grup. W takich przypadkach użytkownicy mogą przejąć prawo własności zasobów danych zarejestrowanych w wykazie i następnie kontrolować widoczność zasobów, które są właścicielami.

> [!NOTE]
> Funkcje opisane w tym artykule jest dostępny tylko w Standard Edition usługi Azure Data Catalog. Bezpłatna wersja nie zapewnia możliwości posiadania i ograniczenie widoczność zasobów danych.
>
>

## <a name="manage-ownership-of-data-assets"></a>Zarządzanie własność zasoby danych
Zasoby danych, które są zarejestrowane w usłudze Data Catalog są domyślnie go. Każdy użytkownik z uprawnieniami dostępu do wykazu może odnajdywać i dodawanie adnotacji do tych zasobów. Użytkownicy mogą przejąć na własność zasoby danych go i następnie ograniczyć widoczność zasobów, które są właścicielami.

Po zasobu danych w usłudze Data Catalog jest właścicielem, tylko użytkownicy, którzy są autoryzowane przez właścicieli można odnaleźć zasobu i wyświetlić jego metadane i tylko dla właścicieli można usunąć elementu zawartości z katalogu.

> [!NOTE]
> Prawa własności w usłudze Data Catalog ma wpływ jedynie metadane są przechowywane w katalogu. Własność nie przyznaje wszystkie uprawnienia w bazowym źródle danych.
>
>

### <a name="take-ownership"></a>Przejęcie na własność
Użytkownicy mogą przejąć prawo własności zasobów danych, wybierając **Przejmij na własność** opcji w portalu usługi Data Catalog. Żadne specjalne ustawienia nie są wymagane do przejęcie na własność zasobu nieposiadanej danych. Każdy użytkownik może zająć własności zasobów nieposiadanej danych.

### <a name="add-owners-and-co-owners"></a>Dodawanie właścicieli i współwłaścicieli
Jeśli należy już do zasobu danych, inni użytkownicy nie mogą po prostu przejęcie na własność. Muszą zostać dodane jako współwłaścicieli przez istniejące właściciela. Wszelkie właściciel subskrypcji może dodać dodatkowych użytkowników lub grup zabezpieczeń jako współwłaścicieli.

> [!NOTE]
> Jest najlepszym rozwiązaniem, aby mieć co najmniej dwóch fizycznych jako właścicieli dla dowolnego zasobu danych należące do firmy.
>
>

### <a name="remove-owners"></a>Usuń właścicieli
Tak, jak dowolnego właściciel zasobu może dodawać współwłaścicieli, wszelkie właściciel zasobu można usunąć wszelkie współwłaściciela.

Właściciel zasobu, który usuwa siebie jako właściciela nie może dłużej zarządzać elementu zawartości. Jeśli właściciel zasobu usuwa siebie jako właściciela wiąże się z nie innych współwłaścicieli, element zawartości zostanie przywrócony go do stanu.

## <a name="control-visibility"></a>Kontrola widoczności
Właściciele zasobów danych można kontrolować widoczność zasobów danych, które są właścicielami. Aby ograniczyć widoczność, jako wartość domyślna, w którym wszyscy użytkownicy wykazu danych można odnalezienia i wyświetlenia zasobów danych, właściciel zasobu można przełączać się ustawienie widoczności na podstawie **wszyscy** do **właściciele i Ci użytkownicy** w właściwości dla elementu zawartości. Następnie można dodać właścicieli, konkretnych użytkowników i grup zabezpieczeń.

> [!NOTE]
> Jeśli to możliwe, uprawnień własności i widoczność zasobów powinny być przypisane do grup zabezpieczeń, a nie do poszczególnych użytkowników.
>
>

## <a name="catalog-administrators"></a>Administratorzy wykazu
Administratorzy wykazu danych są niejawną kolekcją współwłaścicieli wszystkich zasobów w wykazie. Właściciele zasobów mogą nie można usunąć widoczność z Administratorzy i Administratorzy mogą zarządzać prawo własności i widoczność dla wszystkich zasobów danych w wykazie.

## <a name="summary"></a>Podsumowanie
Model crowdsourcingu w postaci wykazu danych do metadanych i danych odnajdywania zasobów umożliwia wszystkim użytkownikom katalogu na potrzeby współtworzenia i odnajdywania. Standard Edition usługi Data Catalog jest przeznaczona dla prawo własności i zarządzania w Aby ograniczyć widoczność oraz korzystanie z danych specyficznych dla zasobów.
