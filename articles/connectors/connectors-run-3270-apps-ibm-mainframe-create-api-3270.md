---
title: Łączenie się z aplikacjami 3270 na komputerach mainframe IBM
description: Integracja i automatyzacja aplikacji opartych na ekranie 3270 za pomocą platformy Azure Logic Apps i łącznika IBM 3270
services: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
ms.openlocfilehash: 808eef5424d678559ae94ffd04e41eacd0f16aee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371098"
---
# <a name="integrate-3270-screen-driven-apps-on-ibm-mainframes-with-azure-by-using-azure-logic-apps-and-ibm-3270-connector"></a>Integracja aplikacji opartych na ekranie 3270 na komputerach mainframe IBM z platformą Azure przy użyciu aplikacji Azure Logic Apps i łącznika IBM 3270

> [!NOTE]
> Ten łącznik znajduje się w [*publicznej wersji zapoznawczej*](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Dzięki usłudze Azure Logic Apps i łącznikowi IBM 3270 można uzyskiwać dostęp do aplikacji IBM mainframe i uruchamiać je, korzystając z ekranów emulatorów 3270. W ten sposób można zintegrować aplikacje IBM mainframe z platformą Azure, firmą Microsoft i innymi aplikacjami, usługami i systemami, tworząc zautomatyzowane przepływy pracy za pomocą usługi Azure Logic Apps. Łącznik komunikuje się z komputerami mainframe IBM przy użyciu protokołu TN3270 i jest dostępny we wszystkich regionach usługi Azure Logic Apps z wyjątkiem platformy Azure Dla Instytucji Rządowych i platformy Azure China 21Vianet. Jeśli jesteś nowy w aplikacjach logiki, sprawdź [Co to jest usługa Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

W tym artykule opisano te aspekty dotyczące korzystania z łącznika 3270: 

* Dlaczego warto korzystać ze łącznika IBM 3270 w usłudze Azure Logic Apps i jak łącznik działa w aplikacjach opartych na ekranie 3270

* Wymagania wstępne i konfiguracja korzystania ze złącza 3270

* Kroki dodawania akcji łącznika 3270 do aplikacji logiki

## <a name="why-use-this-connector"></a>Dlaczego warto korzystać z tego złącza?

Aby uzyskać dostęp do aplikacji na komputerach mainframe IBM, zazwyczaj używasz emulatora terminala 3270, często nazywanego "zielonym ekranem". Ta metoda jest sposób testowany czas, ale ma ograniczenia. Chociaż host integration server (HIS) pomaga pracować bezpośrednio z tymi aplikacjami, czasami oddzielenie ekranu i logiki biznesowej może nie być możliwe. Lub może nie masz już informacji o tym, jak działają aplikacje hosta.

Aby rozszerzyć te scenariusze, łącznik IBM 3270 w usłudze Azure Logic Apps współpracuje z narzędziem do projektowania 3270, które służy do rejestrowania lub "przechwytywania", ekranów hosta używanych do określonego zadania, definiowania przepływu nawigacji dla tego zadania za pośrednictwem aplikacji mainframe i definiowania z parametrami wejściowymi i wyjściowymi dla tego zadania. Narzędzie do projektowania konwertuje te informacje na metadane używane przez łącznik 3270 podczas wywoływania akcji reprezentującej to zadanie z aplikacji logiki.

Po wygenerowaniu pliku metadanych z narzędzia do projektowania należy dodać ten plik do konta integracji na platformie Azure. W ten sposób aplikacja logiki może uzyskać dostęp do metadanych aplikacji po dodaniu akcji łącznika 3270. Łącznik odczytuje plik metadanych z konta integracji, obsługuje nawigację po ekranach 3270 i dynamicznie przedstawia parametry akcji łącznika 3270. Następnie można podać dane do aplikacji hosta, a łącznik zwraca wyniki do aplikacji logiki. W ten sposób można zintegrować starsze aplikacje z platformą Azure, firmą Microsoft i innymi aplikacjami, usługami i systemami obsługiwanymi przez usługę Azure Logic Apps.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Zalecane: [Środowisko usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md) 

  To środowisko można wybrać jako lokalizację tworzenia i uruchamiania aplikacji logiki. Środowisko ISE zapewnia dostęp z aplikacji logiki do zasobów, które są chronione w sieciach wirtualnych platformy Azure.

* Aplikacja logiczna używana do automatyzacji i uruchamiania aplikacji ekranowej 3270

  Łącznik IBM 3270 nie ma wyzwalaczy, więc użyj innego wyzwalacza, aby uruchomić aplikację logiki, taką jak wyzwalacz **cyklu.** Następnie można dodać akcje łącznika 3270. Aby rozpocząć, [utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
  Jeśli używasz ise, wybierz, że ISE jako lokalizacji aplikacji logiki.

* [Pobierz i zainstaluj narzędzie do projektowania 3270](https://aka.ms/3270-design-tool-download).
Jedynym warunkiem wstępnym jest [Microsoft .NET Framework 4.6.1](https://aka.ms/net-framework-download).

  To narzędzie ułatwia rejestrowanie ekranów, ścieżek nawigacyjnych, metod i parametrów zadań w aplikacji, które są dodawanymi i uruchamiane jako akcje łącznika 3270. Narzędzie generuje plik XML (HIDX) projektanta integracji hosta, który zawiera niezbędne metadane dla łącznika do obsługi aplikacji mainframe.
  
  Po pobraniu i zainstalowaniu tego narzędzia wykonaj następujące czynności, aby połączyć się z hostem:

  1. Otwórz narzędzie do projektowania 3270. Z menu **Sesja** wybierz polecenie **Host Sessions**.
  
  1. Podaj informacje o serwerze hosta TN3270.

* [Konto integracji](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), które jest miejscem, w którym przechowujesz plik HIDX jako mapę, dzięki czemu aplikacja logiki może uzyskać dostęp do metadanych i definicji metod w tym pliku. 

  Upewnij się, że twoje konto integracji jest połączone z używana przez Ciebie aplikacja logiki. Ponadto jeśli używasz ise, upewnij się, że lokalizacja konta integracji jest taka sama ise, że aplikacja logiki używa.

* Dostęp do serwera TN3270, na którym znajduje się aplikacja mainframe

<a name="define-app-metadata"></a>

## <a name="create-metadata-overview"></a>Omówienie tworzenia metadanych

W aplikacji ekranowej 3270 ekrany i pola danych są unikatowe dla twoich scenariuszy, więc łącznik 3270 potrzebuje tych informacji o aplikacji, które można podać jako metadane. Te metadane opisują informacje, które pomagają aplikacji logiki identyfikować i rozpoznawać ekrany, opisuje sposób nawigacji między ekranami, gdzie dane wejściowe i gdzie można oczekiwać wyników. Aby określić i wygenerować te metadane, należy użyć narzędzia do projektowania 3270, które przeprowadzi Cię przez te określone tryby lub *etapy,* zgodnie z opisem w dalszej części szczegółów:

* **Przechwytywanie:** W tym trybie rejestrujesz ekrany wymagane do wykonania określonego zadania za pomocą aplikacji mainframe, na przykład uzyskując saldo bankowe.

* **Nawigacja:** W tym trybie można określić plan lub ścieżkę, jak poruszać się po ekranach aplikacji mainframe dla określonego zadania.

* **Metody**: W tym trybie można zdefiniować metodę, na przykład, `GetBalance`opisującą ścieżkę nawigacji ekranu. Można również wybrać pola na każdym ekranie, które stają się parametry wejściowe i wyjściowe metody.

### <a name="unsupported-elements"></a>Nieobsługiwały elementy

Narzędzie do projektowania nie obsługuje następujących elementów:

* Częściowe mapy IBM Basic Mapping Support (BMS): W przypadku importowania mapy BMS narzędzie do projektowania ignoruje częściowe definicje ekranu.
* Parametry wyjmujące/wychodzące: Nie można zdefiniować parametrów W/Wyjście.
* Przetwarzanie menu: nie jest obsługiwane podczas podglądu
* Przetwarzanie macierzy: nie jest obsługiwane podczas podglądu

<a name="capture-screens"></a>

## <a name="capture-screens"></a>Ekrany przechwytywania

W tym trybie oznaczasz element na każdym ekranie 3270, który jednoznacznie identyfikuje ten ekran. Na przykład można określić wiersz tekstu lub bardziej złożony zestaw warunków, takich jak określony tekst i niepuste pole. Te ekrany można nagrywać za pomocą połączenia na żywo z serwerem hosta lub zaimportować te informacje z mapy IBM Basic Mapping Support (BMS). Połączenie na żywo używa emulatora TN3270 do łączenia się z hostem. Każda akcja łącznika musi być mapowana na pojedyncze zadanie, które rozpoczyna się od połączenia z sesją, a kończy na odłączeniu od sesji.

1. Jeśli jeszcze tego nie zrobiłeś, otwórz narzędzie do projektowania 3270. Na pasku narzędzi wybierz pozycję **Przechwytywanie,** aby wejść w tryb przechwytywania.

1. Aby rozpocząć nagrywanie, naciśnij klawisz F5 lub z menu **Nagrywanie,** wybierz polecenie **Rozpocznij nagrywanie**. 

1. Z menu **Sesja** wybierz polecenie **Połącz**.

1. W okienku **Przechwytywanie,** zaczynając od pierwszego ekranu w aplikacji, posuń się po aplikacji do określonego zadania, które nagrywasz.

1. Po zakończeniu zadania wyloguj się z aplikacji w sposób zwykle.

1. Z menu **Sesja** wybierz polecenie **Rozłącz**.

1. Aby zatrzymać nagrywanie, naciśnij klawisze Shift + F5 lub z menu **Nagrywanie** wybierz **pozycję Zatrzymaj nagrywanie**.

   Po przechwyceniu ekranów dla zadania narzędzie projektanta pokazuje miniatury reprezentujące te ekrany. Niektóre uwagi dotyczące tych miniatur:

   * Dodasze ekrany masz ekran o nazwie "Pusty".

     Podczas pierwszego połączenia z [CICS](https://www.ibm.com/it-infrastructure/z/cics)należy wysłać klucz "Wyczyść", zanim będzie można wprowadzić nazwę transakcji, którą chcesz uruchomić. Ekran, na którym wysyłasz klucz "Wyczyść", nie ma żadnych *atrybutów rozpoznawania,* takich jak tytuł ekranu, który można dodać za pomocą edytora rozpoznawania ekranu. Aby przedstawić ten ekran, miniatury zawierają ekran o nazwie "Puste". Możesz później użyć tego ekranu do reprezentowania ekranu, na którym wprowadzono nazwę transakcji.

   * Domyślnie nazwa przechwyconego ekranu używa pierwszego wyrazu na ekranie. Jeśli ta nazwa już istnieje, narzędzie do projektowania dołącza nazwę z podkreśleniem i liczbą, na przykład "WBGB" i "WBGB_1".

1. Aby nadać przechwyconemu ekranowi bardziej miarodajną nazwę, wykonaj następujące czynności:

   1. W okienku **Ekrany hostów** wybierz ekran, którego nazwę chcesz zmienić.

   1. W tym samym okienku, u dołu w tym samym okienku, znajdź **właściwość Nazwa ekranu.**

   1. Zmień bieżącą nazwę ekranu na bardziej opisową.

1. Teraz określ pola do identyfikacji każdego ekranu.

   Dzięki strumieniu danych 3270 ekrany nie mają domyślnych identyfikatorów, więc musisz wybrać unikatowy tekst na każdym ekranie. W przypadku scenariuszy złożonych można określić wiele warunków, na przykład unikatowy tekst i pole z określonym warunkiem.

Po zakończeniu wybierania pól rozpoznawania przejdź do następnego trybu.

### <a name="conditions-for-identifying-repeated-screens"></a>Warunki identyfikacji powtarzających się ekranów

Aby łącznik mógł nawigować i rozróżniać ekrany, zwykle na ekranie można znaleźć unikatowy tekst, którego można użyć jako identyfikatora wśród przechwyconych ekranów. W przypadku powtarzanych ekranów może być potrzebnych więcej metod identyfikacji. Załóżmy na przykład, że masz dwa ekrany, które wyglądają tak samo, z wyjątkiem jednego ekranu zwraca prawidłową wartość, podczas gdy drugi ekran zwraca komunikat o błędzie.

W narzędziu do projektowania można dodać *atrybuty rozpoznawania*, na przykład tytuł ekranu, taki jak "Uzyskaj saldo konta", za pomocą edytora rozpoznawania ekranu. Jeśli masz rozwidlecową ścieżkę i obie gałęzie zwracają ten sam ekran, ale z różnymi wynikami, potrzebujesz innych atrybutów rozpoznawania. W czasie wykonywania łącznik używa tych atrybutów do określania bieżącej gałęzi i rozwidła. Oto warunki, których możesz użyć:

* Określona wartość: Ta wartość jest zgodna z określonym ciągiem w określonej lokalizacji.
* NIE określona wartość: Ta wartość nie pasuje do określonego ciągu w określonej lokalizacji.
* Puste: to pole jest puste.
* NIE jest puste: to pole nie jest puste.

Aby dowiedzieć się więcej, zobacz [przykładowy plan nawigacji](#example-plan) w dalszej części tego tematu.

<a name="define-navigation"></a>

## <a name="define-navigation-plans"></a>Definiowanie planów nawigacji

W tym trybie można zdefiniować przepływ lub kroki nawigowania po ekranach aplikacji mainframe dla określonego zadania. Na przykład czasami może mieć więcej niż jedną ścieżkę, która aplikacja może podjąć, gdzie jedna ścieżka daje poprawny wynik, podczas gdy druga ścieżka generuje błąd. Dla każdego ekranu określ naciśnięcia klawiszy niezbędne do `CICSPROD <enter>`przejścia do następnego ekranu, na przykład .

> [!TIP]
> Jeśli automatyzujesz kilka zadań, które używają tych samych ekranów połącz i rozłącz, narzędzie do projektowania udostępnia specjalne typy planu Połącz i Odłącz. Podczas definiowania tych planów można dodać je do początku i końca planu nawigacji.

### <a name="guidelines-for-plan-definitions"></a>Wytyczne dotyczące definicji planu

* Uwzględnij wszystkie ekrany, zaczynając od połączenia, a kończąc na odłączeniu.

* Można utworzyć plan autonomiczny lub użyć planów Połącz i Rozłącz, które umożliwiają ponowne użycie serii ekranów wspólnych dla wszystkich transakcji.

  * Ostatni ekran planu Połącz musi być tym samym ekranem co pierwszy ekran planu nawigacji.

  * Pierwszy ekran w planie rozłączenia musi być tym samym ekranem co ostatni ekran w planie nawigacji.

* Przechwycone ekrany mogą zawierać wiele powtarzających się ekranów, więc wybierz i użyj tylko jednego wystąpienia powtarzających się ekranów w planie. Oto kilka przykładów powtarzających się ekranów:

  * Na ekranie logowania, na przykład ekran **MSG-10**
  * Ekran powitalny dla CICS
  * Ekran "Wyczyść" lub **Pusty**

<a name="create-plans"></a>

### <a name="create-plans"></a>Tworzenie planów

1. Na pasku narzędziowym narzędzia 3270 Design Tool wybierz pozycję **Nawigacja,** aby wejść w tryb nawigacji.

1. Aby rozpocząć planowanie, w okienku **nawigacji** wybierz pozycję **Nowy plan**.

1. W obszarze **Wybierz nową nazwę planu**wprowadź nazwę planu. Z listy **Typ** wybierz typ planu:

   | Typ planu | Opis |
   |-----------|-------------|
   | **Proces** | W przypadku planów autonomicznych lub połączonych |
   | **Połącz** | Plany dla łączyć |
   | **Rozłącz** | Dla planów rozłączania |
   |||

1. W okienku **Ekrany hostów** przeciągnij przechwycone miniatury na powierzchnię planu nawigacji w okienku **nawigacji.**

   Aby przedstawić pusty ekran, na którym wprowadzasz nazwę transakcji, użyj ekranu "Puste".

1. Rozmieść ekrany w kolejności opisującej definiowane zadanie.

1. Aby zdefiniować ścieżkę przepływu między ekranami, w tym widelcami i sprzężeniami, na pasku narzędziowym narzędzia do projektowania wybierz pozycję **Przepływ**.

1. Wybierz pierwszy ekran w przepływie. Przeciągnij i narysuj połączenie z następnym ekranem w przepływie.

1. Dla każdego ekranu podaj wartości właściwości **AID Key** (Identyfikator uwagi) i właściwości **Stały tekst,** która przenosi przepływ na następny ekran.

   Może być tylko klucz AID lub zarówno klucz AID, jak i tekst stały.

Po zakończeniu planu nawigacji można [zdefiniować metody w następnym trybie](#define-method).

<a name="example-plan"></a>

### <a name="example"></a>Przykład

W tym przykładzie załóżmy, że uruchomisz transakcję CICS o nazwie "WBGB", która ma następujące kroki: 

* Na pierwszym ekranie należy wprowadzić nazwę i konto.
* Na drugim ekranie otrzymasz saldo konta.
* Wychodzisz na ekran "Pusty".
* Wylogujesz się z CICS na ekranie "MSG-10".

Załóżmy również, że powtarzasz te kroki, ale wprowadzasz nieprawidłowe dane, dzięki czemu można przechwycić ekran, na który pojawia się błąd. Oto ekrany, które przechwytujesz:

* MSG-10
* Cics witamy
* Pusty
* WBGB_1 (wejście)
* WBGB_2 (błąd)
* Empty_1
* MSG-10_1

Chociaż wiele ekranów tutaj uzyskać unikalne nazwy, niektóre ekrany są na tym samym ekranie, na przykład "MSG-10" i "Puste". W przypadku powtarzanego ekranu użyj tylko jednego wystąpienia dla tego ekranu w planie. Oto przykłady, które pokazują, jak może wyglądać plan autonomiczny, plan połączenia, rozłącz i połączony plan:

* Plan autonomiczny

  ![Samodzielny plan nawigacji](./media/connectors-create-api-3270/standalone-plan.png)

* Plan połączenia

  ![Plan połączenia](./media/connectors-create-api-3270/connect-plan.png)

* Rozłącz plan

  ![Rozłącz plan](./media/connectors-create-api-3270/disconnect-plan.png)

* Plan łączony

  ![Plan łączony](./media/connectors-create-api-3270/combined-plan.png)

#### <a name="example-identify-repeated-screens"></a>Przykład: Identyfikowanie powtarzających się ekranów

Aby łącznik mógł poruszać się i różnicować ekrany, zwykle na ekranie można znaleźć unikatowy tekst, którego można użyć jako identyfikatora na przechwyconych ekranach. W przypadku powtarzanych ekranów może być potrzebnych więcej metod identyfikacji. Przykładowy plan ma rozwidli, w którym można uzyskać ekrany, które wyglądają podobnie. Jeden ekran zwraca saldo konta, podczas gdy drugi ekran zwraca komunikat o błędzie.

Narzędzie do projektowania umożliwia dodawanie atrybutów rozpoznawania, na przykład tytułu ekranu o nazwie "Get Account Balance", za pomocą edytora rozpoznawania ekranu. W przypadku podobnych ekranów potrzebne są inne atrybuty. W czasie wykonywania łącznik używa tych atrybutów do określania gałęzi i rozwidlić.

* W gałęzi zwracającej prawidłowe dane wejściowe, która jest ekranem z saldem konta, można dodać pole, które ma warunek "nie pusty".

* W gałęzi, która zwraca z błędem, można dodać pole, które ma warunek "pusty".

<a name="define-method"></a>

## <a name="define-methods"></a>Definiowanie metod

W tym trybie można zdefiniować metodę, która jest skojarzona z planem nawigacji. Dla każdego parametru metody należy określić typ danych, taki jak ciąg, liczba całkowita, data lub godzina itd. Po zakończeniu można przetestować metodę na live hosta i potwierdzić, że metoda działa zgodnie z oczekiwaniami. Następnie wygenerujesz plik metadanych lub plik XML (HIDX) projektanta integracji hosta, który ma teraz definicje metod używane do tworzenia i uruchamiania akcji dla łącznika IBM 3270.

1. Na pasku narzędziowym narzędzia 3270 Design Tool wybierz pozycję **Metody,** aby wejść w tryb Metody. 

1. W okienku **nawigacji** wybierz ekran zawierający żądane pola wejściowe.

1. Aby dodać pierwszy parametr wejściowy dla metody, wykonaj następujące kroki:

   1. W okienku **Przechwytywanie** na ekranie emulatora 3270 wybierz całe pole, a nie tylko tekst wewnątrz pola, które ma być pierwszym wejściem.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i zaznaczyć pole pełne, w menu **Widok** wybierz polecenie **Wszystkie pola**.

   1. Na pasku narzędziowym narzędzia do projektowania wybierz pozycję **Pole wprowadzania**. 

   Aby dodać więcej parametrów wejściowych, powtórz poprzednie kroki dla każdego parametru.

1. Aby dodać pierwszy parametr wyjściowy dla metody, wykonaj następujące kroki:

   1. W okienku **Przechwytywanie** na ekranie emulatora 3270 wybierz całe pole, a nie tylko tekst wewnątrz pola, które ma być pierwszym wyjściem.

      > [!TIP]
      > Aby wyświetlić wszystkie pola i zaznaczyć pole pełne, w menu **Widok** wybierz polecenie **Wszystkie pola**.

   1. Na pasku narzędziowym narzędzia do projektowania wybierz pozycję **Pole wyjściowe**.

   Aby dodać więcej parametrów wyjściowych, powtórz poprzednie kroki dla każdego parametru.

1. Po dodaniu wszystkich parametrów metody zdefiniuj te właściwości dla każdego parametru:

   | Nazwa właściwości | Możliwe wartości | 
   |---------------|-----------------|
   | **Typ danych** | Bajt, Data II, Dziesiętny, Int, Długi, Krótki, Ciąg |
   | **Technika wypełniania pól** | Parametry obsługują te typy wypełnień, wypełniając w razie potrzeby puste miejsca: <p><p>- **Typ**: Wprowadź znaki sekwencyjnie w polu. <p>- **Wypełnienie**: Zastąp zawartość pola znakami, wypełniając w razie potrzeby puste miejsca. <p>- **Wymażeetypu:** Wyczyść pole, a następnie wprowadź znaki sekwencyjnie w polu. |
   | **Ciąg formatu** | Niektóre typy danych parametrów używają ciągu formatu, który informuje łącznik 3270 o konwertowaniu tekstu z ekranu na typ danych .NET: <p><p>- **DateTime**: Ciąg formatu DateTime jest zgodny z [niestandardowymi ciągami formatu daty i godziny .NET](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). Na przykład data `06/30/2019` używa ciągu `MM/dd/yyyy`formatu . <p>- **Liczba dziesiętna**: Ciąg formatu dziesiętnego używa [klauzuli COBOL Picture](https://www.ibm.com/support/knowledgecenter/SS6SG3_5.2.0/com.ibm.cobol52.ent.doc/PGandLR/ref/rlddepic.html). Na przykład liczba `100.35` używa ciągu `999V99`formatu . |
   |||

## <a name="save-and-view-metadata"></a>Zapisywanie i wyświetlanie metadanych

Po zdefiniowaniu metody, ale przed przetestowaniem metody, zapisz wszystkie informacje, które zostały zdefiniowane do tej pory do pliku RAP (rap).
Możesz zapisać w tym pliku RAP w dowolnym momencie w dowolnym trybie. Narzędzie do projektowania zawiera również przykładowy plik RAP, który można otworzyć i przejrzeć, przeglądając folder instalacyjny narzędzia do projektowania w tej lokalizacji i otwierając plik "WoodgroveBank.rap":

`..\Program Files\Microsoft Host Integration Server - 3270 Design Tool\SDK\WoodgroveBank.rap`

Jeśli jednak spróbujesz zapisać zmiany w przykładowym pliku RAP lub wygenerować plik HIDX z przykładowego pliku RAP, podczas gdy plik pozostanie w folderze instalacyjnym narzędzia do projektowania, może pojawić się błąd "odmowa dostępu". Domyślnie narzędzie do projektowania instaluje się w folderze Pliki programów bez uprawnień z podwyższonym poziomem uprawnień. Jeśli zostanie wyświetlony błąd, wypróbuj jedno z następujących rozwiązań:

* Skopiuj przykładowy plik do innej lokalizacji.
* Uruchom narzędzie do projektowania jako administrator.
* Zrób sobie właściciela folderu SDK.

## <a name="test-your-method"></a>Sprawdź swoją metodę

1. Aby uruchomić metodę względem hosta na żywo, gdy nadal w trybie Metody, naciśnij klawisz F5 lub z paska narzędziowego narzędzia do projektowania, wybierz pozycję **Uruchom**.

   > [!TIP]
   > Tryby można zmienić w dowolnym momencie. W menu **Plik** wybierz polecenie **Tryb**, a następnie wybierz odpowiedni tryb.

1. Wprowadź wartości parametrów i wybierz **przycisk OK**.

1. Aby przejść do następnego ekranu, wybierz pozycję **Dalej**.

1. Po zakończeniu wybierz pozycję **Gotowe**, które pokazują wartości parametrów wyjściowych.

<a name="add-metadata-integration-account"></a>

## <a name="generate-and-upload-hidx-file"></a>Generowanie i przesyłanie pliku HIDX

Gdy będziesz gotowy, wygeneruj plik HIDX, aby można było przesłać go na swoje konto integracyjne. Narzędzie do projektowania 3270 tworzy plik HIDX w nowym podfolderze, w którym zapisano plik RAP.

1. Na pasku narzędziowym narzędzia 3270 Design Tool wybierz pozycję **Generuj kod**.

1. Przejdź do folderu zawierającego plik RAP i otwórz podfolder utworzony przez narzędzie po wygenerowaniu pliku HIDX. Upewnij się, że narzędzie utworzyło plik HIDX.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i znajdź swoje konto integracji.

1. Dodaj plik HIDX jako mapę do konta integracyjnego, [wykonaj te podobne kroki, aby dodać mapy,](../logic-apps/logic-apps-enterprise-integration-liquid-transform.md)ale po wybraniu typu mapy wybierz **HIDX**.

W dalszej części tego tematu po dodaniu akcji IBM 3270 do aplikacji logiki po raz pierwszy zostanie wyświetlony monit o utworzenie połączenia między aplikacją logiki a serwerem hosta przez podanie informacji o połączeniu, takich jak nazwy konta integracyjnego i serwera hosta . Po utworzeniu połączenia można wybrać wcześniej dodany plik HIDX, metodę uruchamiania i parametry do użycia.

Po zakończeniu wszystkich tych kroków można użyć akcji utworzonej w aplikacji logiki do łączenia się z komputerem głównym IBM, ekranów dysków dla aplikacji, wprowadzania danych, zwracania wyników itd. Można również kontynuować dodawanie innych akcji do aplikacji logiki do integracji z innymi aplikacjami, usługami i systemami.

<a name="run-action"></a>

## <a name="run-ibm-3270-actions"></a>Uruchamianie akcji IBM 3270

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i otwórz aplikację logiki w logice App Designer, jeśli nie jest już otwarty.

1. W obszarze ostatniego kroku, w którym chcesz dodać akcję, wybierz pozycję **Nowy krok**i wybierz pozycję **Dodaj akcję**. 

1. W polu wyszukiwania wybierz pozycję **Przedsiębiorstwo**. W polu wyszukiwania wpisz "3270" jako filtr. Z listy akcji wybierz tę czynność: **Uruchamia program typu mainframe za sprawą połączenia TN3270**

   ![Wybierz akcję 3270](./media/connectors-create-api-3270/select-3270-action.png)

   Aby dodać akcję między krokami, przesuń wskaźnik myszy na strzałkę między krokami. 
   Wybierz wyświetlony znak**+** plus ( ), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli połączenie jeszcze nie istnieje, podaj niezbędne informacje dla połączenia i wybierz pozycję **Utwórz**.

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa połączenia** | Tak | <*nazwa połączenia*> | Nazwa połączenia |
   | **Identyfikator konta integracji** | Tak | <*nazwa konta integracji*> | Nazwa konta integracyjnego |
   | **Adres URL adresu Sygnatury dostępu Współdzielonego konta** | Tak | <*integration-account-SAS-URL*> | Adres URL sygnatury dostępu współdzielonego (SAS) konta integracji, który można wygenerować na podstawie ustawień konta integracji w witrynie Azure portal. <p>1. W menu konta integracyjnego w obszarze **Ustawienia**wybierz **adres URL wywołania zwrotnego**. <br>2. W okienku po prawej stronie skopiuj wartość **wygenerowanego adresu URL wywołania zwrotnego.** |
   | **Serwer** | Tak | <*TN3270-nazwa serwera*> | Nazwa serwera usługi TN3270 |
   | **Portu** | Nie | <*TN3270-port serwera*> | Port używany przez serwer TN3270. Jeśli nie ma miejsca, `23` łącznik użyje jako wartości domyślnej. |
   | **Typ urządzenia** | Nie | <*IBM-terminal-model*> | Nazwa lub numer modelu terminala IBM do naśladowania. Jeśli łącznik pozostanie pusty, użyje wartości domyślnych. |
   | **Strona kodowa** | Nie | <*numer strony kodowej*> | Numer strony kodowej dla hosta. Jeśli nie ma miejsca, `37` łącznik użyje jako wartości domyślnej. |
   | **Nazwa jednostki logicznej** | Nie | <*nazwa jednostki logicznej*> | Określona nazwa jednostki logicznej do zażądania od hosta |
   | **Włączyć SSL?** | Nie | Włączanie lub wyłączanie | Włącz lub wyłącz szyfrowanie TLS. |
   | **Sprawdzanie poprawności certyfikatu ssl hosta?** | Nie | Włączanie lub wyłączanie | Włącz lub wyłącz sprawdzanie poprawności certyfikatu serwera. |
   ||||

   Przykład:

   ![Connection properties (Właściwości połączenia)](./media/connectors-create-api-3270/connection-properties.png)

1. Podać informacje niezbędne do działania:

   | Właściwość | Wymagany | Wartość | Opis |
   |----------|----------|-------|-------------|
   | **Nazwa Hidx** | Tak | <*HIDX-nazwa pliku*> | Wybierz plik 3270 HIDX, którego chcesz użyć. |
   | **Nazwa metody** | Tak | <*nazwa metody*> | Wybierz metodę w pliku HIDX, której chcesz użyć. Po wybraniu metody zostanie wyświetlona lista **Dodaj nowy parametr,** aby można było wybrać parametry do użycia z tą metodą. |
   ||||

   Przykład:

   **Wybierz plik HIDX**

   ![Wybierz plik HIDX](./media/connectors-create-api-3270/select-hidx-file.png)

   **Wybierz metodę**

   ![Wybierz metodę](./media/connectors-create-api-3270/select-method.png)

   **Wybierz parametry**

   ![Wybieranie parametrów](./media/connectors-create-api-3270/add-parameters.png)

1. Po zakończeniu zapisz i uruchom aplikację logiki.

   Po zakończeniu działania aplikacji logiki wyświetlane są kroki z przebiegu. 
   Pomyślne kroki pokazują znaczniki wyboru, podczas gdy nieudane kroki pokazują literę "X".

1. Aby przejrzeć dane wejściowe i wyjściowe dla każdego kroku, rozwiń ten krok.

1. Aby przejrzeć dane wyjściowe, wybierz pozycję **Zobacz nieprzetworzone dane wyjściowe**.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej informacji technicznych na temat tego łącznika, takich jak wyzwalacze, akcje i limity opisane w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](https://docs.microsoft.com/connectors/si3270/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)wersja z etykietą ISE tego łącznika używa [limitów komunikatów ŚRODOWISKA ISE.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o innych [łącznikach aplikacji logiki](../connectors/apis-list.md)
