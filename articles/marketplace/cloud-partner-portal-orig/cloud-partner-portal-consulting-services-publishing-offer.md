---
title: Platforma Azure i oferty usług doradczych dotyczących 365 Dynamcis | Dokumentacja firmy Microsoft
description: Przewodnik do definiowania i publikowania platformy Azure lub Dynamcis 365 doradcze usługi ofertę w portalu Cloud Partner.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pbutlerm
ms.openlocfilehash: 7ea4a58f072071f60cf87d1e8817ae19658be5fb
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310423"
---
# <a name="azure-and-dynamics-365-consulting-service-offer"></a>Platforma Azure i oferty usług doradczych dotyczących Dynamics 365

<table> <tr> <td>W tej sekcji opisano sposób publikowania usługa doradcza do firmy Microsoft <a href="https://azuremarketplace.microsoft.com">portalu Azure Marketplace</a> lub <a href="https://appsource.microsoft.com">Marketplace usługi AppSource</a>. Rozwiązania oparte na programie Microsoft <a href="https://dynamics.microsoft.com">Dynamics 365</a>, <a href="https://products.office.com">usługi Office 365</a>, <a href="https://powerbi.microsoft.com">usługi Power BI</a>, i <a href="https://powerapps.microsoft.com">PowerApps</a> mogą pojawić się w usłudze AppSource. Inne oferty na podstawie innych firmy Microsoft <a href="https://azure.microsoft.com/services">usług platformy Azure</a> kwalifikują się do listy w witrynie Azure Marketplace. </td> <td><img src="./media/consulting-services-publishing-offer/consulting-icon1.png"  alt="Microsoft consulting icon" /></td> </tr> </table>


## <a name="publishing-benefits"></a>Publikowanie korzyści

Publikowanie do jednej z platformach handlowych firmy Microsoft ma znaczące korzyści:

- Podwyższanie poziomu firmy dzięki wykorzystaniu marki firmy Microsoft.
- Potencjalnie Dotrzyj do ponad 100 milionów użytkowników usług Office 365 i Dynamics 365 w usłudze AppSource; dotrzeć do ponad 200 tysiące organizacji za pośrednictwem witryny Azure Marketplace.
- Pobierz wysokiej jakości potencjalnych klientów z tych rynków.
- Uzyskaj usługi wspierane przez zespoły pola i telesales firmy Microsoft.


## <a name="define-your-consulting-services-offer"></a>Zdefiniuj oferty usług doradczych

Zdefiniuj spakowanych doradcze oferty usługi. Skupić się na zakres stały, stała szacowany czas trwania, stała cena (lub zwolnij) i głównie zorientowane na sales wstępnej ofert dla jednego klienta. Wybierz powtarzalne spakowanych engagements, które zostały popularnych i efektywne, prowadzenie nową działalność dla Ciebie.

## <a name="publish-a-consulting-service-offer"></a>Opublikuj ofertę usługi doradcze

W poniższych sekcjach opisano proces publikowania oferty usług doradczych

1.  Utwórz nową ofertę
2.  Zdefiniuj ustawienia oferty
3.  Wprowadź szczegóły sklepu i czy publikować w portalu Azure Marketplace lub w usłudze AppSource.
4.  Publikowanie oferty

### <a name="create-a-new-offer"></a>Utwórz nową ofertę

Aby utworzyć nową ofertę, wykonaj następujące czynności:

1.  W menu głównym portalu Cloud Partner, wybierz **nową ofertę**.
2.  Wybierz z menu nowa oferta **usługi konsultingowe**.

    ![Tworzenie nowej oferty usług doradczych](media/consulting-services-publishing-offer/cppselectnewconsultingoffer.png)

### <a name="define-offer-settings"></a>Zdefiniuj ustawienia oferty

Na ekranie nowa oferta pierwszym krokiem jest utworzenie tożsamości oferty.  Tożsamość oferty składa się z trzech części: **Identyfikator oferty**, **Identyfikatora wydawcy**, i **nazwa**.  Każdy z tych elementów zostały omówione w poniższych sekcjach.

#### <a name="offer-id"></a>Identyfikator oferty

Ten identyfikator jest unikatową nazwą, utworzonej po przesłaniu najpierw oferty. Musi ona zawierać tylko małe znaki alfanumeryczne, łączniki i podkreślenia. Identyfikator oferty będą widoczne w adresie URL i wpływ na wyniki aparatu wyszukiwania. Na przykład *yourcompanyname\_exampleservice*

Jak pokazano w przykładzie, Identyfikatora oferty pobiera dołączany do Identyfikatora wydawcy do utworzenia unikatowego identyfikatora. To jest udostępniany jako łącze stałe, mogą być zapisane, która jest indeksowana przez aparaty wyszukiwania. 

*Po oferta jest przeznaczona na żywo, nie można zaktualizować jej identyfikator*

#### <a name="publisher-id"></a>Identyfikator wydawcy

Ten identyfikator jest związane z Twoim kontem. Gdy użytkownik jest zalogowany przy użyciu swojego konta organizacyjnego, Twój identyfikator wydawcy pojawią się w menu rozwijanym.

#### <a name="name"></a>Name (Nazwa)

Ten ciąg jest o tym, co będzie wyświetlana jako nazwa oferty w usłudze AppSource lub witrynie Azure Marketplace.

**Ważne:** Wprowadź tylko nazwę nazwa rzeczywistej usługi. Nie dołączaj czas trwania i typ usługi.

Poniższy przykład przez Edgewater Fullscope pokazuje, jak nazwa oferty jest składany. Nazwa oferty wygląda następująco:

![Tworzenie nowej oferty usług doradczych](media/consulting-services-publishing-offer/cppsampleconsultingoffer.png)

Nazwa oferty składa się z czterech części:

-   **Czas trwania:** — to zdefiniowano w **szczegóły Storefront** karta edytora. Czas trwania może być wyrażona w godzinach, dniach lub tygodniach.
-   **Typ usługi:** — to zdefiniowano w **szczegóły Storefront** karta edytora. Typy usług `Assessment`, `Briefing`, `Implementation`, `Proof of concept`, i `Workshop`.
-   **Preposition:** — wstawiony przez recenzenta
-   **Nazwa:** — to zdefiniowano w **oferują ustawienia** strony.

Poniższa lista zawiera kilka nazw dobrze nazwane oferty:

-   Podstawy dla profesjonalnych usług: 1-godzinnego widoków
-   Platforma usług w chmurze migracji: 1-godzinnego widoków
-   Usługa PowerApps i Microsoft Flow: 1-dniowych warsztatów
-   Usługi Azure Machine Learning Services: Weryfikacja koncepcji 3 tygodnie
-   Kostki, a następnie kliknij przycisk handlu detalicznego rozwiązania: 1-godzinnego widoków
-   Możesz używać własnych danych: Warsztaty 1 tydzień
-   Analiza w chmurze: 3-dniowych warsztatów
-   Usługa Power BI szkolenia: 3-dniowych warsztatów
-   Rozwiązanie do zarządzania sprzedaży: Implementacja 1 tydzień
-   Szybki Start CRM: 1-dniowych warsztatów
-   Dynamics 365 for Sales: 2-dniowych oceny

Po ukończeniu **oferują ustawienia** karty, możesz zapisać swoje zgłoszenie. Nazwa oferty będzie teraz wyświetlany powyżej edytora, a znajdziesz w oferuje wszystkie.

### <a name="enter-storefront-details"></a>Wprowadź szczegóły sklepu

Następnie należy wprowadzić szczegóły dla witryny Sklepu. Szczegóły sklepu składa się z następujących sekcji:

-   Szczegóły oferty
-   Informacje o wydawcy
-   Szczegółowe informacje dotyczące listy
-   Marketing artefaktów

#### <a name="offer-details"></a>Szczegóły oferty

Sekcja szczegóły oferty zawiera następujące pola:

-   Podsumowanie oferty
-   Opis oferty

##### <a name="offer-summary"></a>Podsumowanie oferty

Podsumowanie oferty jest krótki opis oferty, zostanie wyświetlony tuż poniżej Nazwa oferty. Użyj zwykłego tekstu podczas wprowadzania Podsumowanie oferty i nie powinna mieć podziały wierszy. Poniżej przedstawiono dobre przykłady podsumowania oferty wraz z odpowiadającymi im nazwami oferty:

*Przykład 1*

-   **Nazwa oferty:** Analiza w chmurze: 3-dniowych warsztatów
-   **Podsumowanie oferty:** Omówienie programu Microsoft Azure i usługi Power BI, ocenę bieżącego środowiska i mini weryfikacji Koncepcji.

*Przykład 2*

-   **Nazwa oferty:** Przemysłowego Internetu rzeczy platformy Azure: 30-dniowej weryfikacji koncepcji
-   **Podsumowanie oferty:** Utwórz pilotażu przemysłowych połączonych produktów do bezpiecznego łączenia urządzeń, w polu do rozwiązania Azure IoT Hub za pomocą pulpitów nawigacyjnych, raportów i powiadomień.

*Przykład 3*

-   **Nazwa oferty:** Profesjonalne usługi: 1-godzinnego widoków
-   **Podsumowanie oferty:** Przegląd i pokaz wstępnie skonfigurowane, rozszerzone Dynamics 365 dla rozwiązania Operations, zapewniając rozszerzone Zarządzanie projektami, rozliczeń i zasobami dla profesjonalnych usług.

*Przykład 4*

-   **Nazwa oferty:** Usługa Power BI w sieci World: Warsztaty 4 godz.
-   **Podsumowanie oferty:** Szybkie rozpoczynanie pracy z swój pierwszy pulpit nawigacyjny i poznać najlepsze rozwiązania. Dla uczniów i studentów do 12 należy przeprowadzić na miejscu.

*Przykład 5*

-   **Nazwa oferty:** Dynamics i projektów: Ocena 3 dni
-   **Podsumowanie oferty:** Zbieranie informacji o wymaganiach i oceny dla rozwiązania ERP i przeniesieniu jej przeznaczone dla profesjonalnych usług przedsiębiorstw i przedsiębiorstw oparte na projekt.

##### <a name="offer-description"></a>Opis oferty

Opis oferty usług doradczych. Opis dobra oferta obejmuje szczegółowymi informacjami na temat wyglądu asysty i to, co będzie end elementu dostarczanego klientowi. Wyraźnie powinny pomóc w klientów, zrozumienie, jakie korzyści.

Nie dołączaj łącza wiadomości e-mail lub numer telefonu do kontaktowania się w opisie oferty. Nastąpi kontakt ze mną przycisk z ofertą, który zostanie przekazany prowadzi do docelowego zarządzania potencjalny klient, który możesz zidentyfikować na ofertę.

Wprowadź opis oferty w formacie Markdown. Jeśli nie znasz języka znaczników Markdown lub formatowania dla kodu HTML, możesz przejrzeć zasobów na [witrynie docs](https://docs.microsoft.com/contribute/how-to-write-use-markdown).

Korzystanie z tych formatów pewność, że Twoja oferta ma maksymalną czytelność dla klientów.

Zachowaj krótki opis oferty i stosować limit znaków, jak jest użytkowników nie można odczytać długie teksty. Nadal masz opcję przekazania broszur marketingu, zestawienia i inne dokumenty, które opisują oferty więcej szczegółów.

Poniższy przykład pokazuje, oraz złożone opis oferty i nazwy powiązane i Podsumowanie:

**Nazwa oferty:** Analiza w chmurze: 3-dniowych warsztatów

**Podsumowanie oferty:** Omówienie programu Microsoft Azure i usługi Power BI, ocenę bieżącego środowiska i mini weryfikacji Koncepcji.

**Opis oferty:** Ten 3-dniowych warsztatów służy do liderów technicznych i biznesowych i są przechowywane na miejscu we klienta.

***Plan***

1 dzień

-   Koncentruje się na temat sposobu zabezpieczenia, skalowanie i organizowanie danych w chmurze firmy Microsoft przy użyciu usługi Azure Data Lake, HDInsight i Azure SQL Data Warehouse.

2 dni

-   Omówiono konfigurowanie i wdrażanie zaawansowanych rozwiązań analizy przy użyciu Microsoft R i Azure Machine Learning.

3 dni

-   W tym temacie omówiono narysować podejmowania określonych działań analizy i analizy przy użyciu usługi Power BI, w tym współpracy sesji wspólnie utworzyć pulpit nawigacyjny usługi Power BI.

Koniec warsztatów klient będzie do definiowania ogólnego planu i plan wdrożenia dla rozwiązań danych i analiz w chmurze firmy Microsoft.

*Przykładowy plik języka znaczników Markdown dla oferty zgodnie z następującym formacie:*

    This 3-day workshop is for technical and business leaders and is held on-site at the client’s facility.

      ### Agenda

      **Day 1**

      * Focuses on how to secure, scale, and organize data within the Microsoft cloud using Azure Data Lake, HDInsight, or Azure SQL Data Warehouse

      **Day 2**

      * Covers how to configure and deploy advanced analytics solutions with Microsoft R and Azure Machine Learning

      **Day 3**

      * Covers how to draw actionable insights and operationalize analytics with Power BI, including a collaborative session to co-build a Power BI dashboard.


      ### Deliverables
      By the end of the workshop, the client will be able to define a high-level plan and an implementation roadmap for data and analytics solutions in the Microsoft cloud.


#### <a name="publisher-information"></a>**Informacje o wydawcy**

**IDENTYFIKATOR MPN**

Swój identyfikator 9 cyfr Microsoft Partner Network (MPN). Jeśli nie masz identyfikator MPN, możesz wykorzystać Center partnera firmy Microsoft, aby nabyć jeden.

**Identyfikator Centrum partnerskiego**

Nowe Centrum identyfikator partnera, jeśli nie masz.

**IDENTYFIKATOR MPN**

Wprowadź klucz tajny w celu wyświetlenia podglądu oferty w usłudze AppSource przed przejściem na żywo.
Ten identyfikator nie jest hasłem.

#### <a name="listing-details"></a>Szczegółowe informacje dotyczące listy

**Typ usługi doradcze**

Firma Microsoft jest ukierunkowywanie wyłącznie na zakres stały, stały czas trwania, cena Szacowana lub stałą (lub bezpłatnej) i przede wszystkim wstępnej-sales zorientowane na konsultacji oferty usług dla jednego klienta i oceny, implementacja, weryfikacja koncepcji, widoków, lub oferty warsztaty prowadzone albo na miejscu lub wirtualnie (sprawozdaniach musi być przeprowadzone u klienta).

Uwzględnione są następujące pięć typów ofert:

-   **Ocena:** Ocena środowiska klienta, aby określić możliwości zastosowania rozwiązania i Oszacuj koszt i czas.
-   **Widoków:** Wprowadzenie do rozwiązania lub usługa doradcza do rysowania odsetek klientów przy użyciu struktur, demonstracje i przykłady klientów. Sprawozdaniach musi być przeprowadzone u klienta.
-   **Implementacja:** Pełna instalacja wyniki w pełni działającego rozwiązania. Ten program pilotażowy firma Microsoft zaleca, ograniczenie do rozwiązania, które mogą być implementowane w tygodniu lub mniej.
-   **Weryfikacja koncepcji:** Implementacja ograniczonym zakresie, aby określić, jeśli to rozwiązanie będzie spełniać wymagania klientów.
-   **Warsztaty:** Interaktywne konsultacje przeprowadzone lokalnie klienta, które mogą zawierać sesji szkoleniowych, sprawozdaniach, ocen i pokazy oparta na danych lub środowisko klienta.

**Dostępność kraj/region**

Wybierz kraj i region, w której dostępna będzie oferta usługi doradcze. Nie można opublikować jednej oferty w wielu krajach lub regionach. Nowa oferta musi zostać utworzony dla każdego kraju lub regionu.

**Branże**

Wybierz branż, oferujących usługi konsultingowe dotyczy najbardziej.

**Czas trwania**

Wybierz liczbę (na przykład, 3, 4, itd.) w obszarze czas trwania i następnie wybierz godziny, dnia lub tygodnia.

**Podstawowy produktów**

Aby opublikować w portalu Azure Marketplace, wybierz **Azure** jako podstawowy produkt, a następnie wybierz odpowiednie obszary rozwiązania.

Aby opublikować w usłudze AppSource, wybierz **Dynamics 365**, **usługi Power BI**, lub **PowerApps**jako podstawowy produkt. Możesz również wybrać inne odpowiednich produktów mających zastosowanie, a usługi konsultingowe oferty, wyświetli się na listach zawartości, które są skojarzone z każdą z tych produktów w usłudze AppSource.

**Odpowiednie umiejętności**

Wybierz kompetencje istotne dla tej oferty, aby były wyświetlane wraz ze szczegółami oferty.

#### <a name="marketing-artifacts"></a>Marketing artefaktów

**Logo firmy (format PNG, 48 x 48 pikseli)**

Przekaż obraz, który będzie wyświetlany na kafelku oferty na stronie widok galerii oferty. Obraz musi być obrazem PNG z dokładnością do pikseli 48 x 48.

**Logo firmy logo (format PNG, 216 x 216 pikseli) firmy**

Przekaż obraz, który będzie wyświetlany na stronie szczegółów oferty. Obraz musi być obrazem PNG z dokładnością do pikseli 216 x 216.

**Filmy wideo (maks. 4)**

Przekazywać do czterech filmy wideo z analizą przypadku firmy klienta lub klientów odwołanie wideo. Jeśli nie masz żadnych, Przekaż wideo wyjaśniających firmy doświadczenie związane z tej oferty. Jeśli masz pokaz rozwiązań usługi Power BI lub usłudze PowerApps, należy przekazać pokaz wideo w tym miejscu. Linki wideo musi być w usłudze YouTube lub Vimeo.

**Dokumenty (maks. 3)**

Przekaż broszura marketingu, opisujący ofertą usługi konsultingowe szczegółowo. Alternatywnie możesz również przekazać charakterystyki firmy, arkusze faktów lub przypadków.

**Zrzuty ekranu (maks. 5)**

Przekaż do pięciu obrazów, które zawierają więcej informacji dotyczących tej oferty, cele do zrealizowania oferty lub Twojej firmy. Na przykład fragment broszura marketingu, odpowiednie slajd z prezentacji lub obraz przedstawiający pęd firmy lub specjalizacji.

### <a name="publish-your-offer"></a>Publikowanie oferty

Po zakończeniu oferty ustawienia, szczegóły sklepu i kontakty wybierz **Publikuj** i podaj adres e-mail. Firma Microsoft jest gotowa do opublikowania oferty, otrzymasz wiadomość e-mail, aby wyświetlić jego podgląd, zanim usługa zostanie wprowadzona na żywo. Możesz wrócić do portalu, aby sprawdzić stan oferty w dowolnym momencie w czasie.

Oferty może pojawić się w "Publikuj anulowane" lub "Publikowania nie powiodło się." stan w procesie publikowania. Ten stan jest normalna część procesu i umożliwia firmie Microsoft i wprowadź zmiany do swojej oferty. Jeśli widzisz oferty "Publikowanie anulowane" pozostawić ją w tym stanie.
