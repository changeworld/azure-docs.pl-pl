---
title: Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center | Microsoft Docs
description: Ten dokument ułatwia korzystanie z funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center w celu tworzenia listy dozwolonych aplikacji uruchamianych na maszynach wirtualnych platformy Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 9268b8dd-a327-4e36-918e-0c0b711e99d2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2019
ms.author: monhaber
ms.openlocfilehash: 581db3ebe3ce1cad5c8db3702af179e141f334b4
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143456"
---
# <a name="adaptive-application-controls-in-azure-security-center"></a>Funkcje adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center
Z tego przewodnika dowiesz się, jak skonfigurować funkcje sterowania aplikacjami w usłudze Azure Security Center.

## <a name="what-are-adaptive-application-controls-in-security-center"></a>Co to są funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center?
Funkcji adaptacyjnego sterowania aplikacjami to rozwiązanie umieszczania na białej liście automatyczne aplikacji inteligentnych end-to-end w usłudze Azure Security Center. Ułatwia on kontroli aplikacji, które można uruchomić na platformę Azure i innych — maszyny wirtualne platformy Azure (Windows i Linux), która pozwala między innymi, wzmocnić ochronę tych maszyn wirtualnych przed złośliwym oprogramowaniem. Usługa Security Center korzysta z uczenia maszynowego w celu analizuje aplikacje uruchomione na maszynach wirtualnych i ułatwia zastosowanie listy reguł elementów dozwolonych określonej za pomocą tej analizy. Ta funkcja znacząco upraszcza proces konfigurowania i konserwowania zasady listy dozwolonych aplikacji, dzięki któremu można:

- Blokowanie lub ostrzeganie w przypadku prób uruchomienia złośliwych aplikacji, w tym te, które w przeciwnym razie może zostać niezauważone przez rozwiązania chroniące przed złośliwym kodem.
- Zachowanie zgodności z zasadami zabezpieczeń organizacji, które nakazują korzystanie wyłącznie z licencjonowanego oprogramowania.
- Unikanie używania w danym środowisku niechcianego oprogramowania.
- Unikanie uruchamiania starych i nieobsługiwanych aplikacji.
- Zapobieganie używaniu określonych programów narzędziowych, które nie są dozwolone w organizacji.
- Umożliwianie informatykom sterowania dostępem do poufnych danych za pomocą aplikacji.

> [!NOTE]
> Dla maszyn wirtualnych systemu Linux i spoza platformy Azure funkcje adaptacyjnego sterowania aplikacjami są obsługiwane tylko w trybie inspekcji.

## <a name="how-to-enable-adaptive-application-controls"></a>Jak włączyć funkcje adaptacyjnego sterowania aplikacjami?
Funkcje adaptacyjnego sterowania aplikacjami ułatwiają zdefiniowanie zestawu aplikacji, które mogą być uruchamiane w skonfigurowanych grupach maszyn wirtualnych. Ta funkcja jest dostępna dla platformy Azure i innych niż - Azure Windows (wszystkie wersje, klasyczne lub usługi Azure Resource Manager) i maszyny wirtualne systemu Linux i serwerów. Poniższe kroki umożliwiają skonfigurowanie listy dozwolonych aplikacji w usłudze Security Center:

1. Otwórz pulpit nawigacyjny usługi **Security Center**.
2. W okienku po lewej stronie wybierz pozycję **Funkcje adaptacyjnego sterowania aplikacjami** w obszarze **Zaawansowana ochrona w chmurze**.

    ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig1-new.png)

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
1. Kliknij kartę **Zalecane**, aby uzyskać listę grup z zaleceniami dotyczącymi sterowania aplikacjami:

   ![Zalecane](./media/security-center-adaptive-application/security-center-adaptive-application-fig3.png)

   Lista zawiera:

   - **Nazwa grupy**: Nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery**: Liczba maszyn wirtualnych w grupie
   - **Stan**: stan zaleceń
   - **Ważność**: poziom ważności zaleceń

2. Kliknij grupę, aby otworzyć **Utwórz reguły sterowania aplikacjami** opcji.

   ![Reguły sterowania aplikacjami](./media/security-center-adaptive-application/security-center-adaptive-application-fig4.png)

3. W **wybierz maszyny wirtualne**zapoznaj się z listą zalecanych maszyn wirtualnych i usuń zaznaczenie pola wyboru dowolne czy chcesz zastosować zasady listy dozwolonych aplikacji. Następnie zostaną wyświetlone dwie listy:

   - **Zalecane aplikacje**: listę aplikacji, które są często na maszynach wirtualnych w ramach tej grupy, a następnie zaleca się, aby mogła działać.
   - **Więcej aplikacji**: Lista aplikacji, które są rzadko na maszynach wirtualnych w ramach tej grupy lub które są znane jako wykorzystania (zobacz informacje poniżej) i zalecane do przeglądu.

4. Dokonaj przeglądu aplikacji na każdej liście i wyczyść zaznaczenie pól wyboru tych, które nie mają być stosowane. Każda lista zawiera takie elementy, jak:

   - **Nazwa**: informacje o certyfikacie lub Pełna ścieżka aplikacji
   - **TYPY PLIKÓW**: typ pliku aplikacji. Może to być plik EXE, skrypt, MSI lub permutacji dowolnego z tych typów.
   - **Możliwe do wykorzystania**: ikona ostrzeżenia wskazuje, czy określona aplikacja może wykorzystać przez osobę atakującą do pominięcia rozwiązanie do listy dozwolonych aplikacji. Zaleca się dokonanie przeglądu tych aplikacji przed ich zatwierdzeniem.
   - **UŻYTKOWNICY**: lista użytkowników, którym zezwala się na uruchomienie aplikacji

5. Po wybraniu opcji wybierz przycisk **Utwórz**. <br>
   Po wybraniu przycisku Utwórz usługę Azure Security Center automatycznie tworzy odpowiednie zasady na podstawie rozwiązania umieszczania na białej liście wbudowanej aplikacji dostępnego na serwerach Windows (funkcji AppLocker).

> [!NOTE]
> - Usługa Security Center używa danych z co najmniej dwóch tygodni w celu utworzenia planu bazowego i przygotowania unikatowych zaleceń dla grup maszyn wirtualnych. Nowi klienci usługi Security Center w warstwie Standardowa powinni spodziewać się, że najpierw ich grupy maszyn wirtualnych pojawią się na karcie *Brak zaleceń*.
> - Funkcje adaptacyjnego sterowania aplikacjami w usłudze Security Center nie obsługują maszyn wirtualnych, dla których zasady funkcji AppLocker zostały już włączone za pomocą obiektu zasad grupy lub lokalnych zasad zabezpieczeń.
> -  Jako zabezpieczeń najlepszym rozwiązaniem jest usługa Security Center zawsze próbuje utworzyć regułę wydawcy dla aplikacji, które są wybrane mają być dozwolone i tylko wtedy, gdy aplikacja nie ma informacji o wydawcy (bez podpisu), zostanie utworzona reguła ścieżki dla pełnej ścieżki określonej aplikacji.
>   

### <a name="editing-and-monitoring-a-group-configured-with-application-control"></a>Edytowanie i monitorowanie grupy, dla której skonfigurowano sterowanie aplikacjami

1. Aby edytować i monitorować grupę, skonfigurowano zasady listy dozwolonych aplikacji, wróć do **funkcje adaptacyjnego sterowania aplikacjami** strony i wybierz **SKONFIGUROWANE** w obszarze **grupy maszyn wirtualnych**:

   ![Grupy](./media/security-center-adaptive-application/security-center-adaptive-application-fig5.png)

   Lista zawiera:

   - **Nazwa grupy**: Nazwa subskrypcji i grupy
   - **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie
   - **Tryb**: Tryb inspekcji będą rejestrowane próby uruchomienia aplikacji spoza; Wymuszanie będą nie Pozwól na uruchamianie aplikacji — na liście dozwolonych
   - **Alerty**: wszelkie bieżące naruszenia

2. Kliknij grupę, aby wprowadzić zmiany w **edytowanie zasad kontroli aplikacji** strony.

   ![Ochrona](./media/security-center-adaptive-application/security-center-adaptive-application-fig6.png)

3. W obszarze **Tryb ochrony** do wyboru są następujące opcje:

   - **Inspekcja**: w tym trybie rozwiązanie sterowania aplikacjami nie będzie wymuszać reguł, a jedynie prowadzić inspekcję działań na chronionych maszynach wirtualnych. Jest to zalecane w scenariuszach, w których trzeba najpierw przyjrzeć się ogólnym zachowaniom, zanim zablokuje się uruchamianie aplikacji na docelowej maszynie wirtualnej.
   - **Wymuszanie**: w tym trybie rozwiązanie sterowania aplikacjami będzie wymuszać reguły, a aplikacje, które nie powinny być uruchamiane, będą blokowane.

   > [!NOTE]
   > -  **Wymuszanie** tryb ochrony jest wyłączona do odwołania.
   > - Jak wcześniej wspomniano, domyślnie nowe zasady sterowania aplikacjami są zawsze skonfigurowane w trybie *inspekcji*. 
   >

4. W obszarze **rozszerzenia zasad**, Dodaj dowolną ścieżkę aplikacji, który chcesz zezwolić. Po dodaniu tych ścieżek, usługa Security Center zaktualizuje zasady umieszczania na białej liście aplikacji na maszynach wirtualnych w ramach wybranej grupy maszyn wirtualnych i tworzy odpowiednie zasady dla tych aplikacji, oprócz reguł, które znajdują się już w miejscu.

5. Przejrzyj bieżące naruszenia na liście **ostatnie alerty** sekcji. Kliknij pozycję w każdym wierszu, aby przejść do **alerty** strony w usłudze Azure Security Center, a następnie Wyświetl wszystkie alerty, które zostały wykryte przez usługę Azure Security Center na skojarzonych maszynach wirtualnych.
   - **Alerty**: naruszenie, które zostały zarejestrowane.
   - **Nie. maszyn wirtualnych**: liczba maszyn wirtualnych przy użyciu tego typu alertu.

6. W obszarze **reguł umieszczania na białej liście wydawców**, **reguły umieszczania na białej liście ścieżek**, i **wyznaczania wartości skrótu listy reguł elementów dozwolonych** widać, które są obecnie dostępne reguły listy dozwolonych aplikacji skonfigurowane na maszynach wirtualnych w obrębie grupy, zgodnie z typów kolekcji reguł. Dla każdej reguły widoczne:

   - **Reguła**: Określone parametry zgodnie z którymi aplikacja jest badany przez funkcję AppLocker w celu ustalenia, jeśli aplikacja może być uruchomiona.
   - **Typ pliku**: Typy plików, które są objęte daną regułę. Może to być dowolny z następujących czynności: Plik EXE, skrypt, MSI lub permutacji dowolnego z tych typów plików.
   - **Użytkownicy**: Nazwa lub liczba użytkowników, którzy mogą uruchamiać aplikację, która jest objęta reguły umieszczania na białej liście aplikacji.

   ![Reguły umieszczania na liście dozwolonych](./media/security-center-adaptive-application/security-center-adaptive-application-fig9.png)

7. Kliknij trzy kropki znajdujące się na końcu każdego wiersza, jeśli chcesz usunąć daną regułę lub edytować listę uprawnionych użytkowników.

8. Po wprowadzeniu zmian do **funkcje adaptacyjnego sterowania aplikacjami** zasad, kliknij przycisk **Zapisz**.

### <a name="not-recommended-list"></a>Lista bez zaleceń

Usługa Security Center zaleca tylko zasady listy dozwolonych aplikacji dla maszyn wirtualnych, uruchamiane są stabilne zestawy aplikacji. Zalecenia nie są tworzone w przypadku ciągłych zmian aplikacji na skojarzonych maszynach wirtualnych.

![Zalecenie](./media/security-center-adaptive-application/security-center-adaptive-application-fig11.png)

Lista zawiera:
- **Nazwa grupy**: Nazwa subskrypcji i grupy
- **Maszyny wirtualne i komputery**: liczba maszyn wirtualnych w grupie

Usługa Azure Security Center umożliwia zdefiniowanie zasad umieszczania na białej liście aplikacji na innych niż zalecane grupy maszyn wirtualnych, jak również. Postępuj zgodnie z tymi samymi zasadami, opisane zostały wcześniej, aby skonfigurować zasady umieszczania na białej liście aplikacji na tych grup, jak również.

## <a name="move-a-vm-from-one-group-to-another"></a>Przenoszenie maszyny Wirtualnej z jednej grupy do innego

 Po przeniesieniu do innej maszyny Wirtualnej z jednej grupy zasad kontroli aplikacji, które są stosowane do niego zmian w ustawieniach grupy, przenieść go do. To również przenieść Maszynę wirtualną z skonfigurowanej grupy, do grupy inne niż skonfigurowane, co powoduje usunięcie do zasad kontroli aplikacji, która wcześniej została zastosowana do maszyny Wirtualnej.

 1. Z **funkcje adaptacyjnego sterowania aplikacjami** strony, od **SKONFIGUROWANE** kartę, kliknij grupę, do której należy maszyna wirtualna ma zostać przeniesiona obecnie.
1. Kliknij przycisk **skonfigurowane maszyny wirtualne i komputery**.
1. Kliknij trzy kropki znajdujące się w wierszu maszynę Wirtualną, aby przenieść, a następnie kliknij przycisk **przenieść**. **Przenieś komputer do innej grupy** zostanie otwarte okno.

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group.png)

 1. Wybierz grupę, aby przenieść maszynę Wirtualną, a następnie kliknij przycisk **Przenieś komputer**i kliknij przycisk **Zapisz**.

    ![Ochrona](./media/security-center-adaptive-application/adaptive-application-move-group2.png)

 > [!NOTE]
> Należy kliknąć przycisk **Zapisz** po kliknięciu przycisku **Przenieś komputer**. Jeśli nie klikniesz **Zapisz**, a następnie komputera nie zostaną przeniesione.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie przedstawiono sposób użycia funkcji adaptacyjnego sterowania aplikacjami w usłudze Azure Security Center do listy dozwolonych działających na platformie Azure i - Azure VMs. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń Azure, zobacz następujące artykuły:

* [Reagowanie na alerty zabezpieczeń i zarządzanie nimi w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts). Dowiedz się, jak zarządzać alertami i reagować na zdarzenia związane z bezpieczeństwem w usłudze Security Center.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md). Informacje na temat sposobu monitorowania kondycji zasobów platformy Azure.
* [Informacje o alertach zabezpieczeń w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-alerts-type). Poznaj różne typy alertów zabezpieczeń.
* [Przewodnik rozwiązywania problemów z usługą Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-troubleshooting-guide). Dowiedz się, jak rozwiązywać typowe problemy z usługą Security Center.
* [Azure Security Center — często zadawane pytania](security-center-faq.md). Odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/). Wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.
