---
title: Uwierzytelnianie usługi Azure Stream Analytics zadania usługi Azure Data Lake Storage Gen1 w danych wyjściowych
description: W tym artykule opisano, jak korzystać z zarządzanych tożsamości do uwierzytelniania usługi Azure Data Lake Storage Gen1 danych wyjściowych zadania usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/8/2019
ms.custom: seodec18
ms.openlocfilehash: 9eb66a9000c9add0718c6edf6674a26ce8e479b3
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257981"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Uwierzytelnianie Stream Analytics do usługi Azure Data Lake Storage Gen1 za pomocą tożsamości zarządzanych

Usługa Azure Stream Analytics obsługuje uwierzytelnianie tożsamości zarządzanej z danymi wyjściowymi Gen1 usługi Azure Data Lake Storage (ADLS). Tożsamość jest zarządzanej aplikacji zarejestrowanych w usłudze Azure Active Directory, który reprezentuje dane zadanie usługi Stream Analytics i może być używany do uwierzytelniania zasobów docelowych. Zarządzanych tożsamości wyeliminować ograniczenia metody uwierzytelniania oparte na użytkownikach, takich jak konieczności ponownego uwierzytelnienia z powodu zmiany hasła lub wygaśnięcia tokenu użytkownika, które występują co 90 dni. Ponadto zarządzanych tożsamości pomóc automatyzację wdrożenia zadania usługi Stream Analytics, które dane wyjściowe do usługi Azure Data Lake Storage Gen1.

W tym artykule przedstawiono trzy sposoby, aby włączyć tożsamość zarządzaną przez zadanie usługi Azure Stream Analytics, która danych wyjściowych do usługi Azure Data Lake magazynu Gen1 za pośrednictwem witryny Azure portal, wdrażanie szablonu usługi Azure Resource Manager i Azure Stream Analytics tools for Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Zacznij od utworzenia nowego zadania usługi Stream Analytics lub przez otwarcie istniejącego zadania w witrynie Azure portal. Na pasku menu, znajdujący się po lewej stronie ekranu, wybierz **tożsamości zarządzanej** znajdujący się w folderze **Konfiguruj**.

   ![Konfigurowanie tożsamości usługi Stream Analytics zarządzane](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Wybierz **przypisane użycia systemu tożsamości zarządzanej** z okna, który pojawia się po prawej stronie. Kliknij przycisk **Zapisz** dla jednostki usługi tożsamości zadania usługi Stream Analytics w usłudze Azure Active Directory. Cykl życia tożsamości nowo utworzony będą zarządzane przez platformę Azure. Po usunięciu zadania usługi Stream Analytics skojarzone tożsamości (nazwy głównej usługi) jest automatycznie usuwany przez platformę Azure.

   Podczas zapisywania konfiguracji identyfikator obiektu (OID) nazwy głównej usługi jest wymieniony jako identyfikator podmiotu zabezpieczeń, jak pokazano poniżej:

   ![Identyfikator jednostki usługi Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Jednostka usługi ma taką samą nazwę jak zadanie usługi Stream Analytics. Na przykład, jeśli nazwa zadania jest **MyASAJob**, nazwa utworzono nazwę główną usługi jest również **MyASAJob**.

3. W oknie właściwości danych wyjściowych ujścia danych wyjściowych Gen1 Azure Data Lake Store kliknij tryb uwierzytelniania, listy rozwijanej i wybierz pozycję ** tożsamości zarządzanej **.

4. Wypełnij pozostałych właściwości. Aby dowiedzieć się więcej na temat tworzenia danych wyjściowych usługi ADLS, zobacz [tworzenie wyjściowego Data lake Store za pomocą usługi stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). Gdy skończysz, kliknij przycisk **Zapisz**.

   ![Konfigurowanie usługi Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Przejdź do strony Przegląd Gen1 usługi ADLS i kliknij **Eksplorator danych**.

   ![Konfigurowanie danych Lake magazynu — omówienie](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. W okienku Eksploratora danych, wybierz **dostępu** i kliknij przycisk **Dodaj** w panelu dostępu.

   ![Konfigurowanie dostępu do magazynu jeziora danych](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. W polu tekstowym na **zaznacz użytkownika lub grupę** okienko, wpisz nazwę jednostki usługi. Należy pamiętać, że nazwę jednostki usługi jest również nazwa odpowiedniego zadania usługi Stream Analytics. Po rozpoczęciu, wpisując nazwę główną, pojawi się poniżej pola tekstowego. Wybierz nazwę główną usługi żądany, a następnie kliknij przycisk **wybierz**.

   ![Wybierz nazwę główną usługi](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. W **uprawnienia** okienku wyboru **zapisu** i **Execute** uprawnienia i przypisz ją do **ten Folder i wszystkie obiekty podrzędne**. Następnie kliknij przycisk **Ok**.

   ![Wybierz opcję uprawnień zapisu i wykonywania](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Nazwa główna usługi jest wyświetlana w obszarze **przypisane uprawnienia** na **dostępu** okienko, jak pokazano poniżej. Możesz teraz Przejdź wstecz i uruchomić zadania usługi Stream Analytics.

   ![Stream Analytics, dostęp do listy w portalu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Aby dowiedzieć się więcej na temat uprawnień systemu plików Data Lake Storage Gen1, zobacz [kontroli dostępu w usłudze Azure Data Lake magazynu Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Narzędzia Stream Analytics tools for Visual Studio

1. W JobConfig.json, ustaw **przypisane użycia systemu tożsamości** do **True**.

   ![Konfiguracji zadania usługi Stream Analytics zarządzanych tożsamości](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. W oknie właściwości danych wyjściowych ujścia danych wyjściowych Gen1 Azure Data Lake Store kliknij tryb uwierzytelniania, listy rozwijanej i wybierz pozycję ** tożsamości zarządzanej **.

   ![Azure Data Lake Store, dane wyjściowe zarządzanych tożsamości](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Wypełnij pozostałe właściwości, a następnie kliknij przycisk **Zapisz**.

4. Kliknij przycisk **przesyłania na platformie Azure** w edytorze zapytań.

   Po przesłaniu zadania narzędzia wykonać dwie czynności:

   * Automatycznie tworzy usługę podmiotu zabezpieczeń dla tożsamości zadania usługi Stream Analytics w usłudze Azure Active Directory. Cykl życia tożsamości nowo utworzony będą zarządzane przez platformę Azure. Po usunięciu zadania usługi Stream Analytics skojarzone tożsamości (nazwy głównej usługi) jest automatycznie usuwany przez platformę Azure.

   * Automatycznie ustaw **zapisu** i **Execute** uprawnień dla ADLS Gen1 prefiksu ścieżki używanej w ramach zadania i przypisać ją do tego folderu i wszystkich obiektów podrzędnych.

5. Można wygenerować szablonów usługi Resource Manager za pomocą następujących przy użyciu właściwości [Stream Analytics w ciągłej integracji. Pakiet CD Nuget](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) wersji 1.5.0 lub nowszej na maszynie kompilacji (poza programem Visual Studio). Postępuj zgodnie z usługi Resource Manager do wdrażania szablonu kroki opisane w następnej sekcji, aby pobrać usługę podmiotu zabezpieczeń i udzielać dostępu do jednostki za pomocą programu PowerShell usługi.

## <a name="resource-manager-template-deployment"></a>Wdrażanie szablonu usługi Resource Manager

1. Możesz utworzyć *Microsoft.StreamAnalytics/streamingjobs* zasobów za pomocą tożsamości zarządzanej, umieszczając następujące właściwości w sekcji zasobów w szablonie usługi Resource Manager:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ta właściwość zawiera informacje dla usługi Azure Resource Manager do tworzenia i zarządzania tożsamościami dla zadania usługi Azure Stream Analytics.

   **Przykładowe zadania**

    ```json
    {
      "Name": "AsaJobWithIdentity",
      "Type": "Microsoft.StreamAnalytics/streamingjobs",
      "Location": "West US",
      "Identity": {
        "Type": "SystemAssigned",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
        "outputs": [
          {
            "name": "string",
            "properties":{
              "datasource": {
                "type": "Microsoft.DataLake/Accounts",
                "properties": {
                  "accountName": "myDataLakeAccountName",
                  "filePathPrefix": "cluster1/logs/{date}/{time}",
                  "dateFormat": "YYYY/MM/DD",
                  "timeFormat": "HH",
                  "authenticationMode": "Msi"
                }
              }
   ```
  
   **Przykładowa odpowiedź zadania**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
      }
   ```

   Zanotuj identyfikator podmiotu zabezpieczeń z odpowiedzi zadania, aby udzielić dostępu do wymaganych zasobów usługi ADLS.

   **Identyfikator dzierżawy** jest identyfikator dzierżawy usługi Azure Active Directory, w którym zostanie utworzona jednostka usługi. Nazwa główna usługi jest tworzony w dzierżawie platformy Azure, która jest zaufana dla subskrypcji.

   **Typu** wskazuje typ tożsamości zarządzanej, jak wyjaśniono w typach zarządzanych tożsamości. Obsługiwany jest tylko typ przypisana przez System.

2. Zapewniają dostęp do jednostki usługi przy użyciu programu PowerShell. Aby udzielić dostępu do jednostki za pomocą programu PowerShell usługi, wykonaj następujące polecenie:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** jest identyfikator obiektu nazwy głównej usługi i znajduje się na ekranie portalu po utworzeniu nazwy głównej usługi. Jeśli utworzono zadanie przy użyciu wdrożenia szablonu usługi Resource Manager, identyfikator obiektu znajduje się we właściwości Identity odpowiedzi zadania.

   **Przykład**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Aby dowiedzieć się więcej na temat powyższego polecenia programu PowerShell, zapoznaj się [AzDataLakeStoreItemAclEntry zestaw](https://docs.microsoft.com/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) dokumentacji.

## <a name="limitations"></a>Ograniczenia
Ta funkcja nie obsługuje następujących działań:

1.  **Dostęp do wielu dzierżawców**: Nazwa główna usługi utworzone dla danego zadania usługi Stream Analytics będą znajdować się w dzierżawie usługi Azure Active Directory, na którym utworzono zadanie i nie można używać wobec zasobu, który znajduje się w innej dzierżawie usługi Azure Active Directory. W związku z tym tylko służy MSI na zasobach ADLS generacji 1, które znajdują się w tej samej dzierżawie usługi Azure Active Directory jako zadanie Azure Stream Analytics. 

2.  **[Tożsamość użytkownika z przypisaną](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**: nie jest obsługiwane. oznacza to, użytkownik nie będzie mógł wprowadzić własne jednostki usługi, który będzie używany przez ich zadania usługi Stream Analytics. Nazwa główna usługi jest generowany przez usługę Azure Stream Analytics. 


## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie wyjściowego Data lake Store za pomocą usługi stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie z programem Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testowanie danych na żywo lokalnie przy użyciu usługi Azure Stream Analytics tools for Visual Studio](stream-analytics-live-data-local-testing.md) 
