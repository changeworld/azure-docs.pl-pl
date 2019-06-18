---
title: Używanie o wysokiej dostępności usługi Service Fabric Reliable woluminu dysku w aplikacji usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przechowywać stanu w aplikacji usługi Azure Service Fabric siatki przez zainstalowanie usługi Service Fabric Reliable na dysku woluminu w kontenerze przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: ashishnegi
manager: raunakpandya
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/03/2018
ms.author: asnegi
ms.custom: mvc, devcenter
ms.openlocfilehash: 9f760e7e693334475fb61ba9e5d44df019e78604
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66147492"
---
# <a name="mount-highly-available-service-fabric-reliable-disk-based-volume-in-a-service-fabric-mesh-application"></a>Instalowanie o wysokiej dostępności usługi Service Fabric Reliable dysku na podstawie woluminu w aplikacji usługi Service Fabric siatki 
Typowe metody utrwalanie stanu przy użyciu aplikacji kontenera jest użycie magazynu zdalnego, takich jak usługi Azure File Storage lub bazy danych, takich jak usługi Azure Cosmos DB. To spowoduje naliczenie znaczących odczytu i zapisu opóźnienia sieci do zdalnego magazynu.

W tym artykule przedstawiono sposób przechowywania stanu o wysokiej dostępności usługi Service Fabric Reliable dysku przez zainstalowanie woluminu w kontenerze aplikacji usługi Service Fabric siatki.
Dysk niezawodnej usługi Service Fabric zapewnia woluminów dla odczytów lokalnego zapisów replikowane w obrębie klastra usługi Service Fabric w celu zapewnienia wysokiej dostępności. To spowoduje usunięcie wywołań sieci dla odczytów i zmniejsza jej opóźnienie do zapisu. Jeśli kontener uruchamia się ponownie lub przenoszone do innego węzła, nowego wystąpienia kontenera zostaną wyświetlone tego samego woluminu, jak stary. Ten sposób jest wydajne i o wysokiej dostępności.

W tym przykładzie aplikacja licznika ma usługi platformy ASP.NET Core ze stroną sieci web, która przedstawia wartość licznika w przeglądarce.

`counterService` Co jakiś czas odczytuje wartość licznika z pliku, zwiększa ona i zapisu je z powrotem do pliku. Plik jest przechowywany w folderze, który jest zainstalowany na tym woluminie wspierany przez dysk niezawodnej usługi Service Fabric.

## <a name="prerequisites"></a>Wymagania wstępne

Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Aby użyć wiersza polecenia platformy Azure z tym artykułem, upewnij się, że `az --version` zwraca co najmniej `azure-cli (2.0.43)`.  Zainstalować (lub zaktualizować) modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której zostanie wdrożona aplikacja. Następujące polecenie tworzy grupę zasobów o nazwie `myResourceGroup` w lokalizacji w wschodnich Stanach Zjednoczonych. Jeśli zmienisz nazwę grupy zasobów w poniższego polecenia, pamiętaj, aby ją zmienić w wszystkie polecenia, które należy wykonać.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu

Następujące polecenie służy do wdrażania aplikacji systemu Linux za pomocą [szablonu counter.sfreliablevolume.linux.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.linux.json). Aby wdrożyć aplikację Windows, należy użyć [szablonu counter.sfreliablevolume.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/counter/counter.sfreliablevolume.windows.json). Należy pamiętać, czy większych obrazów kontenerów może potrwać dłużej wdrożenia.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/counter/counter.sfreliablevolume.linux.json
```

Można również wyświetlić stan wdrożenia za pomocą polecenia

```azurecli-interactive
az group deployment show --name counter.sfreliablevolume.linux --resource-group myResourceGroup
```

Zwróć uwagę na nazwę zasobu bramy, która ma typ zasobu `Microsoft.ServiceFabricMesh/gateways`. To posłuży podczas pobierania publiczny adres IP aplikacji.

## <a name="open-the-application"></a>Otwieranie aplikacji

Po pomyślnym wdrożeniu aplikacji, Uzyskaj adres IP zasobu bramy aplikacji. Użyj nazwę bramy, którą można zauważyć, że w powyżej sekcji.
```azurecli-interactive
az mesh gateway show --resource-group myResourceGroup --name counterGateway
```

Dane wyjściowe powinny mieć właściwość `ipAddress` czyli publiczny adres IP punktu końcowego usługi. Otwórz go w przeglądarce. Wyświetli stronę sieci web za pomocą wartość licznika, aktualizowane co sekundę.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja jest w stanie korzystać z woluminu

Aplikacja tworzy plik o nazwie `counter.txt` zbiorczo wewnątrz `counter/counterService` folderu. Zawartość tego pliku jest wartość licznika są wyświetlane na stronie sieci web.

## <a name="delete-the-resources"></a>Usuń zasoby

Często usunąć zasoby, których już używasz na platformie Azure. Aby usunąć zasoby związane z tym przykładzie, należy usunąć grupę zasobów, w którym zostały wdrożone (co spowoduje usunięcie wszystkich skojarzonych z tą grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- Wyświetl przykładowej aplikacji usługi Service Fabric Reliable dysku woluminu na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/counter).
- Aby dowiedzieć się więcej o modelu zasobów usługi Service Fabric, zobacz [Model zasobów usługi Service Fabric Mesh](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
