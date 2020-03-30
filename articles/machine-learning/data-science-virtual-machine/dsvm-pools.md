---
title: Udostępnione pule
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak utworzyć & wdrożyć udostępnioną pulę maszyn wirtualnych do nauki o danych (DSVM) jako zasobu udostępnionego dla zespołu.
keywords: głębokie uczenie się, sztuczna inteligencja, narzędzia do nauki o danych, maszyna wirtualna do nauki o danych, analityka geoprzestrzenna, proces nauki o danych zespołowych
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: cc0efc0a076ddc3fc9425999f1e38b4a32dec7a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477344"
---
# <a name="create-a-shared-pool-of-data-science-virtual-machines"></a>Tworzenie udostępnionej puli maszyn wirtualnych do nauki o danych

W tym artykule dowiesz się, jak utworzyć udostępnioną pulę maszyn wirtualnych do nauki o danych (DSVM) dla zespołu. Korzyści płynące z korzystania z puli współużytkowania obejmują lepsze wykorzystanie zasobów, łatwiejsze udostępnianie i współpracę oraz skuteczniejsze zarządzanie zasobami DSVM.

Można użyć wielu metod i technologii, aby utworzyć pulę dsvms. W tym artykule koncentruje się na pulach interaktywnych maszyn wirtualnych (maszyny wirtualne). Alternatywną zarządzaną infrastrukturą obliczeniową jest azure machine learning compute. Aby uzyskać więcej informacji, zobacz [Konfigurowanie celów obliczeniowych](../how-to-set-up-training-targets.md#amlcompute).

## <a name="interactive-vm-pool"></a>Interaktywna pula maszyn wirtualnych

Pula interaktywnych maszyn wirtualnych, które są współużytkowane przez cały zespół nauki o ai/danych umożliwia użytkownikom zalogowanie się do dostępnego wystąpienia DSVM zamiast dedykowanego wystąpienia dla każdego zestawu użytkowników. Ta konfiguracja umożliwia lepszą dostępność i bardziej efektywne wykorzystanie zasobów.

Technologia [skalowania maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) służy do tworzenia interaktywnej puli maszyn wirtualnych. Zestawy skalowania służy do tworzenia i zarządzania grupą identycznych, równoważenia obciążenia i skalowania automatycznego maszyn wirtualnych.

Użytkownik loguje się do adresu IP lub DNS głównej puli. Zestaw skalowania automatycznie kieruje sesję do dostępnego dsvm w zestawie skalowania. Ponieważ użytkownicy chcą spójne i znane środowisko niezależnie od maszyny Wirtualnej są one logowania do wszystkich wystąpień maszyny Wirtualnej w zestawie skalowania zainstalować współużytkowany dysk sieciowy, takich jak udział usługi Azure Files lub udział sieciowego systemu plików (NFS). Udostępniony obszar roboczy użytkownika jest zwykle przechowywany w udostępnionym magazynie plików, który jest zamontowany w każdym z wystąpień.

Przykładowy szablon usługi Azure Resource Manager, który tworzy zestaw skalowania z wystąpieniami Ubuntu DSVM w [usłudze GitHub.](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json) W tej samej lokalizacji znajdziesz przykładowy [plik parametru](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.parameters.json) szablonu usługi Azure Resource Manager.

Zestaw skalowania można utworzyć na podstawie szablonu usługi Azure Resource Manager, określając wartości dla pliku parametrów w pliku interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az group create --name [[NAME OF RESOURCE GROUP]] --location [[ Data center. For eg: "West US 2"]
az group deployment create --resource-group  [[NAME OF RESOURCE GROUP ABOVE]]  --template-uri https://raw.githubusercontent.com/Azure/DataScienceVM/master/Scripts/CreateDSVM/Ubuntu/dsvm-vmss-cluster.json --parameters @[[PARAMETER JSON FILE]]
```

Powyższe polecenia zakładają, że masz:

* Kopia pliku parametrów z wartościami określonymi dla wystąpienia zestawu skalowania.
* Liczba wystąpień maszyn wirtualnych.
* Wskaźniki do udziału w plikach azure.
* Poświadczenia dla konta magazynu, które będą zainstalowane na każdej maszynie wirtualnej.

Do pliku parametrów odwołuje się lokalnie w poleceniach. Można również przekazać parametry wbudowane lub monit o nie w skrypcie.  

Poprzedni szablon umożliwia SSH i Port JupyterHub z skalowania frontonu ustawionego do puli zaplecza Ubuntu DSVMs. Jako użytkownik logujesz się do maszyny Wirtualnej na bezpiecznej powłoki (SSH) lub na JupyterHub w normalny sposób. Ponieważ wystąpienia maszyny Wirtualnej można skalować dynamicznie w górę lub w dół, każdy stan musi być zapisany w zainstalowanym udziale usługi Azure Files. Tego samego podejścia można użyć do utworzenia puli dsvms systemu Windows.

[Skrypt, który instaluje udział usługi Azure Files](https://raw.githubusercontent.com/Azure/DataScienceVM/master/Extensions/General/mountazurefiles.sh) jest również dostępny w repozytorium DataScienceVM platformy Azure w usłudze GitHub. Skrypt instaluje udział usługi Azure Files w określonym punkcie instalacji w pliku parametru. Skrypt tworzy również miękkie łącza do zainstalowanego dysku w katalogu macierzystym początkowego użytkownika. Katalog notesu specyficzny dla użytkownika w udziale usługi `$HOME/notebooks/remote` Azure Files jest niefunkcjonalny z katalogiem, dzięki czemu użytkownicy mogą uzyskiwać dostęp do notesów jupytera, uruchamiać je i zapisywać. Tej samej konwencji można użyć podczas tworzenia dodatkowych użytkowników na maszynie Wirtualnej, aby wskazać obszar roboczy jupyter każdego użytkownika do udziału usługi Azure Files.

Zestawy skalowania maszyny wirtualnej obsługują skalowanie automatyczne. Można ustawić reguły dotyczące tego, kiedy utworzyć dodatkowe wystąpienia i kiedy skalować w dół wystąpień. Na przykład można skalować w dół do zera wystąpień, aby zaoszczędzić na kosztach użycia sprzętu w chmurze, gdy maszyny wirtualne nie są w ogóle używane. Strony dokumentacji zestawów skalowania maszyny wirtualnej zawierają szczegółowe kroki [skalowania automatycznego.](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview)

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie wspólnej tożsamości](dsvm-common-identity.md)
* [Bezpieczne przechowywanie poświadczeń w celu uzyskania dostępu do zasobów w chmurze](dsvm-secure-access-keys.md)
