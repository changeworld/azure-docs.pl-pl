---
title: Wdrażanie aplikacji usługi Service Fabric siatki, która używa usługi Azure Files woluminu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację, która używa woluminów plików platformy Azure do usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: fb9740efaa9a1033d6602180f5750f6fb550c048
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076250"
---
# <a name="deploy-a-service-fabric-mesh-application-that-uses-the-azure-files-volume"></a>Wdrażanie aplikacji usługi Service Fabric siatki, używającej woluminów plików platformy Azure
Ten przykład ilustruje użycie woluminów magazynu w kontenerze uruchomiona w usługi Azure Service Fabric siatki. W ramach tego przykładu:

- Tworzenie udziału plików za pomocą [usługi Azure Files](/azure/storage/files/storage-files-introduction) 
- Odwołania, które współdzielą jako wolumin do wystąpienia kontenera, który wdrożymy
  - Po uruchomieniu kontenera instaluje udziału jako określonej lokalizacji w kontenerze
- Kod uruchomiony w kontenerze zapisuje plik tekstowy do tej lokalizacji
- Sprawdź, czy poprawnie zapisany plik w udziale, który tworzy kopie woluminu

## <a name="example-json-templates"></a>Przykładowe szablony JSON

Linux: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json)

Windows: [https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json](https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json)

## <a name="create-the-azure-files-file-share"></a>Utwórz udział plików usługi Azure Files

Postępuj zgodnie z instrukcjami w [dokumentacji usługi Azure Files](/azure/storage/files/storage-how-to-create-file-share) do utworzenia udziału plików dla aplikacji do użycia.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie usługi Service Fabric siatki interfejsu wiersza polecenia
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Aby wykonać te kroki, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli zdecydujesz się zainstalować i korzystać z interfejsu wiersza polecenia lokalnie, musisz zainstalować wiersza polecenia platformy Azure w wersji 2.0.35 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować lub uaktualnić do najnowszej wersji interfejsu wiersza polecenia, zobacz [zainstalować Azure CLI 2.0] [azure — interfejs wiersza polecenia instalacji].

Zainstaluj moduł rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure. Dla wersji zapoznawczej usługi Service Fabric siatki wiersza polecenia platformy Azure są zapisywane jako rozszerzenie interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.
Logowanie do platformy Azure i ustawić subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów (RG) do wdrożenia w tym przykładzie, lub możesz użyć istniejącej grupy zasobów i pominąć ten krok. Podgląd jest dostępny tylko w `eastus` lokalizacji.

```azurecli-interactive
az group create --name <resourceGroupName> --location eastus
```

## <a name="deploy-the-template"></a>Wdrożenie szablonu
Tworzenie aplikacji i powiązanych zasobów przy użyciu jednej z następujących poleceń.

Dla systemu Linux:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.linux.json
```

W przypadku systemu Windows:

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://seabreezequickstart.blob.core.windows.net/templates/azurefiles-volume/sbz_rp.windows.json
```

Postępuj zgodnie z monitami, aby wprowadzić nazwę udziału pliku, nazwę konta i klucz konta na potrzeby udziału plików platformy Azure, która zawiera wolumin. W około minucie, polecenia powinny zwracać z `"provisioningState": "Succeeded"`.

Parametr hasła w szablonie jest `string` typ łatwość użycia. Pojawi się na ekranie w postaci zwykłego tekstu i stan wdrożenia.

## <a name="verify-that-the-application-is-able-to-use-the-volume"></a>Sprawdź, czy aplikacja jest w stanie korzystać z woluminu
Aplikacja tworzy plik o nazwie _data.txt_ w udziale plików (jeśli go jeszcze nie istnieje). Zawartość tego pliku jest liczbą, która rośnie co 30 sekund, przez aplikację. Aby zweryfikować, że przykład działa prawidłowo, należy otworzyć _data.txt_ okresowo i sprawdzić, czy liczba jest aktualizowana.

Plik może zostać pobrana przy użyciu dowolnego narzędzia, które umożliwia przeglądanie udział plików usługi Azure Files. [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) jest przykładem takiego narzędzia.

## <a name="next-steps"></a>Kolejne kroki

- Wyświetlanie usługi Azure Files woluminu przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/azurefiles-volume).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).
