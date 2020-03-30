---
title: Jak dodawać adnotacje do źródeł danych w wykazie danych platformy Azure
description: Artykuł in jak opisano sposób dotowania zasobów danych w usłudze Azure Data Catalog, w tym przyjazne nazwy, tagi, opisy i ekspertów.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: c1e022591ce1aee073330055744fbd78d97c0b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950257"
---
# <a name="how-to-annotate-data-sources-in-azure-data-catalog"></a>Jak dodawać adnotacje do źródeł danych w wykazie danych platformy Azure

## <a name="introduction"></a>Wprowadzenie

**Microsoft Azure Data Catalog** to w pełni zarządzana usługa w chmurze, która służy jako system rejestracji i systemu odnajdowania dla źródeł danych przedsiębiorstwa. Innymi słowy, usługa Data Catalog ma na celu pomoc w odkrywaniu, zrozumieniu i używaniu źródeł danych oraz pomaganiu organizacjom w pozyskiwaniu większej wartości z istniejących danych. Gdy źródło danych jest zarejestrowane w wykazie danych, jego metadane są kopiowane i indeksowane przez usługę, ale historia nie kończy się na tym. Wykaz danych umożliwia użytkownikom podanie własnych metadanych opisowych — takich jak opisy i tagi — w celu uzupełnienia metadanych wyodrębnionych ze źródła danych i uczynienia źródła danych bardziej zrozumiałym dla większej liczby osób.

## <a name="annotation-and-crowdsourcing"></a>Adnotacje i crowdsourcing
Każdy ma swoje zdanie. I to jest dobra rzecz.
Usługa Data Catalog rozpoznaje, że różni użytkownicy mają różne perspektywy na źródła danych przedsiębiorstwa i że każda z tych perspektyw może być cenna. Poniżej przedstawiono przykładowy scenariusz:

* Administrator systemu zna umowę dotyczącą poziomu usług dla serwerów lub usług obsługujących źródło danych.
* Administrator bazy danych zna harmonogram tworzenia kopii zapasowych dla każdej bazy danych i dozwolone okna przetwarzania ETL.
* Właściciel systemu zna proces żądania dostępu użytkowników do źródła danych.
* Administrator danych wie, jak zasoby i atrybuty w źródle danych są mapowane do modelu danych przedsiębiorstwa.
* Analityk wie, jak dane są używane w kontekście procesów biznesowych, które obsługują.

Każda z tych perspektyw jest cenna, a wykaz danych używa podejścia crowdsourcingowego do metadanych, które umożliwia przechwytywanie każdego z nich i podanie pełnego obrazu zarejestrowanych źródeł danych. Korzystając z portalu wykazu danych, każdy użytkownik może dodawać i edytować własne adnotacje, jednocześnie mogą wyświetlać adnotacje dostarczane przez innych użytkowników.

## <a name="different-types-of-annotations"></a>Różne typy adnotacji
Wykaz danych obsługuje następujące typy adnotacji:

| Adnotacja | Uwagi |
| --- | --- |
| Przyjazna nazwa |Przyjazne nazwy mogą być dostarczane na poziomie zasobów danych, aby zasoby danych były łatwiejsze do zrozumienia. Przyjazne nazwy są najbardziej przydatne, gdy nazwa obiektu źródłowego jest tajemnicza, skrócona lub w inny sposób nie ma znaczenia dla użytkowników. |
| Opis |Opisy mogą być dostarczane na poziomach zasobów danych i atrybutów / kolumn. Opisy to krótkie adnotacje tekstowe o swobodnej formie, które opisują perspektywę użytkownika na zasób danych lub jego użycie. |
| Znaczniki (tagi użytkownika) |Znaczniki mogą być dostarczane na poziomach zasobów danych i atrybutów / kolumn. Tagi użytkownika to etykiety zdefiniowane przez użytkownika, które mogą służyć do kategoryzowania zasobów danych lub atrybutów. |
| Znaczniki (znaczniki glosariusza) |Znaczniki mogą być dostarczane na poziomach zasobów danych i atrybutów / kolumn. Znaczniki glosariusza to centralnie zdefiniowane terminy glosariusza, które mogą służyć do kategoryzowanie zasobów danych lub atrybutów przy użyciu typowej taksonomii biznesowej. Aby uzyskać więcej informacji, zobacz [How to set up the Business Glossary for Governed Tagging](data-catalog-how-to-business-glossary.md) (Konfigurowanie słownika biznesowego na potrzeby znakowania zarządzanego). |
| Ekspertów |Eksperci mogą być dostarczane na poziomie zasobów danych. Eksperci identyfikują użytkowników lub grupy z perspektywy ekspertów na dane i może służyć jako punkty kontaktowe dla użytkowników, którzy odkrywają zarejestrowane źródła danych i mają pytania, na które nie są odpowiedzi przez istniejące adnotacje. |
| Żądanie dostępu |Informacje o dostępie żądania mogą być dostarczane na poziomie zasobów danych. Te informacje są przeznaczone dla użytkowników, którzy odnajdują źródło danych, do którego nie mają jeszcze uprawnień dostępu. Użytkownicy mogą wprowadzić adres e-mail użytkownika lub grupy, która udziela dostępu, adres URL procesu lub narzędzia, do którego użytkownicy muszą uzyskać dostęp, lub może wprowadzić sam proces jako tekst. |
| Dokumentacja |Dokumentacja może być dostarczana na poziomie zasobów danych. Dokumentacja zasobu to informacje o tekście sformatowanym, które mogą zawierać łącza i obrazy, które mogą zawierać wszelkie informacje, które nie są przekazywane za pośrednictwem opisów i tagów. |

## <a name="annotating-multiple-assets"></a>Dodawanie adnotacji do wielu zasobów
Podczas wybierania wielu zasobów danych w portalu wykazu danych użytkownicy mogą dodawać adnotacje do wszystkich wybranych zasobów w ramach jednej operacji. Adnotacje będą stosowane do wszystkich wybranych zasobów, dzięki czemu można łatwo wybrać i zapewnić spójny opis i zestawy tagów i ekspertów dla powiązanych zasobów danych.

> [!NOTE]
> Znaczniki i eksperci mogą być również dostarczane podczas rejestrowania zasobów danych za pomocą narzędzia do rejestracji źródła danych wykazu danych.
>
>

Podczas wybierania wielu tabel i widoków w portalu wykazu danych będą wyświetlane tylko kolumny, które mają wspólne wszystkie wybrane zasoby danych. Dzięki temu użytkownicy mogą udostępniać tagi i opisy dla wszystkich kolumn o tej samej nazwie dla wszystkich wybranych zasobów.

## <a name="annotations-and-discovery"></a>Adnotacje i odnajdowanie
Podobnie jak metadane wyodrębnione ze źródła danych podczas rejestracji są dodawane do indeksu wyszukiwania wykazu danych, metadane dostarczone przez użytkownika są również indeksowane. Oznacza to, że nie tylko adnotacje ułatwiają użytkownikom zrozumienie danych, które odkrywają, adnotacje ułatwiają również użytkownikom odnajdowanie zasobów danych z adnotacjami, wyszukując przy użyciu terminów, które mają dla nich sens.

## <a name="summary"></a>Podsumowanie
Rejestrowanie źródła danych w wykazie danych umożliwia odnajdowanie tych danych przez skopiowanie metadanych strukturalnych i opisowych ze źródła danych do usługi Wykaz. Po zarejestrowaniu źródła danych użytkownicy mogą dostarczać adnotacje, aby ułatwić odnajdywanie i zrozumienie z poziomu portalu wykazu danych.

## <a name="see-also"></a>Zobacz też
* [Wprowadzenie do samouczka usługi Azure Data Catalog, aby](data-catalog-get-started.md) uzyskać szczegółowe informacje o tym, jak dodawać adnotacje do źródeł danych.
