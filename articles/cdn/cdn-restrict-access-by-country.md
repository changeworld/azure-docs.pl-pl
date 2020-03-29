---
title: Ograniczanie zawartości usługi Azure CDN według kraju/regionu | Dokumenty firmy Microsoft
description: Dowiedz się, jak ograniczyć dostęp według kraju/regionu do zawartości usługi Azure CDN przy użyciu funkcji filtrowania geograficznego.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 75c422d456f2509ce478e2609a6509f78a6eb31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593425"
---
# <a name="restrict-azure-cdn-content-by-countryregion"></a>Ograniczanie zawartości usługi Azure CDN według kraju/regionu

## <a name="overview"></a>Omówienie
Gdy użytkownik żąda treści, domyślnie zawartość jest obsługiwana niezależnie od lokalizacji użytkownika, który złożył żądanie. Jednak w niektórych przypadkach możesz ograniczyć dostęp do swoich treści według kraju/regionu. Za pomocą funkcji *filtrowania geograficznego* można tworzyć reguły dotyczące określonych ścieżek w punkcie końcowym sieci CDN, aby zezwalać lub blokować zawartość w wybranych krajach/regionach.

> [!IMPORTANT]
> **Usługa Azure CDN Standard z** profili firmy Microsoft nie obsługuje geofiltrowania opartego na ścieżce.
> 

## <a name="standard-profiles"></a>Profile standardowe
Procedury w tej sekcji są dla **usługi Azure CDN Standard z Akamai** i **Azure CDN Standard z** profilów Verizon tylko. 

W przypadku profilów **usługi Azure CDN Premium z verizon** należy użyć portalu **Zarządzaj,** aby aktywować filtrowanie geograficzne. Aby uzyskać więcej informacji, zobacz [Azure CDN Premium z profilów Verizon](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Definiowanie ścieżki katalogu
Aby uzyskać dostęp do funkcji filtrowania geograficznego, wybierz punkt końcowy sieci CDN w portalu, a następnie wybierz **filtrowanie geograficzne** w obszarze USTAWIENIA w menu po lewej stronie. 

![Standard filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-standard.png)

W polu **PATH** określ ścieżkę względną do lokalizacji, do której użytkownicy będą mogli lub odmówiono dostępu. 

Filtrowanie geograficzne dla wszystkich plików za pomocą ukośnika (/) lub wybranie określonych folderów można zastosować, określając ścieżki katalogów (na przykład */pictures/*). Filtrowanie geograficzne można również zastosować do pojedynczego pliku (na przykład */pictures/city.png*). Dozwolone jest wiele reguł; po wprowadzeniu reguły pojawi się pusty wiersz, aby wprowadzić następną regułę.

Na przykład wszystkie następujące filtry ścieżki katalogu są prawidłowe:   
*/*                                 
*/Zdjęcia/*     
*/Zdjęcia/Strasburg/*     
*/Zdjęcia/Strasburg/city.png*

### <a name="define-the-type-of-action"></a>Definiowanie typu akcji

Z listy **AKCJA** wybierz pozycję Zezwalaj lub **Blokuj:** **Block** 

- **Zezwalaj:** Tylko użytkownicy z określonych krajów/regionów mają dostęp do zasobów żądanych ze ścieżki cyklicznej.

- **Blok:** Użytkownikom z określonych krajów/regionów odmawia się dostępu do zasobów żądanych ze ścieżki cyklicznej. Jeśli dla tej lokalizacji nie skonfigurowano żadnych innych opcji filtrowania kraju/regionu, wszyscy inni użytkownicy będą mieli dostęp.

Na przykład reguła filtrowania geograficznego służąca do blokowania ścieżki */Zdjęcia/Strasburg/* filtruje następujące pliki:     
*\//http:\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countriesregions"></a>Definiowanie krajów/regionów
Z listy **KODY KRAJÓW** wybierz kraje/regiony, które chcesz zablokować lub zezwolić na ścieżkę. 

Po wybraniu krajów/regionów wybierz pozycję **Zapisz,** aby aktywować nową regułę filtrowania geograficznego. 

![Reguły filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć regułę, zaznacz ją z listy na stronie **Filtrowanie geograficzne,** a następnie wybierz polecenie **Usuń**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Profile usługi Azure CDN Premium z usług Verizon
W **przypadku usługi Azure CDN Premium z** profili Verizon interfejs użytkownika służący do tworzenia reguły filtrowania geograficznego jest inny:

1. Z górnego menu w profilu usługi Azure CDN wybierz pozycję **Zarządzaj**.

2. W portalu Verizon wybierz **pozycję HTTP Large**, a następnie wybierz pozycję **Filtrowanie kraju**.

    ![Standard filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Wybierz **dodaj filtr kraju**.

    Zostanie wyświetlona strona **Krok pierwszy.**

4. Wprowadź ścieżkę katalogu, wybierz pozycję **Blokuj** lub **Dodaj**, a następnie wybierz pozycję **Dalej**.

    Zostanie wyświetlona strona **Krok drugi:** . 

5. Wybierz jeden lub więcej krajów/regionów z listy, a następnie wybierz **pozycję Zakończ,** aby aktywować regułę. 
    
    Nowa reguła pojawi się w tabeli na stronie **Filtrowanie kraju.**

    ![Reguły filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
W tabeli reguł filtrowania kraju/regionu wybierz ikonę usuwania obok reguły, aby ją usunąć, lub ikonę edycji, aby ją zmodyfikować.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zmiany w konfiguracji filtrowania geograficznego nie są wprowadzane natychmiast:
   * W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
   * W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
   * W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
 
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład *).

* Konfiguracja filtrowania geograficznego skojarzona ze ścieżką względną jest stosowana cyklicznie do tej ścieżki.

* Do tej samej ścieżki względnej można zastosować tylko jedną regułę. Oznacza to, że nie można utworzyć wielu filtrów kraju/regionu, które wskazują tę samą ścieżkę względną. Ponieważ jednak filtry kraju/regionu są cykliczne, folder może mieć wiele filtrów kraju/regionu. Innymi słowy, podfolderowi wcześniej skonfigurowanego folderu można przypisać inny filtr kraju/regionu.

* Funkcja filtrowania geograficznego używa kodów krajów do definiowania krajów/regionów, z których żądanie jest dozwolone lub blokowane dla zabezpieczonego katalogu. Chociaż profile Akamai i Verizon obsługują większość tych samych kodów krajów, istnieje kilka różnic. Aby uzyskać więcej informacji, zobacz [Kody krajów usługi Azure CDN](/previous-versions/azure/mt761717(v=azure.100)). 

