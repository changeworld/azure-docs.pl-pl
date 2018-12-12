---
title: Uwierzytelnianie usługi Azure Stream Analytics zadania usługi Azure Data Lake Storage Gen1 w danych wyjściowych
description: W tym artykule opisano, jak korzystać z zarządzanych tożsamości do uwierzytelniania usługi Azure Data Lake Storage Gen1 danych wyjściowych zadania usługi Azure Stream Analytics.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bb25f237450a83a34645ad4dfd9a2839c5525c6f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53090435"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities-preview"></a>Uwierzytelnianie Stream Analytics do usługi Azure Data Lake Storage Gen1 przy użyciu tożsamości zarządzanej (wersja zapoznawcza)

Usługa Azure Stream Analytics obsługuje uwierzytelnianie tożsamości zarządzanej z danymi wyjściowymi Gen1 usługi Azure Data Lake Storage (ADLS). Tożsamość jest zarządzanej aplikacji zarejestrowanych w usłudze Azure Active Directory, który reprezentuje dane zadanie usługi Stream Analytics i może być używany do uwierzytelniania zasobów docelowych. Zarządzanych tożsamości wyeliminować ograniczenia metody uwierzytelniania oparte na użytkownikach, takich jak konieczności ponownego uwierzytelnienia z powodu zmiany hasła lub wygaśnięcia tokenu użytkownika, które występują co 90 dni. Ponadto zarządzanych tożsamości pomóc automatyzację wdrożenia zadania usługi Stream Analytics, które dane wyjściowe do usługi Azure Data Lake Storage Gen1.

Odwiedź stronę [osiem nowych funkcji w usłudze Azure Stream Analytics](https://azure.microsoft.com/blog/eight-new-features-in-azure-stream-analytics/) wpis w blogu, zarejestruj się w tej wersji zapoznawczej i Dowiedz się więcej o nowych funkcjach.

W tym artykule przedstawiono Włączanie zarządzanych tożsamości dla zadania usługi Azure Stream Analytics, która wysyła do usługi Azure Data Lake Storage Gen1 na dwa sposoby: za pośrednictwem witryny Azure portal oraz wdrażanie szablonu usługi Azure Resource Manager.

## <a name="enable-managed-identity-with-azure-portal"></a>Włącz tożsamości zarządzanej przy użyciu witryny Azure portal

1. Zacznij od utworzenia nowego zadania usługi Stream Analytics lub przez otwarcie istniejącego zadania w witrynie Azure portal. Na pasku menu, znajdujący się po lewej stronie ekranu, wybierz **tożsamości zarządzanej (wersja zapoznawcza)** znajdujący się w folderze **Konfiguruj**.

   ![Konfigurowanie tożsamości zarządzanej usługi Stream Analytics w wersji zapoznawczej](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Wybierz **przypisane użycia systemu tożsamości zarządzanej (wersja zapoznawcza)** z okna, który pojawia się po prawej stronie. Kliknij przycisk **Zapisz** do tworzenia nazwy głównej usługi dla tożsamości zadania usługi Stream Analytics w usłudze Azure Active Directory. Cykl życia tożsamości nowo utworzony będą zarządzane przez platformę Azure. Po usunięciu zadania usługi Stream Analytics skojarzone tożsamości (nazwy głównej usługi) jest automatycznie usuwany przez platformę Azure.

   Podczas zapisywania konfiguracji identyfikator obiektu (OID) nazwy głównej usługi jest wymieniony jako identyfikator podmiotu zabezpieczeń, jak pokazano poniżej:

   ![Identyfikator jednostki usługi Stream Analytics](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Jednostka usługi ma taką samą nazwę jak zadanie usługi Stream Analytics. Na przykład, jeśli nazwa zadania jest **MyASAJob**, nazwa utworzono nazwę główną usługi jest również **MyASAJob**.

3. W oknie właściwości danych wyjściowych ujścia danych wyjściowych Gen1 Azure Data Lake Store kliknij przycisk listy rozwijanej i wybierz tryb uwierzytelniania **tożsamości zarządzanej (wersja zapoznawcza)**.

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
                 "accountName": “myDataLakeAccountName",
                 "filePathPrefix": “cluster1/logs/{date}/{time}",
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
   Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** jest identyfikator obiektu nazwy głównej usługi i znajduje się na ekranie portalu po utworzeniu nazwy głównej usługi. Jeśli utworzono zadanie przy użyciu wdrożenia szablonu usługi Resource Manager, identyfikator obiektu znajduje się we właściwości Identity odpowiedzi zadania.

   **Przykład**

   ```powershell
   PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Aby dowiedzieć się więcej na temat powyższego polecenia programu PowerShell, zapoznaj się [AzureRmDataLakeStoreItemAclEntry zestaw](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) dokumentacji.

## <a name="next-steps"></a>Kolejne kroki

* [Tworzenie wyjściowego Data lake Store za pomocą usługi stream analytics](../data-lake-store/data-lake-store-stream-analytics.md)
