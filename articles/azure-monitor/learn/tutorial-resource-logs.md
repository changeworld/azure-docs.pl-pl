---
title: Zbieranie dzienników zasobów z zasobu platformy Azure i analizowanie za pomocą usługi Azure Monitor
description: Samouczek, aby skonfigurować ustawienia diagnostyczne do zbierania dzienników zasobów z zasobu platformy Azure do obszaru roboczego usługi Log Analytics, gdzie mogą być analizowane za pomocą kwerendy dziennika.
ms.subservice: ''
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 12/15/2019
ms.openlocfilehash: d356042d65c419163de4951e64a635a22ea90e6d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78269188"
---
# <a name="tutorial-collect-and-analyze-resource-logs-from-an-azure-resource"></a>Samouczek: Zbieranie i analizowanie dzienników zasobów z zasobu platformy Azure

Dzienniki zasobów zapewniają wgląd w szczegółowe działanie zasobu platformy Azure i są przydatne do monitorowania ich kondycji i dostępności. Zasoby platformy Azure generują dzienniki zasobów automatycznie, ale należy skonfigurować, gdzie powinny być zbierane. W tym samouczku można przejść przez proces tworzenia ustawienia diagnostycznego do zbierania dzienników zasobów dla zasobu w ramach subskrypcji platformy Azure i analizowania go za pomocą kwerendy dziennika.

Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Log Analytics w usłudze Azure Monitor
> * Tworzenie ustawienia diagnostycznego do zbierania dzienników zasobów 
> * Tworzenie prostej kwerendy dziennika do analizowania dzienników


## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten samouczek, potrzebujesz zasobu platformy Azure do monitorowania. Można użyć dowolnego zasobu w ramach subskrypcji platformy Azure, który obsługuje ustawienia diagnostyczne. Aby ustalić, czy zasób obsługuje ustawienia diagnostyczne, przejdź do jego menu w witrynie Azure portal i sprawdź, czy istnieje opcja **ustawień diagnostycznych** w sekcji **Monitorowanie** menu.


## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Zaloguj się do portalu [https://portal.azure.com](https://portal.azure.com)Azure w .


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego
Obszar roboczy usługi Log Analytics w usłudze Azure Monitor zbiera i indeksuje dane dziennika z różnych źródeł i umożliwia zaawansowaną analizę przy użyciu zaawansowanego języka zapytań. Obszar roboczy usługi Log Analytics musi istnieć przed utworzeniem ustawienia diagnostycznego w celu wysłania do niego danych. Można użyć istniejącego obszaru roboczego w subskrypcji platformy Azure lub utworzyć go za pomocą poniższej procedury. 

> [!NOTE]
> Podczas pracy z danymi w obszarach roboczych usługi Log Analytics w menu **Usługi Azure Monitor,** tworzenie obszarów roboczych i zarządzanie nimi w menu **obszarów roboczych usługi Log Analytics.**

1. Z **opcji Wszystkie usługi**wybierz obszar **roboczy usługi Log Analytics**.
2. Kliknij **przycisk Dodaj** u góry ekranu i podaj następujące szczegóły dotyczące obszaru roboczego:
   - **Obszar roboczy usługi Log Analytics:** Nazwa nowego obszaru roboczego. Ta nazwa musi być unikatowa globalnie we wszystkich subskrypcjach usługi Azure Monitor.
   - **Subskrypcja**: Wybierz subskrypcję do przechowywania obszaru roboczego. Nie musi to być taka sama subskrypcja, jak monitorowany zasób.
   - **Grupa zasobów:** Wybierz istniejącą grupę zasobów lub kliknij przycisk **Utwórz nowy,** aby utworzyć nową. Nie musi to być ta sama grupa zasobów taka sama jak monitorowany zasób.
   - **Lokalizacja:** Wybierz region platformy Azure lub utwórz nowy. Nie musi to być taka sama lokalizacja, jak monitorowany zasób.
   - **Warstwa cenowa:** wybierz *płatność zgodnie z rzeczywistym użyciem* jako warstwę cenową. Tę warstwę cenową można zmienić później. Kliknij **łącze z cennikiem usługi Log Analytics,** aby dowiedzieć się więcej o różnych warstwach cenowych.

    ![Nowy obszar roboczy](media/tutorial-resource-logs/new-workspace.png)

3. Kliknij **przycisk OK,** aby utworzyć obszar roboczy.

## <a name="create-a-diagnostic-setting"></a>Tworzenie ustawienia diagnostycznego
[Ustawienia diagnostyczne](../platform/diagnostic-settings.md) określają, gdzie dzienniki zasobów powinny być wysyłane dla określonego zasobu. Jedno ustawienie diagnostyczne może mieć wiele [miejsc docelowych,](../platform/diagnostic-settings.md#destinations)ale w tym samouczku użyjemy tylko obszaru roboczego usługi Log Analytics.

1. W sekcji **Monitorowanie** w menu zasobu wybierz pozycję **Ustawienia diagnostyczne**.
2. Powinien być wyświetlany komunikat "Nie zdefiniowano ustawień diagnostycznych". Kliknij **pozycję Dodaj ustawienie diagnostyczne**.

    ![Ustawienia diagnostyczne](media/tutorial-resource-logs/diagnostic-settings.png)

3. Każde ustawienie diagnostyczne ma trzy podstawowe części:
 
   - **Nazwa**: To nie ma znaczącego wpływu i powinno być po prostu opisowe dla Ciebie.
   - **Miejsca docelowe:** Co najmniej jedno miejsce docelowe do wysłania dzienników. Wszystkie usługi platformy Azure współużytkują ten sam zestaw trzech możliwych miejsc docelowych. Każde ustawienie diagnostyczne można zdefiniować jeden lub więcej miejsc docelowych, ale nie więcej niż jedno miejsce docelowe określonego typu. 
   - **Kategorie**: Kategorie dzienników do wysłania do każdego z miejsc docelowych. Zestaw kategorii będzie się różnić dla każdej usługi platformy Azure.

4. Wybierz **pozycję Wyślij do obszaru roboczego usługi Log Analytics,** a następnie wybierz utworzony obszar roboczy.
5. Wybierz kategorie, które chcesz zebrać. Definicję dostępnych kategorii można znaleźć w dokumentacji każdej usługi.

    ![Ustawienie diagnostyczne](media/tutorial-resource-logs/diagnostic-setting.png)

6. Kliknij **przycisk Zapisz,** aby zapisać ustawienia diagnostyczne.

    
 
 ## <a name="use-a-log-query-to-retrieve-logs"></a>Pobieranie dzienników za pomocą kwerendy dziennika
Dane są pobierane z obszaru roboczego usługi Log Analytics przy użyciu kwerendy dziennika napisanej w języku KQL (Kusto Query Language). Szczegółowe informacje i rozwiązania w usłudze Azure Monitor zapewnią zapytania dziennika do pobierania danych dla określonej usługi, ale można pracować bezpośrednio z zapytaniami dziennika i ich wyniki w witrynie Azure portal z usługi Log Analytics. 

1. W sekcji **Monitorowanie** w menu zasobu wybierz pozycję **Dzienniki**.
2. Usługa Log Analytics zostanie otwarta z pustym oknem kwerendy z zakresem ustawionym na zasób. Wszelkie zapytania będą zawierać tylko rekordy z tego zasobu.

    > [!NOTE]
    > Jeśli dzienniki zostały otwarte z menu usługi Azure Monitor, zakres zostanie ustawiony na obszar roboczy usługi Log Analytics. W takim przypadku wszelkie zapytania będą zawierać wszystkie rekordy w obszarze roboczym.
   
    ![Dzienniki](media/tutorial-resource-logs/logs.png)

4. Usługa pokazana w przykładzie zapisuje dzienniki zasobów do tabeli **AzureDiagnostics,** ale inne usługi mogą zapisywać w innych tabelach. Zobacz [obsługiwane usługi, schematy i kategorie dzienników zasobów platformy Azure](../platform/diagnostic-logs-schema.md) dla tabel używanych przez różne usługi platformy Azure.

    > [!NOTE]
    > Wiele usług zapisuje dzienniki zasobów w tabeli AzureDiagnostics. Jeśli uruchomisz usługę Log Analytics z menu Usługi Azure `where` Monitor, `ResourceProvider` należy dodać instrukcję z kolumną, aby określić określoną usługę. Po uruchomieniu usługi Log Analytics z menu zasobu, a następnie zakres jest ustawiony tylko rekordy z tego zasobu, więc ta kolumna nie jest wymagana. Zobacz dokumentację usługi dla przykładowych zapytań.


5. Wpisz kwerendę i kliknij przycisk **Uruchom,** aby sprawdzić wyniki. 
6. Zobacz [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor,](../log-query/get-started-queries.md) aby uzyskać samouczek dotyczący pisania zapytań dziennika.

    ![Kwerenda dziennika](media/tutorial-resource-logs/log-query-1.png)




## <a name="next-steps"></a>Następne kroki
Teraz, gdy już wiesz, jak zbierać dzienniki zasobów w obszarze roboczym usługi Log Analytics, wykonaj samouczek na temat pisania zapytań dziennika do analizy tych danych.

> [!div class="nextstepaction"]
> [Wprowadzenie do zapytań dziennika w usłudze Azure Monitor](../log-query/get-started-queries.md)
