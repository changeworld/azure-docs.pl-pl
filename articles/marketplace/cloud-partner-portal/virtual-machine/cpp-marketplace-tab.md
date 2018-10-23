---
title: Karta Marketplace maszyny wirtualnej w portalu Cloud Partner na platformie Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano kartę Marketplace używanego podczas tworzenia oferty maszyny Wirtualnej portalu Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, virtual machine
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: aa43a6f1f7c757366e321e7da0fb1e47d4928cee
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639770"
---
# <a name="virtual-machine-marketplace-tab"></a>Karta Marketplace maszyny wirtualnej

**Marketplace** karcie **nowa oferta** strony umożliwia udostępnianie potencjalnych klientów. marketingu, sprzedaży i prawne informacji i umowy i zarządzanie potencjalnymi klientami wygenerowany na podstawie Portal Marketplace. Ten długich fragmentów jest podzielony na cztery sekcje: **Przegląd**, **marketingu artefaktów**, **prowadzić zarządzania**, i **prawne**. 

## <a name="overview-section"></a>Sekcja — Omówienie
W tej sekcji należy wprowadzić ogólne informacje o ofercie Azure Marketplace.  Dołączonych gwiazdki (*) na nazwę pola wskazuje, że jest wymagane.

![Omówienie części karty portalu Marketplace w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_008.png)

W poniższej tabeli opisano przeznaczenie i zawartość tych pól.

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Tytuł**                 | Tytuł oferty, często długie, posiadanie nazwy. Ten tytuł będą wyróżniane w portalu marketplace.  Składać z maksymalnie 50 znaków. |
| **Podsumowanie**               | Krótki opis przeznaczenia lub funkcja rozwiązania.  Składać z maksymalnie 100 znaków. |
| **Długie podsumowanie**          | Cel lub funkcja rozwiązania.  Maksymalna długość 256 znaków. |
| **Opis**           | Opis rozwiązania.  Maksymalną długość znaków 3000 obsługuje prosty format HTML. |
| **Identyfikator marketingu**  | Unikatowy adres URL, aby skojarzyć tę ofertę, obejmują zazwyczaj organizacji i nazwę rozwiązania, maksymalnej długości 50 znaków.  Na przykład: <br/> `https://azuremarketplace.microsoft.com/marketplace/apps/contoso.sampleApp`  |
| **Identyfikatory subskrypcji (wersja zapoznawcza)** | Dodaj jeden do 100 identyfikatorów subskrypcji podglądy. Te subskrypcje biały na liście mają dostęp do tej oferty, po opublikowaniu, zanim usługa zostanie wprowadzona na żywo. |
| **Przydatne linki**          | Wielokrotnego wyboru biznesowe i techniczne kategorie, które oferują może być najlepiej skojarzony.  Maksymalnie pięciu dozwolone.  |
|  |  |


## <a name="marketing-artifacts-section"></a>Sekcja artefaktów marketingu

Ta druga sekcja są podzielone na trzy podsekcje: **logo**, **zrzut ekranu**, i **wideo**. Logo są jedynymi wymagane marketingowych artefaktów, jednak wszystkie są zdecydowanie zaleca się najlepsze odwołania klienta.

![Marketing artefaktów części karty portalu Marketplace w formularzu nowa oferta dla maszyn wirtualnych](./media/publishvm_009.png)

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| *Logo*  |  |
| **Małe**                 | Mapa bitowa .ico 40 x 40 pikseli                                                      |
| **Średni**                | Mapa bitowa .ico 90 x 90 pikseli                                                      |
| **Duże**                 | Mapa bitowa .ico 115 x 115 pikseli                                                   |
| **Szerokie**                  | Mapa bitowa .ico 255 x 115 pikseli                                                    |
| **Element Hero**                  | 815 x 290 mapy bitowej.  Opcjonalne, jednak po przekazany, nie można usunąć ikonę elementu hero. |
| *Zrzuty ekranu*  | Opcjonalne, ale maksymalnie pięć zrzuty ekranu na jednostki SKU. |
| **Nazwa**                  | Nazwa lub tytuł <!-- TODO - max char length? none specified in UI -->                               |
| **Obraz**                 | Obraz przechwytywania ekranie, 533 x 324 pikseli                                         |
| *Filmy wideo*  |  |
| **Nazwa**                  | Nazwa lub tytuł  <!-- TODO - max char length? -->                              |
| **Link**                  | Adres URL filmu wideo, w serwisie YouTube lub Vimeo                                        |
| **Miniatura**             | 533 x 324 mapy bitowej                                                               |
|  |  |


### <a name="logo-guidelines"></a>Wytyczne dotyczące logo

<!-- TD: It seems like this section could be better located in some common area, maybe a AMP Marketing/Design section 
+1 this should all be in a common area and referenced from here to that location.-->

Wszystkie logo, które są przekazywane do portalu Cloud Partner powinien postępuj zgodnie z wytycznymi:

*  Projekt platformy Azure ma prostą paletę kolorów. Niskich numer podstawowy i pomocniczy kolory na logo.
*  Kolory motywu w witrynie Azure Portal są białe i czarne. W związku z tym należy unikać te kolory jako kolor tła logo usługi. Użyj niektóre koloru, która spowodowałaby wprowadzenie Twoje logo widocznym w witrynie Azure portal. Zalecamy proste kolory podstawowe. Następnie korzystając z przezroczystym tłem, upewnij się, że logo/tekstu nie są białe lub czarne lub niebieski.
*  Nie należy używać gradientu tła na logo.
*  Należy unikać wprowadzania tekstu — nawet Twoja firma lub marką — na logo. Wygląd i działanie logo powinno być "płaską" i unikać gradientów.
*  Nie rozciąganie logo.

#### <a name="hero-logo"></a>Logo Duży obraz

Logo usługi Hero jest opcjonalna. Jednak po przekazaniu plików nie można usunąć ikonę elementu hero.  Ikona logo Hero powinien postępuj zgodnie z wytycznymi:

*  Czarny, biały i przezroczyste tło nie są dozwolone dla elementu hero ikon.
*  Należy unikać wszelkich jasny kolor jako tło ikony elementów hero.  Nazwa wyświetlana wydawcy, tytuł plan i ofertę, długie podsumowanie są wyświetlane w kolorze białym czcionki, a musi być na tle.
*  Należy unikać używania większość tekstu podczas projektowania hero logo.  Nazwa wydawcy, tytuł planu, oferty długie Podsumowanie i przycisk Utwórz są osadzone programowo wewnątrz ikonę hero po listy oferty. 
* Obejmują nieużywane prostokąt, po prawej stronie ikona hero, rozmiar 415 x 100 pikseli i przesunięcia 370 pikseli od lewej strony.  

Na przykład następująca ikona hero dotyczy usługi Azure Container Service.  <!-- TD: It would be nice to have the raw bitmap, e.g.before and after embedding. -->

![Przykład ikony hero dla usługi Azure Container Service](./media/publishvm_010.png)


### <a name="marketing-information-example"></a>Przykład — informacje o marketingu 

Poniższa ilustracja pokazuje, jak marketing informacji jest wyświetlana na stronie głównej produktu Microsoft Windows Server.

![Przykładowa strona produktu firmy Microsoft w systemie Windows Server](./media/publishvm_011.png)


## <a name="lead-management-section"></a>Prowadzić sekcji Zarządzanie
<!-- this all should be referenced in a common location for lead management, not in this file. nothing unique for a vm specifically. -->

Trzecia sekcja umożliwia klientom zbieranie potencjalnych klientów jest generowany na podstawie Twoich ofert w portalu Azure Marketplace. Oferuje następujące opcje magazynu (z listy rozwijanej) informacje o potencjalnych klientach.

* **Brak** — ustawienie domyślne, informacje o potencjalnym kliencie nie są zbierane.
* Tabela platformy Azure — zapisywane w tabeli platformy Azure, określona przez ciąg połączenia.
* Dynamics CRM Online — zapisane [Microsoft Dynamics 365 Online](https://dynamics.microsoft.com/) wystąpienia, określonego przez adres URL i uwierzytelniania poświadczeń.
* Punkt końcowy HTTPS - zapisywane w określonym punkcie końcowym HTTPS jako ładunek JSON.
* Marketo - zapisywane do określonego [Marketo](https://www.marketo.com/) wystąpienia, określonego przez identyfikator serwera, identyfikator munchkin i identyfikator formularza.
* SalesForce — zapisane [Salesforce](https://www.salesforce.com/) określona baza danych, za pomocą identyfikatora obiektu.

Po pomyślnym opublikowaniu oferty, połączenia potencjalny klient zostanie zweryfikowana, i potencjalnego klienta testowego jest automatycznie przesyłany do skonfigurowanego miejsca docelowego. Informacje o potencjalnych klientów powinny być stale zarządzane i te ustawienia powinny być na bieżąco aktualizowane przy każdym zmian do architektury zarządzania klienta.

<!-- TD: For more info, see [Need a topic on lead information and processing that mimics the Appendix of the VM Pub Guide]. -->

## <a name="legal-section"></a>Sekcja prawne

Ta ostatnia sekcja umożliwia podanie dwa dokumenty prawne wymagane dla każdej oferty: zasady ochrony prywatności i warunki użytkowania.

|  **Pole**                |     **Opis**                                                          |
|  ---------                |     ---------------                                                          |
| **Adres URL zasad ochrony prywatności**    | Adres URL do zasad zachowania opublikowane                                            |
| **Warunki użytkowania**          | zasady jako zwykły tekst lub HTML proste.  <!-- TODO - max char length? -->       |
|  |  |

<br/>

W ciągu następnych [obsługuje](./cpp-support-tab.md) karcie podaje się technicznych i użytkownika zasoby pomocy technicznej oferty.

