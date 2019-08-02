---
title: Pule maszyna wirtualna do analizy danych — Azure | Dokumentacja firmy Microsoft
description: Wdrażanie pul maszyn wirtualnych do nauki o danych jako zasób udostępniony dla zespołu
keywords: głębokiego uczenia i sztucznej Inteligencji, narzędzia do analizy danych, maszyny wirtualnej do nauki o danych, geoprzestrzenna analiza, zespół danych dla celów naukowych
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2018
ms.author: vijetaj
ms.openlocfilehash: 44983de87ce2e774356bf506e3a868da922a3ba3
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558050"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Utwórz współużytkowanej puli maszyn wirtualnych do nauki o danych

W tym artykule omówiono sposób tworzenia współużytkowanej puli z maszynami wirtualnymi analizy danych (maszyny) dla zespołu do użycia. Korzyści z używania współużytkowanej puli są lepsze wykorzystanie zasobów, ułatwianie udostępniania i współpracy i bardziej efektywne zarządzanie zasobami nauki. 

Aby utworzyć pulę maszyny, można użyć wielu metod i technologii. Ten artykuł koncentruje się na pulach dla interaktywnych maszyn wirtualnych. Alternatywna zarządzana infrastruktura obliczeniowa to Azure Machine Learning COMPUTE. Aby uzyskać więcej informacji, zobacz [Konfigurowanie obiektów docelowych obliczeń](../service/how-to-set-up-training-targets.md#amlcompute) .

## <a name="interactive-vm-pool"></a>Interaktywne puli maszyn wirtualnych

Puli interaktywne maszyn wirtualnych, które są współużytkowane przez cały zespół do nauki o danych/sztuczna Inteligencja umożliwia użytkownikom logować się do dostępnego wystąpienia maszyny wirtualnej DSVM zamiast dedykowanego wystąpienia dla każdej grupy użytkowników. Ta konfiguracja może ułatwić realizację lepszej dostępności i bardziej efektywne wykorzystanie zasobów. 

Technologia, która umożliwia tworzenie interaktywnych puli maszyn wirtualnych jest [zestawów skalowania maszyn wirtualnych platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Tworzenie i zarządzanie nimi grupę identycznych, równoważenia obciążenia i automatyczne skalowanie maszyn wirtualnych, można użyć zestawów skalowania. 

Użytkownik loguje się do głównej puli adresów IP lub DNS. Zestaw skalowania automatycznie trasy sesji do maszyny wirtualnej DSVM dostępne w zestawie skalowania. Ponieważ użytkownicy mają podobne środowiska, niezależnie od maszyny Wirtualnej w przypadku logowania się do, wszystkie wystąpienia maszyn wirtualnych w zestawie skalowania instalacji udostępnionego dysku sieciowego, takich jak udział plików platformy Azure lub udziału NFS. Udostępnionym obszarze roboczym użytkownika jest przechowywany na magazyn udostępniony plik, który jest zainstalowany na wszystkich wystąpień. 

Można znaleźć przykładowy szablon usługi Azure Resource Manager, który tworzy zestaw skalowania z wystąpień maszyny wirtualnej DSVM Ubuntu na [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Próbka [plik parametrów](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) dla usługi Azure Resource Manager szablon jest w tej samej lokalizacji. 

Można utworzyć zestawu skalowania na podstawie szablonu usługi Azure Resource Manager, określając wartości dla pliku parametrów w wiersza polecenia platformy Azure. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Poprzednich poleceniach przyjęto założenie, że masz:
* Kopia pliku parametrów za pomocą wartości określone dla swojego wystąpienia zestawu skalowania.
* Liczba wystąpień maszyn wirtualnych.
* Udostępnij wskaźników do usługi Azure Files.
* Poświadczenia dla konta magazynu zostanie zainstalowany na każdej maszynie Wirtualnej. 

Plik parametrów odwołuje się do lokalnie w poleceniach. Można również przekazać parametry wbudowanych lub wyświetlenie dotyczącego ich monitu w skrypcie.  

Powyższy szablon umożliwia protokołu SSH i port JupyterHub z zestawu do puli zaplecza maszyny Ubuntu skalowania frontonu. Jako użytkownik możesz po prostu Zaloguj się do maszyny Wirtualnej na SSH lub JupyterHub w normalny sposób. Ponieważ wystąpienia maszyn wirtualnych można przeskalować w górę lub w dół dynamicznie, każdy stan musi być zapisana w zainstalowanym udostępniać usługi Azure Files. Tej samej metody można użyć, aby utworzyć pulę Windows maszyny. 

[Skrypt, który instaluje udział usługi Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępna w repozytorium Azure DataScienceVM w usłudze GitHub. Skrypt instaluje udziału plików platformy Azure w punkcie instalacji określonym w pliku parametrów. Skrypt tworzy również linki do zainstalowanego dysku w katalogu macierzystym użytkownika początkowej. Katalog notesu specyficzne dla użytkownika w ramach udziału plików platformy Azure jest elastyczne połączone z `$HOME/notebooks/remote` katalogu, tak aby użytkownicy mogą uzyskać dostęp, uruchamianie i zapisywać swoich notesów programu Jupyter. Podczas tworzenia dodatkowych użytkowników na maszynie Wirtualnej, aby wskazać każdy użytkownik Jupyter w obszarze roboczym do udziału plików platformy Azure, możesz użyć tej samej Konwencji. 

Zestawy skalowania maszyn wirtualnych obsługują skalowanie automatyczne. Można ustawić reguły, kiedy należy utworzyć dodatkowe wystąpienia i kiedy należy skalować w dół wystąpień. Na przykład można skalować w dół zero wystąpienia, aby zaoszczędzić na koszty użycia sprzętu w chmurze, gdy maszyny wirtualne nie są w ogóle używane. Na stronach dokumentacji, zestawów skalowania maszyn wirtualnych znajdują się szczegółowe informacje na temat [skalowania automatycznego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj wspólną tożsamość](dsvm-common-identity.md)
* [Bezpieczne przechowywanie poświadczeń w celu dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)















