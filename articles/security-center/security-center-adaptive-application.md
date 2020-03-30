---
title: Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center
description: Ten dokument ułatwia korzystanie z kontroli aplikacji adaptacyjnych w usłudze Azure Security Center do umieszczania aplikacji działających na urządzeniach platformy Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/23/2019
ms.author: memildin
ms.openlocfilehash: 1dc94c5ec08cc27fb1819ccc16fd766c62aad796
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604677"
---
# <a name="adaptive-application-controls"></a>Funkcje adaptacyjnego sterowania aplikacjami
Z tego przewodnika dowiesz się, jak skonfigurować funkcje sterowania aplikacjami w usłudze Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co to są funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center?
Adaptacyjne sterowanie aplikacjami to inteligentne, zautomatyzowane, kompleksowe rozwiązanie z usługi Azure Security Center, które pomaga kontrolować, które aplikacje mogą działać na platformie Azure i komputerach innych niż Azure (Windows i Linux). Między innymi pomaga to utwardzić maszyny przed złośliwym oprogramowaniem. Usługa Security Center używa uczenia maszynowego do analizowania aplikacji uruchomionych na komputerach i tworzy listę dozwolonych z tej analizy. Ta funkcja znacznie upraszcza proces konfigurowania i utrzymywania zasad listy zezwalania aplikacji, umożliwiając:

- Blokuj lub ostrzegaj o próbach uruchomienia złośliwych aplikacji, w tym tych, które w przeciwnym razie mogłyby zostać pominięte przez rozwiązania ochrony przed złośliwym oprogramowaniem.
- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

> [!NOTE]
> W przypadku komputerów innych niż platformy Azure i linux formantów aplikacji adaptacyjnych są obsługiwane tylko w trybie inspekcji.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak włączyć funkcje adaptacyjnego sterowania aplikacjami?

Adaptacyjne formanty aplikacji ułatwiają definiowanie zestawu aplikacji, które mogą działać na skonfigurowanych grupach maszyn. Ta funkcja jest dostępna zarówno dla platformy Azure, jak i dla systemów Windows innych niż Azure (wszystkie wersje, klasyczne lub usługi Azure Resource Manager) i na komputerach z systemem Linux. Aby skonfigurować listy dozwolonych aplikacji, należy wykonać następujące czynności:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.

1. W okienku po lewej stronie wybierz pozycję **Funkcje adaptacyjnego sterowania aplikacjami** w obszarze **Zaawansowana ochrona w chmurze**.

    [![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png#lightbox)

Zostanie wyświetlona strona **Adaptacyjne kontrolki aplikacji**.

![funkcje sterowania](./media/security-center-adaptive-application/security-center-adaptive-application-fig2.png)

Sekcja **Grupy maszyn wirtualnych** zawiera trzy karty:

* **Skonfigurowane**: lista grup zawierających maszyny wirtualne, dla których skonfigurowano sterowanie aplikacjami.
* **Zalecane**: lista grup, dla których zaleca się sterowanie aplikacjami. Usługa Security Center przy użyciu uczenia maszynowego identyfikuje maszyny wirtualne odpowiednie do zastosowania funkcji sterowania aplikacjami na podstawie tego, czy na tych maszynach są spójnie uruchamiane te same aplikacje.
* **Brak zaleceń**: lista grup zawierających maszyny wirtualne bez żadnych zaleceń dotyczących sterowania aplikacjami. Na przykład maszyny wirtualne, na których aplikacje stale się zmieniają i nie osiągają stanu stabilnego.

> [!NOTE]
> Usługa Security Center korzysta z własnościowego algorytmu klastrowania w celu tworzenia grup maszyn wirtualnych, jednocześnie zapewniając, że podobne maszyny wirtualne uzyskają optymalne zalecane zasady sterowania aplikacjami.
>
>

### <a name="configure-a-new-application-control-policy"></a>Konfigurowanie nowych zasad sterowania aplikacjami

1. Wybierz kartę **Zalecane** dla listy grup z zaleceniami dotyczącymi kontroli aplikacji:

   ![Zalecane](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Lista zawiera:

   - **Nazwa grupy**: Nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery:** Liczba maszyn wirtualnych w grupie
   - **Państwo**: stan zaleceń
   - **Ważność**: poziom ważności zaleceń

2. Kliknij grupę, aby otworzyć opcję **Utwórz reguły kontroli aplikacji.**

   [![Reguły sterowania aplikacjami](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png#lightbox)

3. W **wybierz maszyny wirtualne**przejrzyj listę zalecanych maszyn wirtualnych i odznacz wszystkie, do których nie chcesz stosować zasad białej listy aplikacji. Następnie zostaną wyświetlone dwie listy:

   - **Zalecane aplikacje:** lista aplikacji, które są częste na maszynach wirtualnych w tej grupie i zaleca się, aby mogły być uruchamiane.
   - **Więcej aplikacji:** lista aplikacji, które są rzadsze na maszynach wirtualnych w tej grupie lub które są znane jako Exploitables (zobacz więcej poniżej) i zalecane do przeglądu.

4. Dokonaj przeglądu aplikacji na każdej liście i wyczyść zaznaczenie pól wyboru tych, które nie mają być stosowane. Każda lista zawiera takie elementy, jak:

   - **NAZWA**: informacje o certyfikacie lub pełna ścieżka aplikacji
   - **TYPY PLIKÓW**: typ pliku aplikacji. Może to być EXE, Skrypt, MSI lub dowolnej permutacji tych typów.
   - **EXPLOITABLE**: ikona ostrzeżenia wskazuje, czy określona aplikacja może być używana przez osobę atakującą do ominięcia listy dozwolonych aplikacji. Zaleca się dokonanie przeglądu tych aplikacji przed ich zatwierdzeniem.
   - **UŻYTKOWNICY**: lista użytkowników, którym zezwala się na uruchomienie aplikacji

5. Po wybraniu opcji wybierz przycisk **Utwórz**. <br>
   Po wybraniu opcji Utwórz usługa Azure Security Center automatycznie tworzy odpowiednie reguły na podstawie wbudowanego rozwiązania listy dozwolonych aplikacji dostępnego na serwerach systemu Windows (AppLocker).

> [!NOTE]
> - Usługa Security Center używa danych z co najmniej dwóch tygodni w celu utworzenia planu bazowego i przygotowania unikatowych zaleceń dla grup maszyn wirtualnych. Nowi klienci usługi Security Center w warstwie Standardowa powinni spodziewać się, że najpierw ich grupy maszyn wirtualnych pojawią się na karcie *Brak zaleceń*.
> - Funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center nie obsługują maszyn wirtualnych, dla których zasady funkcji AppLocker zostały już włączone za pomocą obiektu zasad grupy lub lokalnych zasad zabezpieczeń.
> -  Ze względów bezpieczeństwa Usługa Security Center zawsze będzie próbowała utworzyć regułę wydawcy dla aplikacji, które są wybrane jako dozwolone, i tylko wtedy, gdy aplikacja nie ma informacji o wydawcy (aka nie podpisana), reguła ścieżki zostanie utworzona dla pełnej ścieżki konkretnej aplikacji.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edytowanie i monitorowanie grupy, dla której skonfigurowano sterowanie aplikacjami

1. Aby edytować i monitorować grupę skonfigurowaną z zasadami listy zezwalania aplikacji, wróć do strony **Formantów aplikacji Adaptacyjnej** i wybierz pozycję **SKONFIGUROWANO** W obszarze **Grupy maszyn wirtualnych:**

   ![Grupy](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Lista zawiera:

   - **Nazwa grupy**: nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie
   - **Tryb:** Tryb inspekcji będzie rejestrować próby uruchamiania aplikacji, które nie znajdują się na liście dozwolonych; Wymuszanie nie pozwoli na uruchamianie aplikacji, chyba że znajdują się na liście dozwolonych
   - **Alerty**: wszelkie bieżące naruszenia

2. Kliknij grupę, aby wprowadzić zmiany na stronie **Zasad kontroli aplikacji Edytowanie.**

   ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. W obszarze **Tryb ochrony** do wyboru są następujące opcje:

   - **Inspekcja**: w tym trybie rozwiązanie sterowania aplikacjami nie będzie wymuszać reguł, a jedynie prowadzić inspekcję działań na chronionych maszynach wirtualnych. Jest to zalecane w scenariuszach, w których trzeba najpierw przyjrzeć się ogólnym zachowaniom, zanim zablokuje się uruchamianie aplikacji na docelowej maszynie wirtualnej.
   - **Wymuszanie**: w tym trybie rozwiązanie sterowania aplikacjami będzie wymuszać reguły, a aplikacje, które nie powinny być uruchamiane, będą blokowane.

   > [!NOTE]
   > -  **Wymuszanie** trybu ochrony jest wyłączony do odwołania.
   > - Jak wcześniej wspomniano, domyślnie nowe zasady sterowania aplikacjami są zawsze skonfigurowane w trybie *inspekcji*. 
   >

4. W obszarze **Rozszerzenie zasad**dodaj dowolną ścieżkę aplikacji, na którą chcesz zezwolić. Po dodaniu tych ścieżek Usługa Security Center aktualizuje zasady listy zezwalania aplikacji na maszyny wirtualne w wybranej grupie maszyn wirtualnych i tworzy odpowiednie reguły dla tych aplikacji, oprócz reguł, które już istnieją.

5. Przejrzyj bieżące naruszenia wymienione w sekcji **Ostatnie alerty.** Kliknij każdy wiersz, który ma zostać przekierowany do strony **Alerty** w usłudze Azure Security Center i wyświetl wszystkie alerty wykryte przez usługę Azure Security Center na skojarzonych maszynach wirtualnych.
   - **Alerty**: wszelkie naruszenia, które zostały zarejestrowane.
   - **Nie. maszyn wirtualnych:** liczba maszyn wirtualnych z tym typem alertu.

6. W obszarze **Reguły białej listy wydawcy**, **reguły białej listy ścieżki**i **reguły umieszczania na białej liście skrótów** można zobaczyć, które reguły białej listy aplikacji są obecnie skonfigurowane na maszynach wirtualnych w grupie, zgodnie z typem kolekcji reguł. Dla każdej reguły można zobaczyć:

   - **Reguła**: Określone parametry, zgodnie z którymi aplikacja jest badana przez funkcję Ograniczeń oprogramowania w celu ustalenia, czy aplikacja może być uruchamiana.
   - **Typ pliku**: Typy plików, które są objęte określoną regułą. Może to być dowolna z następujących czynności: EXE, Script, MSI lub dowolna permutacja tych typów plików.
   - **Użytkownicy**: Nazwa lub liczba użytkowników, którzy mogą uruchamiać aplikację, która jest objęta regułą umieszczania na białej liście aplikacji.

   ![Reguły umieszczania na liście dozwolonych](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Kliknij trzy kropki na końcu każdego wiersza, jeśli chcesz usunąć określoną regułę lub edytować dozwolonych użytkowników.

8. Po wszedłu zmian w **zasadach sterowania aplikacją adaptacyjną** kliknij przycisk **Zapisz**.

### <a name="not-recommended-list"></a>Lista bez zaleceń

Usługa Security Center zaleca tylko zasady umieszczania aplikacji na białej liście dla maszyn wirtualnych z stabilnym zestawem aplikacji. Zalecenia nie są tworzone w przypadku ciągłych zmian aplikacji na skojarzonych maszynach wirtualnych.

![Zalecenie](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Lista zawiera:
- **Nazwa grupy**: nazwa subskrypcji i grupy
- **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie

Usługa Azure Security Center umożliwia zdefiniowanie zasad umieszczania na białej liście aplikacji na niezalecanych grupach maszyn wirtualnych. Postępuj zgodnie z tymi samymi zasadami, które zostały wcześniej opisane, aby skonfigurować zasady umieszczania na białej liście aplikacji również w tych grupach.

## <a name="move-a-vm-from-one-group-to-another"></a>Przenoszenie maszyny Wirtualnej z jednej grupy do drugiej

 Po przeniesieniu maszyny Wirtualnej z jednej grupy do innej zastosowana do niej zasada kontroli aplikacji zmienia ustawienia grupy, do której została przeniesiona. Maszynę Wirtualną ze skonfigurowanej grupy można również przenieść ze skonfigurowanej grupy do grupy nieskonfigurowanej, co powoduje usunięcie wszystkich zasad kontroli aplikacji, które zostały wcześniej zastosowane do maszyny Wirtualnej.

 1. Na stronie **Formanty aplikacji adaptacyjnej** na karcie **CONFIGURED** kliknij grupę, do której aktualnie należy maszyna wirtualna, do której ma zostać przeniesiona maszyna wirtualna.
1. Kliknij **pozycję Skonfigurowane maszyny wirtualne i komputery**.
1. Kliknij trzy kropki w wierszu maszyny Wirtualnej, aby przejść, a następnie kliknij przycisk **Przenieś**. Zostanie otwarte okno **Przenieś komputer do innej grupy.**

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Zaznacz grupę, do na które ma być przesunięta maszyna wirtualna, a następnie kliknij pozycję **Przenieś komputer**i kliknij przycisk **Zapisz**.

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Pamiętaj, aby kliknąć przycisk **Zapisz** po kliknięciu przycisku **Przenieś komputer**. Jeśli nie klikniesz **przycisku Zapisz,** komputer nie zostanie przeniesiony.

## <a name="next-steps"></a>Następne kroki
W tym dokumencie dowiesz się, jak używać kontroli aplikacji adaptacyjnych w usłudze Azure Security Center do umieszczania aplikacji działających na białej liście na platformie Azure i maszynach wirtualnych innych niż Azure. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Opis alertów zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z centrum zabezpieczeń platformy Azure](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Blog dotyczący zabezpieczeń platformy Azure](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
