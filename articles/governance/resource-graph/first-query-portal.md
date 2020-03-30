---
title: 'Szybki start: Pierwsze zapytanie portalu'
description: W tym przewodniku Szybki start wykonaj kroki, aby uruchomić pierwszą kwerendę z witryny Azure portal przy użyciu Eksploratora wykresu zasobów platformy Azure.
ms.date: 11/21/2019
ms.topic: quickstart
ms.openlocfilehash: 5cf355e78ad51e06d7ba27d48dd352f35b4c0740
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74406795"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-resource-graph-explorer"></a>Szybki start: uruchamianie pierwszej kwerendy wykresu zasobów przy użyciu Eksploratora wykresu zasobów platformy Azure

Możliwości programu Azure Resource Graph są dostępne bezpośrednio w witrynie Azure portal za pośrednictwem Eksploratora wykresów zasobów platformy Azure. Eksplorator wykresu zasobów udostępnia informacje do przeglądania dotyczące typów zasobów usługi Azure Resource Manager i właściwości, które można wysyłać. Eksplorator wykresów zasobów udostępnia również czysty interfejs do pracy z wieloma zapytaniami, oceny wyników, a nawet konwertowania wyników niektórych zapytań na wykres, który można przypiąć do pulpitu nawigacyjnego platformy Azure.

Na końcu tego przewodnika Szybki start zostanie użyta usługa Azure portal i Eksplorator wykresów zasobów do uruchomienia pierwszej kwerendy wykresu zasobów i przypięte wyniki do pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne](https://azure.microsoft.com/free/) konto przed rozpoczęciem.

## <a name="run-your-first-resource-graph-query"></a>Uruchamianie pierwszego zapytania usługi Resource Graph

Otwórz [witrynę Azure Portal,](https://portal.azure.com) aby znaleźć i użyć Eksploratora wykresów zasobów, wykonując następujące kroki, aby uruchomić pierwszą kwerendę wykresu zasobów:

1. W okienku po lewej stronie wybierz pozycję **Wszystkie usługi**. Wyszukaj i wybierz **Eksploratora wykresów zasobów**.

1. W części **kwerendy 1** okna wprowadź `Resources | project name, type | limit 5` kwerendę i wybierz pozycję **Uruchom kwerendę**.

   > [!NOTE]
   > Ponieważ w tym przykładzie kwerendy nie `order by`udostępnia modyfikatora sortowania, takiego jak , uruchamianie tej kwerendy wiele razy może przynieść inny zestaw zasobów na żądanie.

1. Przejrzyj odpowiedź na kwerendę na karcie **Wyniki.** Wybierz kartę **Wiadomości,** aby wyświetlić szczegółowe informacje o kwerendzie, w tym liczbę wyników i czas trwania kwerendy. Błędy, jeśli występują, są wyświetlane na tej karcie.

1. Zaktualizuj kwerendę do `order by` właściwości **Name:** `Resources | project name, type | limit 5 | order by name asc`. Następnie wybierz pozycję **Uruchom kwerendę**.

   > [!NOTE]
   > Tak samo jak w przypadku pierwszego zapytania, wielokrotne uruchomienie tego zapytania prawdopodobnie zwróci inny zestaw zasobów dla każdego żądania. Kolejność poleceń zapytania jest ważna. W tym przykładzie polecenie `order by` następuje po poleceniu `limit`. Spowoduje to najpierw ograniczenie wyników zapytania, a następnie ich uporządkowanie.

1. Zaktualizuj `order by` kwerendę, `limit` aby najpierw właściwości `Resources | project name, type | order by name asc | limit 5` **Name,** a następnie do pięciu najlepszych wyników: . Następnie wybierz pozycję **Uruchom kwerendę**.

Gdy kwerenda końcowa jest uruchamiana kilka razy, przy założeniu, że nic w środowisku się nie zmienia, zwracane wyniki są spójne i zgodnie z oczekiwaniami — uporządkowane przez **Name** właściwości, ale nadal ograniczone do pięciu najlepszych wyników.

### <a name="schema-browser"></a>Przeglądarka schematów

Przeglądarka schematów znajduje się w lewym okienku Eksploratora wykresu zasobów. Ta lista zasobów zawiera wszystkie _typy zasobów_ platformy Azure, które są obsługiwane przez usługę Azure Resource Graph i które istnieją w dzierżawie, do której masz dostęp. Rozwijanie typu zasobu lub właściwości podrzędnych pokazuje właściwości podrzędne, które mogą być używane do tworzenia kwerendy wykresu zasobów.

Zaznaczenie typu zasobu w `where type =="<resource type>"` polu kwerendy. Zaznaczenie jednej z właściwości `where <propertyName> == "INSERT_VALUE_HERE"` podrzędnych dodaje się do pola kwerendy.
Przeglądarka schematów to świetny sposób na odnajdywanie właściwości do użycia w kwerendach. Pamiętaj, aby zastąpić _INSERT\_VALUE\_HERE_ własną wartością, dostosuj zapytanie do warunków, operatorów i funkcji, aby osiągnąć zamierzone rezultaty.

## <a name="create-a-chart-from-the-resource-graph-query"></a>Tworzenie wykresu na podstawie kwerendy Wykres zasobów

Po uruchomieniu ostatniej kwerendy powyżej, jeśli **wybierzesz kartę Wykresy,** zostanie wyświetlony komunikat, że "zestaw wyników nie jest zgodny z wizualizacją wykresu kołowego". Kwerendy, które listy wyników nie mogą być wykonane na wykresie, ale kwerendy, które zapewniają liczbę zasobów można. Za pomocą [przykładowej kwerendy — zliczanie maszyn wirtualnych według typu systemu operacyjnego](./samples/starter.md#count-virtual-machines-by-os-type), utwórzmy wizualizację z kwerendy Wykres zasobów.

1. W części **kwerendy 1** okna wprowadź następującą kwerendę i wybierz pozycję **Uruchom kwerendę**.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Wybierz kartę **Wyniki** i zwróć uwagę, że odpowiedź dla tej kwerendy zawiera liczby.

1. Wybierz kartę **Wykresy.** Teraz wyniki kwerendy w wizualizacje. Zmień typ z _Wybierz typ wykresu..._ na _wykres słupkowy_ lub _pierścieniowy,_ aby eksperymentować z dostępnymi opcjami wizualizacji.

## <a name="pin-the-query-visualization-to-a-dashboard"></a>Przypinanie wizualizacji kwerendy do pulpitu nawigacyjnego

Po wprowadzeniu wyników z kwerendy, która może być wizualizowana, wizualizacja danych może być przypięta do jednego z pulpitów nawigacyjnych. Po uruchomieniu zapytania powyżej wykonaj następujące kroki:

1. Wybierz **pozycję Zapisz** i podaj nazwę "Maszyny wirtualne według typu systemu operacyjnego". Następnie wybierz **pozycję Zapisz** u dołu prawego okienka.

1. Wybierz **pozycję Uruchom kwerendę,** aby ponownie uruchomić kwerendę, która została zapisana.

1. Na karcie **Wykresy** wybierz wizualizację danych. Następnie wybierz **pozycję Przypnij do pulpitu nawigacyjnego**.

1. Wybierz wyświetlane powiadomienie portalu lub wybierz **pulpit nawigacyjny** z lewego okienka.

Kwerenda jest teraz dostępna na pulpicie nawigacyjnym z tytułem kafelka pasującego do nazwy kwerendy. Jeśli kwerenda została niezapisane, gdy została przypięta, zamiast tego nosi nazwę "Kwerenda 1".

Wizualizacja zapytań i wynikowych danych jest uruchamiana i aktualizowana za każdym razem, gdy pulpit nawigacyjny jest ładowany, zapewniając szczegółowe informacje w czasie rzeczywistym i dynamiczne do środowiska platformy Azure bezpośrednio w przepływie pracy.

> [!NOTE]
> Kwerendy, które powodują listę można również przypiąć do pulpitu nawigacyjnego. Ta funkcja nie ogranicza się do wizualizacji danych zapytań.

## <a name="import-example-resource-graph-explorer-dashboards"></a>Importowanie przykładowych pulpitów nawigacyjnych Eksploratora wykresów zasobów

Aby podać przykłady zapytań wykresu zasobów i jak Eksplorator wykresu zasobów może służyć do zwiększenia przepływu pracy witryny Azure portal, wypróbuj te przykładowe pulpity nawigacyjne.

- [Eksplorator wykresów zasobów — przykładowy pulpit nawigacyjny #1](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-1/resourcegraphexplorer-sample-1.json)

  [![Przykładowy obraz przykładowego pulpitu nawigacyjnego #1](./media/arge-sample1-small.png)](./media/arge-sample1-large.png#lightbox)

- [Eksplorator wykresów zasobów — przykładowy pulpit nawigacyjny #2](https://github.com/Azure-Samples/Governance/blob/master/src/resource-graph/portal-dashboards/sample-2/resourcegraphexplorer-sample-2.json)

  [![Przykładowy obraz przykładowego pulpitu nawigacyjnego #2](./media/arge-sample2-small.png)](./media/arge-sample2-large.png#lightbox)

> [!NOTE]
> Liczby i wykresy w powyższym przykładowym panelu zrzuty ekranu będą się różnić w zależności od środowiska platformy Azure.

1. Wybierz i pobierz przykładowy pulpit nawigacyjny, który chcesz ocenić.

1. W witrynie Azure portal wybierz **pulpit nawigacyjny** z lewego okienka.

1. Wybierz **pozycję Przekaż**, a następnie znajdź i wybierz pobrany przykładowy plik pulpitu nawigacyjnego. Następnie wybierz pozycję **Otwórz**.

Importowany pulpit nawigacyjny jest wyświetlany automatycznie. Ponieważ obecnie istnieje w witrynie Azure portal, można eksplorować i wprowadzać zmiany w razie potrzeby lub utworzyć nowe pulpity nawigacyjne z przykładu do udostępniania zespołom. Aby uzyskać więcej informacji na temat pracy z pulpitami nawigacyjnymi, zobacz [Tworzenie i udostępnianie pulpitów nawigacyjnych w witrynie Azure portal](../../azure-portal/azure-portal-dashboards.md).

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli chcesz usunąć przykładowe pulpity nawigacyjne wykresu zasobów ze środowiska witryny Azure portal, możesz to zrobić za pomocą następujących kroków:

1. Wybierz **pozycję Pulpit nawigacyjny** z lewego okienka.

1. Z listy rozwijanej pulpitu nawigacyjnego wybierz przykładowy pulpit nawigacyjny wykresu zasobów, który chcesz usunąć.

1. Wybierz **polecenie Usuń** z menu pulpitu nawigacyjnego u góry pulpitu nawigacyjnego i wybierz pozycję **Ok,** aby potwierdzić.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start użyto eksploratora programu Azure Resource Graph explorer do uruchomienia pierwszego zapytania i przyjrzano się przykładom pulpitu nawigacyjnego obsługiwanego przez program Resource Graph. Aby dowiedzieć się więcej o języku wykresu zasobów, przejdź do strony szczegółów języka kwerendy.

> [!div class="nextstepaction"]
> [Uzyskaj więcej informacji o języku kwerendy](./concepts/query-language.md)