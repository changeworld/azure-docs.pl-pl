---
title: Ogranicz zawartość sieci Azure CDN według kraju | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i ograniczanie dostępu według kraju do zawartości usługi Azure CDN przy użyciu funkcji filtrowania geograficznego.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 12c17cc5-28ee-4b0b-ba22-2266be2e786a
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: magattus
ms.openlocfilehash: 248a51da76cdee06e55438a706c543c70dcf141e
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526185"
---
# <a name="restrict-azure-cdn-content-by-country"></a>Ogranicz zawartość sieci Azure CDN według kraju

## <a name="overview"></a>Omówienie
Gdy użytkownik zażąda zawartości, domyślnie, zawartość są dostarczane niezależnie od lokalizacji użytkownika zgłaszającego żądanie. Jednak w niektórych przypadkach można ograniczyć dostęp do zawartości według kraju. Za pomocą *filtrowania geograficznego* funkcji, można utworzyć reguły w określonych ścieżkach w punkcie końcowym usługi CDN do zezwalania lub blokowania zawartości w wybranych krajach.

> [!IMPORTANT]
> **Usługa Azure CDN Standard from Microsoft** opartego na ścieżkach filtrowania geograficznego nie obsługują profile.
> 

## <a name="standard-profiles"></a>Standardowa profilów
Procedury przedstawione w tej sekcji dotyczą **Azure CDN Standard from Akamai** i **Azure CDN Standard from Verizon** tylko profile. 

Dla **Azure CDN Premium from Verizon** profile, należy użyć **Zarządzaj** portalu, aby aktywować filtrowania geograficznego. Aby uzyskać więcej informacji, zobacz [Azure CDN Premium from Verizon profile](#azure-cdn-premium-from-verizon-profiles).

### <a name="define-the-directory-path"></a>Zdefiniuj ścieżkę katalogu
Aby skorzystać z funkcji filtrowania geograficznego, wybierz punkt końcowy usługi CDN w portalu, a następnie wybierz **filtrowania geograficznego** w obszarze Ustawienia w menu po lewej stronie. 

![Standardowa filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-standard.png)

Z **ścieżki** Określ ścieżkę względną do lokalizacji, do którego użytkownicy będą dozwolone lub odmowa dostępu. 

Można stosować filtrowania geograficznego w przypadku wszystkich plików z przodu ukośnika (/) lub wybrać foldery, określonej przez określenie ścieżek katalogów (na przykład */zdjęcia/miasto.PNG*). Można także zastosować filtrowania geograficznego do pojedynczego pliku (na przykład */pictures/city.png*). Wiele reguł są dozwolone Po wprowadzeniu regułę, wprowadzenie następną regułę pojawi się pusty wiersz.

Na przykład wszystkie z następujących filtrów ścieżki katalogu są prawidłowe:   
*/*                                 
*/Photos/*     
*/Photos/Strasbourg/*     
*/Photos/Strasbourg/city.png*

### <a name="define-the-type-of-action"></a>Zdefiniuj typ akcji

Z **akcji** listy wybierz **Zezwalaj** lub **bloku**: 

- **Zezwalaj na**: Tylko użytkownicy z określonych Państw mają prawa dostępu do zasobów żądane ze ścieżki cykliczne.

- **Blok**: Użytkowników z określonym kraje są odmowa dostępu z zasobami żądane ze ścieżki cykliczne. Jeśli inne opcje filtrowania kraju zostały skonfigurowane dla tej lokalizacji, następnie wszyscy inni użytkownicy będą miały dostęp.

Na przykład filtrowania geograficznego reguły blokowania ścieżce */zdjęcia/Strasburgu/* filtry następujące pliki:     
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/1000.jpg*
*http:\//\<endpoint>.azureedge.net/Photos/Strasbourg/Cathedral/1000.jpg*

### <a name="define-the-countries"></a>Zdefiniuj krajów
Z **numerów KIERUNKOWYCH krajów** Wybierz kraje, które chcesz zablokować lub zezwolić dla ścieżki. 

Po wybraniu krajów wybierz **Zapisz** aktywować nową regułę filtrowania geograficznego. 

![Reguły filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
Aby usunąć regułę, wybierz ją z listy **filtrowania geograficznego** stronie, a następnie wybierz **Usuń**.

## <a name="azure-cdn-premium-from-verizon-profiles"></a>Usługa Azure CDN Premium z profilów firmy Verizon
Aby uzyskać **dla usługi Azure CDN Premium from Verizon** profile interfejsu użytkownika dla tworzenia reguły filtrowania geograficznego różni się:

1. Wybierz z górnego menu w profilu usługi Azure CDN **Zarządzaj**.

2. W portalu firmy Verizon wybierz **HTTP dużych**, a następnie wybierz **filtrowanie kraju**.

    ![Standardowa filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-premium.png)

3. Wybierz **Dodaj filtr kraju**.

    **Jeden krok:** zostanie wyświetlona strona.

4. Wprowadź ścieżkę katalogu, wybierz **bloku** lub **Dodaj**, a następnie wybierz **dalej**.

    **Krok dwa:** zostanie wyświetlona strona. 

5. Wybierz jeden lub więcej krajów, z listy, a następnie wybierz **Zakończ** aby aktywować regułę. 
    
    Nowa reguła zostanie wyświetlona w tabeli na **filtrowanie kraju** strony.

    ![Reguły filtrowania geograficznego](./media/cdn-filtering/cdn-geo-filtering-premium-rules.png)

### <a name="clean-up-resources"></a>Oczyszczanie zasobów
W tabeli reguł filtrowania kraju, wybierz ikonę usuwania obok zasadę, aby ją usunąć lub ikonę edycji, aby go zmodyfikować.

## <a name="considerations"></a>Zagadnienia do rozważenia
* Zmiany w konfiguracji filtrowania geograficznego nie obowiązywać natychmiast:
   * W przypadku profili usługi **Azure CDN Standard from Microsoft** propagacja zwykle trwa do 10 minut. 
   * W przypadku profili usługi **Azure CDN Standard from Akamai** propagacja zwykle trwa mniej niż jedną minutę. 
   * W przypadku profilów usługi **Azure CDN Standard from Verizon** oraz usługi **Azure CDN Premium from Verizon** propagacja zwykle trwa do 10 minut. 
 
* Ta funkcja nie obsługuje symboli wieloznacznych (na przykład *).

* Konfiguracja filtrowania geograficznego, skojarzone ze ścieżką względną jest stosowana cyklicznie do tej ścieżki.

* Tylko jedna reguła może zostać zastosowana do tej samej ścieżce względnej. Oznacza to, że nie można utworzyć wiele filtrów w kraju, które wskazują na tej samej ścieżce względnej. Jednak ponieważ kraj filtry są cykliczne, folder może mieć wiele filtrów kraju. Innymi słowy podfolder folderu wcześniej skonfigurowane można przypisać filtr innym kraju.

* Funkcja filtrowania geograficznego używa numerów kierunkowych krajów do definiowania krajów, z których żądania jest dozwolony lub blokowany dla katalogu zabezpieczone. Mimo że profile firmy Akamai i Verizon obsługuje większość tych samych numerów kierunkowych, ma kilka różnic. Aby uzyskać więcej informacji, zobacz [wysokiej dostępności treści Azure numerów kierunkowych krajów](/previous-versions/azure/mt761717(v=azure.100)). 

