---
title: Ogranicz zawartość sieci Azure CDN według kraju | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć dostęp do treści Azure CDN przy użyciu funkcji filtrowania Geo.
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
ms.openlocfilehash: 30160088d9c770400f342e67527e1cf1cabc4f6b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ogranicz zawartość sieci Azure CDN według kraju

## <a name="overview"></a>Przegląd
Gdy użytkownik zażąda zawartości, domyślnie, niezależnie od tego, w którym użytkownik wprowadzone tego żądania z obsługiwanej zawartości. W niektórych przypadkach można ograniczyć dostęp do zawartości według kraju. W tym temacie wyjaśniono, jak używać **filtrowania geograficznie** funkcji, aby skonfigurować usługę, aby umożliwić lub zablokować dostęp według kraju.

> [!IMPORTANT]
> Produkty Verizon i Akamai mają te same funkcje filtrowania geograficznie, ale ma niewielkie różnice w te numerów kierunkowych krajów obsługiwanych. Zobacz krok 3 dla łącza do różnic.


Informacji o kwestiach dotyczących konfigurowania tego typu ograniczeń znajduje się w temacie [zagadnienia](cdn-restrict-access-by-country.md#considerations) sekcji na końcu tego tematu.  

![Filtrowanie kraju](./media/cdn-filtering/cdn-country-filtering-akamai.png)

## <a name="step-1-define-the-directory-path"></a>Krok 1: Definiowanie ścieżkę katalogu
Wybierz punkt końcowy w portalu, a znaleźć kartę filtrowania geograficznie na nawigacji po lewej stronie, aby znaleźć tę funkcję.

Konfigurując filtr kraju, należy określić ścieżkę względną do lokalizacji, do którego użytkownicy będą dozwolone lub odmowa dostępu. Filtrowanie geograficznie można stosować dla wszystkich plików z "/" lub wybrane foldery, określając ścieżki katalogu "/ obrazy /". Można także zastosować dla filtrowania geograficznie w pojedynczym pliku przez określenie pliku i pomijając wiodący ukośnik "/ zdjęcia/miasto.png".

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
**Filtrowania geograficznie** funkcji używa kodów kraju, aby zdefiniować krajów, z których będą dozwolone lub blokowane dla katalogu zabezpieczonych żądanie. Można znaleźć numerów kierunkowych w [Azure CDN numerów kierunkowych](https://msdn.microsoft.com/library/mt761717.aspx). 

## <a id="considerations"></a>Zagadnienia dotyczące
* Verizon lub kilka minut z Akamai, zmiany w Twoim kraju filtrowania konfiguracja zaczęła obowiązywać może potrwać do 90 minut.
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład "*").
* Konfiguracja filtrowania geograficznie skojarzony ze ścieżką względną będzie cyklicznie zastosowanych do tej ścieżki.
* Tylko jedna reguła może odnosić się do tej samej ścieżce względnej (nie można utworzyć wiele filtrów kraju, które wskazują na tej samej ścieżce względnej. Jednak folder może mieć wiele filtrów kraju. Jest to spowodowane cykliczne rodzaj filtry kraju. Innymi słowy podfolderze folderu wcześniej skonfigurowane można przypisać filtru innego kraju.

