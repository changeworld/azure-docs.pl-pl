---
title: Konfigurowanie słownika biznesowego w usłudze Azure Data Catalog
description: Artykule wyróżnianie słownik biznesowy w usłudze Azure Data Catalog do definiowania i używania wspólnego słownictwa biznesowego tagu zarejestrowanych zasobów danych.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/05/2019
ms.openlocfilehash: 649a842c8c8890713bda938c8e11740c5c8be7aa
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001917"
---
# <a name="set-up-the-business-glossary-for-governed-tagging"></a>Konfigurowanie słownika biznesowego dla podlegają znakowanie

## <a name="introduction"></a>Wprowadzenie

Usługa Azure Data Catalog umożliwia odnajdywanie źródła danych, dzięki czemu można łatwo odnajdywać i zrozumieć użycie źródeł danych, które należy wykonywać analizy i podejmować decyzje. Te możliwości należy największy wpływ podczas mogą odnaleźć i zrozumieć najszerszej gamy dostępnych źródeł danych.

Znakowanie jest jedną funkcję wykazu danych, które ułatwia lepsze zrozumienie zasobów danych. Korzystając z tagowania, można skojarzyć słowa kluczowe z zasobu lub kolumny, która z kolei ułatwia odnajdywanie zasobów przy użyciu wyszukiwania i przeglądania. Znakowanie pomaga też więcej łatwo zrozumieć kontekst i celem elementu zawartości.

Jednak znakowanie czasami może spowodować problemy z własnych. Niektóre przykłady problemów, które może prowadzić do znakowania są:

* Używanie skrótów na pewne elementy zawartości i rozwiniętego tekstu na innych użytkowników. Ta niezgodność hamuje odnajdywania zasobów, nawet jeśli celem było oznaczyć zasoby za pomocą tego samego tagu.
* Potencjalne zmiany znaczenia, w zależności od kontekstu. Na przykład tag o nazwie *przychód* klienta zestawu danych może oznaczać przychód według klienta, ale tego samego tagu w zestawie danych sprzedaży kwartalnej może oznaczać kwartalnej przychodu firmy.  

Do rozwiązywania tych i innych podobnych wyzwań, Data Catalog zawiera słownik biznesowy.

Za pomocą słownika biznesowego Data Catalog, organizacja może dokumentu najważniejszych terminów biznesowych i ich definicji w celu utworzenia wspólnego słownictwa biznesowego. Ta nadzoru umożliwia spójności danych użycia w całej organizacji. Po zdefiniowaniu termin słownika biznesowego, może ona przypisana do zasobu danych w wykazie. To podejście *podlegają znakowanie*, to samo podejście jako znakowanie.

## <a name="glossary-availability-and-privileges"></a>Słownik dostępności i uprawnienia

Słownik biznesowy jest dostępna tylko w Standard Edition usługi Azure Data Catalog. Wersja bezpłatna usługi Data Catalog nie zawiera słownik i nie zapewnia możliwości do znakowania zarządzanych.

Możesz uzyskać dostęp słownik biznesowy za pośrednictwem **słownik** opcji w menu nawigacji w portalu usługi Data Catalog.  

![Uzyskiwanie dostępu do słownika biznesowego](./media/data-catalog-how-to-business-glossary/01-portal-menu.png)

Administratorzy wykazu danych, jak i członkowie roli Administratorzy słownik można utworzyć, edytowanie i usuwanie terminy słownika w słownik biznesowy. Definicje terminów i tagów zasobów za pomocą terminy słownika, można wyświetlić wszystkich użytkowników wykazu danych.

![Dodawanie nowy termin słownika](./media/data-catalog-how-to-business-glossary/02-new-term.png)

## <a name="creating-glossary-terms"></a>Tworzenie terminy słownika

Administratorzy wykazu danych i administratorów glosariusza, można utworzyć terminy słownika klikając **nowy termin** przycisku. Każdy termin słownika zawiera następujące pola:

* Definicję biznesową terminu
* Opis, który przechwytuje zamierzonego użytkowania lub reguły biznesowe dla zasobu lub kolumny
* Lista osób, które można dowiedzieć się najbardziej termin
* Termin nadrzędny, który definiuje hierarchii, w którym jest zorganizowana termin

## <a name="glossary-term-hierarchies"></a>Hierarchie termin słownika

Za pomocą słownika biznesowego wykazu danych, organizacja może opisywać jego słownictwa biznesowego jako hierarchię terminów i może utworzyć klasyfikację warunki, która lepiej odpowiada jego taksonomii biznesowej.

Okres musi być unikatowa na danym poziomie hierarchii. Zduplikowane nazwy nie są dozwolone. Nie ma żadnego limitu liczby poziomów w hierarchii, ale hierarchii jest często bardziej zrozumiały, gdy istnieją trzy poziomy lub mniej.

Korzystanie z hierarchii w programie słownik biznesowy jest opcjonalne. Pozostawienie nadrzędnego terminu pole puste słownik terminów tworzy listę niezhierarchizowaną (niehierarchiczną) warunków w słowniku.  

## <a name="tagging-assets-with-glossary-terms"></a>Tagowanie zasobów za pomocą terminy słownika

Po zdefiniowaniu terminy słownika w wykazie środowisko tagowania zasobów jest zoptymalizowany do wyszukiwania słownictwo, jak użytkownik wpisze tag. Portalu usługi Data Catalog Wyświetla listę pasujące terminy słownika do wyboru. Jeśli użytkownik wybierze termin słownika z listy, termin zostanie dodany do elementu zawartości jako tag (nazywane również tagi słownika). Użytkownik może również wybrać do utworzenia nowego znacznika, wpisując termin, który nie znajduje się w słowniku (nazywane również tag użytkownika).

![Zasób danych oznakowane za pomocą tagu jednego użytkownika i dwa tagi słownika](./media/data-catalog-how-to-business-glossary/03-tagged-asset.png)

> [!NOTE]
> Tagi użytkownika są jedynym typem tagów obsługiwane w wersji bezpłatnej usługi Data Catalog.

### <a name="hover-behavior-on-tags"></a>Umieść kursor w tagi zachowanie

W portalu usługi Data Catalog dwa typy tagów są zachowania wizualnie odrębne i znajdują się różne po wskazaniu wskaźnikiem. Po najechaniu kursorem na tag użytkownika, możesz sprawdzić, tekst tag i użytkownika lub użytkowników, którzy zostały dodane w tagu. Po umieszczeniu tagi słownika umożliwia wyświetlenie definicji termin słownika i link umożliwiający otworzenie słownik biznesowy, aby wyświetlić pełna definicja terminu.

### <a name="search-filters-for-tags"></a>Filtry wyszukiwania dla tagów

Tagi słownika i tagi użytkownika są można wyszukiwać i mogą być stosowane jako filtry w wyszukiwaniu.

## <a name="summary"></a>Podsumowanie

Za pomocą słownika biznesowego w usłudze Azure Data Catalog i znakowanie zarządzanych, które umożliwia, można zidentyfikować, zarządzanie i odnajdywanie zasobów danych w sposób ciągły. Słownik biznesowy podwyższyć poziom uczenie się ich słownictwa biznesowego członkom organizacji. Słownik obsługuje również przechwytywania istotne metadane, co upraszcza odnajdywanie zasobów i zrozumienia.

## <a name="next-steps"></a>Kolejne kroki

* [Dokumentacja interfejsu API REST na operacje biznesowe w słowniku](/rest/api/datacatalog/data-catalog-glossary)
