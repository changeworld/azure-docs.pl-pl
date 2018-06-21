---
title: Ogranicz zawartość sieci Azure CDN według kraju | Dokumentacja firmy Microsoft
description: Dowiedz się, jak ograniczyć dostęp według kraju do usługi Azure CDN zawartości za pomocą funkcji filtrowania geo.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: v-deasim
ms.openlocfilehash: 661356aeb2369bc1bbddd6caee57b256dd9e1212
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285019"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ogranicz zawartość sieci Azure CDN według kraju

## <a name="overview"></a>Przegląd
Gdy użytkownik zażąda zawartości, domyślnie, bez względu na lokalizację użytkownika zgłaszającego żądanie obsługiwanej zawartości. Jednak w niektórych przypadkach można ograniczyć dostęp do zawartości według kraju. Z *filtrowania geograficznie* funkcji, można utworzyć reguły na określonej ścieżce na punkt końcowy CDN blokowana zawartość krajów.

> [!IMPORTANT]
> **Azure CDN Standard from Microsoft** profile nie obsługują na podstawie ścieżki geograficznie filtrowania.
> 

## <a name="standard-profiles"></a>Standardowa profilów
Procedury przedstawione w tej sekcji dotyczą **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon** tylko profile. 

Dla **Azure CDN Premium from Verizon** profile, należy użyć **Zarządzaj** portalu, aby aktywować filtrowania geo. Aby uzyskać więcej informacji, zobacz [Azure CDN Premium from Verizon profile](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Zdefiniuj ścieżkę katalogu
Aby uzyskać dostęp do funkcji filtrowania geograficznie, wybierz punkt końcowy sieci CDN w portalu, a następnie wybierz **filtrowania geograficznie** w obszarze Ustawienia w menu po lewej stronie. 

![Filtrowanie replikacji geograficznej — warstwa standardowa](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Z **ścieżki** Określ ścieżkę względną do lokalizacji, do którego użytkownicy będą dozwolone lub odmowa dostępu. 

Można zastosować filtrowanie geograficznie dla wszystkich plików z przodu ukośnika (/), lub wybrać określonych folderów, określając ścieżki katalogu (na przykład */pictures/*). Można także zastosować filtrowanie geograficznie w pojedynczym pliku (na przykład */pictures/city.png*). Dozwolonych jest wiele reguł; Po wprowadzeniu regułę wprowadzenie następnej reguły pojawi się pusty wiersz.

Na przykład wszystkie następujące filtry ścieżki katalogu są prawidłowe:   
*/*                                 
*/Photos/*     
*/Photos/Strasburgu /*     
*/Photos/Strasbourg/City.PNG*

### <a name="define-the-type-of-action"></a>Zdefiniuj typ akcji

Z **akcji** listy, wybierz **Zezwalaj** lub **bloku**: 

- **Zezwalaj na**: tylko użytkowników z określonym krajów mają dostęp do zasobów zażądał ze ścieżki cyklicznego.

- **Blok**: z krajów określonego braku dostępu do zasobów zażądał ze ścieżki cyklicznego. Jeśli inne opcje filtrowania kraju zostały skonfigurowane dla tej lokalizacji, następnie wszyscy inni użytkownicy będą mieć dostęp.

Na przykład filtrowanie geograficznie reguły dotyczące blokowania ścieżka */fotografie/Strasburgu/* filtruje następujące pliki:     
*http://<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http://<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Zdefiniuj krajów
Z **numerów KIERUNKOWYCH** wybierz krajów, które chcesz zablokować lub zezwolić dla ścieżki. 

Po wybraniu krajów wybierz **zapisać** aktywować nową regułę filtrowania geo. 

![Reguły filtrowania Geo](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć regułę, wybierz ją z listy **filtrowania geograficznie** strony, a następnie wybierz pozycję **usunąć**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Azure CDN Premium from Verizon profilów
Aby uzyskać **dla usługi Azure CDN Premium from Verizon** profile interfejsu użytkownika dla tworzenia reguły filtrowania geograficznie różni się:

1. Wybierz z górnego menu w Twoim profilu usługi Azure CDN **Zarządzaj**.

2. Wybierz z portalu Verizon **HTTP dużych**, a następnie wybierz pozycję **filtrowanie kraju**.

    ![Filtrowanie replikacji geograficznej — warstwa standardowa](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Wybierz **Dodaj filtr kraju**.

    **Jeden krok:** zostanie wyświetlona strona.

4. Wpisz ścieżkę do katalogu, wybierz **bloku** lub **Dodaj**, a następnie wybierz pozycję **dalej**.

    **Krok dwa:** zostanie wyświetlona strona. 

5. Wybierz co najmniej jeden krajach z listy, a następnie wybierz **Zakończ** uaktywnić reguły. 
    
    Nowa reguła zostanie wyświetlona w tabeli na **filtrowanie kraju** strony.

    ![Reguły filtrowania Geo](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
W tabeli reguły filtrowania kraju wybierz ikonę Usuń obok zasadę, aby ją usunąć lub ikonę Edytuj, aby go zmodyfikować.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zmiany w konfiguracji filtrowania geograficznie nie będą obowiązywać natychmiast:
   * W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
   * W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
   * W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
 
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład *).

* Konfiguracja filtrowania geograficznie skojarzony ze ścieżką względną jest rekursywnie zastosowanych do tej ścieżki.

* Tylko jedna reguła może odnosić się do tej samej ścieżki względnej. Oznacza to, że nie można utworzyć wiele filtrów kraju, które wskazują na tej samej ścieżce względnej. Jednak ponieważ filtry kraju cykliczne, folderem może mieć wiele filtrów kraju. Innymi słowy podfolderze folderu wcześniej skonfigurowane można przypisać filtru innego kraju.

* Funkcja filtrowania geograficznie używa numerów kierunkowych krajów do definiowania krajów, z których żądanie jest dozwolone lub blokowane dla zabezpieczonych katalogu. Chociaż profile Akamai i Verizon obsługuje większość tych samych kodów kraju, mogą występować niewielkie różnice. Aby uzyskać więcej informacji, zobacz [Azure CDN numerów kierunkowych](https://msdn.microsoft.com/library/mt761717.aspx). 

