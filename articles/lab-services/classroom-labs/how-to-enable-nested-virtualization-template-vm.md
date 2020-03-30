---
title: Włączanie wirtualizacji zagnieżdżonej na szablonie maszyny Wirtualnej w usłudze Azure Lab Services | Dokumenty firmy Microsoft
description: Dowiedz się, jak utworzyć szablon maszyny Wirtualnej z wieloma maszynami wirtualnymi wewnątrz.  Innymi słowy włącz zagnieżdżoną wirtualizację na szablonie maszyny Wirtualnej w usłudze Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: spelluru
ms.openlocfilehash: 59b32834369f76d39bb4a253dad4ec541e7ef999
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502011"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usługach Azure Lab Services

Obecnie usługa Azure Lab Services umożliwia skonfigurowanie jednej maszyny wirtualnej szablonu w laboratorium i udostępnienie jednej kopii każdemu z użytkowników. Jeśli jesteś profesorem nauczania sieci, zabezpieczeń lub zajęć IT, może być konieczne zapewnienie każdemu z uczniów środowiska, w którym wiele maszyn wirtualnych może rozmawiać ze sobą za pośrednictwem sieci.

Wirtualizacja zagnieżdżona umożliwia utworzenie środowiska wielu maszyn wirtualnych wewnątrz maszyny wirtualnej szablonu laboratorium. Publikowanie szablonu zapewni każdemu użytkownikowi w laboratorium maszynę wirtualną skonfigurowaną z wieloma maszynami wirtualnymi w nim.  W tym artykule opisano sposób konfigurowania zagnieżdżonej wirtualizacji na komputerze szablonu w usłudze Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Co to jest wirtualizacja zagnieżdżona?

Wirtualizacja zagnieżdżona umożliwia tworzenie maszyn wirtualnych w obrębie maszyny wirtualnej. Wirtualizacja zagnieżdżona odbywa się za pośrednictwem funkcji Hyper-V i jest dostępna tylko na maszynach wirtualnych systemu Windows.

Aby uzyskać więcej informacji na temat wirtualizacji zagnieżdżonej, zobacz następujące artykuły:

- [Zagnieżdżona wirtualizacja na platformie Azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Jak włączyć wirtualizację zagnieżdżoną na maszynie Wirtualnej platformy Azure](../../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Zagadnienia do rozważenia

Przed utworzeniem laboratorium z zagnieżdżoną wirtualizacji, oto kilka rzeczy, które należy wziąć pod uwagę.

- Podczas tworzenia nowego laboratorium wybierz średnie **(wirtualizacja zagnieżdżona)** lub **Duże (wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej. Te rozmiary maszyn wirtualnych obsługują wirtualizację zagnieżdżoną.
- Wybierz rozmiar, który zapewni dobrą wydajność zarówno dla maszyn wirtualnych hosta, jak i klienta.  Należy pamiętać, że podczas korzystania z wirtualizacji, rozmiar, który wybierzesz musi być odpowiedni nie tylko dla jednego komputera, ale hosta, jak również wszystkie maszyny klienckie, które muszą być uruchamiane jednocześnie.
- Maszyny wirtualne klienta nie będą miały dostępu do zasobów platformy Azure, takich jak serwery DNS w sieci wirtualnej platformy Azure.
- Host maszyny wirtualnej wymaga konfiguracji, aby umożliwić maszynie klienckiej mieć łączność z Internetem.
- Maszyny wirtualne klienta są licencjonowane jako niezależne maszyny. Informacje na temat licencjonowania systemów i produktów firmy Microsoft można znaleźć w [witrynie Licencjonowanie](https://www.microsoft.com/licensing/default) firmy Microsoft. Przed skonfigurowaniem komputera szablonu sprawdź umowy licencyjne dotyczące innego oprogramowania używanego.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu

W tym artykule przyjęto założenie, że utworzono konto laboratorium i laboratorium.  Aby uzyskać więcej informacji na temat tworzenia nowego konta laboratorium, zobacz [samouczek konfigurowania konta laboratorium](tutorial-setup-lab-account.md). Aby uzyskać więcej informacji na temat tworzenia laboratorium, zobacz [konfigurowanie samouczka laboratorium w klasie](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Wybierz **opcję Duże (wirtualizacja zagnieżdżona)** lub **Średnia (wirtualizacja zagnieżdżona)** dla rozmiaru maszyny wirtualnej podczas tworzenia laboratorium.  Zagnieżdżona wirtualizacja nie będzie działać inaczej.  

Aby połączyć się z urządzeniem szablonu, zobacz [tworzenie szablonu klasy i zarządzanie nim](how-to-create-manage-template.md).

### <a name="using-script-to-enable-nested-virtualization"></a>Za pomocą skryptu umożliwiającego zagnieżdżoną wirtualizację

Aby użyć zautomatyzowanej konfiguracji do zagnieżdżonej wirtualizacji w systemie Windows Server 2016 lub Windows Server 2019, zobacz [Włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usługach Azure Lab Services przy użyciu skryptu](how-to-enable-nested-virtualization-template-vm-using-script.md). Do zainstalowania roli funkcji Hyper-V użyjesz skryptów ze [skryptów Funkcji Hyper-V usług Lab Services.](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV)  Skrypty skonfigurują również sieć, dzięki czemu maszyny wirtualne funkcji Hyper-V mogą mieć dostęp do Internetu.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Włączanie wirtualizacji zagnieżdżonej zagnieżdżonej za pomocą narzędzi systemu Windows

Konfiguracja wirtualizacji zagnieżdżonej dla systemu Windows Server 2016 lub Windows Server 2019 przy użyciu ról systemu Windows i narzędzi administracyjnych, zobacz [Ręczne włączanie wirtualizacji zagnieżdżonej na maszynie wirtualnej szablonu w usłudze Azure Lab Services](how-to-enable-nested-virtualization-template-vm-ui.md).  Instrukcje obejmują również sposób konfigurowania sieci, aby maszyny wirtualne funkcji Hyper-V mogły mieć dostęp do Internetu.
