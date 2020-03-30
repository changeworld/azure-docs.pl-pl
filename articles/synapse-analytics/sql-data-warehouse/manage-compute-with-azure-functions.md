---
title: 'Samouczek: Zarządzanie obliczeniami za pomocą funkcji platformy Azure'
description: Jak używać funkcji platformy Azure do zarządzania obliczeniami puli SQL w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: e0317b3a3e7ab13a78a5d1fe3672d664030436ab
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80346637"
---
# <a name="use-azure-functions-to-manage-compute-resources-in-azure-synapse-analytics-sql-pool"></a>Zarządzanie zasobami obliczeniowymi w puli SQL usługi Azure Synapse Analytics za pomocą funkcji Azure Functions

W tym samouczku użyto funkcji platformy Azure do zarządzania zasobami obliczeniowymi dla puli SQL w usłudze Azure Synapse Analytics.

Aby używać aplikacji funkcji platformy Azure z pulą SQL, należy utworzyć [konto jednostki usługi](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) z dostępem współautora w ramach tej samej subskrypcji co wystąpienie puli SQL. 

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Wdrażanie skalowania opartego na czasomierzu za pomocą szablonu usługi Azure Resource Manager

Aby wdrożyć szablon, potrzebne są następujące informacje:

- Nazwa grupy zasobów, w skład na które znajduje się wystąpienie puli SQL
- Nazwa serwera logicznego, na którego znajduje się wystąpienie puli SQL
- Nazwa wystąpienia puli SQL
- Identyfikator dzierżawy (identyfikator katalogu ) usługi Azure Active Directory
- Identyfikator subskrypcji 
- Identyfikator aplikacji nazwy głównej usługi
- Klucz tajny usługi nazwy głównej usługi

Po uzyskaniu powyższych informacji należy wdrożyć ten szablon:

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json" target="_blank">
<img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png"/>
</a>

Po wdrożeniu szablonu należy znaleźć trzy nowe zasoby: bezpłatny plan usługi Azure App Service, plan aplikacji oparty na zużyciu oraz konto magazynu obsługujące rejestrowanie i kolejkę operacji. Kontynuuj czytanie pozostałych sekcji, aby dowiedzieć się, jak zmodyfikować wdrożone funkcje i dostosować je do swoich potrzeb.

## <a name="change-the-compute-level"></a>Zmienianie poziomu obliczeniowego

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji. 

   ![Funkcje wdrażane przy użyciu szablonu](./media/manage-compute-with-azure-functions/five-functions.png)

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić czas skalowania w górę, czy czas skalowania w dół. W menu rozwijanym wybierz polecenie Integruj.

   ![Wybieranie pozycji Integruj dla funkcji](./media/manage-compute-with-azure-functions/select-integrate.png)

3. Obecnie powinna być wyświetlana wartość *%ScaleDownTime%* lub *%ScaleUpTime%*. Te wartości wskazują, że harmonogram jest oparty na wartościach określonych w [ustawieniach aplikacji](../../azure-functions/functions-how-to-use-azure-function-app-settings.md). Na razie możesz zignorować tę wartość i zmienić harmonogram na preferowany czas na podstawie kolejnych kroków.

4. W obszarze harmonogramu dodaj godzinę, o której wyrażenie CRON ma odzwierciedlać, jak często usługa SQL Data Warehouse ma być skalowana w górę. 

   ![Zmienianie harmonogramu funkcji](./media/manage-compute-with-azure-functions/change-schedule.png)

   Wartość `schedule` jest [wyrażeniem CRON](https://en.wikipedia.org/wiki/Cron#CRON_expression) obejmującym sześć pól: 
   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   Na przykład *"0 30 9 * * 1-5"* odzwierciedla wyzwalacz co dzień powszedni o 9:30. Aby uzyskać więcej informacji, zapoznaj się z [przykładami harmonogramów](../../azure-functions/functions-bindings-timer.md#example) usługi Azure Functions.


## <a name="change-the-time-of-the-scale-operation"></a>Zmienianie czasu pracy wagi

1. Przejdź do usługi aplikacji funkcji. Jeśli szablon został wdrożony przy użyciu wartości domyślnych, usługa ta powinna mieć nazwę *DWOperations*. Po otwarciu aplikacji funkcji powinno być widocznych pięć funkcji wdrożonych w usłudze aplikacji funkcji. 

2. Wybierz pozycję *DWScaleDownTrigger* lub *DWScaleUpTrigger* w zależności od tego, czy chcesz zmienić wartość obliczeniową skalowania w górę, czy wartość obliczeniową skalowania w dół. Po wybraniu funkcji w okienku powinien zostać wyświetlony plik *index.js*.

   ![Zmienianie poziomu obliczeniowego wyzwalacza funkcji](././media/manage-compute-with-azure-functions/index-js.png)

3. Zmień wartość elementu *ServiceLevelObjective* na odpowiedni poziom i wybierz pozycję Zapisz. Ta wartość jest poziomem obliczeniowym, do który zostanie skaliowane wystąpienie magazynu danych na podstawie harmonogramu zdefiniowanego w sekcji Integruj.

## <a name="use-pause-or-resume-instead-of-scale"></a>Używanie wstrzymywania lub wznawiania zamiast skalowania 

Obecnie funkcje włączone domyślnie to *DWScaleDownTrigger* i *DWScaleUpTrigger*. Jeśli zamiast tego chcesz korzystać z funkcji wstrzymywania i wznawiania, możesz włączyć funkcje *DWPauseTrigger* lub *DWResumeTrigger*.

1. Przejdź do okienka Funkcje.

   ![Okienko Funkcje](./media/manage-compute-with-azure-functions/functions-pane.png)



2. Kliknij przełącznik obok wyzwalaczy, które chcesz włączyć.

3. Przejdź do kart *Integracja* odpowiednich wyzwalaczy, aby zmienić ich harmonogram.

   > [!NOTE]
   > Różnica funkcjonalna między wyzwalaczami skalowania a wyzwalaczami wstrzymania/wznowienia jest komunikatem wysyłany do kolejki. Aby uzyskać więcej informacji, zobacz [Dodawanie nowej funkcji wyzwalacza](manage-compute-with-azure-functions.md#add-a-new-trigger-function).


## <a name="add-a-new-trigger-function"></a>Dodawanie nowej funkcji wyzwalacza

Obecnie szablon zawiera tylko dwie funkcje skalowania. Dzięki tym funkcjom w ciągu dnia można skalować tylko raz w dół. Aby uzyskać bardziej szczegółową kontrolę, na przykład skalowanie w dół wiele razy dziennie lub o różnych zachowań skalowania w weekendy, należy dodać inny wyzwalacz.

1. Utwórz nową pustą funkcję. Wybierz *+* przycisk w pobliżu lokalizacji funkcji, aby wyświetlić okienko szablonu funkcji.

   ![Tworzenie nowej funkcji](./media/manage-compute-with-azure-functions/create-new-function.png)

2. W polu Język wybierz pozycję *JavaScript*, a następnie wybierz pozycję *TimerTrigger*.

   ![Tworzenie nowej funkcji](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. Nazwij funkcję i ustaw harmonogram. Ilustracja przedstawia, jak można ustawić wyzwalanie funkcji w każdą sobotę o północy (późny wieczór w piątek).

   ![Skalowanie w dół w sobotę](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. Skopiuj zawartość pliku *index.js* z jednej z funkcji wyzwalacza.

   ![Kopiowanie pliku index.js](././media/manage-compute-with-azure-functions/index-js.png)

5. Ustaw zmienną operacyjną na żądane zachowanie w następujący sposób:

   ```javascript
   // Resume the SQL pool instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the SQL pool instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the SQL pool instance to DW600
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600"
   }
   ```


## <a name="complex-scheduling"></a>Sporządzanie złożonego harmonogramu

W tej sekcji pokrótce pokazano, co jest konieczne, aby uzyskać bardziej złożone harmonogramy wstrzymania, wznowienia i skalowania możliwości.

### <a name="example-1"></a>Przykład 1:

Codzienne skalowanie w górę o godz. 8:00 do wartości DW600 i skalowanie w dół o godz. 20:00 do wartości DW200.

| Funkcja  | Harmonogram     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200"}` |

### <a name="example-2"></a>Przykład 2: 

Codziennie skalowanie w górę o 8 rano do DW1000, skalowanie w dół raz do DW600 na 4pm, i skalować w dół o 22:00 do DW200.

| Funkcja  | Harmonogram     | Operacja                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000"}` |
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



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach platformy Azure z [wyzwalaczem czasomierza](../../azure-functions/functions-create-scheduled-function.md).

Wyewidencjonuj [repozytorium próbek](https://github.com/Microsoft/sql-data-warehouse-samples)puli SQL .

