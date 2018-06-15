---
title: 'Samouczek: Zarządzanie obliczeń w środowisku Azure Functions w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft'
description: Jak zarządzać zasobami obliczeniowymi magazynu danych przy użyciu usługi Azure Functions.
services: sql-data-warehouse
author: kavithaj
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: consume
ms.date: 04/27/2018
ms.author: kavithaj
ms.reviewer: igorstan
ms.openlocfilehash: 48428ef329de4719a25afd20c21ac102bba540a8
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32188450"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-sql-data-warehouse"></a>Zasoby w usłudze Azure SQL Data Warehouse obliczeniowe użycia usługi Azure Functions do zarządzania

Ten samouczek używa usługi Azure Functions do zarządzania zasoby obliczeniowe dla hurtowni danych w magazynie danych SQL Azure.

Aby móc używać aplikacji funkcji platformy Azure z usługą SQL Data Warehouse, należy utworzyć [konto nazwy głównej usługi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) z dostępem współautora w ramach tej samej subskrypcji, w której znajduje się wystąpienie magazynu danych. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Wdrażanie na podstawie czasomierza skalowanie za pomocą szablonu usługi Azure Resource Manager

Aby wdrożyć szablon, potrzebne są następujące informacje:

- Nazwa grupy zasobów, w której znajduje się wystąpienie usługi SQL DW
- Nazwa serwera logicznego, na którym znajduje się wystąpienie usługi SQL DW
- Nazwa wystąpienia usługi SQL DW
- Identyfikator dzierżawy (identyfikator katalogu ) usługi Azure Active Directory
- Identyfikator subskrypcji 
- Identyfikator aplikacji nazwy głównej usługi
- Klucz tajny usługi nazwy głównej usługi

Po utworzeniu powyższych informacji, można wdrożyć tego szablonu:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Po wdrożeniu szablonu, należy odnaleźć trzy nowe zasoby: wolnego Plan usługi aplikacji Azure, planu funkcji aplikacji na podstawie zużycia i konto magazynu, które obsługuje rejestrowanie i kolejki operacji. Kontynuuj czytanie pozostałych sekcji, aby dowiedzieć się, jak zmodyfikować wdrożone funkcje i dostosować je do swoich potrzeb.

## <a name="change-the-compute-level"></a>Zmiana poziomu obliczeniowej

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji. 

   ![Funkcje wdrażane przy użyciu szablonu](media/manage-compute-with-azure-functions/five-functions.png)

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić czas skalowania w górę, czy czas skalowania w dół. W menu rozwijanego wybierz integracji.

   ![Wybieranie pozycji Integruj dla funkcji](media/manage-compute-with-azure-functions/select-integrate.png)

3. Obecnie powinna być wyświetlana wartość *%ScaleDownTime%* lub *%ScaleUpTime%*. Te wartości wskazują, że harmonogram jest oparty na wartościach określonych w [ustawieniach aplikacji][Application Settings]. Obecnie można zignorować tę wartość i dostosować harmonogram do czasu preferowanych oparte na następne kroki.

4. W obszarze harmonogramu dodaj godzinę, o której wyrażenie CRON ma odzwierciedlać, jak często usługa SQL Data Warehouse ma być skalowana w górę. 

  ![Zmienianie harmonogramu funkcji](media/manage-compute-with-azure-functions/change-schedule.png)

  Wartość `schedule` jest [wyrażeniem CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) obejmującym sześć pól: 
  ```json
  {second} {minute} {hour} {day} {month} {day-of-week}
  ```

  Na przykład *"0 30 9 ** 1-5"* będzie odzwierciedlać wyzwalacz każdy dzień tygodnia w 9:30 am. Aby uzyskać więcej informacji, zapoznaj się z [przykładami harmonogramów][schedule examples] usługi Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Zmień czas operacji skalowania

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji. 

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić wartość obliczeniową skalowania w górę, czy wartość obliczeniową skalowania w dół. Po wybraniu funkcji w okienku powinien zostać wyświetlony plik *index.js*.

   ![Zmienianie poziomu obliczeniowego wyzwalacza funkcji](media/manage-compute-with-azure-functions/index-js.png)

3. Zmień wartość elementu *ServiceLevelObjective* na odpowiedni poziom i wybierz pozycję Zapisz. Ta wartość jest wystąpienie magazynu danych będzie skalowana do na podstawie harmonogramu zdefiniowane w sekcji integracja poziomu obliczeniowej.

## <a name="use-pause-or-resume-instead-of-scale"></a>Używanie wstrzymywania lub wznawiania zamiast skalowania 

Obecnie funkcje włączone domyślnie to *DWScaleDownTrigger* i *DWScaleUpTrigger*. Jeśli zamiast tego chcesz korzystać z funkcji wstrzymywania i wznawiania, możesz włączyć funkcje *DWPauseTrigger* lub *DWResumeTrigger*.

1. Przejdź do okienka Funkcje.

   ![Okienko Funkcje](media/manage-compute-with-azure-functions/functions-pane.png)



2. Kliknij przełącznik obok wyzwalaczy, które chcesz włączyć.

3. Przejdź do kart *Integracja* odpowiednich wyzwalaczy, aby zmienić ich harmonogram.

   > [!NOTE]
   > Funkcjonalnych różnic między skalowania wyzwalaczy i wyzwalaczy wstrzymywania/wznawiania jest komunikat, który jest wysyłany do kolejki. Aby uzyskać więcej informacji, zobacz [dodanie nowych funkcji wyzwalacza][Add a new trigger function].


## <a name="add-a-new-trigger-function"></a>Dodawanie nowej funkcji wyzwalacza

Obecnie szablon zawiera tylko dwie funkcje skalowania. Z tych funkcji w ciągu dnia, możesz tylko skalować w dół raz i raz. Aby uzyskać większą kontrolę, takich jak skalowania wiele razy dziennie lub o różnych zachowanie skalowania w weekendy należy dodać inny wyzwalacz.

1. Utwórz nową pustą funkcję. Wybierz *+* przycisk Twojej lokalizacji funkcji pokazanie okienka szablonu funkcji.

   ![Tworzenie nowej funkcji](media/manage-compute-with-azure-functions/create-new-function.png)

2. W polu Język wybierz pozycję *JavaScript*, a następnie wybierz pozycję *TimerTrigger*.

   ![Tworzenie nowej funkcji](media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nazwij funkcję i ustaw harmonogram. Ilustracja przedstawia, jak można ustawić wyzwalanie funkcji w każdą sobotę o północy (późny wieczór w piątek).

   ![Skalowanie w dół w sobotę](media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Skopiuj zawartość pliku *index.js* z jednej z funkcji wyzwalacza.

   ![Kopiowanie pliku index.js](media/manage-compute-with-azure-functions/index-js.png)

5. Ustaw do zmiennej operacji na zachowanie w następujący sposób:

   ```javascript
   // Resume the data warehouse instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the data warehouse instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the data warehouse instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Sporządzanie złożonego harmonogramu

W tej sekcji przedstawiono krótkie, co jest wymagane uzyskanie bardziej złożonych planowanie wstrzymania, wznowienia i możliwościami skalowania.

### <a name="example-1"></a>Przykład 1:

Codzienne skalowanie w górę o godz. 8:00 do wartości DW600 i skalowanie w dół o godz. 20:00 do wartości DW200.

| Funkcja  | Harmonogram     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Przykład 2: 

Codzienne skali w górę w 8 do DW1000, skali raz, aby DW600 godzinie 4 i w dół w 22: 00 do DW200.

| Funkcja  | Harmonogram     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",  "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-3"></a>Przykład 3: 

Skalowanie w górę o godz. 8:00 do wartości DW1000, jednokrotne skalowanie w dół do wartości DW600 o godz. 16:00 w dni robocze. Wstrzymanie w piątek o godz. 23:00, wznowienie w poniedziałek rano o godz. 7:00.

| Funkcja  | Harmonogram       | Operacja                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 0    | `var operation = {"operationType": "ResumeDw"}` |



## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o funkcjach platformy Azure z [wyzwalaczem czasomierza](../azure-functions/functions-create-scheduled-function.md).

Zapoznaj się z [repozytorium przykładów](https://github.com/Microsoft/sql-data-warehouse-samples) usługi SQL Data Warehouse.



[schedule examples]: ../azure-functions/functions-bindings-timer.md#example

[Application Settings]: ../azure-functions/functions-how-to-use-azure-function-app-settings.md
[Add a new trigger function]: manage-compute-with-azure-functions.md#add-a-new-trigger-function