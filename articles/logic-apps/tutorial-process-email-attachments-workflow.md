---
title: Samouczek — Automatyzowanie wiadomości e-mail dotyczących przetwarzania i załączników — Azure Logic Apps
description: Samouczek — Tworzenie zautomatyzowanych przepływów pracy na potrzeby obsługi wiadomości e-mail i załączników przy użyciu usług Azure Logic Apps, Azure Storage i Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
manager: carmonm
ms.topic: tutorial
ms.custom: mvc
ms.date: 05/07/2019
ms.openlocfilehash: d203f5f10c7a24b63693a7fe5d6387be284d5034
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982995"
---
# <a name="tutorial-automate-handling-emails-and-attachments-with-azure-logic-apps"></a>Samouczek: Automatyzowanie obsługi wiadomości e-mail i załączników za pomocą usługi Azure Logic Apps

Usługa Azure Logic Apps pomaga automatyzować przepływy pracy i integrować dane w usługach platformy Azure, usługach firmy Microsoft, innych aplikacjach typu oprogramowanie jako usługa (SaaS) oraz systemach lokalnych. Ten samouczek pokazuje sposób tworzenia [aplikacji logiki](../logic-apps/logic-apps-overview.md), która obsługuje przychodzące wiadomości e-mail i wszelkie załączniki. Ta aplikacja logiki analizuje zawartość wiadomości e-mail, zapisuje ją w usłudze Azure Storage oraz wysyła powiadomienia dotyczące przeglądania zawartości.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowania [magazynu platformy Azure](../storage/common/storage-introduction.md) i Eksploratora usługi Storage na potrzeby sprawdzania zapisanych wiadomości e-mail i załączników.
> * Tworzenia [funkcji platformy Azure](../azure-functions/functions-overview.md) usuwającej kod HTML z wiadomości e-mail. Ten samouczek zawiera kod, którego można użyć na potrzeby tej funkcji.
> * Tworzenia pustej aplikacji logiki.
> * Dodawania wyzwalacza monitorującego wiadomości e-mail pod kątem załączników.
> * Dodawania warunku sprawdzającego, czy wiadomości e-mail zawierają załączniki.
> * Dodawania akcji, która wywołuje funkcję platformy Azure, jeśli wiadomość e-mail ma załączniki.
> * Dodawania akcji, która tworzy obiekty blob magazynu dla wiadomości e-mail i załączników.
> * Dodawania akcji, która wysyła powiadomienia w wiadomościach e-mail.

Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Ukończona aplikacja logiki wysokiego poziomu](./media/tutorial-process-email-attachments-workflow/overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Konto e-mail od dostawcy obsługiwanego przez usługę Logic Apps, na przykład Office 365 Outlook, Outlook.com lub Gmail. W przypadku innych dostawców [przejrzyj tę listę łączników](https://docs.microsoft.com/connectors/).

  Ta aplikacja logiki korzysta z konta Office 365 Outlook. 
  Jeśli korzystasz z innego konta e-mail, ogólne kroki pozostają takie same, ale Twój interfejs użytkownika może wyglądać trochę inaczej.

* Pobierz i zainstaluj [bezpłatny Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com/). To narzędzie ułatwia sprawdzanie, czy kontener magazynu został skonfigurowany prawidłowo.

## <a name="sign-in-to-azure-portal"></a>Logowanie do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="set-up-storage-to-save-attachments"></a>Konfigurowanie magazynu na potrzeby zapisywania załączników

Możesz zapisywać przychodzące wiadomości e-mail i załączniki jako obiekty blob w [kontenerze magazynu platformy Azure](../storage/common/storage-introduction.md). 

1. Przed utworzeniem kontenera magazynu [Utwórz konto magazynu](../storage/common/storage-quickstart-create-account.md) z tymi ustawieniami na karcie **podstawowe** w Azure Portal:

   | Ustawienie | Wartość | Opis |
   |---------|-------|-------------|
   | **Subskrypcja** | <*Azure-subscription-name*> | Nazwa subskrypcji platformy Azure |  
   | **Grupa zasobów** | <*Azure-resource-group*> | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/resource-group-overview.md) używana do organizowania powiązanych zasobów i zarządzania nimi. W tym przykładzie zastosowano "LA-samouczek-RG". <p>**Uwaga:** Grupa zasobów istnieje w konkretnym regionie. Chociaż elementy w tym samouczku mogą nie być dostępne we wszystkich regionach, spróbuj używać tego samego regionu, jeśli jest to możliwe. |
   | **Nazwa konta magazynu** | <*Azure-Storage-account-name*> | Nazwa konta magazynu, która musi mieć 3-24 znaków i może zawierać tylko małe litery i cyfry. W tym przykładzie zastosowano "attachmentstorageacct". |
   | **Location** | <*Platforma Azure — region*> | Region, w którym są przechowywane informacje o koncie magazynu. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Wydajność** | Standardowa (Standard) | To ustawienie określa obsługiwane typy danych oraz nośniki do przechowywania danych. Zobacz [Typy kont magazynu](../storage/common/storage-introduction.md#types-of-storage-accounts). |
   | **Rodzaj konta** | Ogólnego przeznaczenia | [Typ konta magazynu](../storage/common/storage-introduction.md#types-of-storage-accounts) |
   | **Replikacja** | Magazyn lokalnie nadmiarowy (LRS) | To ustawienie określa sposób kopiowania, przechowywania i synchronizowania danych oraz zarządzania nimi. Zobacz [Locally redundant storage (LRS): Low-cost data redundancy for Azure Storage](../storage/common/storage-redundancy-lrs.md) (Magazyn lokalnie nadmiarowy: nadmiarowość danych o niskich kosztach dla usługi Azure Storage). |
   ||||

   Na karcie **Zaawansowane** wybierz to ustawienie:

   | Ustawienie | Wartość | Opis |
   |---------|-------|-------------|
   | **Wymagany bezpieczny transfer** | Wyłączone | To ustawienie określa wymagane zabezpieczenia dla żądań z połączeń. Zobacz [Require secure transfer (Wymaganie bezpiecznego transferu)](../storage/common/storage-require-secure-transfer.md). |
   ||||

   Aby utworzyć konto magazynu, możesz również użyć [programu Azure PowerShell](../storage/common/storage-quickstart-create-storage-account-powershell.md) lub [wiersza polecenia platformy Azure](../storage/common/storage-quickstart-create-storage-account-cli.md).

1. Gdy skończysz, wybierz pozycję **Przegląd + Utwórz**.

1. Po wdrożeniu konta magazynu przez platformę Azure pobierz klucz dostępu konta magazynu:

   1. W menu konta magazynu w obszarze **Ustawienia** wybierz pozycję **Klucze dostępu**.

   2. Skopiuj nazwę konta magazynu i klucz **key1**, a następnie zapisz te wartości w bezpiecznym miejscu.

      ![Kopiowanie i zapisywanie nazwy i klucza konta magazynu](./media/tutorial-process-email-attachments-workflow/copy-save-storage-name-key.png)

   Aby uzyskać klucz dostępu do konta magazynu, możesz również użyć [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageaccountkey) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest.md#az-storage-account-keys-list).

1. Utwórz kontener magazynu obiektów blob na potrzeby załączników do wiadomości e-mail.

   1. W menu konta magazynu wybierz pozycję **Omówienie**. 
   W obszarze **usługi**wybierz pozycję **obiekty blob**.

      ![Dodawanie kontenera magazynu obiektów blob](./media/tutorial-process-email-attachments-workflow/create-storage-container.png)

   2. Po otwarciu strony **Kontenery** na pasku narzędzi wybierz pozycję **Kontener**.

   3. W obszarze **Nowy kontener** wprowadź ciąg „attachments” jako nazwę kontenera. 
   W obszarze **Poziom dostępu publicznego** wybierz pozycję **Kontener (anonimowy dostęp do odczytu dla kontenerów i obiektów blob)** , a następnie wybierz przycisk **OK**.

      Gdy wszystko będzie gotowe, będziesz w stanie znaleźć kontener magazynu na koncie magazynu w witrynie Azure Portal:

      ![Ukończony kontener magazynu](./media/tutorial-process-email-attachments-workflow/created-storage-container.png)

   Aby utworzyć kontener magazynu, możesz również użyć [programu Azure PowerShell](https://docs.microsoft.com/powershell/module/az.storage/new-azstoragecontainer) lub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create).

Następnie połącz Eksplorator usługi Storage z kontem magazynu.

## <a name="set-up-storage-explorer"></a>Konfiguracja Eksploratora usługi Storage

Teraz połącz Eksplorator usługi Storage z kontem magazynu, aby potwierdzić, że aplikacja logiki może prawidłowo zapisywać załączniki jako obiekty blob w kontenerze magazynu.

1. Otwórz Eksplorator usługi Microsoft Azure Storage.

   Eksplorator usługi Storage wyświetli monit o połączenie z kontem magazynu. 

2. W okienku **łączenia z usługą Azure Storage** wybierz pozycję **Użyj nazwy i klucza konta magazynu**, a następnie wybierz pozycję **Dalej**.

   ![Eksplorator usługi Storage — łączenie z kontem magazynu](./media/tutorial-process-email-attachments-workflow/storage-explorer-choose-storage-account.png)

   > [!TIP]
   > Jeśli nie zostanie wyświetlony monit, na pasku narzędzi Eksploratora usługi Storage wybierz pozycję **Dodaj konto**.

3. W obszarze **Nazwa konta** podaj nazwę konta magazynu. W obszarze **Klucz konta** podaj wcześniej zapisany klucz dostępu. Wybierz **dalej**.

4. Potwierdź informacje o połączeniu, a następnie wybierz pozycję **Połącz**.

   Eksplorator usługi Storage tworzy połączenie i wyświetla konto magazynu w oknie Eksploratora w obszarze **(Lokalne i dołączone)** > **Konta magazynu**.

5. Aby znaleźć kontener magazynu obiektów blob, w obszarze **Konta magazynu** rozwiń swoje konto magazynu (tutaj **attachmentstorageacct**), a następnie rozwiń węzeł **Kontenery obiektów blob**, w którym znajdziesz kontener **attachments**, na przykład:

   ![Eksplorator usługi Storage — wyszukiwanie kontenera magazynu](./media/tutorial-process-email-attachments-workflow/storage-explorer-check-contianer.png)

Następnie utwórz [funkcję platformy Azure](../azure-functions/functions-overview.md), która będzie usuwać kod HTML z przychodzących wiadomości e-mail.

## <a name="create-function-to-clean-html"></a>Tworzenie funkcji do czyszczenia kodu HTML

Teraz użyj fragmentu kodu zapewnionego przez te kroki, aby utworzyć funkcję platformy Azure, która będzie usuwać kod HTML z przychodzących wiadomości e-mail. W ten sposób zawartość wiadomości e-mail będzie czystsza i łatwiejsza do przetworzenia. Następnie możesz wywołać tę funkcję z poziomu aplikacji logiki.

1. Aby można było utworzyć funkcję, [utwórz aplikację funkcji](../azure-functions/functions-create-function-app-portal.md) przy użyciu następujących ustawień:

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Nazwa aplikacji** | <*Function-App-Name*> | Opisowa i globalnie unikatowa nazwa aplikacji funkcji, która jest "CleanTextFunctionApp" w tym przykładzie, więc podaj inną nazwę, taką jak "MyCleanTextFunctionApp" |
   | **Subskrypcja** | <*your-Azure-subscription-name*> | Ta sama subskrypcja platformy Azure, której użyto wcześniej | 
   | **Grupa zasobów** | LA-Tutorial-RG | Ta sama grupa zasobów platformy Azure, której użyto wcześniej |
   | **Plan hostingu** | Plan zużycia | To ustawienie określa sposób przydzielania i skalowania zasobów, takich jak moc obliczeniowa, na potrzeby uruchamiania aplikacji funkcji. Zobacz [Hosting plan comparison (Porównanie planów hostingu)](../azure-functions/functions-scale.md). | 
   | **Location** | Zachodnie stany USA | Ten sam region, którego użyto wcześniej |
   | **Stos środowiska uruchomieniowego** | Preferowany język | Wybierz środowisko uruchomieniowe, które obsługuje ulubiony język programowania funkcji. Wybierz pozycję .NET C# dla F# funkcji i. |
   | **Storage** | cleantextfunctionstorageacct | Utwórz konto magazynu dla aplikacji funkcji. Użyj tylko małych liter i cyfr. <p>**Uwaga:** To konto magazynu zawiera aplikacje funkcji i różni się od poprzednio utworzonego konta magazynu do przechowywania załączników wiadomości e-mail. |
   | **Application Insights** | Wyłączone | Włącza monitorowanie aplikacji za pomocą usługi [Application Insights](../azure-monitor/app/app-insights-overview.md), ale na potrzeby tego samouczka wybierz ustawienie **Wyłączone**. |
   ||||

   Jeśli aplikacja funkcji nie otworzy się automatycznie po wdrożeniu, znajdź ją w witrynie [Azure Portal](https://portal.azure.com). 
   W głównym menu platformy Azure wybierz pozycję **Aplikacje funkcji**, a następnie wybierz aplikację funkcji.

   ![Wybieranie aplikacji funkcji](./media/tutorial-process-email-attachments-workflow/select-function-app.png)

   Jeśli pozycja **Aplikacje funkcji** nie zostanie wyświetlona w menu platformy Azure, przejdź do pozycji **Wszystkie usługi**. W polu wyszukiwania znajdź i wybierz opcję **Aplikacje funkcji**. Aby uzyskać więcej informacji, zobacz [Tworzenie funkcji](../azure-functions/functions-create-first-azure-function.md).

   W przeciwnym razie platforma Azure automatycznie otworzy aplikację funkcji, jak pokazano poniżej:

   ![Utworzona aplikacja funkcji](./media/tutorial-process-email-attachments-workflow/function-app-created.png)

   Aby utworzyć aplikację funkcji, możesz też użyć [interfejsu wiersza polecenia platformy Azure](../azure-functions/functions-create-first-azure-function-azure-cli.md) lub [szablonów programu PowerShell i usługi Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

2. W obszarze **aplikacje funkcji**rozwiń aplikację funkcji, w której znajduje się wartość "CleanTextFunctionApp" w tym przykładzie, a następnie wybierz pozycję Functions ( **funkcje**). Na pasku narzędzi funkcji wybierz pozycję **Nowa funkcja**.

   ![Tworzenie nowej funkcji](./media/tutorial-process-email-attachments-workflow/function-app-new-function.png)

3. W obszarze **Wybierz szablon poniżej lub przejdź do przewodnika Szybki Start**wybierz szablon **wyzwalacza http** .

   ![Wybieranie szablonu wyzwalacza HTTP](./media/tutorial-process-email-attachments-workflow/function-select-httptrigger-csharp-function-template.png)

   Platforma Azure tworzy funkcję przy użyciu szablonu specyficznego dla języka dla funkcji wyzwalanej przez protokół HTTP.

4. W okienku **Nowa funkcja** w obszarze **Nazwa** wprowadź `RemoveHTMLFunction`. Zachowaj pozycję **Poziom autoryzacji** ustawioną na wartość **Funkcja** i wybierz pozycję **Utwórz**.

   ![Nadaj nazwę funkcji](./media/tutorial-process-email-attachments-workflow/function-provide-name.png)

5. Po otwarciu edytora zastąp kod szablonu poniższym kodem przykładowym, który usuwa kod HTML i zwraca wyniki do obiektu wywołującego:

   ``` CSharp
   #r "Newtonsoft.Json"

   using System.Net;
   using Microsoft.AspNetCore.Mvc;
   using Microsoft.Extensions.Primitives;
   using Newtonsoft.Json;
   using System.Text.RegularExpressions;

   public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
   {
      log.LogInformation("HttpWebhook triggered");

      // Parse query parameter
      string emailBodyContent = await new StreamReader(req.Body).ReadToEndAsync();

      // Replace HTML with other characters
      string updatedBody = Regex.Replace(emailBodyContent, "<.*?>", string.Empty);
      updatedBody = updatedBody.Replace("\\r\\n", " ");
      updatedBody = updatedBody.Replace(@"&nbsp;", " ");

      // Return cleaned text
      return (ActionResult)new OkObjectResult(new { updatedBody });
   }
   ```

6. Gdy wszystko będzie gotowe, wybierz pozycję **Zapisz**. Aby przetestować funkcję, przy prawej krawędzi edytora pod ikoną strzałki ( **<** ) wybierz pozycję **Test**.

   ![Otwieranie okienka „Test”](./media/tutorial-process-email-attachments-workflow/function-choose-test.png)

7. W okienku **Test** w obszarze **Treść żądania** wprowadź poniższy wiersz i wybierz pozycję **Uruchom**.

   ```json
   {"name": "<p><p>Testing my function</br></p></p>"}
   ```

   ![Testuj funkcję](./media/tutorial-process-email-attachments-workflow/function-run-test.png)

   W oknie **Dane wyjściowe** jest wyświetlany wynik funkcji:

   ```json
   {"updatedBody":"{\"name\": \"Testing my function\"}"}
   ```

Po sprawdzeniu, czy funkcja działa, utwórz aplikację logiki. Chociaż ten samouczek przedstawia sposób utworzenia funkcji usuwającej kod HTML z wiadomości e-mail, usługa Logic Apps również oferuje łącznik **HTML do tekstu**.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. W głównym menu platformy Azure wybierz pozycję **Utwórz zasób** > 
**Integracja** > **Aplikacja logiki**.

   ![Tworzenie aplikacji logiki](./media/tutorial-process-email-attachments-workflow/create-logic-app.png)

2. W obszarze **Tworzenie aplikacji logiki** wprowadź następujące informacje na temat aplikacji logiki. Gdy skończysz, wybierz pozycję **Przypnij do pulpitu nawigacyjnego** > **Utwórz**.

   ![Podawanie informacji na temat aplikacji logiki](./media/tutorial-process-email-attachments-workflow/create-logic-app-settings.png)

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Nazwa** | LA-ProcessAttachment | Nazwa aplikacji logiki |
   | **Subskrypcja** | <*your-Azure-subscription-name*> | Ta sama subskrypcja platformy Azure, której użyto wcześniej |
   | **Grupa zasobów** | LA-Tutorial-RG | Ta sama grupa zasobów platformy Azure, której użyto wcześniej |
   | **Location** | Zachodnie stany USA | Ten sam region, którego użyto wcześniej |
   | **Log Analytics** | Wyłączone | Na potrzeby tego samouczka wybierz ustawienie **Wyłączone**. |
   ||||

3. Po wdrożeniu aplikacji na platformie Azure zostanie otwarty Projektant aplikacji usługi Logic Apps ze stroną zawierającą wprowadzający film wideo i szablony typowych wzorców aplikacji logiki. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie szablonu pustej aplikacji logiki](./media/tutorial-process-email-attachments-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie nasłuchiwać przychodzących wiadomości e-mail zawierających załączniki. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku przez nowe dane. Aby uzyskać więcej informacji, zobacz [Tworzenie pierwszej aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="monitor-incoming-email"></a>Monitorowanie przychodzących wiadomości e-mail

1. W projektancie w polu wyszukiwania wprowadź ciąg „po nadejściu nowej wiadomości e-mail” jako filtr. Wybierz ten wyzwalacz dla dostawcy poczty e-mail: **Po nadejściu nowej wiadomości e-mail — <*dostawca poczty e-mail*>**

   Przykład:

   ![Wybierz ten wyzwalacz dla dostawcy poczty e-mail: „Po nadejściu nowej wiadomości e-mail”](./media/tutorial-process-email-attachments-workflow/add-trigger-when-email-arrives.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.

   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

2. Jeśli pojawi się prośba o podanie poświadczeń, zaloguj się do swojego konta e-mail, aby usługa Logic Apps mogła utworzyć połączenie z tym kontem e-mail.

3. Teraz podaj kryteria, których wyzwalacz będzie używać do filtrowania nowych wiadomości e-mail.

   1. Określ te ustawienia do sprawdzania wiadomości e-mail.

      ![Określanie folderu, interwału i częstotliwości sprawdzania wiadomości e-mail](./media/tutorial-process-email-attachments-workflow/set-up-email-trigger.png)

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Folder** | Skrzynka odbiorcza | Folder poczty e-mail do sprawdzania |
      | **Zawiera załącznik** | Tak | Pobieraj tylko wiadomości e-mail z załącznikami. <p>**Uwaga:** Wyzwalacz nie usuwa żadnych wiadomości e-mail z konta; sprawdza tylko nowe wiadomości i przetwarza tylko te wiadomości, które pasują do filtru tematu. |
      | **Uwzględnij załączniki** | Tak | Pobieraj załączniki jako dane wejściowe dla przepływu pracy, zamiast tylko sprawdzać wiadomości pod kątem istnienia załączników. |
      | **Interval** | 1 | Liczba interwałów do odczekania między sprawdzaniami |
      | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniami |
      ||||
  
   1. Z listy **Dodaj nowy parametr** wybierz opcję **Filtr podmiotu**.

   1. Po pojawieniu się pola **filtru podmiotu** w akcji Określ temat wymieniony tutaj:

      | Ustawienie | Wartość | Opis |
      | ------- | ----- | ----------- |
      | **Filtr tematu** | ```Business Analyst 2 #423501``` | Tekst do wyszukania w temacie wiadomości e-mail |
      ||||

4. Aby na razie ukryć szczegóły wyzwalacza, kliknij wewnątrz jego paska tytułu.

   ![Zwinięty kształt w celu ukrycia szczegółów](./media/tutorial-process-email-attachments-workflow/collapse-trigger-shape.png)

5. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

   Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem wiadomości e-mail. 
   Następnie dodaj warunek określający kryteria umożliwiające kontynuowanie przepływu pracy.

## <a name="check-for-attachments"></a>Sprawdzanie załączników

Teraz dodaj warunek, który powoduje wybranie tylko wiadomości e-mail z załącznikami.

1. W obszarze wyzwalacza wybierz pozycję **nowy krok**.

   !["Nowy krok"](./media/tutorial-process-email-attachments-workflow/add-condition-under-trigger.png)

2. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź wartość "warunek". Wybierz tę akcję: **Kontrola warunku**

   ![Wybierz pozycję "warunek"](./media/tutorial-process-email-attachments-workflow/select-condition.png)

   1. Zmień nazwę warunku na lepszy opis. 
   Na pasku tytułu warunku wybierz przycisk wielokropka ( **...** ), > **zmienić nazwę**.

      ![Zmienianie nazwy warunku](./media/tutorial-process-email-attachments-workflow/condition-rename.png)

   1. Zmień nazwę warunku na następujący opis: ```If email has attachments and key subject phrase```

3. Utwórz warunek, który wyszukuje wiadomości e-mail z załącznikami. 

   1. W pierwszym wierszu w obszarze **I** kliknij wewnątrz pola po lewej stronie. 
   Z wyświetlonej listy zawartości dynamicznej wybierz właściwość **Ma załącznik**.

      ![Kompilowanie warunku](./media/tutorial-process-email-attachments-workflow/build-condition.png)

   2. W środkowym polu zachowaj operator **jest równe**.

   3. W polu po prawej stronie wprowadź **true** jako wartość do porównania z wartością właściwości **Ma załącznik** z wyzwalacza.

      ![Kompilowanie warunku](./media/tutorial-process-email-attachments-workflow/finished-condition.png)

      Jeśli obie wartości są równe, wiadomość e-mail ma co najmniej jeden załącznik, warunek zostaje spełniony, a przepływ pracy jest kontynuowany.

   W podstawowej definicji aplikacji logiki, którą można wyświetlić w oknie edytora kodu, ten warunek wygląda jak poniższy przykład:

   ```json
   "Condition": {
      "actions": { <actions-to-run-when-condition-passes> },
      "expression": {
         "and": [ {
            "equals": [
               "@triggerBody()?['HasAttachment']",
                 "true"
            ]
         } ]
      },
      "runAfter": {},
      "type": "If"
   }
   ```

4. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

### <a name="test-your-condition"></a>Testowanie warunku

Teraz należy sprawdzić, czy warunek działa prawidłowo:

1. Jeśli aplikacja logiki jeszcze nie działa, wybierz pozycję **Uruchom** na pasku narzędzi projektanta.

   Ten krok spowoduje ręczne uruchomienie aplikacji logiki bez potrzeby oczekiwania na koniec określonego interwału. 
   Niemniej jednak nic się nie stanie do momentu otrzymania wiadomości e-mail w skrzynce odbiorczej. 

2. Wyślij do siebie wiadomość e-mail spełniającą następujące kryteria:

   * Temat wiadomości ma tekst określony w pozycji **Filtr tematu** wyzwalacza: ```Business Analyst 2 #423501```

   * Wiadomość e-mail zawiera jeden załącznik. 
   Teraz po prostu utwórz pusty plik tekstowy i załącz go do wiadomości e-mail.

   Po odebraniu wiadomości e-mail aplikacja logiki sprawdzi ją pod kątem załączników oraz określonego tekstu tematu.
   Jeśli warunek zostanie spełniony, wyzwalacz spowoduje utworzenie wystąpienia aplikacji logiki przez aparat usługi Logic Apps oraz uruchomienie przepływu pracy.

3. Aby sprawdzić, czy wyzwalacz zadziałał i aplikacja logiki została uruchomiona pomyślnie, w menu aplikacji logiki wybierz pozycję **Omówienie**.

   ![Sprawdzanie historii wyzwalacza i uruchomień](./media/tutorial-process-email-attachments-workflow/checkpoint-run-history.png)

   Jeśli aplikacja logiki nie została wyzwolona lub nie została uruchomiona mimo pomyślnego uruchomienia wyzwalacza, zobacz [Troubleshoot your logic app (Rozwiązywanie problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Następnie zdefiniuj akcje do wykonania w przypadku gałęzi **W przypadku wartości true**. Aby zapisać wiadomość e-mail wraz z załącznikami, usuń kod HTML z treści wiadomości e-mail, a następnie utwórz obiekty blob w kontenerze magazynu dla wiadomości e-mail i załączników.

> [!NOTE]
> Twoja aplikacja logiki nie musi podejmować żadnych działań dla gałęzi **W przypadku wartości false**, jeśli wiadomość e-mail nie ma załączników. W ramach dodatkowego ćwiczenia po zakończeniu tego samouczka możesz dodać wszelkie odpowiednie akcje, które powinny zostać wykonane dla gałęzi **W przypadku wartości false**.

## <a name="call-removehtmlfunction"></a>Wywoływanie funkcji RemoveHTMLFunction

Ten krok powoduje dodanie wcześniej utworzonej funkcji platformy Azure do aplikacji logiki i przekazanie zawartości treści wiadomości e-mail z wyzwalacza poczty e-mail do funkcji.

1. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki**. W gałęzi **W przypadku wartości true** wybierz pozycję **Dodaj akcję**.

   ![Wnętrze warunku „W przypadku wartości true”, dodawanie akcji](./media/tutorial-process-email-attachments-workflow/if-true-add-action.png)

2. W polu wyszukiwania znajdź frazę „azure functions” i wybierz tę akcję: **Wybierz funkcję platformy Azure — Azure Functions**

   ![Wybieranie akcji dla pozycji „Wybierz funkcję platformy Azure”](./media/tutorial-process-email-attachments-workflow/add-action-azure-function.png)

3. Wybierz wcześniej utworzoną aplikację funkcji, która jest w tym przykładzie "CleanTextFunctionApp":

   ![Wybieranie aplikacji funkcji platformy Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function-app.png)

4. Teraz wybierz funkcję: **RemoveHTMLFunction**

   ![Wybieranie funkcji platformy Azure](./media/tutorial-process-email-attachments-workflow/add-action-select-azure-function.png)

5. Zmień nazwę kształtu funkcji na ten opis: ```Call RemoveHTMLFunction to clean email body```

6. Teraz określ dane wejściowe funkcji do przetworzenia.

   1. W obszarze **Treść żądania** wprowadź poniższy tekst ze spacją końcową: 

      ```{ "emailBody":```

      Gdy będziesz pracować z tymi danymi wyjściowymi w następnych krokach, do momentu wprowadzenia tych danych w poprawnym formacie JSON będzie wyświetlany komunikat o błędzie nieprawidłowego kodu JSON.
      Podczas wcześniejszego testowania tej funkcji dane wejściowe określone dla funkcji używały formatu JavaScript Object Notation (JSON). 
      Dlatego treść żądania musi również używać tego samego formatu.

      Ponadto, gdy kursor znajduje się wewnątrz pola **Treść żądania**, pojawi się dynamiczna lista zawartości, z której będzie można wybrać dostępne wartości właściwości z poprzednich akcji.

   2. Z listy zawartości dynamicznej w obszarze **Po nadejściu nowej wiadomości e-mail** wybierz właściwość **Treść**. Pamiętaj, aby po tej właściwości dodać zamykający nawias klamrowy: ```}```

      ![Określanie treści żądania do przekazania do funkcji](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing.png)

   Gdy wszystko będzie gotowe, dane wejściowe funkcji będą wyglądać jak w poniższym przykładzie:

   ![Gotowa treść żądania do przekazania do funkcji](./media/tutorial-process-email-attachments-workflow/add-email-body-for-function-processing-2.png)

7. Zapisz aplikację logiki.

Następnie dodaj akcję, która utworzy obiekt blob w kontenerze magazynu, co umożliwi zapisywanie treści wiadomości e-mail.

## <a name="create-blob-for-email-body"></a>Tworzenie obiektu blob na potrzeby treści wiadomości e-mail

1. W bloku **W przypadku wartości true** i w obszarze funkcji platformy Azure wybierz pozycję **Dodaj akcję**.

2. W polu wyszukiwania wprowadź frazę „utwórz obiekt blob” jako filtr i wybierz następującą akcję: **Utwórz obiekt blob — Azure Blob Storage**

   ![Dodawanie akcji w celu utworzenia obiektu blob na potrzeby treści wiadomości e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-email-body.png)

3. Utwórz połączenie z kontem magazynu przy użyciu tych ustawień, jak pokazano i opisano w tym miejscu. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

   ![Tworzenie połączenia z kontem magazynu](./media/tutorial-process-email-attachments-workflow/create-storage-account-connection-first.png)

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Nazwa połączenia** | AttachmentStorageConnection | Nazwa opisowa połączenia |
   | **Konto magazynu** | attachmentstorageacct | Nazwa konta magazynu, który został utworzony wcześniej do zapisywania załączników |
   ||||

4. Zmień nazwę akcji **Utwórz obiekt blob** na następujący opis: ```Create blob for email body```

5. W akcji **Utwórz obiekt blob** podaj te informacje, a następnie wybierz te pola, aby utworzyć obiekt blob, jak pokazano i opisano w tym miejscu:

   ![Podawanie informacji o obiekcie blob na potrzeby treści wiadomości e-mail](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body.png)

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Ścieżka folderu** | /attachments | Ścieżka i nazwa wcześniej utworzonego kontenera. W tym przykładzie kliknij ikonę folderu, a następnie wybierz kontener „/attachments”. |
   | **Nazwa obiektu blob** | Pole **Od** | W tym przykładzie użyj nazwy nadawcy jako nazwy obiektu blob. Kliknij wewnątrz pola, aby wyświetlić dynamiczną listę zawartości, a następnie wybierz pole **Od** w obszarze akcji **Po nadejściu nowej wiadomości e-mail**. |
   | **Zawartość obiektu blob** | Pole **Zawartość** | W tym przykładzie użyj treści wiadomości e-mail bez kodu HTML jako zawartości obiektu blob. Kliknij wewnątrz pola, aby wyświetlić dynamiczną listę zawartości, a następnie wybierz pozycję **Treść** w obszarze akcji **Wywołaj funkcję RemoveHTMLFunction, aby wyczyścić treść wiadomości e-mail**. |
   ||||

   Gdy wszystko będzie gotowe, akcja będzie wyglądać jak następujący przykład:

   ![Zakończona akcja „Utwórz obiekt blob”](./media/tutorial-process-email-attachments-workflow/create-blob-for-email-body-done.png)

6. Zapisz aplikację logiki.

### <a name="check-attachment-handling"></a>Sprawdzanie obsługi załączników

Teraz przetestuj aplikację logiki pod kątem określonego sposobu obsługi wiadomości e-mail:

1. Jeśli aplikacja logiki jeszcze nie działa, wybierz pozycję **Uruchom** na pasku narzędzi projektanta.

2. Wyślij do siebie wiadomość e-mail spełniającą następujące kryteria:

   * Temat wiadomości ma tekst określony w pozycji **Filtr tematu** wyzwalacza: ```Business Analyst 2 #423501```

   * Wiadomość e-mail zawiera co najmniej jeden załącznik. 
   Teraz po prostu utwórz pusty plik tekstowy i załącz go do wiadomości e-mail.

   * Wiadomość e-mail ma testową zawartość w treści, na przykład: 

     ```text
     Testing my logic app
     ```

   Jeśli aplikacja logiki nie została wyzwolona lub nie została uruchomiona mimo pomyślnego uruchomienia wyzwalacza, zobacz [Troubleshoot your logic app (Rozwiązywanie problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

3. Sprawdź, czy aplikacja logiki zapisała wiadomość e-mail do prawidłowego kontenera magazynu.

   1. W Eksploratorze usługi Storage rozwiń pozycję **(Lokalne i dołączone)** > 
   **Konta magazynu** > **attachmentstorageacct (zewnętrzne)** > 
   **Kontenery obiektów blob** > **attachments**.

   2. Sprawdź kontener **attachments** pod kątem wiadomości e-mail.

      W tym momencie w kontenerze jest wyświetlana tylko wiadomość e-mail, ponieważ aplikacja logiki nie przetwarza jeszcze załączników.

      ![Sprawdzanie Eksploratora usługi Storage pod kątem zapisanej wiadomości e-mail](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-email.png)

   3. Gdy wszystko będzie gotowe, usuń wiadomość e-mail w Eksploratorze usługi Storage.

4. Opcjonalnie, aby przetestować gałąź **W przypadku wartości false**, która obecnie nic nie robi, możesz wysłać wiadomość e-mail, która nie spełnia kryteriów.

Następnie dodaj pętlę, aby przetwarzać wszystkie załączniki poczty e-mail.

## <a name="process-attachments"></a>Przetwarzanie załączników

Aby przetwarzać wszystkie załączniki do wiadomości e-mail, dodaj pętlę **For each** do przepływu pracy aplikacji logiki.

1. W obszarze kształt **Tworzenie obiektu BLOB dla treści wiadomości e-mail** wybierz pozycję **Dodaj akcję**.

   ![Dodawanie pętli „for each”](./media/tutorial-process-email-attachments-workflow/add-for-each-loop.png)

1. W obszarze **Wybierz akcję**w polu wyszukiwania wprowadź ciąg "for each" jako filtr. Wybierz tę akcję: **Dla każdej kontrolki**

   ![Wybierz opcję "for each"](./media/tutorial-process-email-attachments-workflow/select-for-each.png)

1. Zmień nazwę pętli na następujący opis: ```For each email attachment```

1. Teraz określ dane dla pętli do przetworzenia. Kliknij wewnątrz pola **Wybierz dane wyjściowe z poprzednich kroków**, aby otworzyć dynamiczną listę zawartości, a następnie wybierz pozycję **Załączniki**.

   ![Wybieranie pozycji „Załączniki”](./media/tutorial-process-email-attachments-workflow/select-attachments.png)

   Pole **Załączniki** przekazuje tablicę zawierającą wszystkie załączniki dołączone do wiadomości e-mail. 
   Pętla **For each** powtarza akcje wobec każdego elementu przekazanego w tablicy.

1. Zapisz aplikację logiki.

Następnie dodaj akcję, która zapisuje każdy załącznik jako obiekt blob w kontenerze magazynu **attachments**.

## <a name="create-blob-for-each-attachment"></a>Tworzenie obiektu blob dla każdego załącznika

1. W pętli **załącznika do wiadomości e-mail typu For each** wybierz pozycję **Dodaj akcję**, aby określić zadanie do wykonania wobec każdego znalezionego załącznika.

   ![Dodawanie akcji do pętli](./media/tutorial-process-email-attachments-workflow/for-each-add-action.png)

2. W polu wyszukiwania wprowadź frazę „utwórz obiekt blob” jako filtr, a następnie wybierz następującą akcję: **Utwórz obiekt blob — Azure Blob Storage**

   ![Dodawanie akcji umożliwiającej utworzenie obiektu blob](./media/tutorial-process-email-attachments-workflow/create-blob-action-for-attachments.png)

3. Zmień nazwę akcji **Utwórz obiekt blob 2** na następujący opis: ```Create blob for each email attachment```

4. W akcji **Utwórz obiekt blob dla każdego załącznika wiadomości e-mail** podaj te informacje, a następnie wybierz właściwości poszczególnych obiektów blob do utworzenia, tak jak pokazano i opisano tutaj:

   ![Podawanie informacji o obiekcie blob](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment.png)

   | Ustawienie | Wartość | Opis |
   | ------- | ----- | ----------- |
   | **Ścieżka folderu** | /attachments | Ścieżka i nazwa wcześniej utworzonego kontenera. W tym przykładzie kliknij ikonę folderu, a następnie wybierz kontener „/attachments”. |
   | **Nazwa obiektu blob** | Pole **Nazwa** | W tym przykładzie użyj nazwy załącznika jako nazwy obiektu blob. Kliknij wewnątrz pola, aby wyświetlić dynamiczną listę zawartości, a następnie wybierz pole **Nazwa** w obszarze akcji **Po nadejściu nowej wiadomości e-mail**. |
   | **Zawartość obiektu blob** | Pole **Zawartość** | W tym przykładzie użyj pola **Zawartość** jako zawartości obiektu blob. Kliknij wewnątrz tego pola, aby wyświetlić dynamiczną listę zawartości, a następnie wybierz pole **Zawartość** w obszarze akcji **Po nadejściu nowej wiadomości e-mail**. |
   ||||

   Gdy wszystko będzie gotowe, akcja będzie wyglądać jak następujący przykład:

   ![Zakończona akcja „Utwórz obiekt blob”](./media/tutorial-process-email-attachments-workflow/create-blob-per-attachment-done.png)

5. Zapisz aplikację logiki. 

### <a name="check-attachment-handling"></a>Sprawdzanie obsługi załączników

Następnie przetestuj aplikację logiki pod kątem określonego sposobu obsługi załączników:

1. Jeśli aplikacja logiki jeszcze nie działa, wybierz pozycję **Uruchom** na pasku narzędzi projektanta.

2. Wyślij do siebie wiadomość e-mail spełniającą następujące kryteria:

   * Temat wiadomości ma tekst określony w pozycji **Filtr tematu** wyzwalacza: ```Business Analyst 2 #423501```

   * Wiadomość e-mail zawiera co najmniej dwa załączniki. 
   Teraz po prostu utwórz dwa puste pliki tekstowe i załącz je do wiadomości e-mail.

   Jeśli aplikacja logiki nie została wyzwolona lub nie została uruchomiona mimo pomyślnego uruchomienia wyzwalacza, zobacz [Troubleshoot your logic app (Rozwiązywanie problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

3. Sprawdź, czy aplikacja logiki zapisała wiadomość e-mail oraz załączniki do prawidłowego kontenera magazynu. 

   1. W Eksploratorze usługi Storage rozwiń pozycję **(Lokalne i dołączone)** > 
   **Konta magazynu** > **attachmentstorageacct (zewnętrzne)** > 
   **Kontenery obiektów blob** > **attachments**.

   2. Sprawdź kontener **attachments** pod kątem wiadomości e-mail i załączników.

      ![Sprawdzanie zapisanej wiadomości e-mail i załączników](./media/tutorial-process-email-attachments-workflow/storage-explorer-saved-attachments.png)

   3. Gdy wszystko będzie gotowe, usuń wiadomość e-mail i załączniki w Eksploratorze usługi Storage.

Następnie dodaj akcję, dzięki której aplikacja logiki będzie wysyłać wiadomość e-mail w celu sprawdzenia załączników.

## <a name="send-email-notifications"></a>Wysyłanie powiadomień w wiadomościach e-mail

1. W gałęzi **W przypadku wartości true** w obszarze pętli **For each dla załączników wiadomości e-mail** wybierz opcję **Dodaj akcję**.

   ![Dodaj akcję w obszarze pętli „for each”](./media/tutorial-process-email-attachments-workflow/add-action-send-email.png)

2. W polu wyszukiwania wprowadź frazę „wyślij wiadomość e-mail” jako filtr, a następnie wybierz akcję „Wyślij wiadomość e-mail” dla dostawcy poczty e-mail.

   Aby na liście akcji wyświetlić tylko konkretną usługę, można najpierw wybrać łącznik.

   ![Wybieranie akcji „wyślij wiadomość e-mail” dla dostawcy poczty e-mail](./media/tutorial-process-email-attachments-workflow/add-action-select-send-email.png)

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.

   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

3. Jeśli pojawi się prośba o podanie poświadczeń, zaloguj się do swojego konta e-mail, aby usługa Logic Apps utworzyła połączenie z tym kontem e-mail.

4. Zmień nazwę akcji **Wyślij wiadomość e-mail** na następujący opis: ```Send email for review```

5. Podaj informacje dotyczące tej akcji i wybierz pola, które chcesz uwzględnić w wiadomości e-mail, tak jak pokazano i opisano. Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.  

   ![Wysyłanie powiadomienia w wiadomości e-mail](./media/tutorial-process-email-attachments-workflow/send-email-notification.png)

   Jeśli nie możesz znaleźć oczekiwanego pola na dynamicznej liście zawartości, wybierz pozycję **Zobacz więcej** obok pozycji **Po nadejściu nowej wiadomości e-mail**.

   | Ustawienie | Wartość | Uwagi | 
   | ------- | ----- | ----- | 
   | **Body** | ```Please review new applicant:``` <p>```Applicant name:``` **Od** <p>```Application file location:``` **Ścieżka** <p>```Application email content:``` **Treść** | Treść wiadomości e-mail. Kliknij wewnątrz tego pola, wprowadź przykładowy tekst i z dynamicznej listy zawartości wybierz następujące pola: <p>– Pole **Od** w obszarze **Po nadejściu nowej wiadomości e-mail** </br>– Pole **Ścieżka** w obszarze **Utwórz obiekt blob na potrzeby treści wiadomości e-mail** </br>– Pole **Treść** w obszarze **Wywołaj funkcję RemoveHTMLFunction, aby wyczyścić treść wiadomości e-mail** |
   | **Subject**  | ```ASAP - Review applicant for position:``` **Temat** | Temat wiadomości e-mail, który chcesz uwzględnić. Kliknij wewnątrz tego pola, wprowadź przykładowy tekst i z dynamicznej listy zawartości wybierz pole **Temat** w obszarze **Po nadejściu nowej wiadomości e-mail**. |
   | **To** | <*recipient-email-address*> | Do celów testowych możesz użyć własnego adresu e-mail. |
   ||||

   > [!NOTE]
   > Jeśli wybierzesz pole zawierające tablicę, takie jak **Zawartość**, które jest tablicą zawierającą załączniki, projektant automatycznie doda pętlę „For each” wokół akcji, która odwołuje się do tego pola. Dzięki temu Twoja aplikacja logiki może wykonać tę akcję dla każdego elementu tablicy. Aby usunąć pętlę, usuń pole dla tablicy, przenieś akcję odwołującą poza pętlę, wybierz wielokropek ( **...** ) na pasku tytułowym pętli, a następnie wybierz pozycję **Usuń**.

6. Zapisz aplikację logiki.

Teraz przetestuj aplikację logiki, która powinna wyglądać następująco:

![Ukończona aplikacja logiki](./media/tutorial-process-email-attachments-workflow/complete.png)

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

1. Wyślij do siebie wiadomość e-mail spełniającą następujące kryteria:

   * Temat wiadomości ma tekst określony w pozycji **Filtr tematu** wyzwalacza: ```Business Analyst 2 #423501```

   * Wiadomość e-mail zawiera co najmniej jeden załącznik. 
   Możesz ponownie użyć pustego pliku tekstowego z poprzedniego testu. 
   Aby zrealizować bardziej realistyczny scenariusz, dołącz plik z życiorysem.

   * Treść wiadomości e-mail zawiera tekst, który możesz skopiować i wkleić:

     ```text

     Name: Jamal Hartnett

     Street address: 12345 Anywhere Road

     City: Any Town

     State or Country: Any State

     Postal code: 00000

     Email address: jamhartnett@outlook.com

     Phone number: 000-000-0000

     Position: Business Analyst 2 #423501

     Technical skills: Dynamics CRM, MySQL, Microsoft SQL Server, JavaScript, Perl, Power BI, Tableau, Microsoft Office: Excel, Visio, Word, PowerPoint, SharePoint, and Outlook

     Professional skills: Data, process, workflow, statistics, risk analysis, modeling; technical writing, expert communicator and presenter, logical and analytical thinker, team builder, mediator, negotiator, self-starter, self-managing  

     Certifications: Six Sigma Green Belt, Lean Project Management

     Language skills: English, Mandarin, Spanish

     Education: Master of Business Administration
     ```

2. Uruchom aplikację logiki. W przypadku powodzenia aplikacja logiki wyśle wiadomość e-mail, która będzie wyglądać następująco:

   ![Powiadomienie e-mail wysłane przez aplikację logiki](./media/tutorial-process-email-attachments-workflow/email-notification.png)

   Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci. 
   Filtr wiadomości-śmieci Twojej poczty e-mail może przekierowywać tego rodzaju wiadomości. 
   W przeciwnym razie, jeśli nie masz pewności, czy aplikacja logiki została poprawnie uruchomiona, zobacz [Troubleshoot your logic app (Rozwiązywania problemów z aplikacją logiki)](../logic-apps/logic-apps-diagnosing-failures.md).

Gratulacje. Udało Ci się utworzyć i uruchomić aplikację logiki, która automatyzuje zadania w różnych usługach platformy Azure i wywołuje niestandardowy węzeł.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów zawierająca aplikację logiki i powiązane zasoby nie będzie już potrzebna, usuń ją. W menu głównym platformy Azure przejdź do pozycji **Grupy zasobów** i wybierz grupę zasobów aplikacji logiki. Wybierz pozycję **Usuń grupę zasobów**. Aby potwierdzić, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

![Usuwanie grupy zasobów aplikacji logiki](./media/tutorial-process-email-attachments-workflow/delete-resource-group.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku utworzono aplikację logiki, która przetwarza i przechowuje załączniki wiadomości e-mail przez integrowanie usług platformy Azure, takich jak Azure Storage i Azure Functions. Teraz dowiedz się więcej o innych łącznikach, których możesz używać do tworzenia aplikacji logiki.

> [!div class="nextstepaction"]
> [Dowiedz się więcej na temat łączników dla usługi Logic Apps](../connectors/apis-list.md)
