---
title: Błędy konsoli szeregowych platformy Azure | Dokumenty firmy Microsoft
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
ms.openlocfilehash: 61ae0ef92fe522a2a038a6076a5e0c0a10ee47b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060689"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Typowe błędy w konsoli szeregowej platformy Azure
Istnieje zestaw znanych błędów w konsoli szeregowej platformy Azure. Jest to lista tych błędów i kroki ograniczające zagrożenie dla nich.

## <a name="common-errors"></a>Typowe błędy

Błąd                             |   Środki zaradcze
:---------------------------------|:--------------------------------------------|
"Usługa Azure Serial Console wymaga włączenia diagnostyki rozruchu. Kliknij tutaj, aby skonfigurować diagnostykę rozruchu dla maszyny wirtualnej." | Upewnij się, że zestaw skalowania maszyny wirtualnej lub maszyny wirtualnej ma włączoną [diagnostykę rozruchu.](boot-diagnostics.md) Jeśli używasz konsoli szeregowej w wystąpieniu zestawu skalowania maszyny wirtualnej, upewnij się, że wystąpienie ma najnowszy model.
"Usługa Azure Serial Console wymaga uruchomienia maszyny wirtualnej. Użyj przycisku Start powyżej, aby uruchomić maszynę wirtualną."  | Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi być w stanie uruchomionym, aby uzyskać dostęp do konsoli szeregowej (maszyna wirtualna nie może zostać zatrzymana lub cofnięta alokacja). Upewnij się, że wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej jest uruchomione i spróbuj ponownie.
"Usługa Azure Serial Console nie jest włączona dla tej subskrypcji, skontaktuj się z administratorem subskrypcji, aby włączyć." | Konsola szeregowa platformy Azure można wyłączyć na poziomie subskrypcji. Jeśli jesteś administratorem subskrypcji, możesz [włączyć i wyłączyć konsolę szeregową platformy Azure](./serial-console-enable-disable.md). Jeśli nie jesteś administratorem subskrypcji, skontaktuj się z administratorem subskrypcji, aby uzyskać kolejne kroki.
Odpowiedź "Zabronione" została napotkana podczas uzyskiwania dostępu do tego konta magazynu diagnostycznego rozruchu tej maszyny Wirtualnej. | Upewnij się, że diagnostyka rozruchu nie ma zapory konta. Dostępne konto przechowywania diagnostycznego rozruchu jest niezbędne do działania konsoli szeregowej. Konsola szeregowa według projektu nie może pracować z zaporami kont magazynu włączonymi na koncie magazynu diagnostyki rozruchu.
Nie masz wymaganych uprawnień do używania tej maszyny Wirtualnej z konsolą szeregową. Upewnij się, że masz co najmniej uprawnienia roli współautora maszyny wirtualnej.| Dostęp do konsoli szeregowej wymaga dostępu do poziomu współautora lub powyżej na zestawie skalowania maszyny wirtualnej lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [stronę przeglądu](serial-console-overview.md).
Nie można odnaleźć konta magazynu używanego do diagnostyki rozruchu na tej maszynie wirtualnej. Sprawdź, czy diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej, to konto magazynu nie zostało usunięte i masz dostęp do tego konta magazynu. | Sprawdź, czy konto magazynu diagnostyki rozruchu nie zostało usunięte dla zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej
Połączenie konsoli szeregowej z maszyną wirtualną napotkało błąd: "Złe żądanie" (400) | Może się tak zdarzyć, jeśli identyfikator URI diagnostyki rozruchu jest niepoprawny. Na przykład zamiast "https://" użyto "http://". Identyfikator URI diagnostyki rozruchu można naprawić za pomocą tego polecenia:`az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Nie masz wymaganych uprawnień do zapisu na koncie magazynu diagnostyki rozruchu dla tej maszyny Wirtualnej. Upewnij się, że masz co najmniej uprawnienia współautora maszyn wirtualnych | Dostęp do konsoli szeregowej wymaga dostępu na poziomie współautora na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przeglądu](serial-console-overview.md).
Nie można określić grupy zasobów dla * &lt;&gt;* konta magazynu przechowywania diagnostyki rozruchu STORAGEACCOUNTNAME . Sprawdź, czy diagnostyka rozruchu jest włączona dla tej maszyny Wirtualnej i masz dostęp do tego konta magazynu. | Dostęp do konsoli szeregowej wymaga dostępu na poziomie współautora na koncie magazynu diagnostyki rozruchu. Aby uzyskać więcej informacji, zobacz [stronę przeglądu](serial-console-overview.md).
Inicjowanie obsługi administracyjnej dla tej maszyny Wirtualnej nie powiodło się jeszcze. Upewnij się, że maszyna wirtualna jest w pełni wdrożona i ponów próbę połączenia konsoli szeregowej. | Zestaw skalowania maszyny wirtualnej lub maszyny wirtualnej może nadal inicjowania obsługi administracyjnej. Poczekaj chwilę i spróbuj ponownie.
Gniazdo sieci Web jest zamknięte lub nie można go otworzyć. | Może być konieczne dodanie `*.console.azure.com`dostępu do zapory . Bardziej szczegółowe, ale dłuższe podejście jest umożliwienie dostępu zapory do [zakresów adresów IP centrum danych platformy Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), które zmieniają się dość regularnie.
Konsola szeregowa nie działa z kontem magazynu przy użyciu usługi Azure Data Lake Storage Gen2 z hierarchicznymi obszarami nazw. | Jest to znany problem z hierarchicznymi przestrzeniami nazw. Aby rozwiązać ten temat, upewnij się, że konto magazynu diagnostyki rozruchowej maszyny Wirtualnej nie jest tworzone przy użyciu usługi Azure Data Lake Storage Gen2. Tę opcję można ustawić tylko przy tworzeniu konta magazynu. Może być trzeba utworzyć oddzielne konto magazynu diagnostyki rozruchu bez usługi Azure Data Lake Storage Gen2 włączone w celu złagodzenia tego problemu.
Połączenie konsoli szeregowej z maszyną wirtualną napotkało błąd: 'Forbidden'(SubscriptionNotEnabled) - \<Nazwa subskrypcji niezdefiniowana, identyfikator subskrypcji identyfikator>a identyfikatora identyfikatora identyfikatora id jest w stanie niezdefiniowanym | Ten problem może wystąpić, jeśli subskrypcja, w którym użytkownik utworzył swoje konto magazynu usługi Cloud Shell, została wyłączona. Aby złagodzić, uruchom usługę Cloud Shell i [wykonaj kroki niezbędne](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#unmount-clouddrive-1) do ponownego obsługi administracyjnej zapasowego konta magazynu dla usługi Cloud Shell w bieżącej subskrypcji.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [maszynach wirtualnych azure serial console dla systemów linux](./serial-console-linux.md)
* Dowiedz się więcej o [platformie Azure Serial Console dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md)