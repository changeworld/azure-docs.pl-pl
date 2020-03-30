---
title: Konfigurowanie glosariusza biznesowego w usłudze Azure Data Catalog
description: Artykuł inicjujący podświetlanie glosariusza biznesowego w usłudze Azure Data Catalog do definiowania i używania wspólnego słownictwa biznesowego do oznaczania zarejestrowanych zasobów danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 1065abecb1f0ef57eb13b1ec3f194f07ae01eaee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68976788"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Konfigurowanie słowniczka biznesowego do tagowania

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Data Catalog umożliwia odnajdowanie źródeł danych, dzięki czemu można łatwo odnajdywać i rozumieć źródła danych, które są potrzebne do przeprowadzania analiz i podejmowania decyzji. Te możliwości mają największy wpływ, gdy można znaleźć i zrozumieć najszerszy zakres dostępnych źródeł danych.

Jedną z funkcji wykazu danych, która promuje lepsze zrozumienie danych zasobów jest tagowanie. Korzystając z tagowania, możesz skojarzyć słowa kluczowe z zasobem lub kolumną, co z kolei ułatwia odnajdowanie zasobu poprzez wyszukiwanie lub przeglądanie. Tagowanie pomaga również łatwiej zrozumieć kontekst i intencję zasobu.

Jednak tagowanie może czasami powodować własne problemy. Oto kilka przykładów problemów, które można wprowadzić w tagowaniu:

* Użycie skrótów na niektórych zasobach i rozszerzonego tekstu na innych. Ta niespójność utrudnia odnajdowanie zasobów, mimo że celem było oznaczenie zasobów tym samym tagiem.
* Potencjalne różnice w znaczeniu, w zależności od kontekstu. Na przykład tag o nazwie *Przychód* na zestawie danych odbiorcy może oznaczać przychody według klienta, ale ten sam tag w kwartalnym zestawie danych sprzedaży może oznaczać kwartalne przychody dla firmy.  

Aby pomóc w rozwiązaniu tych i innych podobnych problemów, katalog danych zawiera słowniczek biznesowy.

Korzystając z glosariusza biznesowego wykazu danych, organizacja może dokumentować kluczowe terminy biznesowe i ich definicje w celu utworzenia wspólnego słownictwa biznesowego. To zarządzanie umożliwia spójność użycia danych w całej organizacji. Po zdefiniowaniu terminu w słowniczku biznesowym można go przypisać do zasobu danych w katalogu. Takie podejście, *regulowane tagowanie,* jest takie samo podejście jak tagowanie.

## <a name="glossary-availability-and-privileges"></a>Dostępność glosariusza i uprawnienia

Słowniczek biznesowy jest dostępny tylko w wersji standardowej usługi Azure Data Catalog. Bezpłatna wersja wykazu danych nie zawiera glosariusza i nie zapewnia możliwości tagowania regulowanego.

Dostęp do słowniczka biznesowego można uzyskać za pomocą opcji **Słowniczek** w menu nawigacyjnym portalu wykazu danych.  

![Katalog danych — dostęp do słowniczka biznesowego](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Administratorzy wykazu danych i członkowie roli administratorzy glosariuszy mogą tworzyć, edytować i usuwać terminy glosariuszy w słowniczku biznesowym. Wszyscy użytkownicy wykazu danych mogą wyświetlać definicje terminów i oznaczać zasoby za pomocą terminów słowniczek.

![Katalog danych — dodawanie nowego terminu słowniczka](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Tworzenie terminów glosariusza

Administratorzy wykazu danych i administratorzy glosariuszy mogą tworzyć terminy glosariusza, klikając przycisk **Nowy termin.** Każdy termin glosariusza zawiera następujące pola:

* Definicja biznesowa terminu
* Opis, który przechwytuje zamierzone zastosowanie lub reguły biznesowe dla środka trwałego lub kolumny
* Lista zainteresowanych stron, które wiedzą najwięcej o tym terminie
* Termin nadrzędny, który definiuje hierarchię, w której termin jest zorganizowany

## <a name="glossary-term-hierarchies"></a>Hierarchie terminów słowniczek

Korzystając z glosariusza biznesowego wykazu danych, organizacja może opisać swoje słownictwo biznesowe jako hierarchię terminów i może utworzyć klasyfikację terminów, które lepiej reprezentują jej taksonomię biznesową.

Termin musi być unikatowy na danym poziomie hierarchii. Zduplikowane nazwy nie są dozwolone. Nie ma ograniczeń co do liczby poziomów w hierarchii, ale hierarchia jest często łatwiej zrozumiałe, gdy istnieją trzy poziomy lub mniej.

Korzystanie z hierarchii w słowniczku biznesowym jest opcjonalne. Pozostawienie pola terminu nadrzędnego puste dla terminów glosariusza tworzy płaską (nieiarżrówną) listę terminów w słowniczku.  

## <a name="tagging-assets-with-glossary-terms"></a>Oznaczanie zasobów za pomocą terminów glosariusza

Po zdefiniowaniu terminów glosariusza w katalogu środowisko tagowania zasobów jest optymalizowane do wyszukiwania w słowniczku, gdy użytkownik wpisuje znacznik. Portal wykazu danych wyświetla listę pasujących terminów glosariusza do wyboru. Jeśli użytkownik wybierze z listy termin słowniczek, termin ten jest dodawany do zasobu jako znacznik (nazywany również znacznikiem słowniczkowym). Użytkownik może również utworzyć nowy tag, wpisując termin, który nie znajduje się w słowniczku (nazywany także tagiem użytkownika).

![Zasób danych oznaczony tagiem użytkownika i dwoma znacznikami glosariusza](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Tagi użytkownika są jedynym typem tagu obsługiwanego w bezpłatnej wersji wykazu danych.

### <a name="hover-behavior-on-tags"></a>Zachowanie aktywowania znaczników

W portalu wykazu danych dwa typy tagów są wizualnie różne i prezentują różne zachowania hover. Po umieszczeniu wskaźnika myszy na tagu użytkownika można wyświetlić tekst znacznika oraz użytkownika lub użytkowników, którzy dodali znacznik. Po umieszczeniu wskaźnika myszy na znaczniku słowniczka zobaczysz również definicję terminu słowniczka i łącze, aby otworzyć słowniczek biznesowy, aby wyświetlić pełną definicję tego terminu.

### <a name="search-filters-for-tags"></a>Filtry wyszukiwania tagów

Znaczniki glosariusza i tagi użytkownika można przeszukiwać i można je zastosować jako filtry w wyszukiwaniu.

## <a name="summary"></a>Podsumowanie

Korzystając z glosariusza biznesowego w usłudze Azure Data Catalog i regulowanego tagowania, które umożliwia, można identyfikować zasoby danych, zarządzać nimi i wykrywać je w spójny sposób. Słowniczek biznesowy może promować uczenie się słownictwa biznesowego przez członków organizacji. Słowniczek obsługuje również przechwytywanie znaczących metadanych, co upraszcza wykrywanie i rozumienie zasobów.

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja INTERFEJSU API REST dla operacji glosariusza biznesowego](/rest/api/datacatalog/data-catalog-glossary)
