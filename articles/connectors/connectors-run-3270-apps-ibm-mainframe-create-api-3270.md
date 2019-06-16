---
title: Połącz aplikacje 3270 na komputery mainframe firmy IBM na platformie Azure — Azure Logic Apps
description: Integrowanie oraz Automatyzowanie 3270 oparte na ekranie aplikacje na platformie Azure za pomocą łącznika usługi Azure Logic Apps i IBM 3270
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 7388dc0c61dad9c31da0c178febcee4c8481bc50
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60538912"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integrowanie 3270 aplikacje oparte na ekranie, na komputery mainframe firmy IBM z platformą Azure za pomocą łącznika usługi Azure Logic Apps i IBM 3270

> [!NOTE]
> Ten łącznik jest w [ *publicznej wersji zapoznawczej*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Za pomocą usługi Azure Logic Apps i łącznik IBM 3270 można uzyskać dostęp i uruchamiać aplikacje mainframe firmy IBM, które zazwyczaj dysku, przechodząc przez 3270 ekrany emulatora. W ten sposób możesz zintegrować swoje aplikacje mainframe firmy IBM z platformy Azure, Microsoft, a inne aplikacje, usługami i systemami przez tworzenie automatycznych przepływów pracy z usługą Azure Logic Apps. Łącznik komunikuje się z Komputery mainframe firmy IBM, za pomocą protokołu TN3270 i jest dostępna we wszystkich regionach usługi Azure Logic Apps, z wyjątkiem platformy Azure Government i platformy Azure China 21Vianet. Jeśli dopiero zaczynasz pracę z usługi logic apps, zapoznaj się z [co to jest Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

W tym artykule opisano kwestie dotyczące korzystania z łącznika 3270: 

* Dlaczego warto korzystać z łącznika IBM 3270 w usłudze Azure Logic Apps i jak łącznik nie zostanie uruchomiony 3270 aplikacje oparte na ekranie

* Wymagania wstępne i konfiguracji na potrzeby przy użyciu łącznika 3270

* Procedura dodawania 3270 akcji łącznika do aplikacji logiki

## <a name="why-use-this-connector"></a>Do czego służy ten łącznik?

Aby uzyskać dostęp do aplikacji na komputery mainframe firmy IBM, używa się zazwyczaj 3270 emulatora terminala, często nazywane "zielony ekran". Ta metoda jest metodą sprawdzona, ale ma ograniczenia. Mimo że Host Integration Server (HIS) pomaga pracować bezpośrednio z tych aplikacji, czasami oddzielenie logiki ekranu i biznesowych może nie być możliwe. Lub, być może masz już informacje dotyczące sposobu działania aplikacji hosta.

Aby rozszerzyć tych scenariuszy, łącznik IBM 3270 w usłudze Azure Logic Apps współpracuje z narzędzia projektowania 3270, które umożliwia rekord lub "Przechwytywanie" ekrany hosta, używane do wykonania określonego zadania, określają przepływ nawigacji dla tego zadania za pośrednictwem aplikacji mainframe i zdefiniować metody z parametrami wejściowymi i wyjściowymi tego zadania. Narzędzia do projektowania konwertuje te informacje do metadanych, który łącznika 3270 używa podczas wywoływania akcji, która reprezentuje zadania z aplikacji logiki.

Po wygenerowaniu pliku metadanych z narzędzia do projektowania, Dodaj ten plik do konta integracji w systemie Azure. W ten sposób Twoja aplikacja logiki dostęp można uzyskać metadanych aplikacji po dodaniu akcji łącznika 3270. Łącznik odczytuje plik metadanych z konta integracji obsługuje nawigacji między ekranami 3270 i dynamicznie przedstawia parametry akcji łącznika 3270. Możesz także podać dane do aplikacji hosta, a łącznik zwraca wyniki do aplikacji logiki. W ten sposób można zintegrować starszych aplikacji z platformy Azure, Microsoft i innych aplikacji, usług i systemów, które obsługuje usługi Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, <a href="https://azure.microsoft.com/free/" target="_blank">zarejestruj się w celu założenia bezpłatnego konta platformy Azure</a>.

* Podstawową wiedzę na temat o [sposób tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Zalecane: [Środowiska integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  Możesz wybrać tego środowiska, jako lokalizację do tworzenia i uruchamiania aplikacji logiki. ISE zapewnia dostęp do zasobów, które są chronione wewnątrz sieci wirtualnej platformy Azure z aplikacji logiki.

* Aplikacja logiki służące do automatyzacji i uruchamiania aplikacji opartych na ekranie 3270

  Łącznik IBM 3270 nie może mieć wyzwalaczy, więc Użyj kolejnego wyzwalacza, aby uruchomić aplikację logiki, takich jak **cyklu** wyzwalacza. Następnie można dodać 3270 akcji łącznika. Aby rozpocząć pracę, [Tworzenie pustej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Jeśli używasz środowiska ISE, wybierz ten ISE jako lokalizacja aplikacji logiki.

* [Pobierz i zainstaluj narzędzie do projektowania 3270](https://aka.ms/3270-design-tool-download).
Jest jedynym wymaganiem wstępnym koniecznym [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  Narzędzie to pomaga zarejestrować ekranów, ścieżki nawigacji, metod i parametry dla zadań w swojej aplikacji, dodać i Uruchom jako 3270 akcji łącznika. Narzędzie generuje plik XML elementu (Host Integration projektanta HIDX), który zawiera metadane potrzebne dla łącznika usługi służące do obsługi aplikacji mainframe.
  
  Po pobraniu i zainstalowaniu tego narzędzia, wykonaj następujące kroki, do łączenia się z hostem:

  1. Otwórz narzędzie do projektowania 3270. Z **sesji** menu, wybierz opcję **hosta sesji**.
  
  1. Podaj hosta TN3270 informacje o serwerze.

* [Konta integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), która jest miejscem, w którym przechowywania pliku elementu HIDX jako mapę aby Twoja aplikacja logiki może uzyskiwać dostęp do definicji metadanych i metody, w tym pliku. 

  Upewnij się, że Twoje konto integracji jest połączone z aplikacją logiki, którego używasz. Ponadto jeśli używasz środowiska ISE, upewnij się, że lokalizacja konta integracji jest ten sam ISE, który korzysta z aplikacji logiki.

* Dostęp do serwera TN3270, która jest hostem aplikacji mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Utwórz Przegląd metadanych

W 3270 aplikacji opartej na ekranie ekranów i pola danych są unikatowe dla Twoich scenariuszy, więc łącznika 3270 musi te informacje o aplikacji, co może zapewnić jako metadane. Te metadane opisują informacje, które pomaga zidentyfikować i rozpoznać ekrany aplikacji logiki, w tym artykule opisano sposób przechodzenia między ekranami, gdzie dane wejściowe i gdzie można oczekiwać, wyniki. Aby określić i Generowanie metadanych, użyj narzędzia projektowania 3270 przeprowadzi Cię przez te, które są określone *tryby*, lub etapach, zgodnie z opisem w dalszej części więcej szczegółów:

* **Przechwytywanie**: W tym trybie rejestrowania ekrany wymagane do wykonania określonego zadania za pomocą aplikacji mainframe, na przykład wprowadzenie saldo konta bankowego.

* **Nawigacja**: W tym trybie określ plan lub ścieżka, w nawigowaniu ekrany aplikacji mainframe do określonego zadania.

* **metody**: W tym trybie można zdefiniować metody, na przykład `GetBalance`, opisujący ścieżki nawigacji ekranu. Możesz również wybrać pola na każdym ekranie, które stają się metody w danych wejściowych i wyjściowych parametrów.

### <a name="unsupported-elements"></a>Nieobsługiwane elementy

Narzędzie do projektowania nie obsługuje tych elementów:

* Mapuje częściowe IBM podstawowe mapowanie obsługi (MS): Po zaimportowaniu mapy MS narzędzia do projektowania ignoruje definicje częściowe ekranu.
* / Ściemnianie parametry: Nie można zdefiniować parametry wchodzącym/wychodzącym.
* Przetwarzanie menu: Nie jest obsługiwana w wersji zapoznawczej
* Przetwarzanie tablicy: Nie jest obsługiwana w wersji zapoznawczej

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Przechwytywanie ekranów

W tym trybie można oznaczyć elementu na każdym ekranie 3270, który unikatowo identyfikuje ten ekran. Na przykład można określić wiersza tekstu lub bardziej złożonego zestawu warunków, takich jak określony tekst i pola niepusta. Możesz zarejestrować te ekrany za pośrednictwem połączenia na żywo na hoście lub zaimportować te informacje z mapą IBM podstawowe mapowanie obsługi (MS). Połączenia na żywo używa emulatora TN3270 do łączenia się z hosta. Każde działanie łącznika musi być mapowane do pojedynczego zadania, które zaczyna się od łączenia z sesji środowiska i kończy się rozłączanie sesji.

1. Jeśli jeszcze nie, Otwórz narzędzie do projektowania 3270. Na pasku narzędzi wybierz **przechwytywania** tak, aby wprowadzić tryb przechwytywania.

1. Aby rozpocząć rejestrowanie, naciśnij klawisz F5 lub **rejestrowania** menu, wybierz opcję **Rozpocznij nagrywanie**. 

1. Z **sesji** menu, wybierz opcję **Connect**.

1. W **przechwytywania** okienko, zaczynając od pierwszego ekranu w aplikacji, krok po kroku dla określonego zadania, które rejestrowania aplikacji.

1. Po zakończeniu zadania, wyloguj się z aplikacji w zwykły sposób.

1. Z **sesji** menu, wybierz opcję **rozłączenia**.

1. Aby zatrzymać nagrywanie, naciśnij klawisze Shift + F5 kluczy, lub z **rejestrowanie** menu, wybierz opcję **Zatrzymaj rejestrowanie**.

   Po przechwyceniu ekrany dla zadania podrzędnego, narzędzie Projektant wyświetlane są miniatury, które reprezentują te ekrany. Kilka uwag dotyczących tych miniatur:

   * Dołączone do Twojej ekrany przechwycone, masz ekranu o nazwie "Pusty".

     W przypadku pierwszego podłączenia do [CICS](https://www.ibm.com/it-infrastructure/z/cics), można wprowadzić nazwę transakcji, należy uruchomić, musisz wysłać klucz "Clear". Ekran, gdzie wysłać klucz "Clear" nie ma żadnych *atrybuty rozpoznawania*, takie jak tytuł ekranu, które można dodać za pomocą edytora rozpoznawania ekranu. Do reprezentowania ten ekran, miniatury obejmuje ekran o nazwie "Pusty". Ten ekran mógł później użyć do reprezentowania na ekranie, w którym wprowadzasz nazwę transakcji.

   * Domyślnie nazwa przechwycony ekran używa pierwszy wyraz na ekranie. Jeśli takiej nazwie już istnieje, narzędzia do projektowania dołącza nazwę z znakiem podkreślenia i numeru, na przykład "WBGB" i "WBGB_1".

1. Aby zapewnić bardziej opisową nazwę z przechwycone ekranem, wykonaj następujące kroki:

   1. W **ekrany hosta** okienku wybierz ekran, o których chcesz zmienić.

   1. W tym samym okienku w dolnej części, w tym samym okienku Znajdź **nazwę ekranową** właściwości.

   1. Zmień bieżącą nazwę ekranu na bardziej opisową nazwę.

1. Teraz Określ pola do identyfikowania poszczególnych ekranach.

   Ze strumieniem danych 3270 ekrany nie mają domyślne identyfikatory, więc musisz wybrać unikatowy tekst na każdym ekranie. W przypadku złożonych scenariuszy można określić wiele warunków, na przykład unikatowy tekst i pola z określonego warunku.

Po wybraniu pola rozpoznawania Przenieś do następnego trybu.

### <a name="conditions-for-identifying-repeated-screens"></a>Warunki do identyfikowania powtarzanych ekranów

Łącznik do nawigowania po rozróżnienia między ekranami zwykle znaleźć unikatowy tekst na ekranie, który służy jako identyfikator między ekrany przechwycone. Dla ekranów powtarzane możesz potrzebować więcej metod identyfikacji. Na przykład załóżmy, że masz dwa ekrany, które wyglądają tak samo, z wyjątkiem jednego ekranu zwraca prawidłową wartość, a drugim ekranie zwraca komunikat o błędzie.

Narzędzie do projektowania, można dodać *atrybuty rozpoznawania*, na przykład tytuł ekranu takich jak "Pobierz saldo konta", przy użyciu edytora rozpoznawania ekranu. Jeśli masz rozwidlone ścieżki i obu gałęzi Zwróć tym samym ekranie, ale z różne wyniki, należy się inne atrybuty rozpoznawania. W czasie wykonywania łącznik używa tych atrybutów do określania bieżącej gałęzi i rozwidlenia. Poniżej przedstawiono warunki, których można użyć:

* Określona wartość: Ta wartość jest zgodna z określonego ciągu w określonej lokalizacji.
* Określona wartość: Ta wartość nie jest zgodna określonego ciągu w określonej lokalizacji.
* Pusta: To pole jest puste.
* NIE jest pusty: To pole nie jest pusta.

Aby dowiedzieć się więcej, zobacz [planu nawigacji przykład](#example-plan) w dalszej części tego tematu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Zdefiniuj plany nawigacji

W tym trybie zdefiniuj przepływ lub kroki do przechodzenia między ekrany aplikacji mainframe określonego zadania. Na przykład czasami może masz więcej niż jedną ścieżkę, który aplikacja może potrwać, gdzie jedną ścieżkę tworzy odpowiedni wynik, gdy generuje błąd, innej ścieżki. Dla każdego ekranu, należy określić naciśnięć klawiszy potrzeby dotyczące przechodzenia na następnym ekranie, takich jak `CICSPROD <enter>`.

> [!TIP]
> Jeśli automatyzujesz kilka zadań, użyj tego samego i Odłącz ekranów, narzędzia do projektowania zawiera specjalne typy planów Connect i Rozłącz. Podczas definiowania tych planów, możesz dodać je do początku i końcu planu nawigacji.

### <a name="guidelines-for-plan-definitions"></a>Wytyczne dotyczące definicji planu

* Obejmują wszystkie ekrany, począwszy od łączenia i kończąc odłączanie.

* Można utworzyć plan autonomiczny lub użyć Połącz i Odłącz plany, które umożliwiają ponowne użycie serii ekranów, które są wspólne dla wszystkich transakcji.

  * Ostatni ekran w planie Connect musi być tym samym ekranie jako pierwszy ekran w planie nawigacji.

  * Pierwszy ekran w planie rozłączenia musi być tym samym ekranie co ostatni ekran w planie nawigacji.

* Twoje ekrany przechwycone może zawiera wiele ekranów powtarzanych, więc wybierz i użyj tylko jedno wystąpienie powtarzanych ekranów w planie. Poniżej przedstawiono kilka przykładów powtarzanych ekrany:

  * Logowanie na ekranie, na przykład **MSG 10** ekranu
  * Ekran powitalny dla CICS
  * "Wyczyść" lub **pusty** ekranu

<a name="create-plans"></a>

### <a name="create-plans"></a>Tworzenie planów

1. Na pasku narzędzi Narzędzia projektowania 3270 wybierz **nawigacji** tak, aby wprowadzić tryb nawigacji.

1. Aby uruchomić w planie, **nawigacji** okienku wybierz **nowy Plan**.

1. W obszarze **wybierz nową nazwę planu**, wprowadź nazwę dla planu. Z **typu** , wybierz typ planu na liście:

   | Typ planu | Opis |
   |-----------|-------------|
   | **Proces** | Autonomiczny lub połączone plany |
   | **Połącz** | W przypadku planów Connect |
   | **Rozłącz** | W przypadku planów rozłączenia |
   |||

1. Z **ekrany hosta** okienko, przeciągnij przechwycone na wyświetlanie miniatur przez plan nawigacji powierzchni w **nawigacji** okienka.

   Do reprezentowania pusty ekran, na którym należy wprowadzić nazwę transakcji, użyj "Puste" ekranu.

1. Rozmieść ekranów w kolejności, w tym artykule opisano zadania, które definiujesz.

1. Aby zdefiniować ścieżkę przepływ między ekranami, łącznie z rozwidlenia i sprzężenia, na pasku narzędzi Narzędzia do projektowania, wybierz opcję **przepływ**.

1. Wybierz pierwszy ekran w przepływie. Przeciągnij i narysuj połączenia do następnego ekranu w przepływie.

1. Dla każdego ekranu, podaj wartości dla **klucz pomocy** właściwości (identyfikator uwagi) i **stałego tekstu** właściwość, która przenosi przepływ na następnym ekranie.

   Może mieć tylko klawisz Pomocy lub zarówno klucz pomocy i stałego tekstu.

Po zakończeniu wprowadzania zmian planu nawigacji, można [definiowania metod w trybie dalej](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Przykład

W tym przykładzie załóżmy, że uruchomieniu transakcji CICS o nazwie "WBGB", który ma następujące kroki: 

* Na pierwszym ekranie wprowadź nazwę i konta.
* Na drugim ekranie otrzymasz salda konta.
* Możesz wyjść do ekranu "Pusty".
* Możesz wylogować się z CICS do ekranu "MSG-10".

Załóżmy również, powtórz te kroki, ale możesz wprowadzić niepoprawne dane, więc istnieje możliwość przechwytywania ekranu, na którym jest wyświetlany błąd. Poniżej przedstawiono ekrany, które przechwytywania:

* MSG-10
* CICS — Zapraszamy!
* Pusty
* WBGB_1 (dane wejściowe)
* WBGB_2 (błąd)
* Empty_1
* MSG-10_1

Chociaż wiele ekranów w tym miejscu uzyskać unikatowe nazwy, niektóre ekrany są tym samym ekranie, na przykład "MSG-10" i "Empty". Dla powtarzanych ekranu należy użyć tylko jedno wystąpienie tego ekranu w planie. Poniżej przedstawiono przykłady pokazujące, jak może wyglądać plan autonomiczny, Połącz plan, Rozłącz planu i połączone planu:

* Plan autonomiczny

  ![Plan nawigacji autonomiczny](./media/connectors-create-api-3270/standalone-plan.png)

* Połącz planu

  ![Połącz planu](./media/connectors-create-api-3270/connect-plan.png)

* Odłącz planu

  ![Odłącz planu](./media/connectors-create-api-3270/disconnect-plan.png)

* Połączone planu

  ![Połączone planu](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Przykład: Identyfikowanie powtarzanych ekranów

Łącznik do nawigowania i odróżnienia ekranów zwykle znaleźć unikatowy tekst na ekranie, który służy jako identyfikator na ekranach przechwycone. Dla ekranów powtarzane możesz potrzebować więcej metod identyfikacji. Plan przykład ma rozwidlenie, w którym uzyskujesz ekrany, które będą przypominać. Jeden ekran zwraca saldo konta, a drugim ekranie zwraca komunikat o błędzie.

Narzędzie do projektowania pozwala na dodawanie atrybutów rozpoznawania, na przykład tytuł ekranu o nazwie "Pobierz saldo konta", przy użyciu rozpoznawania ekranu edytora. W przypadku podobnych ekranów potrzebne są inne atrybuty. W czasie wykonywania łącznik używa tych atrybutów do określania gałęzi i rozwidlenia.

* W gałęzi, która zwraca prawidłowych danych wejściowych, czyli ekranu z saldem konta, można dodać pola, które zawiera warunek "nie jest pusty".

* W gałęzi, która zwraca błąd można dodać pola, które zawiera warunek "pusty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definiowanie metody

W tym trybie możesz zdefiniować metodę, która jest skojarzona z planem nawigacji. Dla każdego parametru metody, określ typ danych, takich jak ciąg, liczba całkowita, daty lub godziny i tak dalej. Gdy wszystko będzie gotowe, możesz przetestować metodę na hoście, na żywo i upewnij się, że metoda działa zgodnie z oczekiwaniami. Następnie Wygeneruj plik metadanych lub plik XML elementu (Host Integration projektanta HIDX), który ma teraz definicje metody służące do tworzenia i uruchamiania akcję dla łącznika IBM 3270.

1. Na pasku narzędzi Narzędzia projektowania 3270 wybierz **metody** tak, aby tryb metody. 

1. W **nawigacji** okienku wybierz ekran, który ma pola wejściowe, które chcesz.

1. Aby dodać pierwszy parametr wejściowy dla wybranej metody, wykonaj następujące kroki:

   1. W **przechwytywania** okienku na ekranie emulatora 3270 wybierz całe pole, nie tylko tekst wewnątrz pola mają jako pierwszy element danych wejściowych.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i upewnij się, zaznaczyć pole Wykonano na **widoku** menu, wybierz opcję **wszystkie pola**.

   1. Na pasku narzędzi Narzędzia do projektowania, wybierz **pola danych wejściowych**. 

   Aby dodać więcej parametrów wejściowych, powtórz poprzednie kroki dla każdego parametru.

1. Aby dodać pierwszy parametr danych wyjściowych dla wybranej metody, wykonaj następujące kroki:

   1. W **przechwytywania** okienku na ekranie emulatora 3270 wybierz całe pole, nie tylko tekst wewnątrz pola mają jako pierwszy dane wyjściowe.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i upewnij się, zaznaczyć pole Wykonano na **widoku** menu, wybierz opcję **wszystkie pola**.

   1. Na pasku narzędzi Narzędzia do projektowania, wybierz **danych wyjściowych**.

   Aby dodać większą liczbę parametrów danych wyjściowych, powtórz poprzednie kroki dla każdego parametru.

1. Po dodaniu parametry wszystkie swoje metody, należy zdefiniować następujące właściwości dla każdego parametru:

   | Nazwa właściwości | Możliwe wartości | 
   |---------------|-----------------|
   | **Typ danych** | Byte, Data i godzina, Decimal, Int, Long, w skrócie ciągu |
   | **Technika wypełnienie pola** | Parametry obsługi tych typów wypełnienia wypełnianie wartości puste, jeśli to konieczne: <p><p>- **Typ**: Wprowadź znaki sekwencyjnie w polu. <p>- **Wypełnij**: Zastąp zawartość tego pola znakami wypełnianie wartości puste, jeśli to konieczne. <p>- **EraseEofType**: Usuń zaznaczenie pola, a następnie w polu wprowadź kolejno znaków. |
   | **Ciąg formatu** | Niektóre typy danych parametrów użyj ciąg formatu, który informuje 3270 łącznika sposób konwertowania tekstu z ekranu na typ danych .NET: <p><p>- **Data i godzina**: Ciąg formatu daty i godziny następuje [.NET niestandardowa data i godzina ciągi formatujące](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Na przykład data `06/30/2019` używa ciągu formatu `MM/dd/yyyy`. <p>- **Dziesiętna**: Ciąg formatu dziesiętnego używa [klauzuli obraz COBOL](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Na przykład numer `100.35` używa ciągu formatu `999V99`. |
   |||

## <a name="save-and-view-metadata"></a>Zapisz i wyświetl metadane

Po zdefiniowaniu metoda, ale przed testowaniem metodę, należy zapisać wszystkie informacje, które do tej pory zdefiniowane w pliku RAP (.rap).
Do tego pliku RSZY można zapisać w dowolnym momencie w dowolnym trybie. Narzędzie do projektowania zawiera również przykładowy plik RSZY, który można otworzyć i przejrzeć, przechodząc do folderu instalacji narzędzia do projektowania, w tym miejscu i otwierania pliku "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Jednak Jeśli spróbujesz zapisywaniu zmian do przykładowego RSZY pliku lub generowanie pliku elementu HIDX z przykładowego RSZY pliku, gdy plik pozostanie w folderze instalacji narzędzia do projektowania, możesz otrzymać błąd "odmowa dostępu". Domyślnie narzędzie do projektowania jest instalowana w folderze Program Files bez podniesionego poziomu uprawnień. Jeśli wystąpi błąd, spróbuj wykonać jedną z tych rozwiązań:

* Skopiuj przykładowy plik do innej lokalizacji.
* Uruchom narzędzie do projektowania, jako administrator.
* Upewnij się tylko właściciel folderu zestawu SDK.

## <a name="test-your-method"></a>Test usługi — metoda

1. Uruchamiać metodę hosta na żywo, mając nadal w trybie metod, naciśnij klawisz F5 lub z narzędzia do projektowania, narzędzi, wybierz **Uruchom**.

   > [!TIP]
   > Tryby można zmienić w dowolnym momencie. Na **pliku** menu, wybierz opcję **tryb**, a następnie wybierz tryb ma.

1. Wprowadź wartości z parametrów, a następnie wybierz **OK**.

1. Aby kontynuować do następnego ekranu, wybierz **dalej**.

1. Gdy skończysz, wybierz pozycję **gotowe**, które wyświetla wartości parametrów są dane wyjściowe.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generowanie i przekazywanie pliku elementu HIDX

Gdy wszystko będzie gotowe, Wygeneruj plik elementu HIDX, dzięki czemu możesz przekazać na koncie integracji. Narzędzie do projektowania 3270 tworzy plik elementu HIDX w nowy podfolder, w której zapisano plik RAP.

1. Na pasku narzędzi Narzędzia projektowania 3270 wybierz **Generuj kod**.

1. Przejdź do folderu, który zawiera plik RSZY i otwórz utworzony przez narzędzie po wygenerowaniu pliku elementu HIDX podfolderu. Upewnij się, że narzędzie utworzony plik elementu HIDX.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Znajdź swoje konto integracji.

1. Dodać pliku elementu HIDX jako mapowanie na koncie integracji przez [wykonaj następujące kroki podobne do dodawania mapy](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md), ale po wybraniu typu mapy, wybrać **elementu HIDX**.

W dalszej części tego tematu podczas dodawania akcji IBM 3270 do aplikacji logiki po raz pierwszy, zostanie wyświetlony monit utworzyć połączenie między aplikacją logiki i serwer hosta, podając informacje o połączeniu, takich jak nazwy konta i hosta serwera integracji . Po utworzeniu połączenia, można wybrać poprzednio dodanego pliku elementu HIDX, metodę do uruchomienia i parametry do użycia.

Po zakończeniu wszystkie te kroki, można użyć akcji, którą utworzysz w aplikacji logiki do łączenia się z Twojego komputera mainframe firmy IBM dysku ekrany dla aplikacji, wprowadź dane, zwracają wyniki i tak dalej. Nadal m ożna również dodawanie innych akcji do aplikacji logiki w celu integracji z innych aplikacji, usługami i systemami.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Uruchom akcje IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i Otwórz swoją aplikację logiki w Projektancie aplikacji logiki, jeśli nie otwarto już.

1. W ostatnim kroku, której chcesz dodać akcję, wybierz **nowy krok**i wybierz **Dodaj akcję**. 

1. W polu wyszukiwania, wybierz **Enterprise**. W polu wyszukiwania wprowadź "3270" jako filtr. Z listy akcji wybierz następującą akcję: **Uruchamia mainframe program za pośrednictwem połączenia TN3270**

   ![Wybierz akcję 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Aby dodać akcję między krokami, wskaźnik myszy nad strzałką znajdującą się między krokami. 
   Wybierz znak plus ( **+** ) pojawia się, a następnie wybierz **Dodaj akcję**.

1. Jeśli jeszcze nie istnieją żadne połączenia, podaj niezbędne informacje dotyczące połączenia i wybierz polecenie **Utwórz**.

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Yes | <*connection-name*> | Nazwa połączenia |
   | **Identyfikator konta integracji** | Tak | <*integration-account-name*> | Nazwa konta integracji |
   | **Adres URL sygnatury dostępu Współdzielonego konta integracji** | Tak | <*integration-account-SAS-URL*> | Konta integracji sygnatury dostępu współdzielonego (SAS) adresu URL, który można wygenerować z ustawień konta integracji w witrynie Azure portal. <p>1. Na integracji usługi menu konta, w obszarze **ustawienia**, wybierz opcję **adresów URL wywołania zwrotnego**. <br>2. W okienku po prawej stronie Skopiuj **wygenerowany adres URL wywołania zwrotnego** wartość. |
   | **Serwer** | Yes | <*Nazwy serwera TN3270*> | Nazwa serwera usługi TN3270 |
   | **Port** | Nie | <*Port w przypadku serwera TN3270*> | Port używany przez serwer TN3270. Jeśli pole pozostanie puste, łącznik używa `23` jako wartość domyślną. |
   | **Typ urządzenia** | Nie | <*IBM-terminal-model*> | Nazwa modelu lub numer terminalu IBM do emulacji. Jeśli pole pozostanie puste, łącznik używa wartości domyślnych. |
   | **Strona kodowa** | Nie | <*code-page-number*> | Numer strony kodowej dla hosta. Jeśli pole pozostanie puste, łącznik używa `37` jako wartość domyślną. |
   | **Nazwa jednostki logicznej** | Nie | <*logical-unit-name*> | Nazwa określoną jednostkę logiczną do żądania od hosta |
   | **Włączyć SSL?** | Nie | Włączanie/wyłączanie | Włącz lub wyłącz szyfrowanie SSL. |
   | **Sprawdź poprawność certyfikatu ssl hosta?** | Nie | Włączanie/wyłączanie | Włącz lub wyłącz funkcję sprawdzania poprawności dla certyfikatu serwera. |
   ||||

   Na przykład:

   ![Connection properties (Właściwości połączenia)](./media/connectors-create-api-3270/connection-properties.png)

1. Podaj wymagane informacje dotyczące akcji:

   | Właściwość | Wymagany | Value | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa elementu Hidx** | Tak | <*HIDX-file-name*> | Wybierz plik elementu HIDX 3270, którego chcesz używać. |
   | **Nazwa metody** | Yes | <*method-name*> | Wybierz metodę w pliku elementu HIDX, którego chcesz użyć. Po wybraniu metody **dodano nowy parametr** zostanie wyświetlona lista, dzięki czemu można wybrać parametry używane przez tę metodę. |
   ||||

   Na przykład:

   **Wybierz plik elementu HIDX**

   ![Wybierz plik elementu HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Wybierz metodę**

   ![Wybierz metodę](./media/connectors-create-api-3270/select-method.png)

   **Wybierz parametry**

   ![Wybierz parametry](./media/connectors-create-api-3270/add-parameters.png)

1. Gdy skończysz, Zapisz i uruchom aplikację logiki.

   Po logika aplikacji zakończy się uruchamianie z wykonywania pojawiają się procedury. 
   Pomyślne kroki pokazują znaczniki wyboru, a niepomyślnych kroki zawierają litery "X".

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, należy rozwinąć ten krok.

1. Aby przejrzeć dane wyjściowe, wybierz **Zobacz nieprzetworzone dane wyjściowe**.

## <a name="connector-reference"></a>Dokumentacja łączników

Szczegółowe informacje techniczne dotyczące wyzwalaczy, akcje i limit, który opisano przez łącznika interfejsu OpenAPI (dawniej Swagger) opis, przejrzyj [strona referencyjna łącznika](/connectors/si3270/).

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Kolejne kroki

* Dowiedz się więcej o innych [łączników Logic Apps](../connectors/apis-list.md)
