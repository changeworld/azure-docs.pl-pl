---
title: Zarządzanie obraz maszyny wirtualnej w witrynie Azure Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowy przewodnik na temat zarządzania obraz maszyny wirtualnej w witrynie Azure Marketplace po początkowej publikacji
services: Azure Marketplace
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
ROBOTS: NOINDEX
ms.openlocfilehash: 7aea357c22a928d8af96791bf9cb4bd3c94f37b4
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077066"
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Przewodnik poprodukcyjnych oferty maszyny wirtualnej w witrynie Azure Marketplace
W tym artykule wyjaśniono, jak można zaktualizować na żywo maszyny wirtualnej z tej oferty w portalu Azure Marketplace. Jego przeprowadzi Cię przez proces dodawania jednego lub więcej nowych jednostek SKU do istniejącej oferty. On również przeprowadzi Cię przez proces usuwania oferty na żywo maszyny wirtualnej lub jednostki SKU z witryny Marketplace.

Po oferty/jednostki SKU jest umieszczone w [witryny Azure portal](http://portal.azure.com), nie można zmienić następujące pola tekstowe:

* **Identyfikator oferty**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** i wybierz swoją ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **identyfikator oferty**.
* **Identyfikator jednostki SKU**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** i wybierz swoją ofertę. Następnie kliknij przycisk **jednostki SKU** > **Dodaj jednostkę SKU**.
* **Namespace wydawcy**: W publikowania portalu, przejdź do **maszyn wirtualnych** > **wskazówki** > **Poinformuj nas o Twoja firma** (znajdują się w "Zarejestruj się w kroku 2 usługi "Firma") > **Namespace wydawcy** > **Namespace**.

Po oferty/jednostki SKU znajduje się w [Marketplace](https://azure.microsoft.com/marketplace), nie można zmienić następujące pola tekstowe:

* **Identyfikator oferty**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** i wybierz swoją ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **identyfikator oferty**.
* **Identyfikator jednostki SKU**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** i wybierz swoją ofertę. Następnie kliknij przycisk **jednostki SKU** > **Dodaj jednostkę SKU**.
* **Namespace wydawcy**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** > **wskazówki** > **Poinformuj nas o Twoja firma** (znajdujący się w obszarze "Krok 2 Register") **Namespace wydawcy** > **Namespace**.
* **Porty**: Podczas publikowania w portalu, przejdź do **maszyn wirtualnych** i wybierz swoją ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **otwierać porty**.
* **Zmiany wymienione dla jednostek SKU cennika**
* **Zmiana modelu rozliczeń wymienionych dla jednostek SKU**
* **Usuwanie rozliczeń regionach wymienionych dla jednostek SKU**
* **Zmiana liczba dysków danych z wymienionych dla jednostek SKU**

## <a name="update-the-technical-details-of-a-sku"></a>Aktualizuj szczegóły techniczne dotyczące jednostki SKU
Dodawanie nowej wersji na liście jednostek SKU i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** kartę.
4. W **jednostki SKU** sekcji, zlokalizuj jednostkę SKU, które chcesz zaktualizować.
5. Dodaj nowy numer wersji dla jednostki SKU, a następnie kliknij przycisk **+** przycisku. Nowa wersja powinna mieć format X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Zmiany wersji powinny być tylko przyrostowe.
6. W **adres URL wirtualnego dysku twardego systemu operacyjnego** polu wprowadź sygnatury dostępu współdzielonego, identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego i Zapisz zmiany.

   > [!IMPORTANT]
   > Użytkownik nie inkrementacyjnego/dekrementacyjnego liczba dysków danych wymienione jednostki SKU. Musisz utworzyć nowe jednostki SKU w tym przypadku. Aby uzyskać szczegółowe instrukcje można znaleźć w sekcji [Dodawanie nowej jednostki SKU w ramach oferty wymienionych](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Obrazy maszyn wirtualnych](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Aktualizowanie nietechnicznym szczegółów oferty lub jednostki SKU
Możesz zaktualizować nietechnicznym (marketingu, prawne, pomocy technicznej, kategorie) szczegóły oferty na żywo lub jednostki SKU w portalu Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Zaktualizuj opis oferty i logo użytkownika
Aby zaktualizować szczegóły oferty i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **marketingu** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **szczegóły** kartę. W **opis** sekcji, zaktualizuj oferty **tytuł**, **Podsumowanie**, i **długie Podsumowanie** i Zapisz zmiany.

   > [!NOTE]
   > Po zaktualizowaniu szczegółów jednostki SKU należy pamiętać o tych ograniczeń: 
   * Nie należy wprowadzać tekst zduplikowany opis oferty i opis jednostki SKU.
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i długie Podsumowanie oferty. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i Podsumowanie oferty.
   >
   >

    ![Szczegóły](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. W **logo** części **szczegóły** pozycję Aktualizuj logo. Upewnij się, skorzystaj z logo [portalu Azure Marketplace, wytycznych](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Ikona elementów hero jest opcjonalne. Istnieje możliwość nie Przekaż ikonę elementu hero. Po przekazaniu ikony elementów hero, nie ma jednak nie należy go usunąć z publikowanie portalu. Postępuj zgodnie z [hero ikonę wskazówki](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Logo](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Zaktualizuj opis jednostki SKU
Aktualizowanie szczegółów SKU i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **marketingu** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **plany** kartę. W **jednostki SKU** sekcji, zaktualizuj jednostkę SKU **tytuł**, **Podsumowanie**, i **opis** i Zapisz zmiany.

   > [!NOTE]
   > Po zaktualizowaniu szczegółów jednostki SKU należy pamiętać o tych ograniczeń: 
   * Nie należy wprowadzać tekst zduplikowany opis oferty i opis jednostki SKU. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i długie Podsumowanie oferty. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i Podsumowanie oferty.
   >
   >
6. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Plany](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Zmień istniejące linki lub dodawania nowych łączy
Aby zmienić istniejące linki lub dodawania nowych łączy, a następnie ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **marketingu** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **łącza** kartę.
6. Aby dodać nowy link w **łącza** kliknij **+ Dodaj LINK**. W **łącze Dodaj** okna dialogowego wprowadź łącze **tytuł** i **adresu URL** i Zapisz zmiany. Można wprowadzić dowolny link, który zawiera informacje, która może ułatwić klientom.
7. Aby zaktualizować lub usunąć istniejące łącze, wybierz łącze, a następnie kliknij przycisk **Edytuj** przycisk lub **Usuń** przycisku.

   > [!NOTE]
   > Upewnij się, czy łącza, które zostały wprowadzone w tej sekcji działają prawidłowo, ponieważ te linki walidacji podczas procesu produkcji żądania.
   >
   >
8. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Linki](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Dodaj łącze](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Zmień istniejący obraz przykładowej lub Dodaj nowy obraz przykładowych
Aby zmienić istniejącego obrazu przykład lub dodać nowe przykładowe obrazy i ponownie opublikować ofertę, wykonaj następujące kroki:

> [!NOTE]
> Tylko jeden przykładowy obraz jest wyświetlany w [witryny Azure portal](https://portal.azure.com).
>
>

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **marketingu** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **przykładowe obrazy** kartę.
6. Aby dodać nowy obraz przykładowej w **przykładowe obrazy** kliknij **PRZEKAŻ nowy obraz** i Zapisz zmiany.

   > [!NOTE]
   > W tym przykładowy obraz jest opcjonalne.
   >
7. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Przykładowe obrazy](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Aktualizacja zawartości prawne
Aby zaktualizować zawartość prawne i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **marketingu** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **prawne** kartę. W **prawne** sekcji, zaktualizuj Twoich zasad lub warunków użytkowania. Wpisz lub Wklej zasad lub warunków w **warunki użytkowania** pole, a następnie zapisz zmiany.
6. Limit liczby znaków prawne warunki użytkowania wynosi 1 milion znaków.
7. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Informacje prawne](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Zaktualizuj informacje o pomocy technicznej
Aby zaktualizować informacje o pomocy technicznej i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **pomocy technicznej** kartę.
4. W **inżynierów skontaktuj się z pomocą** sekcji, zaktualizuj Inżynieria kontaktowe. Te informacje są używane tylko do komunikacji wewnętrznej między partnerem i firmą Microsoft.
5. W **obsługi klienta** sekcji, zaktualizuj szczegółowe dane kontaktowe pomocy technicznej i **URL pomocy technicznej**. Te informacje są używane tylko do komunikacji wewnętrznej między partnerem i firmą Microsoft.

   > [!NOTE]
   > Jeśli chcesz udostępnić tylko obsługa poczty e-mail, wprowadź numer telefonu fikcyjnego w **obsługi klienta** sekcji. W tym przypadku podany adres e-mail jest używany zamiast tego.
   >
   >
6. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Pomoc techniczna](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Aktualizuj kategorie
Aby zaktualizować sekcję kategorii oferty i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **kategorie** kartę.
4. W **kategorie** sekcji, aktualizację kategorii oferty i Zapisz zmiany. Możesz wybrać maksymalnie pięć kategorii dla galerii w portalu Azure Marketplace.
5. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Kategorie](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Dodawanie nowej jednostki SKU w ramach oferty uwzględnione na liście
Aby dodać nowe jednostki SKU w ofercie na żywo, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę. Następnie kliknij przycisk **Dodaj jednostkę SKU**. 
4. W oknie dialogowym wprowadź **identyfikator jednostki SKU** małymi literami. Wybierz **Przełącz model rozliczeń swojej własnej licencji (BYOL)** pole wyboru, jeśli chcesz opublikować nowej jednostki SKU z modelem rozliczeń BYOL. W przeciwnym razie wyczyść pole wyboru. Kliknij przycisk znacznika do utworzenia nowej jednostki SKU. Jeśli nie wybierzesz model rozliczeń w ramach opcji BYOL, model rozliczania ustawiono automatycznie co godzinę. 30-dniowej bezpłatnej wersji próbnej dla modelu rozliczania co godzinę, zaznacz **jednego miesiąca** dla **jest dostępna bezpłatna wersja próbna?** W przeciwnym razie wybierz **wersji próbnej nie**. (**Jest dostępna bezpłatna wersja próbna?**  pojawia się tylko wtedy, gdy w ramach opcji BYOL nie zostały wybrane podczas tworzenia nowej jednostki SKU.)

   > [!IMPORTANT]
   > **Ukryj tej jednostki SKU z witryny Marketplace, ponieważ zawsze powinny zostać zakupione za pomocą szablonu rozwiązania** powinien być **tak** *tylko* Jeśli jest zatwierdzona do publikowania szablonu rozwiązania. W przeciwnym razie ta opcja powinna zawsze być **nie**.
   >
   >
4. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** kartę i Dowiedz się, nowej jednostki SKU, który został utworzony.
5. Aby skonfigurować nowej jednostki SKU, zobacz [uzyskiwanie certyfikacji obrazu maszyny Wirtualnej](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) wskazówki.
6. Aby dodać materiały marketingowe dla nowej jednostki SKU, zobacz [zapewniają marketingowych zawartości witryny Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Aby dodać informacje o cenach dla nowej jednostki SKU, zobacz [ustalanie cen](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

    ![Jednostki SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Dodaj jednostkę SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Zmień liczba dysków danych dla listy jednostek SKU
Użytkownik nie inkrementacyjnego/dekrementacyjnego liczba dysków danych wymienione jednostki SKU. Musisz utworzyć nowe jednostki SKU w tym przypadku. Aby uzyskać szczegółowe instrukcje można znaleźć w sekcji [Dodawanie nowej jednostki SKU w ramach oferty wymienionych](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Usuń ofertę wymienionych w witrynie Marketplace
Różne aspekty muszą być wykonywane w przypadku wniosek o usunięcie oferty na żywo. Aby uzyskać wskazówki z zespołem pomocy technicznej, aby usunąć ofertę opartą na liście w portalu Marketplace, wykonaj następujące kroki:

1. Umieszczać bilet pomocy technicznej na [utworzenia zdarzenia](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) strony.

2. Wybierz **typ problemu** jako **Zarządzanie ofertami**i wybierz **kategorii** jako **modyfikowanie oferty i/lub jednostek SKU już w środowisku produkcyjnym**.
3. Prześlij żądanie.

Zespół pomocy technicznej przeprowadzi Cię przez proces usuwania oferty/jednostki SKU.

> [!NOTE]
> Zawsze możesz usunąć ofertę, gdy jest on w stanie projektu (ale nie przejściowych lub produkcyjnych). Na **HISTORII** kliknij pozycję **Odrzuć wersję ROBOCZĄ**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Usuń jednostki SKU wymienione w portalu Marketplace
Aby usunąć jednostkę SKU wymienionych w witrynie Marketplace, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W okienku po lewej stronie kliknij **jednostki SKU** kartę.
4. Wybierz jednostkę SKU, który chcesz usunąć, a następnie kliknij przycisk **Usuń** przycisku.
5. Przejdź do **PUBLIKUJ** kartę podczas publikowania w portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.
6. Po opublikowaniu oferty w portalu Marketplace, jednostki SKU został usunięty z portalu Marketplace oraz witryny Azure portal.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Usuń bieżącą wersję jednostki SKU wymienionych w witrynie Marketplace
Aby usunąć bieżącą wersję jednostki SKU wymienione w portalu Marketplace, wykonaj następujące kroki: 

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** kartę.
4. Wybierz jednostkę SKU którego bieżącą wersję, które chcesz usunąć, a następnie kliknij przycisk **Usuń** przycisku.
5. Przejdź do **PUBLIKUJ** kartę podczas publikowania w portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.
6. Po oferty pobiera ponownie opublikować w portalu Marketplace, bieżącą wersję, uwzględnione na liście jednostek SKU zostanie usunięty z portalu Marketplace oraz witryny Azure portal. Jednostka SKU jest następnie przywrócenie poprzedniej wersji.

## <a name="revert-the-listing-price-to-production-values"></a>Przywróć cena listy do produkcji wartości
Aby cofnąć cena listy do produkcji wartości, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **ceny** kartę.
4. Wybierz region, którego cena chcesz zresetować.

    ![Cennik regionów](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Jednostki SKU przy użyciu modelu rozliczeń godzinowych zresetować ceny dla wszystkich rdzeni, są one w środowisku produkcyjnym i wybranego regionu. Jednostki SKU z modelem rozliczeń BYOL, udostępnić jednostki SKU w regionie, zaznaczając pole wyboru dla jednostki SKU w **dostępności jednostki SKU EXTERNALLY-LICENSED (BYOL)** sekcji.

    ![Modele cen](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Kliknij przycisk **AUTOPRICE rynkach na podstawie cen w Stanach Zjednoczonych**.

   > [!NOTE]
   > Etykieta przycisku mogą się różnić w zależności od regionu, którą wybierzesz. Ponieważ wybrano Stanów Zjednoczonych, ten przycisk ma etykietę **AUTOPRICE inne rynków oparte na ceny w Stany Zjednoczone**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Na stronie 1 Kreatora Autoprice Wybierz podstawowy rynku i kliknij **strzałkę** przycisku.

    ![Podstawowy rynku](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na stronie 2, wybierz plany usługi i liczniki (rdzenie) i kliknij **strzałkę** przycisku.

    ![Plany usługi i liczniki](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na stronie 3 kliknij pozycję **Przełącz wszystkie** do wybrania we wszystkich regionach. Lub można ręcznie wybrać pola wyboru dla określonych regionów. Kliknij przycisk **strzałkę** przycisku.

    ![Wybierz rynków](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na stronie 4 Przejrzyj kursy wymiany i kliknij przycisk **Zakończ**. Kreator resetuje, ceny, w zależności od wyborów.

11. Na **ceny** kliknij pozycję **Widok Podsumowanie zmian**.
    Dla **wyświetlanie wersji**, wybierz opcję **projekt**oraz **porównania z**, wybierz opcję **produkcji**. Jeśli widzisz żadnej różnicy cen, cena produkcji wartości pomyślnie przywrócone.

    ![Wyświetlanie podsumowania i zmiany](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Przywróć model rozliczeń do produkcji wartości
Aby cofnąć model rozliczeń do produkcji wartości, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Kliknij przycisk **Edytuj** przycisk, aby przywrócić model rozliczeń. W wyświetlonym oknie zostanie otwarta, zaznacz lub wyczyść **Pomoc dotycząca rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (zwane również Bring Your Own License)** pole wyboru.

    ![Edytuj rozliczeń](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Wykonaj kroki w "Wycofaj cena listy do produkcji wartości", w tym artykule.
6. Przejdź do **PUBLIKUJ** kartę, a następnie kliknij przycisk **WYPYCHANIE do tymczasowego**. Aby uzyskać szczegółowe instrukcje dotyczące testowania oferty w środowisku przejściowym, zobacz [przetestować ofertę maszyny Wirtualnej w portalu Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę w środowisku tymczasowym, przejdź do **PUBLIKUJ** karcie publikowanie portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Przywróć ustawienie widoczności wymienionych jednostki SKU na wartość produkcji
Aby cofnąć ustawienie widoczności wymienionych jednostki SKU do produkcji wartości, wykonaj następujące kroki:

1. Zaloguj się do [portal wydawców](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz swoją ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Wybierz jednostki SKU, a następnie Przywróć ustawienie widoczności SKU, aby wartość produkcji.

    ![Widoczność](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Po wykonaniu tych czynności przy użyciu zmian, kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w portalu Marketplace.

## <a name="see-also"></a>Zobacz także
* [Rozpocznij pracę: Publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Omówienie raportowania wypłaty](marketplace-publishing-report-payout.md)
* [Zmień swoje incentive sprzedawcą dostawcy rozwiązań w chmurze](marketplace-publishing-csp-incentive.md)
* [Rozwiązywanie typowych problemów związanych z publikowania w portalu Marketplace](marketplace-publishing-support-common-issues.md)
* [Uzyskaj pomoc techniczną jako wydawca](marketplace-publishing-get-publisher-support.md)
* [Tworzenie obrazu maszyny Wirtualnej w środowisku lokalnym](marketplace-publishing-vm-image-creation-on-premise.md)
* [Tworzenie maszyny wirtualnej z systemem Windows w witrynie Azure portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
