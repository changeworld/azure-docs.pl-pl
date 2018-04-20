---
title: Zarządzanie obrazu maszyny wirtualnej w portalu Azure Marketplace | Dokumentacja firmy Microsoft
description: Szczegółowy przewodnik na temat zarządzania po opublikowaniu początkowej obrazu maszyny wirtualnej w portalu Azure Marketplace
services: Azure Marketplace
documentationcenter: ''
author: msmbaldwin
manager: mbaldwin
editor: ''
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: mbaldwin;
ms.openlocfilehash: 19f60de4a6c651885da7ff1fb7612a77f3d0c8f1
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Przewodnik po produkcyjnych w przypadku ofert maszyny wirtualnej w portalu Azure Marketplace
W tym artykule opisano sposób aktualizowania maszyny wirtualnej na żywo z tej oferty w portalu Azure Marketplace. Go prowadzi użytkownika przez proces dodawania jednego lub więcej nowe jednostki SKU do istniejącej oferty. On również prowadzi użytkownika przez proces usuwania oferty na żywo maszyny wirtualnej lub wersji z witryny Marketplace.

Po oferta/jednostka SKU jest umieszczane w [portalu Azure](http://portal.azure.com), nie można zmienić następujących polach tekstowych:

* **Identyfikator oferty**: portal w publikowania, przejdź do **maszyn wirtualnych** i wybierz ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **identyfikator oferty**.
* **Identyfikator jednostki SKU**: portal w publikowania, przejdź do **maszyn wirtualnych** i wybierz ofertę. Następnie kliknij przycisk **jednostki SKU** > **Dodaj jednostki SKU**.
* **Namespace wydawcy**: portal w publikowania, przejdź do **maszyn wirtualnych** > **wskazówki** > **Powiedz nam o Twoja firma**(znajdujący się w obszarze "Krok 2 zarejestrować Twoja firma") > **Namespace wydawcy** > **Namespace**.

Po oferta/jednostka SKU jest wymieniony w [Marketplace](http://azure.microsoft.com/marketplace), nie można zmienić następujących polach tekstowych:

* **Identyfikator oferty**: portal w publikowania, przejdź do **maszyn wirtualnych** i wybierz ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **identyfikator oferty**.
* **Identyfikator jednostki SKU**: portal w publikowania, przejdź do **maszyn wirtualnych** i wybierz ofertę. Następnie kliknij przycisk **jednostki SKU** > **Dodaj jednostki SKU**.
* **Namespace wydawcy**: portal w publikowania, przejdź do **maszyn wirtualnych** > **wskazówki** > **Powiedz nam o Twoja firma**(znajdujący się w obszarze "Krok 2 Register") **Namespace wydawcy** > **Namespace**.
* **Porty**: portal w publikowania, przejdź do **maszyn wirtualnych** i wybierz ofertę. Następnie kliknij przycisk **obrazów maszyn wirtualnych** > **Otwieranie portów**.
* **Zmiany listy SKU(s) cennika**
* **Zmiana modelu rozliczeń wymienionych SKU(s)**
* **Usuwanie rozliczeń regionów wymienionych SKU(s)**
* **Zmiana liczba dysków danych z listy SKU(s)**

## <a name="update-the-technical-details-of-a-sku"></a>Aktualizuj szczegóły techniczne dotyczące jednostki SKU
Aby dodać nową wersję do wymienionych wersji i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** kartę.
4. W **jednostki SKU** sekcji, Znajdź jednostka SKU, który chcesz zaktualizować.
5. Dodaj nowy numer wersji jednostki SKU i kliknij przycisk **+** przycisku. Nowa wersja powinna mieć format X.Y.Z, gdzie X, Y i Z są liczbami całkowitymi. Powinien mieć tylko przyrostowe zmiany wersji.
6. W **adres URL dysku VHD systemu operacyjnego** , wprowadź sygnatury dostępu współdzielonego, identyfikator URI utworzony dla wirtualnego dysku twardego systemu operacyjnego i Zapisz zmiany.

   > [!IMPORTANT]
   > Użytkownik nie przyrostu/dekrementacja liczba dysków danych z wymienionych wersji. Musisz utworzyć nowe jednostki SKU w takim przypadku. Aby uzyskać szczegółowe wskazówki, zapoznaj się z rozdziałem [Dodaj nowe jednostki SKU w ramach wymienionych oferty](#add-a-new-sku-under-a-listed-offer).
   >
   >
7. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Obrazy maszyn wirtualnych](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="update-the-nontechnical-details-of-an-offer-or-a-sku"></a>Aktualizuj szczegóły nietechnicznym oferty lub wersji
Można aktualizować nietechnicznym (marketing, prawnych, obsługi, kategorie) szczegółowe informacje na żywo oferty lub wersji w witrynie Marketplace.

### <a name="update-the-offer-description-and-logos"></a>Zaktualizuj opis oferty i logo
Aby zaktualizować szczegóły oferty i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **MARKETING** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **szczegóły** kartę. W **opis** sekcji, zaktualizuj oferty **tytuł**, **Podsumowanie**, i **długie Podsumowanie** i zapisać zmiany.

   > [!NOTE]
   > Po zaktualizowaniu szczegóły jednostka SKU, należy pamiętać o tych ograniczeń: 
   * Nie należy wprowadzać tekst zduplikowany opis oferty i opis jednostki SKU.
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i długie Podsumowanie oferty. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i Podsumowanie oferty.
   >
   >

    ![Szczegóły](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. W **logo** sekcji **szczegóły** pozycję Aktualizuj logo. Upewnij się, że logo wykonaj [wskazówki dotyczące portalu Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).

   > [!NOTE]
   > Ikona bohater jest opcjonalna. Można pominąć Przekaż ikonę bohater. Po przekazaniu ikona bohater nie ma jednak nie należy go usunąć z publikowania portalu. Postępuj zgodnie z [bohater ikony wskazówki](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
   >
   >
7. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Logo](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="update-the-sku-description"></a>Opis jednostki SKU aktualizacji
Aktualizuj szczegóły jednostki SKU i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **MARKETING** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **plany** kartę. W **jednostki SKU** sekcji, zaktualizuj jednostki SKU **tytuł**, **Podsumowanie**, i **opis** i zapisać zmiany.

   > [!NOTE]
   > Po zaktualizowaniu szczegóły jednostka SKU, należy pamiętać o tych ograniczeń: 
   * Nie należy wprowadzać tekst zduplikowany opis oferty i opis jednostki SKU. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i długie Podsumowanie oferty. 
   * Nie należy wprowadzać tekst zduplikowany tytuł jednostki SKU i Podsumowanie oferty.
   >
   >
6. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Plany](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="change-existing-links-or-add-new-links"></a>Zmień istniejące linki lub Dodaj nowe łącza
Aby zmiany istniejące linki lub dodać nowe łącza, a następnie ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **MARKETING** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **łącza** kartę.
6. Aby dodać nowy link w **łącza** kliknij **+ Dodaj**. W **Dodaj łącze** okna dialogowego wprowadź łącze **tytuł** i **adres URL** i zapisać zmiany. Można wprowadzić każde łącze, które zawierają informacje, które mogą ułatwić klientom.
7. Aby zaktualizować lub usunąć istniejące łącze, wybierz łącze, a następnie kliknij przycisk **Edytuj** przycisk lub **usunąć** przycisku.

   > [!NOTE]
   > Upewnij się, że linki, które zostały wprowadzone w tej sekcji działają poprawnie, ponieważ te linki uzyskać weryfikowane podczas procesu żądania produkcji.
   >
   >
8. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Linki](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)

    ![Dodaj łącze](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="change-an-existing-sample-image-or-add-a-new-sample-image"></a>Zmień istniejący obraz przykładowej lub Dodaj nowy obraz próbki
Aby zmienić istniejącego obrazu próbki lub Dodaj nowe próbki obrazy i ponownie opublikować ofertę, wykonaj następujące kroki:

> [!NOTE]
> Obraz tylko jeden przykład jest wyświetlany w [portalu Azure](https://portal.azure.com).
>
>

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **MARKETING** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **przykładowe obrazy** kartę.
6. Aby dodać nowy obraz przykładowej w **przykładowe obrazy** kliknij **PRZEKAŻ nowy obraz** i zapisać zmiany.

   > [!NOTE]
   > Łącznie z obrazem próbki jest opcjonalna.
   >
7. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Przykładowe obrazy](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="update-the-legal-content"></a>Aktualizacja zawartości prawnych
Aby zaktualizować zawartość prawne i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **MARKETING** kartę.
4. Kliknij przycisk **angielski (USA)**.
5. Kliknij przycisk **prawne** kartę. W **prawne** sekcji, aktualizacja Twojego zasad/warunki użytkowania. Wpisz lub Wklej zasad/warunki **warunki użytkowania** i Zapisz zmiany.
6. Jest za długa dla prawne warunki użytkowania to 1 milion znaków.
7. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
8. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Informacje prawne](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="update-the-support-information"></a>Zaktualizuj informacje pomocy technicznej
Aby zaktualizować informacje o pomocy technicznej i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **Obsługa** kartę.
4. W **Engineering skontaktuj się z** sekcji, zaktualizuj engineering szczegóły dotyczące kontaktu. Te informacje są używane do wewnętrznej komunikacji między partnerami i Microsoft tylko.
5. W **pomocy technicznej** sekcji, zaktualizuj dane kontaktowe pomocy technicznej i **OBSŁUGUJE adres URL**. Te informacje są używane do wewnętrznej komunikacji między partnerami i Microsoft tylko.

   > [!NOTE]
   > Jeśli chcesz podać tylko do obsługi poczty e-mail, wprowadź numer telefonu zastępczego w **pomocy technicznej** sekcji. W takim przypadku wiadomości e-mail, podane zamiast niego jest używana.
   >
   >
6. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Pomoc techniczna](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="update-the-categories"></a>Aktualizowanie kategorii
Aby zaktualizować sekcji kategorie dla danej oferty i ponownie opublikować ofertę, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **kategorii** kartę.
4. W **kategorii** sekcji, zaktualizuj kategorie dla danej oferty i zapisać zmiany. Można wybrać maksymalnie pięć kategorii dla galerii Azure Marketplace.
5. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
6. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Kategorie](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="add-a-new-sku-under-a-listed-offer"></a>Dodaj nowe jednostki SKU w ramach wymienionych oferty
Aby dodać nowe jednostki SKU w ramach Twojej oferty na żywo, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę. Następnie kliknij przycisk **Dodaj jednostki SKU**. 
4. W oknie dialogowym wprowadź **identyfikator jednostki SKU** pisane małymi literami. Wybierz **Przełącz własnej licencji (BYOL) modelu rozliczeń** pole wyboru, jeśli chcesz opublikować nowe jednostki SKU z modelu BYOL rozliczeń. W przeciwnym razie wyczyść to pole wyboru. Kliknij przycisk znacznika, aby utworzyć nowe jednostki SKU. Jeśli nie wybierzesz modelu rozliczeń BYOL, modelu rozliczenia jest automatycznie ustawiana co godzinę. 30-dniowej bezpłatnej wersji próbnej dla modelu rozliczenia godzinowe, wybierz opcję **miesiąc** dla **jest dostępna bezpłatna wersja próbna?** W przeciwnym razie wybierz **wersji próbnej nie**. (**Jest dostępna bezpłatna wersja próbna?**  jest wyświetlana tylko wtedy, gdy nie wybrano BYOL podczas tworzenia nowej wersji.)

   > [!IMPORTANT]
   > **Ukryj tej jednostki SKU z witryny Marketplace, ponieważ zawsze powinna zostać zakupione za pośrednictwem szablon rozwiązania** powinien być **tak** *tylko* Jeśli zatwierdzeniu publikowania szablon rozwiązania. W przeciwnym razie ta opcja powinna być zawsze **nr**.
   >
   >
4. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** karcie i dowiedzieć się, nowe jednostki SKU, które zostały utworzone.
5. Aby skonfigurować nowe jednostki SKU, zobacz [uzyskać certyfikacji dla obrazu maszyny Wirtualnej](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) orientacji.
6. Aby dodać przeznaczoną dla nowej wersji produktu, zobacz [Marketplace Podaj marketingu zawartości](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Aby dodać informacje o cenach dla nowej wersji produktu, zobacz [ustawić cenach](marketplace-publishing-push-to-staging.md#step-2-set-your-prices).
8. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
9. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

    ![Jednostki SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)

    ![Dodaj jednostki SKU](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="change-the-data-disk-count-for-a-listed-sku"></a>Zmień liczbę dysków danych wymienionych jednostki SKU
Użytkownik nie przyrostu/dekrementacja liczba dysków danych z wymienionych wersji. Musisz utworzyć nowe jednostki SKU w takim przypadku. Aby uzyskać szczegółowe wskazówki, zapoznaj się z rozdziałem [Dodaj nowe jednostki SKU w ramach wymienionych oferty](#add-a-new-sku-under-a-listed-offer).

## <a name="delete-a-listed-offer-from-the-marketplace"></a>Usuń to wymienione oferta z witryny Marketplace
Różne aspekty konieczne można wybrać ofertę w przypadku żądania, aby usunąć ofertę na żywo. Aby uzyskać wskazówki dotyczące z zespołem pomocy technicznej, aby usunąć to wymienione oferta z witryny Marketplace, wykonaj następujące kroki:

1. Zgłoś biletu pomocy technicznej na [utworzenia zdarzenia](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681) strony.

2. Wybierz **typ problemu** jako **Zarządzanie oferty**i wybierz **kategorii** jako **modyfikowanie oferty i/lub jednostki SKU już w środowisku produkcyjnym**.
3. Prześlij żądanie.

Zespół pomocy technicznej prowadzi użytkownika przez proces usuwania oferty lub jednostki SKU.

> [!NOTE]
> Możesz usunąć ofertę zawsze, gdy jest on w stanie projektu (ale nie tymczasowym czy produkcyjnym). Na **HISTORII** , kliknij pozycję **Odrzuć wersję ROBOCZĄ**.
>
>

## <a name="delete-a-listed-sku-from-the-marketplace"></a>Usuń wymienionych jednostki SKU z witryny Marketplace
Aby usunąć wymienionych jednostki SKU z witryny Marketplace, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W okienku po lewej stronie kliknij **jednostki SKU** kartę.
4. Jednostka SKU, który chcesz usunąć, a następnie kliknij przycisk Wybierz **usunąć** przycisku.
5. Przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować oferta w witrynie Marketplace.
6. Po opublikowaniu oferty w portalu Marketplace, jednostka SKU jest usuwane z witryny Marketplace i portalu Azure.

## <a name="delete-the-current-version-of-a-listed-sku-from-the-marketplace"></a>Usuń bieżącej wersji listy jednostki SKU z witryny Marketplace
Aby usunąć bieżącej wersji listy jednostki SKU z witryny Marketplace, wykonaj następujące kroki: 

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **obrazów maszyn wirtualnych** kartę.
4. Wybierz jednostki SKU którego bieżącej wersji ma zostać usunięty, a następnie kliknij przycisk **usunąć** przycisku.
5. Przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować oferta w witrynie Marketplace.
6. Po oferty pobiera ponownie opublikować w witrynie Marketplace, bieżącej wersji listy Jednostka SKU jest usuwane z witryny Marketplace i portalu Azure. Jednostka SKU jest następnie przywrócenie poprzedniej wersji.

## <a name="revert-the-listing-price-to-production-values"></a>Przywróć cen listę wartości produkcji
Aby przywrócić cen listę wartości produkcji, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).
2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **CENNIK** kartę.
4. Wybierz region, którego chcesz zresetować cennik.

    ![Cennik regionów](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. Dla jednostki SKU o modelu rozliczenia godzinowe Zresetuj ceny dla wszystkich rdzeni, są one w środowisku produkcyjnym dla wybranego regionu. Dla jednostki SKU z modelu rozliczeń BYOL, udostępnić jednostki SKU w regionie, zaznaczając pole wyboru dla jednostki SKU w **dostępności jednostki SKU EXTERNALLY-LICENSED (BYOL)** sekcji.

    ![Modelami cenowymi](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Kliknij przycisk **AUTOPRICE innymi rynkach na podstawie cen w Stanach Zjednoczonych**.

   > [!NOTE]
   > Etykieta przycisku mogą się różnić w zależności od wybranego regionu. Ponieważ wybrano Stanów Zjednoczonych, etykietą przycisku **AUTOPRICE inne rynkach oparte na cen w UNITED STATES**.
   >
   >

    ![Autoprice](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. Na stronie 1 Autoprice kreatora wybierz rynku podstawowej i kliknij **strzałka** przycisku.

    ![Podstawowy rynku](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. Na stronie 2, wybierz planów usług i liczników (rdzenie) i kliknij **strzałka** przycisku.

    ![Plany usługi i liczników](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. Na stronie 3 kliknij **Przełącz wszystkie** aby zaznaczyć wszystkich regionach. Lub użytkownik może ręcznie wybrać pola wyboru dla konkretnych regionów. Kliknij przycisk **strzałka** przycisku.

    ![Wybierz rynkach](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. Na stronie 4 Przejrzyj kursy wymiany i kliknij przycisk **Zakończ**. Kreator resetuje cennik zgodnie z wybrane opcje.

11. Na **CENNIK** , kliknij pozycję **WIDOKU podsumowanie zmian**.
    Dla **wyświetlanie wersji**, wybierz pozycję **projekt**oraz **porównania z**, wybierz pozycję **produkcji**. Jeśli widzisz różnic cenową cennik Przywrócono wartości produkcji pomyślnie.

    ![Wyświetl podsumowanie i zmiany](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
13. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

## <a name="revert-the-billing-model-to-production-values"></a>Przywrócenie modelu rozliczeń do produkcji
Aby przywrócić modelu rozliczeń do produkcji, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Kliknij przycisk **Edytuj** przycisk, aby przywrócić modelu rozliczeń. W wyświetlonym oknie Wybierz lub wyczyść **rozliczeń i licencjonowania odbywa się zewnętrznie z platformy Azure (alias Bring Your Own License)** pole wyboru.

    ![Edytuj rozliczeń](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Postępuj zgodnie z instrukcjami "Przywróć cen listę wartości produkcji" w tym artykule.
6. Przejdź do **publikowania** , a następnie kliknij pozycję **WYPYCHANYCH STAGING**. Szczegółowe wskazówki dotyczące testowania ofertę w środowisku przemieszczania, zobacz [Test ofertę maszyny Wirtualnej dla witryny Marketplace](marketplace-publishing-vm-image-test-in-staging.md).
7. Po przetestowaniu ofertę tymczasowych, przejdź do **publikowania** kartę w publikacji portalu. Kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

## <a name="revert-the-visibility-setting-of-a-listed-sku-to-the-production-value"></a>Przywróć ustawienie widoczności wymienionych SKU wartość produkcji
Aby przywrócić ustawienia widoczności dla wymienionych SKU wartości produkcji, wykonaj następujące kroki:

1. Zaloguj się do [publikowania portal](https://publish.windowsazure.com).

2. Przejdź do **maszyn wirtualnych** , a następnie wybierz ofertę.
3. W menu po lewej stronie kliknij **jednostki SKU** kartę.
4. Wybierz użytkownika jednostki SKU i przywrócić ustawienia widoczności dla jednostki SKU w wartości produkcji.

    ![Widoczność](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Po wykonaniu tych zmian, kliknij przycisk **ŻĄDANIA zatwierdzenia do WYPYCHANIA do produkcji** ponownie opublikować ofertę w witrynie Marketplace.

## <a name="see-also"></a>Zobacz także
* [Get Started: Publikowanie oferty w portalu Azure Marketplace](marketplace-publishing-getting-started.md)
* [Zrozumienie wypłaty raportowania](marketplace-publishing-report-payout.md)
* [Zmień programu incentive odsprzedawcy Cloud Solution Provider](marketplace-publishing-csp-incentive.md)
* [Rozwiązywanie typowych problemów publikowania w witrynie Marketplace](marketplace-publishing-support-common-issues.md)
* [Uzyskaj pomoc techniczną jako wydawca](marketplace-publishing-get-publisher-support.md)
* [Utwórz lokalną obrazu maszyny Wirtualnej](marketplace-publishing-vm-image-creation-on-premise.md)
* [Utwórz maszynę wirtualną z systemem Windows w portalu Azure w wersji zapoznawczej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
