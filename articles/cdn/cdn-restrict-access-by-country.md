---
title: Ogranicz zawartość sieci Azure CDN według kraju | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć dostęp do treści Azure CDN przy użyciu funkcji filtrowania geo.
services: cdn
documentationcenter: ''
author: lichard
manager: akucer
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: bb757ab115d03ab04dac4468d23f446696a971a9
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2018
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ogranicz zawartość sieci Azure CDN według kraju

## <a name="overview"></a>Przegląd
Gdy użytkownik zażąda zawartości, domyślnie, niezależnie od tego, w którym użytkownik wprowadzone tego żądania z obsługiwanej zawartości. W niektórych przypadkach można ograniczyć dostęp do zawartości według kraju. W tym artykule opisano sposób użycia *filtrowania geograficznie* funkcji, aby skonfigurować usługę, aby umożliwić lub zablokować dostęp według kraju.

> [!IMPORTANT]
> Produkty Azure CDN wszystkie mają te same funkcje filtrowania geograficznie, ale ma niewielkie różnice w te numerów kierunkowych, których obsługują. Zobacz krok 3 dla łącza do różnic.


Informacji o kwestiach dotyczących konfigurowania tego typu ograniczeń znajduje się w temacie [zagadnienia](cdn-restrict-access-by-country.md#considerations).  

![Filtrowanie kraju](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Krok 1: Definiowanie ścieżkę katalogu
> [!IMPORTANT]
> **Azure CDN Standard from Microsoft** profile nie obsługują na podstawie ścieżki geograficznie filtrowania.
>


Wybierz punkt końcowy w portalu, a znaleźć kartę filtrowania geograficznie na nawigacji po lewej stronie, aby znaleźć tę funkcję.

Konfigurując filtr kraju, należy określić ścieżkę względną do lokalizacji, do którego użytkownicy będą dozwolone lub odmowa dostępu. Możesz zastosować filtrowanie geograficzną wszystkich plików z ukośnikiem (/) lub wybranych folderów, określając ścieżki katalogu */pictures/*. Można także zastosować dla filtrowania geograficznie w pojedynczym pliku przez określenie pliku i pomijając wiodący ukośnik */pictures/city.png*.

Przykładowy filtr ścieżki katalogu:

    /                                 
    /Photos/
    /Photos/Strasbourg/
      /Photos/Strasbourg/city.png

## <a name="step-2-define-the-action-block-or-allow"></a>Krok 2: Definiowanie akcji: blokowanie lub zezwalanie na
**Zablokuj:** użytkowników z określonym krajów nie będą mieli dostępu do zasobów zażąda tej ścieżki cyklicznego. Jeśli inne opcje filtrowania kraju zostały skonfigurowane dla tej lokalizacji, następnie wszyscy inni użytkownicy będą mieć dostęp.

**Zezwalaj na:** tylko użytkowników z określonym krajów będą mieć dostęp do zasobów zażąda tej ścieżki cyklicznego.

## <a name="step-3-define-the-countries"></a>Krok 3: Definiowanie krajów
Wybierz krajów, które chcesz zablokować lub zezwolić dla ścieżki. 

Na przykład reguła blokowania /Photos/Strasburgu/będzie filtrować pliki w tym:

    http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg
    http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg


### <a name="country-codes"></a>Kody krajów
Funkcję filtrowania geograficznie używa numerów kierunkowych do definiowania krajów, z których będą dozwolone lub blokowane dla katalogu zabezpieczonych żądanie. Mimo że wszystkie produkty Azure CDN mają te same funkcje filtrowania geograficznie, jest niewielkie różnice w kodów kraju, których obsługują. Aby uzyskać informacje, zobacz [Azure CDN numerów kierunkowych](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zmiany w konfiguracji filtrowania kraju nie będą obowiązywać natychmiast:
   * Dla **Azure CDN Standard from Microsoft** profile, propagacji zazwyczaj kończy w ciągu 10 minut. 
   * Aby uzyskać **Azure CDN Standard from Akamai** profile, propagacji zazwyczaj kończy w ciągu jednej minuty. 
   * Dla **Azure CDN Standard from Verizon** i **Azure CDN Premium from Verizon** profile, propagacji zazwyczaj kończy w ciągu 90 minut.  
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład "*").
* Konfiguracja filtrowania geograficznie skojarzony ze ścieżką względną będzie cyklicznie zastosowanych do tej ścieżki.
* Tylko jedna reguła może odnosić się do tej samej ścieżce względnej (nie można utworzyć wiele filtrów kraju, które wskazują na tej samej ścieżce względnej. Jednak folder może mieć wiele filtrów kraju. Jest to spowodowane cykliczne rodzaj filtry kraju. Innymi słowy podfolderze folderu wcześniej skonfigurowane można przypisać filtru innego kraju.

