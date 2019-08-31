---
title: Konsola szeregowa Azure | Microsoft Docs
description: Konsola szeregowa Azure umożliwia łączenie się z maszyną wirtualną, gdy SSH lub RDP nie są dostępne.
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
ms.date: 8/30/2019
ms.author: alsin
ms.openlocfilehash: 2ae1f3769254575e33eaa5b4e3a24c47b9eb1aff
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194164"
---
# <a name="azure-serial-console"></a>Konsola szeregowa platformy Azure

Konsola szeregowa w Azure Portal zapewnia dostęp do konsoli opartej na tekście dla maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych z systemem Linux lub Windows. To połączenie szeregowe łączy się z portem szeregowym ttyS0 lub COM1 wystąpienia zestawu skalowania maszyn wirtualnych, zapewniając dostęp niezależny od sieci lub stanu systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko przy użyciu Azure Portal i jest to dozwolone tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższą dla maszyny wirtualnej lub zestawu skalowania maszyn wirtualnych.

Konsola szeregowa działa w taki sam sposób w przypadku maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. W tym dokumencie wszystkie wzmianki dotyczące maszyn wirtualnych będą niejawnie obejmować wystąpienia zestawu skalowania maszyn wirtualnych, chyba że określono inaczej.

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure. Nie jest jeszcze dostępne w Azure dla instytucji rządowych lub chmury chińskiej wersji platformy Azure.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Wymagania wstępne dotyczące uzyskiwania dostępu do konsoli szeregowej platformy Azure
Aby uzyskać dostęp do konsoli szeregowej na maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych, potrzebne są następujące elementy:

- Diagnostyka rozruchu musi być włączona dla maszyny wirtualnej
- Konto użytkownika, które używa uwierzytelniania hasła, musi istnieć na maszynie wirtualnej. Można utworzyć użytkownika opartego na hasłach za pomocą funkcji [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny wirtualnej. Wybierz **Resetuj hasło** z **pomoc techniczna i rozwiązywanie problemów z** sekcji.
- Konto platformy Azure z dostępem do konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) zarówno dla maszyny wirtualnej, jak i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md) .

> [!NOTE]
> - W przypadku wdrożeń klasycznych nie są obsługiwane. W maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyn wirtualnych musi być używany Azure Resource Manager model wdrażania.

## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych i zestawu skalowania maszyn wirtualnych jest dostępna tylko za pośrednictwem Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla Virtual Machines
Konsola szeregowa dla maszyn wirtualnych jest tak prosta jak kliknięcie **konsola szeregowa** w sekcji **Pomoc techniczna i rozwiązywanie problemów** w Azure Portal.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona przegląd dla maszyny wirtualnej.

  1. Przewiń w dół do **pomoc techniczna i rozwiązywanie problemów z** i wybierz pozycję **konsoli szeregowej**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

     ![Okno konsoli szeregowej systemu Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla Virtual Machine Scale Sets
Konsola szeregowa jest dostępna dla zestawów skalowania maszyn wirtualnych, które są dostępne dla każdego wystąpienia w zestawie skalowania. Przed wyświetleniem przycisku **konsola szeregowa** należy przejść do poszczególnych wystąpień zestawu skalowania maszyn wirtualnych. Jeśli zestaw skalowania maszyn wirtualnych nie ma włączonej diagnostyki rozruchu, należy zaktualizować model zestawu skalowania maszyn wirtualnych, aby umożliwić diagnostykę rozruchu, a następnie uaktualnić wszystkie wystąpienia do nowego modelu w celu uzyskania dostępu do konsoli szeregowej.
  1. Otwórz [portal Azure](https://portal.azure.com).

  1. Przejdź do opcji **wszystkie zasoby** i wybierz zestaw skalowania maszyn wirtualnych. Zostanie otwarta strona przegląd zestawu skalowania maszyn wirtualnych.

  1. Przejdź do **wystąpień**

  1. Wybierz wystąpienie zestawu skalowania maszyn wirtualnych

  1. W sekcji **Pomoc techniczna i rozwiązywanie problemów** wybierz pozycję **konsola szeregowa**. Nowe okienko z konsolą szeregową otwiera się i rozpoczyna połączenie.

     ![Konsola szeregowa zestawu skalowania maszyn wirtualnych z systemem Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Zaawansowane zastosowania konsoli szeregowej
Poza dostępem konsoli do maszyny wirtualnej można również użyć konsoli szeregowej platformy Azure dla następujących zasobów:
* Wysyłanie [polecenia żądania systemowego do maszyny wirtualnej](./serial-console-nmi-sysrq.md)
* Wysyłanie [przerwania niemaskowalnego do maszyny wirtualnej](./serial-console-nmi-sysrq.md)
* Bezpieczne [Ponowne uruchamianie lub wymuszone wymuszanie napędu maszyny wirtualnej](./serial-console-power-options.md)


## <a name="next-steps"></a>Następne kroki
Dodatkowa dokumentacja konsoli szeregowej jest dostępna na pasku bocznym.
- Więcej informacji można znaleźć w [konsoli szeregowej dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md).
- Więcej informacji można znaleźć w [konsoli szeregowej dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md).
