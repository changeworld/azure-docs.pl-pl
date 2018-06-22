---
title: Dane maszyny wirtualnej nauki pule - Azure | Dokumentacja firmy Microsoft
description: Wdrażanie pul maszyn wirtualnych nauki danych jako zasób udostępniony dla zespołu
keywords: bezpośrednie uczenia AI, narzędzia do analizy danych, maszyna wirtualna nauki danych, dane geograficzne analytics, proces nauki danych zespołu
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: gokuma
ms.openlocfilehash: 0740ff7542d066442146b8e80e188ad5ba49a2b5
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309402"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Tworzenie udostępnionego puli maszyn wirtualnych nauki danych

W tym artykule opisano sposób tworzenia współużytkowanej puli z danych nauki maszyn wirtualnych (DSVMs) do zespołu do użycia. Korzyści wynikające ze stosowania współużytkowanej puli są lepsze wykorzystanie zasobów ułatwiających udostępnianie i współpracy i skuteczniejsze zarządzanie zasobami DSVM. 

Aby utworzyć pulę DSVMs, można użyć wielu metod i technologii. Ten artykuł dotyczy pule przetwarzania wsadowego i interaktywne maszyn wirtualnych.

## <a name="batch-processing-pool"></a>Przetwarzanie wsadowe puli
Jeśli chcesz skonfigurować pulę DSVMs głównie w celu uruchomienia zadań w partii w trybie offline, możesz użyć [AI usługi partia zadań Azure](https://docs.microsoft.com/azure/batch-ai/) lub [partii zadań Azure](https://docs.microsoft.com/azure/batch/) usługi. Ten artykuł dotyczy AI usługi partia zadań Azure.

Edycja Ubuntu DSVM jest obsługiwany jako jeden z obrazów w AI usługi partia zadań Azure. Azure CLI lub zestaw SDK Python, w której utworzono klaster AI usługi partia zadań Azure, można określić `image` parametru i wartości `UbuntuDSVM`. Można wybrać, jaki rodzaj węzłach przetwarzania ma: oparte na procesorze Graficznym wystąpień i wystąpień tylko do Procesora, liczbę procesorów i pamięci z [szeroki wybór wystąpień maszyn wirtualnych](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) dostępnych na platformie Azure. 

Gdy używasz obrazu Ubuntu DSVM w partii AI z węzłami oparte na procesorze Graficznym preinstalowany wszystkie niezbędne wersji sterowników procesora GPU i bezpośrednie uczenia struktury. Preinstalacji zapisuje wiele czasu przygotowania węzłów partii. W rzeczywistości projektujesz na DSVM Ubuntu interaktywnego, można zauważyć, że węzły AI partii są dokładnie tę samą konfigurację i Konfiguracja środowiska. 

Zwykle podczas tworzenia klastra AI partii, możesz również utworzyć udział plików, który został zainstalowany przez wszystkie węzły. Udział plików jest używany dla danych wejściowych i wyjściowych danych, jak również przechowywanie zadania wsadowego kodu/skryptów. 

Po utworzeniu klastra AI partii, można użyć tego samego interfejsu wiersza polecenia lub zestawu SDK Python umożliwiają przesyłanie zadań do uruchomienia. Płaci się tylko czas, który służy do uruchamiania zadań wsadowych. 

Aby uzyskać więcej informacji, zobacz:
* Przewodnik krok po kroku dotyczący użycia [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/azure/batch-ai/quickstart-cli) do zarządzania partii AI
* Przewodnik krok po kroku dotyczący użycia [Python](https://docs.microsoft.com/azure/batch-ai/quickstart-python) do zarządzania partii AI
* [Partii przepisami AI](https://github.com/Azure/BatchAI) które pokazują, jak używać różnych AI i bezpośrednie uczenia struktury z partii AI

## <a name="interactive-vm-pool"></a>Interakcyjne puli maszyn wirtualnych

Pula interakcyjne maszyn wirtualnych, które są udostępniane przez cały zespół nauki AI/danych umożliwia użytkownikom zalogować się do dowolnego dostępnego wystąpienia DSVM zamiast dedykowanego wystąpienia dla każdej grupy użytkowników. Taka konfiguracja ułatwia większą dostępność i bardziej efektywne wykorzystanie zasobów. 

Jest to technologia, która umożliwia tworzenie interaktywnych puli maszyn wirtualnych [zestawy skalowania maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/). Zestawy skalowania służy do tworzenia i zarządzania nimi grupa identyczne, równoważeniem obciążenia i maszyny wirtualne Skalowanie automatyczne. 

Użytkownik loguje się do głównej puli adresów IP lub DNS. Skala ustawioną automatycznie trasy sesji DSVM dostępne w zestawie skalowania. Ponieważ użytkownicy mają podobne środowisko niezależnie od maszyny Wirtualnej jest logowanie do, wszystkie wystąpienia maszyny wirtualnej w zestawie skalowania instalacji udostępnionego dysku sieciowego, takich jak udział plików Azure lub udziału NFS. Udostępniony obszar roboczy użytkownika zwykle jest przechowywana na magazynu udostępnionego pliku, który jest zainstalowany na wszystkich wystąpień. 

Przykładowy szablon usługi Azure Resource Manager, który pozwala ustawić wystąpieniami Ubuntu DSVM na skali można znaleźć [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Przykładowe [pliku parametrów](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) dla Menedżera zasobów Azure szablon jest w tej samej lokalizacji. 

Możesz utworzyć zestaw z szablonu usługi Azure Resource Manager za pośrednictwem wartości dla pliku parametrów wiersza polecenia platformy Azure skalowania. 

```
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```
Powyższych poleceniach założono, że masz:
* Kopia pliku parametrów przy użyciu wartości określone dla swojego wystąpienia zestawu skali.
* Liczba wystąpień maszyn wirtualnych.
* Udostępnianie wskaźników do usługi pliki Azure.
* Poświadczenia dla konta magazynu będzie instalowany na każdej maszynie Wirtualnej. 

Plik parametr jest używany lokalnie w poleceniach. Można również przekazać wbudowanego parametrów lub monit dla nich w skrypcie.  

Powyższy szablon umożliwia SSH i portu JupyterHub z frontonu zestaw do puli zaplecza Ubuntu DSVMs skalowania. Jako użytkownik po prostu logujesz się do maszyny Wirtualnej na SSH lub JupyterHub w zwykły sposób. Ponieważ wystąpień maszyn wirtualnych można skalować w lub w dół dynamicznie, każdy stan musi być zapisany w zainstalowanym udział plików Azure. Te same podejście umożliwia utworzenie puli DSVMs systemu Windows. 

[Skrypt, który instaluje udział plików Azure](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępna w repozytorium Azure DataScienceVM w witrynie GitHub. Skrypt instaluje udział plików Azure w punkcie instalacji określony w pliku parametrów. Skrypt tworzy również linki do zainstalowanego dysku w katalogu macierzystego użytkownika początkowej. Katalog notesu specyficzne dla użytkownika w obrębie udziału plików Azure to elastyczne połączone z `$HOME/notebooks/remote` katalogu, dzięki czemu użytkownicy mogą uzyskać dostępu, uruchamiania i zapisywać ich notesów Jupyter. Podczas tworzenia dodatkowych użytkowników na maszynie Wirtualnej, aby wskazywały roboczym Jupyter każdego użytkownika do udziału plików platformy Azure, można użyć tej samej Konwencji. 

Obsługa Skalowanie automatyczne zestawach skali maszyn wirtualnych. Można ustawić reguły, kiedy należy utworzyć dodatkowe wystąpienia i kiedy skalować w dół wystąpień. Na przykład można skalować w dół zero wystąpień do zapisania na koszty użycia sprzętu chmury, gdy maszyny wirtualne nie są używane na wszystkich. Na stronach dokumentacji zestawów skali maszyny wirtualnej znajdują się szczegółowe informacje na temat [Skalowanie automatyczne](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie wspólną tożsamość](dsvm-common-identity.md)
* [Bezpiecznie przechowywać poświadczenia dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)















