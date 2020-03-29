---
title: Niezawodny wolumin dysku sieci szkieletowej usług z siatką sieci szkieletowej usług
description: Dowiedz się, jak przechowywać stan w aplikacji usługi Azure Service Fabric Mesh, montując wolumin oparty na niezawodnej sieci szkieletowej usług w kontenerze przy użyciu interfejsu wiersza polecenia platformy Azure.
author: ashishnegi
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: f26fe70afe7d9e2872f06ac6da7143556278b1b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75497967"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Instalowanie woluminu opartego na niezawodnym dysku sieci szkieletowej usług o wysokiej dostępności w aplikacji sieci szkieletowej usług 
Typową metodą utrwalania stanu za pomocą aplikacji kontenera jest użycie magazynu zdalnego, takiego jak usługa Azure File Storage lub bazy danych, takiej jak usługa Azure Cosmos DB. Powoduje to znaczne opóźnienie sieci odczytu i zapisu do magazynu zdalnego.

W tym artykule pokazano, jak przechowywać stan w wysoce dostępnej sieci szkieletowej usługi niezawodny dysk przez zamontowanie woluminu wewnątrz kontenera aplikacji sieci szkieletowej usługi.
Sieć szkieletowa usług Niezawodny dysk zapewnia woluminy dla odczytów lokalnych z zapisami replikowanymi w klastrze sieci szkieletowej usług w celu zapewnienia wysokiej dostępności. Spowoduje to usunięcie wywołań sieciowych dla odczytów i zmniejsza opóźnienie sieci dla zapisów. Jeśli kontener zostanie ponownie uruchomiony lub zostanie przeniesiony do innego węzła, nowe wystąpienie kontenera będzie widoczne na tym samym woluminie, co starszy. W ten sposób jest zarówno wydajny, jak i wysoce dostępny.

W tym przykładzie aplikacja Counter ma usługę ASP.NET Core ze stroną sieci web, która pokazuje wartość licznika w przeglądarce.

Okresowo `counterService` odczytuje wartość licznika z pliku, zwiększa ją i zapisuje z powrotem do pliku. Plik jest przechowywany w folderze zainstalowanym na woluminie wspieranym przez dysk niezawodny sieci szkieletowej usług.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Aby użyć interfejsu wiersza polecenia `az --version` platformy Azure `azure-cli (2.0.43)`w tym artykule, upewnij się, że zwraca co najmniej .  Zainstaluj (lub zaktualizuj) moduł rozszerzenia interfejsu wiersza polecenia sieci szkieletowej usługi Azure, postępując zgodnie z tymi [instrukcjami](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której zostanie wdrożona aplikacja. Następujące polecenie tworzy grupę `myResourceGroup` zasobów o nazwie w lokalizacji we wschodnich Stanach Zjednoczonych. Jeśli zmienisz nazwę grupy zasobów w poniższym poleceniu, pamiętaj, aby zmienić ją we wszystkich kolejnych poleceniach.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Następujące polecenie wdraża aplikację Systemu Linux przy użyciu [szablonu counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Aby wdrożyć aplikację systemu Windows, użyj [szablonu counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Należy pamiętać, że większe obrazy kontenera może trwać dłużej do wdrożenia.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Można również zobaczyć stan wdrożenia za pomocą polecenia

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Zwróć uwagę na nazwę zasobu `Microsoft.ServiceFabricMesh/gateways`bramy, który ma typ zasobu jako . Będzie to używane w uzyskaniu publicznego adresu IP aplikacji.

## <a name="open-the-application"></a>Otwieranie aplikacji

Po pomyślnym wdrożeniu aplikacji pobierz adres ipAddress zasobu bramy dla aplikacji. Użyj nazwy bramy, którą zauważyłeś w powyższej sekcji.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Dane wyjściowe powinny `ipAddress` mieć właściwość, która jest publiczny adres IP dla punktu końcowego usługi. Otwórz go w przeglądarce. Wyświetli stronę internetową z wartością licznika aktualizowaną co sekundę.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja jest w stanie korzystać z woluminu

Aplikacja tworzy plik `counter.txt` o nazwie `counter/counterService` w woluminie wewnątrz folderu. Zawartość tego pliku jest wartością licznika wyświetlaną na stronie internetowej.

## <a name="delete-the-resources"></a>Usuwanie zasobów

Często usuwaj zasoby, których nie używasz już na platformie Azure. Aby usunąć zasoby związane z tym przykładem, usuń grupę zasobów, w której zostały wdrożone (która usuwa wszystko skojarzone z grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

- Wyświetl przykładową aplikację Dysk niezawodny wolumin sieci szkieletowej usług w [usłudze GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
