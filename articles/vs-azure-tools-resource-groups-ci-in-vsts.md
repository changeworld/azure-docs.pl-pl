---
title: Ciągła integracja w VS Team Services za pomocą projekty grupy zasobów platformy Azure | Dokumentacja firmy Microsoft
description: Zawiera opis sposobu konfigurowania ciągłej integracji w programie Visual Studio Team Services za pomocą projekty wdrażania grup zasobów platformy Azure w programie Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mlearned
manager: erickson-doug
editor: ''
ms.assetid: b81c172a-be87-4adc-861e-d20b94be9e38
ms.service: azure-resource-manager
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: mlearned
ms.openlocfilehash: fc5a45c899cd72c051dd08f7db039565a57381a7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
ms.locfileid: "32192948"
---
# <a name="continuous-integration-in-visual-studio-team-services-using-azure-resource-group-deployment-projects"></a>Ciągła integracja w programie Visual Studio Team Services za pomocą projekty wdrażania grup zasobów platformy Azure
Do wdrożenia szablonu platformy Azure, możesz wykonać zadania w różnych etapach: kopiowania budowania, testowania na platformie Azure (zwane również "Tymczasowości") i wdrażania szablonu. Istnieją dwa różne sposoby wdrażania szablonów w programie Visual Studio Team Services (VS Team Services). Obie metody zapewniają te same wyniki, dlatego wybierz jedną, która najlepiej pasuje do przepływu pracy.

1. Dodaj pojedynczy krok do definicji kompilacji, który uruchamia skrypt programu PowerShell, który jest zawarty w projekcie wdrożenia grupy zasobów platformy Azure (wdrażanie AzureResourceGroup.ps1). Skrypt kopiuje artefaktów, a następnie wdraża szablonu.
2. Dodaj wiele VS Team Services kroki procesu kompilacji, każda z nich zrealizowane etapu.

W tym artykule przedstawiono obie opcje. Pierwsza opcja ma zaletą używania tego samego skryptu, używany przez deweloperów w programie Visual Studio i udostępnienie spójności w całym cyklu życia. Drugą opcją można używać zamiast wbudowanego skryptu. Obu procedurach założono, że masz już Projekt wdrożenia programu Visual Studio w wersji programu VS Team Services.

## <a name="copy-artifacts-to-azure"></a>Kopiowanie artefaktów na platformie Azure
Niezależnie od tego, w tym scenariuszu jeśli masz wszelkie artefakty, które są wymagane do wdrożenia szablonu należy przyznać dostęp do usługi Azure Resource Manager do ich. Tych artefaktów może zawierać pliki takie jak:

* Zagnieżdżone szablony
* Konfiguracja i skryptów DSC
* Pliki binarne aplikacji

### <a name="nested-templates-and-configuration-scripts"></a>Zagnieżdżone szablony i skrypty do konfiguracji
Jeśli używasz szablonów dostarczonych przez program Visual Studio (lub skompilowanej za pomocą wstawki kodu programu Visual Studio), skrypt programu PowerShell nie tylko przygotuje artefaktów, również parameterizes identyfikator URI dla zasobów dla różnych wdrożeń. Skrypt, a następnie kopiuje artefakty do bezpiecznego kontenera na platformie Azure, tworzy token SaS dla kontenera i następnie przekazuje informację do wdrażania szablonu. Zobacz [Utwórz wdrożenie szablonu](https://msdn.microsoft.com/library/azure/dn790564.aspx) Aby dowiedzieć się więcej na temat zagnieżdżone szablony.  Przy użyciu zadań w programie VS Team Services, należy wybrać odpowiedni zadań do wdrożenia szablonu i w razie potrzeby podać wartości parametrów z kroku przemieszczania do wdrożenia szablonu.

## <a name="set-up-continuous-deployment-in-vs-team-services"></a>Konfigurowanie ciągłego wdrażania w programie VS Team Services
Aby wywołać skrypt programu PowerShell w wersji programu VS Team Services, należy zaktualizować definicję kompilacji. Krótko mówiąc kroki są: 

1. Edytowanie definicji kompilacji.
2. Konfigurowanie autoryzacji Azure w wersji programu VS Team Services.
3. Dodaj krok kompilacji programu Azure PowerShell, który odwołuje się do skryptu programu PowerShell w projekcie wdrożenia grupy zasobów platformy Azure.
4. Ustaw wartość *- ArtifactsStagingDirectory* parametru do pracy z projektem wbudowane VS Team Services.

### <a name="detailed-walkthrough-for-option-1"></a>Szczegółowy przewodnik dotyczący opcję 1
Poniższe procedury prowadzą użytkownika przez kroki niezbędne do skonfigurowania ciągłe wdrażanie w VS Team Services za pomocą jednego zadania, które uruchamia skrypt programu PowerShell w projekcie. 

1. Edytowanie definicji kompilacji programu VS Team Services i dodać kroku kompilacji programu Azure PowerShell. Wybierz definicję kompilacji w obszarze **definicje kompilacji** kategorii, a następnie wybierz **Edytuj** łącza.
   
   ![Edytowanie definicji kompilacji][0]
2. Dodaj nową **programu Azure PowerShell** kompilacji krok do definicji kompilacji, a następnie wybierz pozycję **Dodaj krok kompilacji...** Dodaj...
   
   ![Dodawanie kroku kompilacji][1]
3. Wybierz **zadań Wdróż** kategorii, wybierz opcję **programu Azure PowerShell** zadań, a następnie wybierz pozycję jego **Dodaj** przycisku.
   
   ![Dodawanie zadań][2]
4. Wybierz **programu Azure PowerShell** kroku kompilacji, a następnie wypełnij jego wartości.
   
   1. Jeśli masz już dodany do programu VS Team Services punktu końcowego usługi Azure, wybierz subskrypcję w **subskrypcji Azure** pole listy rozwijanej, a następnie przejdź do następnej sekcji. 
      
      Jeśli nie masz punkt końcowy usługi Azure w wersji programu VS Team Services, musisz dodać jeden. W tej podsekcji przeprowadza użytkownika przez proces. Jeśli konto Azure korzysta z konta Microsoft (na przykład usługi Hotmail), należy wykonać następujące kroki, aby pobrać uwierzytelnianie nazwy głównej usługi.
   2. Wybierz **Zarządzaj** znajdujący się obok podsekcji **subskrypcji Azure** pole listy rozwijanej.
      
      ![Zarządzać subskrypcjami platformy Azure][3]
   3. Wybierz **Azure** w **nowy punkt końcowy usługi** pole listy rozwijanej.
      
      ![Nowy punkt końcowy usługi][4]
   4. W **dodawania subskrypcji platformy Azure** okno dialogowe, wybierz opcję **nazwy głównej usługi** opcji.
      
      ![Opcja głównej usługi][5]
   5. Dodawanie informacji o subskrypcji platformy Azure do **dodawania subskrypcji platformy Azure** okno dialogowe. Należy podać następujące elementy:
      
      * Identyfikator subskrypcji
      * Nazwa subskrypcji
      * Identyfikator nazwy głównej usługi
      * Klucz nazwy głównej usługi
      * Identyfikator dzierżawy
   6. Dodaj nazwę wybór **subskrypcji** pole Nazwa. Ta wartość jest później wyświetlana w **subskrypcji Azure** listy rozwijanej w VS Team Services. 
   7. Jeśli nie znasz identyfikator subskrypcji platformy Azure, można użyć jednej z poniższych poleceń, można go pobrać.
      
      Dla skryptów programu PowerShell użyj polecenia:
      
      `Get-AzureRmSubscription`
      
      W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:
      
      `azure account show`
   8. Można pobrać Identyfikatora nazwy głównej usługi, klucz nazwy głównej usługi i identyfikator dzierżawy, postępuj zgodnie z procedurą w [aplikacji usługi Active Directory Utwórz i nazwę główną usługi za pomocą portalu](resource-group-create-service-principal-portal.md) lub [uwierzytelniania nazwy głównej usługi z usługą Azure Resource Manager](resource-group-authenticate-service-principal.md).
   9. Dodawanie wartości Identyfikatora nazwy głównej usługi, klucz nazwy głównej usługi i identyfikator dzierżawcy do **dodawania subskrypcji platformy Azure** okna dialogowego polu, a następnie wybierz pozycję **OK** przycisku.
      
      Obecnie masz prawidłowe nazwy głównej usługi służące do uruchamiania skryptu programu PowerShell systemu Azure.
5. Edytowanie definicji kompilacji, a następnie wybierz pozycję **programu Azure PowerShell** kroku kompilacji. Wybierz subskrypcję w **subskrypcji Azure** pole listy rozwijanej. (Jeśli nie ma subskrypcji, wybierz **Odśwież** obok przycisku **Zarządzaj** łącza.) 
   
   ![Skonfiguruj zadania kompilacji programu Azure PowerShell][8]
6. Podaj ścieżkę do skryptu programu PowerShell AzureResourceGroup.ps1 wdrażania. Aby to zrobić, wybierz przycisk wielokropka (...) **ścieżka skryptu** polu, przejdź do skryptu programu PowerShell AzureResourceGroup.ps1 Wdróż w **skryptów** folderu projektu, zaznacz go, a następnie wybierz **OK** przycisku.    
   
   ![Wybierz ścieżkę do skryptu][9]
7. Po wybraniu skryptu zaktualizuj ścieżkę do skryptu, dzięki czemu jest uruchamiany z Build.StagingDirectory (tym samym katalogu który *ArtifactsLocation* ma ustawioną wartość). Można to zrobić przez dodanie "$(Build.StagingDirectory)/"na początku ścieżka skryptu.
   
    ![Edytuj ścieżka do skryptu][10]
8. W **argumenty skryptu** wprowadź następujące parametry (w jednym wierszu). Po uruchomieniu skryptu w programie Visual Studio, możesz sprawdzić, jak VS używa parametrów w **dane wyjściowe** okna. Służy to jako punktu wyjścia do ustawiania wartości parametrów w Twojej kroku kompilacji.
   
   | Parametr | Opis |
   | --- | --- |
   | -ResourceGroupLocation |Wartość lokalizacja geograficzna, których grupa zasobów znajduje się, takich jak **eastus** lub **wschodnie stany USA**. (Dodaj apostrofy, jeśli jest spacja w nazwie). Zobacz [regiony platformy Azure](https://azure.microsoft.com/regions/) Aby uzyskać więcej informacji. |
   | -ResourceGroupName |Nazwa grupy zasobów, użyty dla tego wdrożenia. |
   | -UploadArtifacts |Tego parametru, jeśli jest obecny, określa, że artefakty, które należy przekazać do platformy Azure z systemu lokalnego. Należy ustawić ten przełącznik, jeśli wdrożenie szablonu wymaga artefakty dodatkowe, które mają być etap przy użyciu skryptu środowiska PowerShell (na przykład skrypty do konfiguracji lub zagnieżdżone szablony). |
   | -StorageAccountName |Nazwa konta magazynu, użyty do etapu artefaktów dla tego wdrożenia. Ten parametr jest używany tylko, jeśli są przemieszczania artefakty do wdrożenia. Jeśli podano tego parametru, jeśli skrypt nie został utworzony podczas poprzedniego wdrożenia jest tworzone nowe konto magazynu. Jeśli parametr jest określony, konta magazynu musi już istnieć. |
   | -StorageAccountResourceGroupName |Nazwa grupy zasobów, skojarzone z kontem magazynu. Ten parametr jest wymagany tylko wtedy, gdy Podaj wartość dla parametru StorageAccountName. |
   | -TemplateFile |Ścieżka do pliku szablonu w projekcie wdrożenia grupy zasobów platformy Azure. Aby zwiększyć elastyczność, należy użyć ścieżki dla tego parametru, który względną wobec lokalizacji skrypt programu PowerShell, a nie ścieżką bezwzględną. |
   | -TemplateParametersFile |Ścieżka do pliku parametrów w projekcie wdrożenia grupy zasobów platformy Azure. Aby zwiększyć elastyczność, należy użyć ścieżki dla tego parametru, który względną wobec lokalizacji skrypt programu PowerShell, a nie ścieżką bezwzględną. |
   | -ArtifactStagingDirectory |Ten parametr umożliwia programu PowerShell skryptu znany folder, z którym powinny zostać skopiowane pliki binarne projektu. Ta wartość zastępuje wartości domyślne używane przez skrypt programu PowerShell. Użyć wersji programu VS Team Services, ustaw wartość: $(Build.StagingDirectory) - ArtifactStagingDirectory |
   
   Oto przykładowy skrypt argumentów (podzielone dla czytelności wiersz):
   
   ```    
   -ResourceGroupName 'MyGroup' -ResourceGroupLocation 'eastus' -TemplateFile '..\templates\azuredeploy.json' 
   -TemplateParametersFile '..\templates\azuredeploy.parameters.json' -UploadArtifacts -StorageAccountName 'mystorageacct' 
   –StorageAccountResourceGroupName 'Default-Storage-EastUS' -ArtifactStagingDirectory '$(Build.StagingDirectory)'    
   ```
   
   Po zakończeniu, **argumenty skryptu** pole powinno przypominać następujące listy:
   
   ![Argumenty skryptu][11]
9. Po dodaniu wszystkie elementy wymagane do kroku kompilacji programu Azure PowerShell, wybierz **kolejki** kompilacji przycisk, aby skompilować projekt. **Kompilacji** ekranie zostaną wyświetlone dane wyjściowe skryptu programu PowerShell.

### <a name="detailed-walkthrough-for-option-2"></a>Szczegółowy przewodnik dotyczący opcja 2
Poniższe procedury przeprowadzenie czynności niezbędnych do skonfigurowania ciągłe wdrażanie w VS Team Services za pomocą wbudowanych zadań.

1. Edytuj definicję kompilacji programu VS Team Services, aby dodać dwie nowe kroki procesu kompilacji. Wybierz definicję kompilacji w obszarze **definicje kompilacji** kategorii, a następnie wybierz **Edytuj** łącza.
   
   ![Edytowanie definicji kompilacji][12]
2. Dodaj nowe kroki procesu kompilacji do definicji kompilacji przy użyciu **Dodaj krok kompilacji...** Dodaj...
   
   ![Dodawanie kroku kompilacji][13]
3. Wybierz **Wdróż** kategorii zadań, wybierz opcję **Azure File Copy** zadań, a następnie wybierz pozycję jego **Dodaj** przycisku.
   
   ![Dodaj zadanie kopiowania plików na platformę Azure][14]
4. Wybierz **wdrożenie grupy zasobów Azure** zadań, a następnie wybierz jego **Dodaj** przycisk, a następnie **Zamknij** **katalogu zadania**.
   
   ![Zadanie wdrażania dodać grupy zasobów platformy Azure][15]
5. Wybierz **Azure File Copy** zadań i podać jego wartości.
   
   Jeśli masz już dodany do programu VS Team Services punktu końcowego usługi Azure, wybierz subskrypcję w **subskrypcji Azure** pole listy rozwijanej. Jeśli nie masz subskrypcji, zobacz [opcję 1](#detailed-walkthrough-for-option-1) instrukcje dotyczące konfigurowania co w usłudze VS Team Services.
   
   * Źródło — wprowadź **$(Build.StagingDirectory)**
   * Typ połączenia Azure - wybierz **usługi Azure Resource Manager**
   * Subskrypcja platformy Azure RM — Wybierz subskrypcję dla konta magazynu, którego chcesz użyć w **subskrypcji Azure** pole listy rozwijanej. Jeśli nie ma subskrypcji, wybierz **Odśwież** obok przycisku **Zarządzaj** łącza.
   * Typ docelowy — wybierz **obiektów Blob platformy Azure**
   * RM konta magazynu — wybierz konto magazynu, ma zostać użyta do przemieszczania artefaktów
   * Nazwa kontenera — wprowadź nazwę kontenera chcesz użyć dla przemieszczania; go może być dowolną nazwę prawidłowego kontenera, ale użyj jednej tej definicji kompilacji w wersji dedykowanej
   
   Wartości danych wyjściowych:
   
   * Wprowadź kontenera magazynu URI - **artifactsLocation**
   * Token sygnatury dostępu Współdzielonego kontenera magazynu — wprowadź **artifactsLocationSasToken**
   
   ![Skonfiguruj zadania kopiowania plików na platformę Azure][16]
6. Wybierz **wdrożenie grupy zasobów Azure** kroku kompilacji, a następnie wypełnij jego wartości.
   
   * Typ połączenia Azure - wybierz **usługi Azure Resource Manager**
   * Subskrypcja platformy Azure RM — Wybierz subskrypcję do wdrożenia w **subskrypcji Azure** pole listy rozwijanej. Są to zazwyczaj tej samej subskrypcji, które są używane w poprzednim kroku
   * Akcja — wybierz **tworzenia lub aktualizacji grupy zasobów**
   * Grupa zasobów — wybierz grupę zasobów lub wprowadź nazwę nowej grupy zasobów dla wdrożenia
   * Lokalizacja — wybierz lokalizację dla grupy zasobów
   * Szablon — wprowadź ścieżkę i nazwę szablonu, aby wdrożyć dołączanie **$(Build.StagingDirectory)**, na przykład: **$(Build.StagingDirectory/DSC-CI/azuredeploy.json)**
   * Parametry szablonu — wprowadź ścieżkę i nazwę parametrów, które mają być używane, dołączanie **$(Build.StagingDirectory)**, na przykład: **$(Build.StagingDirectory/DSC-CI/azuredeploy.parameters.json)**
   * Zastąp parametrów szablonu — wprowadź lub skopiuj i wklej następujący kod:
     
     ```    
     -_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken (ConvertTo-SecureString -String "$(artifactsLocationSasToken)" -AsPlainText -Force)
     ```
     ![Skonfiguruj zadania wdrażania grupy zasobów platformy Azure][17]
7. Po dodaniu wszystkich wymaganych elementów, zapisać definicję kompilacji i wybierz polecenie **nowej kompilacji w kolejce** u góry.

## <a name="next-steps"></a>Kolejne kroki
Odczyt [Omówienie usługi Azure Resource Manager](azure-resource-manager/resource-group-overview.md) Aby dowiedzieć się więcej na temat usługi Azure Resource Manager i grup zasobów platformy Azure.

[0]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough1.png
[1]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough2.png
[2]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough3.png
[3]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough4.png
[4]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough5.png
[5]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough6.png
[8]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough9.png
[9]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough10.png
[10]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough11b.png
[11]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough12.png
[12]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough13.png
[13]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough14.png
[14]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough15.png
[15]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough16.png
[16]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough17.png
[17]: ./media/vs-azure-tools-resource-groups-ci-in-vsts/walkthrough18.png
