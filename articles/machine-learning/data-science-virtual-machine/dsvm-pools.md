---
title: Pule danych do maszyny wirtualnej nauki - Azure | Dokumentacja firmy Microsoft
description: Wdrażanie pul wirtualna nauki danych jako zasób udostępniony dla zespołu
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 625154378fecbd4d45904fa5669adb866fc9487c
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
---
# <a name="creating-a-shared-pool-of-data-science-virtual-machines"></a>Tworzenie udostępnionego puli maszyn wirtualnych nauki danych

W tym artykule opisano, jak można utworzyć puli udostępnionych z danych nauki maszyn wirtualnych (DSVM) do użycia przez zespół. Zaletą używania współużytkowanej puli jest lepsze wykorzystanie zasobów ułatwiających udostępnianie i współpracy, dzięki czemu IT skuteczniej zarządzać zasobami DSVM. 

Istnieje wiele sposobów i różnych technologii, których można użyć do utworzenia puli DSVM.  Poniżej przedstawiono główne scenariusze:

* Puli do przetwarzania wsadowego
* Pula interakcyjne maszyny wirtualne

## <a name="batch-processing-pool"></a>Pula przetwarzania wsadowego
Jeśli chcesz skonfigurować pulę DSVM głównie w celu uruchomienia zadań w partii w trybie offline, a następnie można użyć [AI usługi partia zadań Azure](https://docs.microsoft.com/azure/batch-ai/) usługi lub [partii zadań Azure](https://docs.microsoft.com/azure/batch/). 

### <a name="azure-batch-ai"></a>Sztuczna inteligencja w usłudze Azure Batch
Edycja Ubuntu DSVM jest obsługiwany jako jeden z obrazów w AI usługi partia zadań Azure. W wiersza polecenia platformy Azure lub zestawu SDK Python, w której utworzono klaster AI usługi partia zadań Azure, można określić ```image``` parametru i wartości ```UbuntuDSVM```. Można wybrać, jakiego rodzaju przetwarzania węzły mają — oparte na procesorze Graficznym wystąpień CPU vs tylko wystąpienia, liczba procesorów CPU, pamięć z [szeroki wybór wystąpień maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) dostępnych na platformie Azure. Używanie obrazu Ubuntu DSVM w partii AI oparte na procesorze Graficznym węzłów, wszystkie niezbędne sterowniki procesora GPU i głębokie uczenia struktury są preinstalowany, co pozwala zaoszczędzić wiele czasu przygotowania węzłów partii. W rzeczywistości Jeśli tworzysz na DSVM Ubuntu interaktywnego, można zauważyć, że węzły AI partii są dokładnie tego samego instalację i konfigurację środowiska. Zwykle podczas tworzenia klastra AI partii można również utworzyć udziału plików, które są instalowane przez wszystkie węzły i jest używany dla danych wejściowych i wyjściowych danych, jak również przechowywanie kodu zadania wsadowego / skrypty. 

Po utworzeniu klastra AI partii, można użyć tego samego interfejsu wiersza polecenia lub zestawu SDK Python umożliwiają przesyłanie zadań do uruchomienia. Płacisz tylko za czas, który służy do uruchamiania zadań wsadowych. 

#### <a name="more-information"></a>Więcej informacji
* Przewodnik krok po kroku dotyczący użycia [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) zarządzania partii AI
* Przewodnik krok po kroku dotyczący użycia [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) zarządzania partii AI
* [Partii przepisami AI](https://github.com/Azure/BatchAI) są dostępne, pokazująca, jak używać różnych AI/bezpośrednich uczenia struktury z AI partii.

## <a name="interactive-vm-pool"></a>Interakcyjne puli maszyn wirtualnych

Pula DSVMs interaktywne, współużytkowane przez cały AI / zespołu nauki danych pozwala użytkownikom na logowanie się do dowolnego dostępnego wystąpienia DSVM zamiast dedykowanego wystąpienia dla każdej grupy użytkowników. Dzięki temu można lepiej dostępności i bardziej efektywne wykorzystanie zasobów. 

Jest to technologia umożliwiająca tworzenie interaktywnych puli maszyn wirtualnych [zestawy skalowania maszyny wirtualnej Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) (VMSS), która umożliwia tworzenie i zarządzanie grupy identyczne, równoważenia obciążenia i skalowanie automatyczne maszyn wirtualnych. Dzienniki użytkownika do głównej puli adresów IP lub DNS. Skala ustawioną automatycznie trasy sesji DSVM dostępne w zestawie skalowania. Ponieważ użytkownik chcieliby środowisko podobne niezależnie od maszyny Wirtualnej logowania się do, wszystkie wystąpienia maszyny wirtualnej w zestawie skalowania instaluje udostępnionego dysku sieciowego, takich jak pliki Azure lub udziału NFS. Udostępniony obszar roboczy użytkownika zwykle jest przechowywana na magazynu udostępnionego pliku, który jest zainstalowany na wszystkich wystąpień. 

Przykładowy szablon usługi Azure Resource Manager, który tworzy zestawu skali maszyny Wirtualnej z wystąpieniami Ubuntu DSVMs można znaleźć w [github](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Przykładowe szablonu usługi Azure Resource Manager [pliku parametrów](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) jest również udostępniany w tej samej lokalizacji. 

Możesz utworzyć zestaw z szablonu usługi Azure Resource Manager przez określenie odpowiedniej wartości dla pliku parametrów przy użyciu interfejsu wiersza polecenia Azure skalowania maszyny Wirtualnej. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Powyższych poleceń założono, że kopia pliku parametru o wartości określone dla swojego wystąpienia zestawu skalowania maszyny Wirtualnej, liczba wystąpień maszyn wirtualnych, wskaźników do plików Azure oraz poświadczenia dla konta magazynu będzie instalowany na każdej maszynie Wirtualnej. Plik parametr jest używany lokalnie w powyższym poleceniu. Można przekazać również wbudowanego parametrów lub monit dla nich w skrypcie.  

Szablon powyżej umożliwia SSH i Ustaw port Jupyterhub z frontonu skalowania maszyny Wirtualnej Ubuntu DSVMs puli wewnętrznej bazy danych.  Jako użytkownik po prostu logujesz się do maszyny Wirtualnej na SSH lub JupyterHub w normalnym trybie. Ponieważ wystąpień maszyn wirtualnych można skalować w lub w dół dynamicznie, każdy stan musi być zapisany w zainstalowanym udziału plików platformy Azure. Te same podejście może służyć do tworzenia puli DSVMs systemu Windows. 

[Skrypt, który instaluje pliki Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępna w serwisie DataScienceVM Github Azure. Oprócz instalowania plików Azure w punkcie instalacji określony w pliku parametrów, tworzy również dodatkowe linki, które znajdują się na dysku zainstalowanego w katalogu macierzystego użytkownika początkowej i katalog notesu specyficzne dla użytkownika w ramach udostępnionych plików Azure to elastyczne połączone z ```$HOME/notebooks/remote``` directory umożliwiające użytkownikowi dostępu, uruchamiania i zapisywanie ich notesów Jupyter.  Podczas tworzenia dodatkowych użytkowników na maszynie Wirtualnej, aby wskazywał każdego użytkownika roboczym Jupyter do udostępnionych plików Azure można tej samej Konwencji. 

Skalowania maszyny Wirtualnej Azure ustawia Skalowanie automatyczne pomocy technicznej, którym można ustawić zasady, na kiedy należy utworzyć dodatkowe wystąpienia i w obszarze co okoliczności skali wystąpień, łącznie z jej przejściem w tryb dół zero wystąpień do zapisania na chmury koszty sprzętu użycia w przypadku maszyn wirtualnych nie są używane na wszystkich . Na stronach dokumentacji zestawów skali maszyny Wirtualnej znajdują się szczegółowe informacje na temat [automatyczne skalowanie](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie wspólną tożsamość](dsvm-common-identity.md)
* [Bezpiecznie przechowywać poświadczenia dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)















