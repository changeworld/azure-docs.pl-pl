---
title: Konfigurowanie ról dla usługi w chmurze Azure z programem Visual Studio | Dokumentacja firmy Microsoft
description: Informacje o sposobie instalowania i konfigurowania ról dla usług w chmurze Azure przy użyciu programu Visual Studio.
services: visual-studio-online
author: ghogen
manager: douge
assetId: d397ef87-64e5-401a-aad5-7f83f1022e16
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.workload: azure
ms.topic: conceptual
ms.date: 03/21/2017
ms.author: ghogen
ms.openlocfilehash: 09e6c3a9c27342ef27d49674d62ccf74d70d2e0f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="configure-azure-cloud-service-roles-with-visual-studio"></a>Skonfigurować role usługi w chmurze Azure z programem Visual Studio
Usługi w chmurze Azure może mieć co najmniej jednego procesu roboczego lub role sieci web. Dla każdej roli należy zdefiniować sposób konfigurowania tej roli, a także skonfigurować sposób uruchamiania tej roli. Aby dowiedzieć się więcej na temat ról usług w chmurze, zobacz wideo [wprowadzenie do usługi w chmurze Azure](https://channel9.msdn.com/Series/Windows-Azure-Cloud-Services-Tutorials/Introduction-to-Windows-Azure-Cloud-Services). 

Informacje dla usługi w chmurze są przechowywane w następujących plikach:

- **ServiceDefinition.csdef** -pliku definicji usługi definiuje ustawienia środowiska uruchomieniowego dla usługi chmury, w tym role, jakie są wymagane, punktów końcowych i rozmiar maszyny wirtualnej. Brak danych przechowywanych w `ServiceDefinition.csdef` można zmieniać w przypadku roli użytkownika jest uruchomiona.
- **Pliku ServiceConfiguration.cscfg** — plik konfiguracji usługi konfiguruje liczbę wystąpień roli są uruchamiane i wartości ustawienia zdefiniowane dla roli. Dane przechowywane w `ServiceConfiguration.cscfg` można zmienić uruchomionej roli użytkownika.

Aby zapisać różne wartości ustawień, które kontrolują sposób uruchamiania roli, można zdefiniować wiele konfiguracji usługi. W przypadku używania konfiguracji innej usługi, dla poszczególnych środowisk wdrażania. Na przykład można ustawić parametry połączenia konta magazynu, tak aby użyć emulatora magazynu Azure lokalnej konfiguracji Usługa lokalna i utworzyć inną konfigurację usługi do użycia usługi Azure storage w chmurze.

Podczas tworzenia usługi w chmurze platformy Azure w programie Visual Studio, dwóch konfiguracji usługi są automatycznie tworzone i dodane do projektu platformy Azure:

- `ServiceConfiguration.Cloud.cscfg`
- `ServiceConfiguration.Local.cscfg`

## <a name="configure-an-azure-cloud-service"></a>Konfigurowanie usługi w chmurze Azure
Usługi w chmurze platformy Azure w Eksploratorze rozwiązania można skonfigurować w programie Visual Studio, jak pokazano w poniższych krokach:

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz z menu kontekstowego **właściwości**.
   
    ![Menu kontekstowe projektu Eksploratora rozwiązań](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-project-context-menu.png)

1. Na stronie właściwości projektu, zaznacz **programowanie** kartę. 

    ![Strona właściwości projektu — karta programowanie](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-development-tab.png)

1. W **konfiguracji usługi** listy, wybierz nazwę konfiguracji usługi, które chcesz edytować. (Wprowadzić zmiany w konfiguracji usługi dla tej roli, należy zaznaczyć **wszystkie konfiguracje**.)
   
    > [!IMPORTANT]
    > Jeśli wybierzesz opcję konfiguracji określonej usługi, niektóre właściwości są wyłączone, ponieważ ich można ustawić tylko w przypadku wszystkich konfiguracji. Aby edytować te właściwości, należy wybrać **wszystkie konfiguracje**.
    > 
    > 
   
    ![Lista konfiguracji usługi dla usługi w chmurze Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/cloud-service-service-configuration-property.png)

## <a name="change-the-number-of-role-instances"></a>Zmień liczbę wystąpień roli
Aby poprawić wydajność usługi w chmurze, można zmienić liczbę wystąpień roli, które działają na podstawie liczby użytkowników lub obciążenia dla określonej roli. Oddzielnej maszynie wirtualnej jest tworzony dla każdego wystąpienia roli, po uruchomieniu usługi w chmurze na platformie Azure. Ma to wpływ na rozliczenia dla wdrożenia tej usługi w chmurze. Aby uzyskać więcej informacji dotyczących rozliczeń, zobacz [zrozumieć rachunku platformy Microsoft Azure](billing/billing-understand-your-bill.md).

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu. W obszarze **ról** węzła, kliknij prawym przyciskiem myszy rolę, które chcesz zaktualizować, a z menu kontekstowego wybierz **właściwości**.

    ![Menu kontekstowe roli Azure Eksploratora rozwiązań](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Wybierz **konfiguracji** kartę.

    ![Karta Konfiguracja](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page.png)

1. W **konfiguracji usługi** wybierz konfigurację usługi, które chcesz zaktualizować.
   
    ![Konfiguracji usługi na liście](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-select-configuration.png)

1. W **wystąpienia licznika** tekst Wprowadź liczbę wystąpień, które chcesz uruchomić dla tej roli. Każde wystąpienie działa na oddzielnej maszynie wirtualnej po opublikowaniu usługi w chmurze na platformie Azure.

    ![Aktualizowanie liczba wystąpień](./media/vs-azure-tools-configure-roles-for-cloud-service/role-configuration-properties-page-instance-count.png)

1. W programie Visual Studio narzędzi, wybierz opcję **zapisać**.

## <a name="manage-connection-strings-for-storage-accounts"></a>Zarządzanie parametry połączenia dla konta magazynu
Można dodać, usunąć ani zmodyfikować parametrów połączenia dla konfiguracji usługi. Na przykład może być ciąg połączenia lokalnej konfiguracji usługi lokalnej, która ma wartość `UseDevelopmentStorage=true`. Można również konfigurację usługi chmury, który używa konta magazynu na platformie Azure.

> [!WARNING]
> Po wprowadzeniu informacji klucza konta magazynu platformy Azure dla parametrów połączenia konta magazynu, te informacje są przechowywane lokalnie w pliku konfiguracji usługi. Jednak ta informacje obecnie nie są przechowywane jako tekst zaszyfrowany.
> 
> 

Za pomocą innej wartości dla każdej konfiguracji usługi, nie trzeba w usłudze w chmurze, użyj parametrów połączenia różnych lub modyfikowania kodu podczas publikowania usługi w chmurze na platformie Azure. Można użyć tej samej nazwie w ciągu połączenia w kodzie i wartość jest inny, na podstawie wybranej podczas tworzenia usługi w chmurze lub podczas jego publikowania konfiguracji usługi.

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu. W obszarze **ról** węzła, kliknij prawym przyciskiem myszy rolę, które chcesz zaktualizować, a z menu kontekstowego wybierz **właściwości**.

    ![Menu kontekstowe roli Azure Eksploratora rozwiązań](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Wybierz **ustawienia** kartę.

    ![Karta Ustawienia](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. W **konfiguracji usługi** wybierz konfigurację usługi, które chcesz zaktualizować.

    ![Konfiguracja usługi](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Aby dodać parametry połączenia, zaznacz **Dodaj ustawienie**.

    ![Dodaj ciąg połączenia](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Po dodaniu nowe ustawienie do listy aktualizacji wiersza na liście niezbędne informacje.

    ![Nowe parametry połączenia](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nazwa** — wprowadź nazwę, która ma być używany dla parametrów połączenia.
    - **Typ** — wybierz tę opcję **ciąg połączenia** z listy rozwijanej.
    - **Wartość** — można wprowadzić parametry połączenia bezpośrednio do **wartość** komórki lub wybierz wielokropek (...) do pracy w **utworzyć parametry połączenia magazynu** okna dialogowego.  

1. W **utworzyć parametry połączenia magazynu** okno dialogowe, wybierz opcję **łączyć się przy użyciu**. Następnie postępuj zgodnie z instrukcjami dotyczącymi wybranej opcji:

    - **Emulator magazynu Microsoft Azure** — Jeśli wybierzesz tę opcję, pozostałe ustawienia w oknie dialogowym są wyłączone, ponieważ mają one zastosowanie tylko do platformy Azure. Kliknij przycisk **OK**.
    - **Subskrypcja** — po wybraniu tej opcji, za pomocą listy rozwijanej można wybrać i zaloguj się do konta Microsoft lub dodać konta Microsoft. Wybierz konto platformy Azure subskrypcji i magazynu. Kliknij przycisk **OK**.
    - **Ręcznie wprowadzić poświadczenia** — wprowadź nazwę konta magazynu i klucz podstawowy lub drugiego. Wybierz opcję wyznaczenia **połączenia** (HTTPS jest zalecane dla większości scenariuszy). Wybierz **OK**.

1. Aby usunąć ciąg połączenia, wybierz parametry połączenia, a następnie wybierz **Usuń ustawienie**.

1. W programie Visual Studio narzędzi, wybierz opcję **zapisać**.

## <a name="programmatically-access-a-connection-string"></a>Uzyskania programowego dostępu do parametrów połączenia

Poniższe kroki przedstawiają sposób uzyskania programowego dostępu do ciągu połączenia przy użyciu języka C#.

1. Dodaj następujący kod przy użyciu dyrektywy w pliku C# Jeżeli zamierzasz użyć ustawienia:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Poniższy kod przedstawia przykład sposobu otwierania ciąg połączenia. Zastąp &lt;ConnectionStringName > symbolu zastępczego na odpowiednią wartość. 

    ```csharp
    // Setup the connection to Azure Storage
    var storageAccount = CloudStorageAccount.Parse(RoleEnvironment.GetConfigurationSettingValue("<ConnectionStringName>"));
    ```

## <a name="add-custom-settings-to-use-in-your-azure-cloud-service"></a>Dodaj niestandardowe ustawienia do użycia w usłudze w chmurze Azure
Ustawienia niestandardowe w pliku konfiguracji usługi pozwalają Dodaj nazwę i wartość ciągu dla określonej usługi konfiguracji. Można to ustawienie służy do konfigurowania funkcji w usłudze w chmurze przez odczytanie wartość ustawienia i kontrolowania logiki w kodzie za pomocą tej wartości. Bez konieczności Skompiluj ponownie pakiet usługi lub gdy jest uruchomiona usługa w chmurze, można zmienić tych wartości konfiguracji usługi. Powiadomienia o kodzie można sprawdzić podczas zmiany ustawień. Zobacz [RoleEnvironment.Changing zdarzeń](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.changing.aspx).

Można dodać, usunąć ani zmodyfikować własne ustawienia konfiguracji usługi. Może być różne wartości dla tych ciągów w przypadku konfiguracji z innej usługi.

Za pomocą innej wartości dla każdej konfiguracji usługi, nie trzeba Użyj różnych parametrów w usłudze w chmurze lub modyfikowania kodu podczas publikowania usługi w chmurze na platformie Azure. Można użyć takiej samej nazwy ciągu w kodzie i wartość jest inny, na podstawie wybranej podczas tworzenia usługi w chmurze lub podczas jego publikowania konfiguracji usługi.

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu. W obszarze **ról** węzła, kliknij prawym przyciskiem myszy rolę, które chcesz zaktualizować, a z menu kontekstowego wybierz **właściwości**.

    ![Menu kontekstowe roli Azure Eksploratora rozwiązań](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Wybierz **ustawienia** kartę.

    ![Karta Ustawienia](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab.png)

1. W **konfiguracji usługi** wybierz konfigurację usługi, które chcesz zaktualizować.

    ![Konfiguracji usługi na liście](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-select-configuration.png)

1. Aby dodać niestandardową wartość ustawienia, wybierz **Dodaj ustawienie**.

    ![Dodaj niestandardową wartość ustawienia](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting.png)

1. Po dodaniu nowe ustawienie do listy aktualizacji wiersza na liście niezbędne informacje.

    ![Nowe ustawienie niestandardowe](./media/vs-azure-tools-configure-roles-for-cloud-service/project-properties-settings-tab-add-setting-new-setting.png)

    - **Nazwa** — wprowadź nazwę ustawienia.
    - **Typ** — wybierz tę opcję **ciąg** z listy rozwijanej.
    - **Wartość** — wprowadź wartość ustawienia. Można wprowadzić wartości bezpośrednio do **wartość** komórki lub wybierz wielokropek (...), aby wprowadzić wartość **Edytowanie ciągu** okna dialogowego.  

1. Aby usunąć ustawienia niestandardowe, wybierz ustawienie, a następnie wybierz **Usuń ustawienie**.

1. W programie Visual Studio narzędzi, wybierz opcję **zapisać**.

## <a name="programmatically-access-a-custom-settings-value"></a>Uzyskania programowego dostępu do wartości ustawienia niestandardowego
 
Poniższe kroki przedstawiają sposób uzyskania programowego dostępu do ustawienia niestandardowego przy użyciu języka C#.

1. Dodaj następujący kod przy użyciu dyrektywy w pliku C# Jeżeli zamierzasz użyć ustawienia:

    ```csharp
    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.ServiceRuntime;
    ```

1. Poniższy kod przedstawia przykład sposobu otwierania ustawienia niestandardowego. Zastąp &lt;SettingName > symbolu zastępczego na odpowiednią wartość. 
    
    ```csharp
    var settingValue = RoleEnvironment.GetConfigurationSettingValue("<SettingName>");
    ```

## <a name="manage-local-storage-for-each-role-instance"></a>Zarządzanie magazynem lokalnym dla każdego wystąpienia roli
Możesz dodać magazyn systemu pliku lokalnego dla każdego wystąpienia roli. Dane przechowywane w tym magazynu nie jest dostępny przez innych wystąpień roli, dla którego dane są przechowywane lub innych ról.  

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, rozwiń węzeł projektu. W obszarze **ról** węzła, kliknij prawym przyciskiem myszy rolę, które chcesz zaktualizować, a z menu kontekstowego wybierz **właściwości**.

    ![Menu kontekstowe roli Azure Eksploratora rozwiązań](./media/vs-azure-tools-configure-roles-for-cloud-service/solution-explorer-azure-role-context-menu.png)

1. Wybierz **Magazyn lokalny** kartę.

    ![Karta magazynu lokalnego](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab.png)

1. W **konfiguracji usługi** listy, upewnij się, że **wszystkie konfiguracje** wybrano jako ustawienia magazynu lokalnego mają zastosowanie do wszystkich konfiguracji usługi. Powoduje wszelkie inne wartości pól wejściowych na stronie wyłączana. 

    ![Konfiguracji usługi na liście](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-service-configuration.png)

1. Aby dodać wpis magazynu lokalnego, wybierz **dodać magazyn lokalny**.

    ![Dodaj magazyn lokalny](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-add-local-storage.png)

1. Po dodaniu nowego wpisu w lokalnej pamięci masowej do listy aktualizacji wiersza na liście niezbędne informacje.

    ![Nowy wpis w lokalnej pamięci masowej](./media/vs-azure-tools-configure-roles-for-cloud-service/role-local-storage-tab-new-local-storage.png)

    - **Nazwa** — wprowadź nazwę, która ma być używany dla nowego magazynu lokalnego.
    - **Rozmiar (MB)** — wprowadź rozmiar w MB potrzebnym do nowego magazynu lokalnego.
    - **Wyczyść na roli odtworzenia** — wybierz tę opcję, aby usunąć dane w nowym magazynie lokalnym podczas odtwarzania dla roli maszyny wirtualnej.

1. Aby usunąć wpis magazynu lokalnego, wybierz wpis, a następnie wybierz **usunąć magazyn lokalny**.

1. W programie Visual Studio narzędzi, wybierz opcję **zapisać**.

## <a name="programmatically-accessing-local-storage"></a>Uzyskiwania dostępu do lokalnego magazynu

W tej sekcji przedstawiono sposób uzyskania programowego dostępu do magazynu lokalnego przy użyciu języka C# zapisując plik tekstowy testu `MyLocalStorageTest.txt`.  

### <a name="write-a-text-file-to-local-storage"></a>Wpisywanie tekstu do pliku do lokalnego magazynu

Poniższy kod przedstawia przykład sposobu wpisywanie tekstu do pliku do magazynu lokalnego. Zastąp &lt;LocalStorageName > symbolu zastępczego na odpowiednią wartość. 

    ```csharp
    // Retrieve an object that points to the local storage resource
    LocalResource localResource = RoleEnvironment.GetLocalResource("<LocalStorageName>");
    
    //Define the file name and path
    string[] paths = { localResource.RootPath, "MyLocalStorageTest.txt" };
    String filePath = Path.Combine(paths);
    
    using (FileStream writeStream = File.Create(filePath))
    {
        Byte[] textToWrite = new UTF8Encoding(true).GetBytes("Testing Web role storage");
        writeStream.Write(textToWrite, 0, textToWrite.Length);
    }

    ```

### <a name="find-a-file-written-to-local-storage"></a>Znajdź plik zapisany w lokalnym magazynie

Aby wyświetlić plik utworzony przez kod w poprzedniej sekcji, wykonaj następujące kroki:
    
1.  W obszarze powiadomień systemu Windows kliknij prawym przyciskiem myszy ikonę platformy Azure i, z menu kontekstowego wybierz **Pokaż interfejs użytkownika emulatora obliczeń**. 

    ![Pokaż emulatora obliczeń platformy Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/show-compute-emulator.png)

1. Wybierz rolę sieci web.

    ![Emulator obliczeń platformy Azure](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator.png)

1. Na **Microsoft Azure obliczeniowe emulatora** menu, wybierz opcję **narzędzia** > **Otwórz magazyn lokalny**.

    ![Otwórz magazyn lokalny element menu](./media/vs-azure-tools-configure-roles-for-cloud-service/compute-emulator-open-local-store-menu.png)

1. Po otwarciu okna Eksploratora Windows, wprowadź "MyLocalStorageTest.txt" do **wyszukiwania** pola tekstowego, a następnie wybierz **Enter** ma się rozpocząć wyszukiwanie. 

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o Azure projekty w programie Visual Studio, odczytując [Konfigurowanie projektu platformy Azure](vs-azure-tools-configuring-an-azure-project.md). Dowiedz się więcej o schematu usługi chmury, odczytując [odwołanie do schematu](https://msdn.microsoft.com/library/azure/dd179398).

