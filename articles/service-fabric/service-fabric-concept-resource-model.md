---
title: Model zasobów aplikacji platformy Azure Service Fabric | Microsoft Docs
description: Ten artykuł zawiera omówienie zarządzania aplikacją Service Fabric platformy Azure przy użyciu Azure Resource Manager
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: atsenthi
ms.openlocfilehash: dcffc1ba783b49343bf3380b62c3d4085f5aa347
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390091"
---
# <a name="what-is-the-service-fabric-application-resource-model"></a>Co to jest model zasobów aplikacji Service Fabric?
Zaleca się, aby Service Fabric aplikacje zostały wdrożone w klastrze Service Fabric za pośrednictwem Azure Resource Manager. Dzięki tej metodzie można opisać aplikacje i usługi w formacie JSON i wdrożyć je w tym samym szablonie Menedżer zasobów co klaster. W przeciwieństwie do wdrażania aplikacji i zarządzania nimi za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure nie trzeba czekać, aż klaster będzie gotowy. Proces rejestracji, aprowizacji i wdrażania aplikacji może obejmować tylko jeden krok. Jest to najlepsze rozwiązanie w zakresie zarządzania cyklem życiowym aplikacji w klastrze. Aby uzyskać więcej informacji, zapoznaj się z [najlepszymi rozwiązaniami](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code#azure-service-fabric-resources).

W razie potrzeby Zarządzaj aplikacjami jako zasobami Menedżer zasobów, aby zwiększyć:
* Dziennik inspekcji: Menedżer zasobów przeprowadza inspekcję każdej operacji i utrzymuje szczegółowy *Dziennik aktywności* , który może pomóc w śledzeniu wszelkich zmian wprowadzonych w tych aplikacjach i w klastrze.
* Kontrola dostępu oparta na rolach: zarządzanie dostępem do klastrów oraz aplikacji wdrożonych w klastrze można wykonać za pomocą tego samego szablonu Menedżer zasobów.
* Azure Resource Manager (za pośrednictwem Azure Portal) stanie się jednym zatrzymywaniem do zarządzania klastrem i krytycznymi wdrożeniami aplikacji.

## <a name="service-fabric-application-life-cycle-with-azure-resource-manager"></a>Cykl życia aplikacji Service Fabric z Azure Resource Manager 
W tym dokumencie dowiesz się, jak:

> [!div class="checklist"]
> * Wdrażanie zasobów aplikacji przy użyciu Azure Resource Manager 
> * Uaktualnianie zasobów aplikacji przy użyciu Azure Resource Manager
> * Usuwanie zasobów aplikacji

## <a name="deploy-application-resources-using-azure-resource-manager"></a>Wdrażanie zasobów aplikacji przy użyciu Azure Resource Manager  
Aby wdrożyć aplikację i jej usługi przy użyciu Azure Resource Manager modelu zasobów aplikacji, należy spakować kod aplikacji, przekazać pakiet, a następnie odwołać się do lokalizacji pakietu w szablonie Azure Resource Manager jako aplikacji zasoby. Aby uzyskać więcej informacji, zobacz [pakowanie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg).
          
Następnie Utwórz szablon Azure Resource Manager, zaktualizuj plik parametrów przy użyciu szczegółów aplikacji i Wdróż go w klastrze Service Fabric. W [tym miejscu](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM)zapoznaj się z przykładami.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu 
Wdrożenie aplikacji na podstawie szablonu Menedżer zasobów wymaga konta magazynu na potrzeby przygotowania obrazu aplikacji. Możesz ponownie użyć istniejącego konta magazynu lub utworzyć nowe konto magazynu w celu przygotowania aplikacji. Jeśli chcesz użyć istniejącego konta magazynu, możesz pominąć ten krok. 

![Tworzenie konta magazynu][CreateStorageAccount]

### <a name="configure-storage-account"></a>Skonfiguruj konto magazynu 
Po utworzeniu konta magazynu należy utworzyć kontener obiektów blob, w którym można przemieszczać aplikacje. W Azure Portal przejdź do konta magazynu, na którym chcesz przechowywać aplikacje. Wybierz blok **obiekty blob** , a następnie kliknij przycisk **Dodaj kontener** . Dodaj nowy kontener z poziomem dostępu publicznego obiektu BLOB.
   
![Utwórz obiekt BLOB][CreateBlob]

### <a name="stage-application-in-a-storage-account"></a>Przygotowanie aplikacji na koncie magazynu
Aby można było wdrożyć aplikację, należy ją przemieszczać w usłudze BLOB Storage. W tym samouczku utworzysz pakiet aplikacji ręcznie, jednak ten krok będzie można zautomatyzować.  Aby uzyskać więcej informacji, zobacz [pakowanie aplikacji](https://docs.microsoft.com/azure/service-fabric/service-fabric-package-apps#create-an-sfpkg). W poniższych krokach zostanie użyta [Przykładowa aplikacja do głosowania](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart) .

1. W programie Visual Studio kliknij prawym przyciskiem myszy projekt głosowania i wybierz polecenie pakiet.   
![Aplikacja pakietu][PackageApplication]  
2. Otwórz katalog **.\Service-Fabric-dotnet-quickstart\Voting\pkg\Debug** , który został właśnie utworzony, i zanotuj zawartość do pliku o nazwie **głosu. zip** , tak że ApplicationManifest. XML znajduje się w katalogu głównym pliku zip.  
![Zip aplikacji @ no__t-1  
3. Zmień nazwę rozszerzenia pliku z. zip na **. sfpkg**.
4. W Azure Portal, w kontenerze **aplikacje** konta magazynu, kliknij przycisk **Przekaż** i przekaż **głosu. sfpkg**.  
@no__t — pakiet aplikacji 0Upload @ no__t-1

Aplikacja jest teraz przygotowana. Teraz można utworzyć szablon Azure Resource Manager, aby wdrożyć aplikację.      
   
### <a name="create-the-azure-resource-manager-template"></a>Tworzenie szablonu Azure Resource Manager
Przykładowa aplikacja zawiera [szablony Azure Resource Manager](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/tree/master/ARM) , których można użyć do wdrożenia aplikacji. Pliki szablonów mają nazwę **UserApp. JSON** i **UserApp. Parameters. JSON**. 

> [!NOTE] 
> Plik **UserApp. Parameters. JSON** należy zaktualizować nazwą klastra.
>
>

| Parametr              | Opis                                 | Przykład                                                      | Komentarze                                                     |
| ---------------------- | ------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| clusterName            | Nazwa klastra, na którym jest wdrażana | SF-cluster123                                                |                                                              |
| Aplikacja            | Nazwa aplikacji                 | Wybor                                                       |
| applicationTypeName    | Nazwa typu aplikacji           | VotingType                                                   | Musi być zgodna z ApplicationManifest. XML                 |
| ApplicationTypeVersion | Wersja typu aplikacji         | 1.0.0                                                        | Musi być zgodna z ApplicationManifest. XML                 |
| ServiceName            | Nazwa usługi, która jest usługą         | Głosowanie ~ VotingWeb                                             | Musi być w formacie ApplicationName ~ Service            |
| Typ ServiceTypeName        | Nazwa typu usługi                | VotingWeb                                                    | Musi być zgodna z danymi w pliku servicemanifest. XML                 |
| appPackageUrl          | Adres URL magazynu obiektów BLOB aplikacji     | https://servicefabricapps.blob.core.windows.net/apps/Voting.sfpkg | Adres URL pakietu aplikacji w usłudze BLOB Storage (Procedura ustawiania tego ustawienia została opisana poniżej). |
       
```json
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applicationTypes/versions",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationTypeName'), '/', parameters('applicationTypeVersion'))]",
    "location": "[variables('clusterLocation')]",
},
{
    "apiVersion": "2019-03-01",
    "type": "Microsoft.ServiceFabric/clusters/applications/services",
    "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'), '/', parameters('serviceName'))]",
    "location": "[variables('clusterLocation')]"
}
```

### <a name="deploy-the-application"></a>Wdrażanie aplikacji 
Aby wdrożyć aplikację, uruchom polecenie New-AzResourceGroupDeployment w celu wdrożenia w grupie zasobów, która zawiera klaster.
```powershell
New-AzResourceGroupDeployment -ResourceGroupName "sf-cluster-rg" -TemplateParameterFile ".\UserApp.Parameters.json" -TemplateFile ".\UserApp.json" -Verbose
```

## <a name="upgrade-service-fabric-application-using-azure-resource-manager"></a>Uaktualnij aplikację Service Fabric przy użyciu Azure Resource Manager
Aplikacje wdrożone już w klastrze Service Fabric zostaną uaktualnione z następujących powodów:

1. Nowa usługa zostanie dodana do aplikacji. Definicję usługi należy dodać do pliku Service-manifest. XML i Application-manifest. XML. Następnie aby odzwierciedlić nową wersję aplikacji, należy zaktualizować wersję typu aplikacji z 1.0.0 do 1.0.1 [UserApp. Parameters. JSON](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/blob/master/ARM/UserApp.Parameters.json).

    ```
    "applicationTypeVersion": {
        "value": "1.0.1"
    },
    "serviceName2": {
        "value": "Voting~VotingData"
    },
    "serviceTypeName2": {
        "value": "VotingDataType"
    }
    ```
2. Nowa wersja istniejącej usługi zostanie dodana do aplikacji. Dotyczy to zmian w kodzie aplikacji oraz aktualizacji wersji i nazwy typu aplikacji.

    ```
     "applicationTypeVersion": {
        "value": "1.0.1"
    },
    ```

## <a name="delete-application-resources"></a>Usuwanie zasobów aplikacji
Aplikacje wdrożone przy użyciu modelu zasobów aplikacji w Azure Resource Manager można usunąć z klastra, wykonując poniższe kroki

1) Pobieranie identyfikatora zasobu dla aplikacji za pomocą polecenia [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource?view=azps-2.5.0)  

    #### <a name="get-resource-id-for-application"></a>Pobierz identyfikator zasobu dla aplikacji
    ```
    Get-AzResource  -Name <String> | f1
    ```
2) Usuwanie zasobów aplikacji za pomocą polecenia [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource?view=azps-2.5.0)  

    #### <a name="delete-application-resource-using-its-resource-id"></a>Usuń zasób aplikacji przy użyciu jego identyfikatora zasobu
    ```
    Remove-AzResource  -ResourceId <String> [-Force] [-ApiVersion <String>]
    ```

## <a name="next-steps"></a>Następne kroki
Pobierz informacje o modelu zasobów aplikacji:

* [Modelowanie aplikacji w Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-model)
* [Service Fabric manifesty aplikacji i usług](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-and-service-manifests)

## <a name="see-also"></a>Zobacz też
* [Najlepsze rozwiązania](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)
* [Zarządzanie aplikacjami i usługami jako zasobami platformy Azure](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code)

<!--Image references-->
[CreateStorageAccount]: ./media/service-fabric-application-model/create-storage-account.png
[CreateBlob]: ./media/service-fabric-application-model/create-blob.png
[PackageApplication]: ./media/service-fabric-application-model/package-application.png
[ZipApplication]: ./media/service-fabric-application-model/zip-application.png
[UploadAppPkg]: ./media/service-fabric-application-model/upload-app-pkg.png
