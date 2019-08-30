---
title: Błędy konsoli szeregowej platformy Azure | Microsoft Docs
description: Typowe błędy w konsoli szeregowej platformy Azure
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: a997675a2f7a280e8311e2bba7dca21de82a086e
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129668"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Typowe błędy w konsoli szeregowej platformy Azure
Istnieje zestaw znanych błędów w konsoli szeregowej platformy Azure. Jest to lista błędów i czynności zaradczych.

## <a name="common-errors"></a>Typowe błędy

Błąd                            |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
Nie można pobrać ustawień diagnostyki rozruchu dla  *&lt;VMNAME&gt;* . Aby korzystać z konsoli szeregowej, upewnij się, że Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej. ![Błąd diagnostyki rozruchu](./media/virtual-machines-serial-console/virtual-machines-serial-console-boot-diagnostics-error.png) | Upewnij się, że maszyna wirtualna lub zestaw skalowania maszyn wirtualnych ma włączoną [diagnostykę rozruchu](boot-diagnostics.md) . Jeśli używasz konsoli szeregowej w wystąpieniu zestawu skalowania maszyn wirtualnych, upewnij się, że wystąpienie ma najnowszy model.
Maszyna wirtualna jest w stanie zatrzymania, przydział zostanie cofnięty. Uruchom maszynę Wirtualną i spróbuj ponownie nawiązać połączenie konsoli szeregowej. ![Cofnięto przydział błędu](./media/virtual-machines-serial-console/virtual-machines-serial-console-deallocating-error.png) | Aby można było uzyskać dostęp do konsoli szeregowej, maszyna wirtualna lub wystąpienie zestawu skalowania maszyn wirtualnych musi znajdować się w stanie uruchomienia. Uruchom maszynę wirtualną lub wystąpienie zestawu skalowania maszyn wirtualnych i spróbuj ponownie.
Odpowiedź "Dostęp zabroniony" napotkano podczas uzyskiwania dostępu do konta magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. ![Błąd zapory konta magazynu](./media/virtual-machines-serial-console/virtual-machines-serial-console-firewall-error.png)| Upewnij się, że Diagnostyka rozruchu nie ma zapory konta. Konto magazynu diagnostyki rozruchu dostępny jest niezbędne do konsoli szeregowej funkcjonowania.
Nie masz wymaganych uprawnień do korzystania z tej maszyny wirtualnej za pomocą konsoli szeregowej. Upewnij się, że co najmniej uprawnienia roli Współautor maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga, aby na maszynie wirtualnej lub w zestawie skalowania maszyn wirtualnych miał dostęp na poziomie współautora lub wyższy. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Nie można odnaleźć konta magazynu "" używanego na potrzeby diagnostyki rozruchu na tej maszynie wirtualnej. Sprawdź, czy dla tej maszyny wirtualnej włączono diagnostykę rozruchu, to konto magazynu nie zostało usunięte i masz dostęp do tego konta magazynu. | Sprawdź, czy nie usunięto konta magazynu diagnostyki rozruchu dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych
Inicjowanie obsługi dla tej maszyny wirtualnej nie powiodło się. Upewnij się, że maszyna wirtualna jest w pełni wdrożona, i ponów próbę połączenia z konsolą szeregową. | Obsługa maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych nadal może być niedostępna. Poczekaj chwilę i spróbuj ponownie.
Nie masz wymaganych uprawnień do zapisu na koncie magazynu diagnostyki rozruchu dla tej maszyny wirtualnej. Upewnij się, że masz co najmniej uprawnienia współautora maszyny wirtualnej na "". | Konsola szeregowa dostęp wymaga dostępu na poziomie współautor na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Nie można określić grupy zasobów dla konta magazynu diagnostyki rozruchu  *&lt;STORAGEACCOUNTNAME&gt;* . Sprawdź, czy Diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, i czy masz dostęp do tego konta magazynu. | Konsola szeregowa dostęp wymaga dostępu na poziomie współautor na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przegląd](serial-console-overview.md).
Gniazda sieci Web został zamknięty lub nie można otworzyć. | Może być konieczne dodanie dostępu `*.console.azure.com`do zapory. Bardziej szczegółowym, ale dłuższym podejściem jest umożliwienie dostępu zapory do [zakresów adresów IP centrum danych Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które zmieniają się dość często.


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md)
* Dowiedz się więcej o [usłudze Azure serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)