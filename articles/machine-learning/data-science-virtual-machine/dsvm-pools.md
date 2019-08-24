---
title: Pule maszyna wirtualna do analizy danych — Azure | Dokumentacja firmy Microsoft
description: Wdrażanie pul DSVMs jako zasobu udostępnionego dla zespołu
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
ms.openlocfilehash: 76a715838fa5eb4f806fccc98675ea5acdbe1822
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992033"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Utwórz współużytkowanej puli maszyn wirtualnych do nauki o danych

W tym artykule omówiono sposób tworzenia udostępnionej puli Virtual Machines analizy danych (DSVMs) dla zespołu. Zalety korzystania z puli udostępnionej obejmują lepsze wykorzystanie zasobów, łatwiejsze udostępnianie i współpracę oraz wydajniejsze zarządzanie zasobami DSVM.

Aby utworzyć pulę maszyny, można użyć wielu metod i technologii. Ten artykuł koncentruje się na pulach dla interaktywnych maszyn wirtualnych. Alternatywna zarządzana infrastruktura obliczeniowa to Azure Machine Learning COMPUTE. Aby uzyskać więcej informacji, zobacz [set up COMPUTE targets](../service/how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktywne puli maszyn wirtualnych

Puli interaktywne maszyn wirtualnych, które są współużytkowane przez cały zespół do nauki o danych/sztuczna Inteligencja umożliwia użytkownikom logować się do dostępnego wystąpienia maszyny wirtualnej DSVM zamiast dedykowanego wystąpienia dla każdej grupy użytkowników. Ta konfiguracja zapewnia lepszą dostępność i bardziej efektywne wykorzystanie zasobów.

Za pomocą technologii [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) można utworzyć interaktywną pulę maszyn wirtualnych. Tworzenie i zarządzanie nimi grupę identycznych, równoważenia obciążenia i automatyczne skalowanie maszyn wirtualnych, można użyć zestawów skalowania.

Użytkownik loguje się do głównej puli adresów IP lub DNS. Zestaw skalowania automatycznie trasy sesji do maszyny wirtualnej DSVM dostępne w zestawie skalowania. Ponieważ użytkownicy chcą spójne i znane środowisko niezależnie od maszyny wirtualnej, do której się logują, wszystkie wystąpienia maszyny wirtualnej w zestawie skalowania instalują udostępniony dysk sieciowy, taki jak udział Azure Files lub udział systemu plików NFS. Udostępnionym obszarze roboczym użytkownika jest przechowywany na magazyn udostępniony plik, który jest zainstalowany na wszystkich wystąpień.

Można znaleźć przykładowy szablon usługi Azure Resource Manager, który tworzy zestaw skalowania z wystąpień maszyny wirtualnej DSVM Ubuntu na [GitHub](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json). Przykład [pliku parametrów](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) dla szablonu Azure Resource Manager można znaleźć w tej samej lokalizacji.

Zestaw skalowania można utworzyć na podstawie szablonu Azure Resource Manager, określając wartości dla pliku parametru w interfejsie wiersza polecenia platformy Azure:

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

Powyższy szablon umożliwia protokołu SSH i port JupyterHub z zestawu do puli zaplecza maszyny Ubuntu skalowania frontonu. Użytkownik loguje się do maszyny wirtualnej na Secure Shell (SSH) lub na JupyterHub w normalny sposób. Ponieważ wystąpienia maszyn wirtualnych można skalować w górę lub w dół, każdy stan musi być zapisany w zainstalowanym Azure Files udziale. Tej samej metody można użyć, aby utworzyć pulę Windows maszyny.

[Skrypt, który instaluje udział usługi Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępna w repozytorium Azure DataScienceVM w usłudze GitHub. Skrypt instaluje udziału plików platformy Azure w punkcie instalacji określonym w pliku parametrów. Skrypt tworzy również linki do zainstalowanego dysku w katalogu macierzystym użytkownika początkowej. Katalog notesu specyficzny dla użytkownika w udziale Azure Files jest niezależny do `$HOME/notebooks/remote` katalogu, dzięki czemu użytkownicy mogą uzyskiwać dostęp do swoich notesów Jupyter, uruchamiać je i zapisywać. Podczas tworzenia dodatkowych użytkowników na maszynie Wirtualnej, aby wskazać każdy użytkownik Jupyter w obszarze roboczym do udziału plików platformy Azure, możesz użyć tej samej Konwencji.

Zestawy skalowania maszyn wirtualnych obsługują skalowanie automatyczne. Możesz ustawić reguły, kiedy należy utworzyć dodatkowe wystąpienia i kiedy skalować wystąpienia w dół. Na przykład można skalować w dół zero wystąpienia, aby zaoszczędzić na koszty użycia sprzętu w chmurze, gdy maszyny wirtualne nie są w ogóle używane. Na stronach dokumentacji, zestawów skalowania maszyn wirtualnych znajdują się szczegółowe informacje na temat [skalowania automatycznego](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview).

## <a name="next-steps"></a>Kolejne kroki

* [Skonfiguruj wspólną tożsamość](dsvm-common-identity.md)
* [Bezpieczne przechowywanie poświadczeń w celu dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)















