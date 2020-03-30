---
title: Uwierzytelnij usługę Azure Stream Analytics w usłudze Azure Data Lake Storage Gen1
description: W tym artykule opisano sposób używania tożsamości zarządzanych do uwierzytelniania zadania usługi Azure Stream Analytics na danych wyjściowych usługi Azure Data Lake Storage Gen1.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 04/08/2019
ms.custom: seodec18
ms.openlocfilehash: 01741ea56b9e6f55c1393e88fc7991d410c33119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254380"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Uwierzytelnij usługę Stream Analytics w usłudze Azure Data Lake Storage Gen1 przy użyciu tożsamości zarządzanych

Usługa Azure Stream Analytics obsługuje uwierzytelnianie tożsamości zarządzanej przy użyciu danych wyjściowych usługi Azure Data Lake Storage (ADLS) Gen1. Tożsamość jest aplikacją zarządzaną zarejestrowaną w usłudze Azure Active Directory, która reprezentuje dane zadanie usługi Stream Analytics i może służyć do uwierzytelniania do zasobu docelowego. Tożsamości zarządzane eliminują ograniczenia metod uwierzytelniania opartego na użytkownikach, takie jak konieczność ponownego uwierzytelnienia z powodu zmian hasła lub wygaśnięcia tokenu użytkownika, które występują co 90 dni. Ponadto tożsamości zarządzane pomagają w automatyzacji wdrożeń zadań usługi Stream Analytics, które są dane wyjściowe do usługi Azure Data Lake Storage Gen1.

W tym artykule przedstawiono trzy sposoby włączenia tożsamości zarządzanej dla zadania usługi Azure Stream Analytics, które wyprowadza się do usługi Azure Data Lake Storage Gen1 za pośrednictwem witryny Azure portal, wdrożenia szablonu usługi Azure Resource Manager i narzędzi usługi Azure Stream Analytics dla programu Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Portal Azure

1. Zacznij od utworzenia nowego zadania usługi Stream Analytics lub otwarcia istniejącego zadania w witrynie Azure portal. Na pasku menu znajdującym się po lewej stronie ekranu wybierz pozycję **Tożsamość zarządzana** znajdująca się w obszarze **Konfiguruj**.

   ![Konfigurowanie tożsamości zarządzanej usługi Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Z okna wyświetlanego po prawej stronie wybierz **pozycję Użyj tożsamości zarządzanej przypisanej** do systemu. Kliknij **przycisk Zapisz** do jednostki usługi dla tożsamości zadania usługi Stream Analytics w usłudze Azure Active Directory. Cykl życia nowo utworzonej tożsamości będzie zarządzany przez platformę Azure. Po usunięciu zadania usługi Stream Analytics skojarzona tożsamość (czyli podmiot usługi) jest automatycznie usuwana przez platformę Azure.

   Po zapisaniu konfiguracji identyfikator obiektu (OID) jednostki usługi jest wyświetlany jako identyfikator główny, jak pokazano poniżej:

   ![Stream Analytics service principal ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Podmiot usługi ma taką samą nazwę jak zadanie usługi Stream Analytics. Na przykład, jeśli nazwa zadania jest **MyASAJob**, nazwa jednostki usługi utworzone jest również **MyASAJob**.

3. W oknie właściwości danych wyjściowych ujścia wyjściowego ADLS Gen1 kliknij z listy rozwijanej Tryb uwierzytelniania i wybierz opcję **Tożsamość zarządzana **.

4. Wypełnij pozostałe właściwości. Aby dowiedzieć się więcej o tworzeniu danych wyjściowych ADLS, zobacz [Tworzenie danych wyjściowych magazynu jeziora z analizą strumienia.](../data-lake-store/data-lake-store-stream-analytics.md) Po zakończeniu kliknij pozycję **Zapisz**.

   ![Konfigurowanie usługi Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Przejdź do strony Przegląd usługi ADLS Gen1 i kliknij **Eksplorator danych**.

   ![Omówienie konfigurowania magazynu aplikacji Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. W okienku Eksplorator danych wybierz pozycję **Dostęp** i kliknij pozycję **Dodaj** w okienku programu Access.

   ![Konfigurowanie dostępu do magazynu usługi Data Lake](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. W polu tekstowym w okienku **Wybierz użytkownika lub grupę** wpisz nazwę jednostki usługi. Należy pamiętać, że nazwa jednostki usługi jest również nazwa odpowiedniego zadania usługi Stream Analytics. Po rozpoczęciu wpisywania nazwy głównej pojawi się pod polem tekstowym. Wybierz żądaną nazwę głównej usługi i kliknij przycisk **Wybierz**.

   ![Wybieranie nazwy jednostkowej usługi](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. W okienku **Uprawnienia** sprawdź uprawnienia **Zapisuj** i **wykonaj** i przypisz je do **tego folderu i wszystkich obrażeń .** Następnie kliknij **przycisk Ok**.

   ![Wybieranie uprawnień do zapisu i wykonywania](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Podmiot usługi jest wymieniony w obszarze **Uprawnienia przypisane** w okienku **programu Access,** jak pokazano poniżej. Teraz możesz wrócić i rozpocząć pracę usługi Stream Analytics.

   ![Lista dostępu usługi Stream Analytics w portalu](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Aby dowiedzieć się więcej o uprawnieniach systemu plików Data Lake Storage Gen1, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Narzędzia analizy strumienia dla programu Visual Studio

1. W pliku JobConfig.json ustaw **wartość True przydzieloną do systemu** na Wartość **True**.

   ![Tożsamości zarządzane przez konfigura zadań usługi Stream Analytics](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. W oknie właściwości danych wyjściowych ujścia wyjściowego ADLS Gen1 kliknij z listy rozwijanej Tryb uwierzytelniania i wybierz opcję **Tożsamość zarządzana **.

   ![Tożsamości zarządzane danych wyjściowych ADLS](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Wypełnij pozostałe właściwości, a następnie kliknij przycisk **Zapisz**.

4. Kliknij **pozycję Prześlij na platformę Azure** w edytorze zapytań.

   Po przesłaniu zadania narzędzia wykonują dwie czynności:

   * Automatycznie tworzy jednostkę usługi dla tożsamości zadania usługi Stream Analytics w usłudze Azure Active Directory. Cykl życia nowo utworzonej tożsamości będzie zarządzany przez platformę Azure. Po usunięciu zadania usługi Stream Analytics skojarzona tożsamość (czyli podmiot usługi) jest automatycznie usuwana przez platformę Azure.

   * Automatycznie ustaw uprawnienia **zapisuj** i **wykonaj** dla ścieżki prefiksu ADLS Gen1 używanej w zadaniu i przypisz ją do tego folderu i wszystkich obrażeń podrzędnych.

5. Można wygenerować szablony Menedżera zasobów za pomocą następującej właściwości przy użyciu [usługi Stream Analytics CI. Pakiet CD Nuget](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) w wersji 1.5.0 lub wyższej na komputerze kompilacji (poza programem Visual Studio). Wykonaj kroki wdrażania szablonu Menedżera zasobów w następnej sekcji, aby uzyskać jednostkę usługi i udzielić dostępu do jednostki usługi za pośrednictwem programu PowerShell.

## <a name="resource-manager-template-deployment"></a>Wdrażanie szablonu Menedżera zasobów

1. Zasób *Microsoft.StreamAnalytics/streamingjobs* z zarządzaną tożsamością można utworzyć, dołączając następującą właściwość w sekcji zasobów szablonu Menedżera zasobów:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Ta właściwość informuje usługę Azure Resource Manager, aby utworzyć i zarządzać tożsamości dla zadania usługi Azure Stream Analytics.

   **Przykładowe zadanie**
   
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
         }
       }
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
   }
   ```

   Zanotuj identyfikator główny z odpowiedzi zadania, aby udzielić dostępu do wymaganego zasobu ADLS.

   **Identyfikator dzierżawy** to identyfikator dzierżawy usługi Azure Active Directory, w której tworzona jest jednostka usługi. Podmiot zabezpieczeń usługi jest tworzony w dzierżawie platformy Azure, który jest zaufany przez subskrypcję.

   **Typ** wskazuje typ tożsamości zarządzanej, jak wyjaśniono w typach tożsamości zarządzanych. Obsługiwany jest tylko typ przypisany system.

2. Zapewnij dostęp do jednostki usługi przy użyciu programu PowerShell. Aby udzielić dostępu do jednostki usługi za pośrednictwem programu PowerShell, wykonaj następujące polecenie:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **Identyfikator główny** jest identyfikatorem obiektu jednostki usługi i jest wyświetlany na ekranie portalu po utworzeniu jednostki usługi. Jeśli zadanie utworzono przy użyciu wdrożenia szablonu Menedżera zasobów, identyfikator obiektu jest wyświetlany we właściwości Tożsamość odpowiedzi zadania.

   **Przykład**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Aby dowiedzieć się więcej na temat powyższego polecenia programu PowerShell, zapoznaj się z [dokumentacją Set-AzDataLakeStoreItemAclEntry.](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry)

## <a name="limitations"></a>Ograniczenia
Ta funkcja nie obsługuje następujących funkcji:

1. **Dostęp dla wielu dzierżawców:** podmiot zabezpieczeń usługi utworzony dla danego zadania usługi Stream Analytics będzie znajdować się w dzierżawie usługi Azure Active Directory, na której utworzono zadanie, i nie może być używany względem zasobu, który znajduje się w innej dzierżawie usługi Azure Active Directory. W związku z tym można używać tylko msi na ADLS Gen 1 zasobów, które znajdują się w tej samej dzierżawy usługi Azure Active Directory jako zadanie usługi Azure Stream Analytics. 

2. **[Tożsamość przypisana przez użytkownika:](../active-directory/managed-identities-azure-resources/overview.md)** nie jest obsługiwana. Oznacza to, że użytkownik nie może wprowadzić własnego podmiotu usługi, który ma być używany przez zadanie usługi Stream Analytics. Podmiot usługi jest generowany przez usługę Azure Stream Analytics.

## <a name="next-steps"></a>Następne kroki

* [Tworzenie danych wyjściowych magazynu usługi Data lake za pomocą analizy strumienia](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testowanie zapytań usługi Stream Analytics lokalnie za pomocą programu Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testowanie danych na żywo lokalnie przy użyciu narzędzi usługi Azure Stream Analytics dla programu Visual Studio](stream-analytics-live-data-local-testing.md) 
