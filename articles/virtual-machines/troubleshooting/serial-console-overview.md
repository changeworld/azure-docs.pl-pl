---
title: Konsola szeregowa platformy Azure | Dokumenty firmy Microsoft
description: Konsola szeregowa platformy Azure umożliwia łączenie się z maszyną wirtualną, gdy SSH lub RDP nie są dostępne.
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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267016"
---
# <a name="azure-serial-console"></a>Konsola szeregowa platformy Azure

Konsola szeregowa w witrynie Azure portal zapewnia dostęp do konsoli tekstowej dla maszyn wirtualnych (VM) i scenografię skalowania maszyny wirtualnej z systemem Linux lub Windows. To połączenie szeregowe łączy się z portem szeregowym ttyS0 lub COM1 wystąpienia zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej, zapewniając dostęp niezależnie od stanu sieci lub systemu operacyjnego. Dostęp do konsoli szeregowej można uzyskać tylko za pomocą witryny Azure portal i jest dozwolony tylko dla tych użytkowników, którzy mają rolę dostępu współautora lub wyższego do zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej.

Konsola szeregowa działa w ten sam sposób dla maszyn wirtualnych i wystąpień zestawu skalowania maszyny wirtualnej. W tym doc wszystkie wzmianki do maszyn wirtualnych niejawnie będzie zawierać wystąpienia zestawu skalowania maszyny wirtualnej, chyba że określono inaczej.

> [!NOTE]
> Konsola szeregowa jest ogólnie dostępna w globalnych regionach platformy Azure i w publicznej wersji zapoznawczej w usłudze Azure Dla Instytucji Rządowych. Nie jest jeszcze dostępna w chmurze Azure China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Wymagania wstępne dostępu do konsoli szeregowej platformy Azure
Aby uzyskać dostęp do konsoli szeregowej na maszynie wirtualnej lub wystąpieniu zestawu skalowania maszyny wirtualnej, potrzebne są następujące elementy:

- Diagnostyka rozruchu musi być włączona dla maszyny Wirtualnej
- Konto użytkownika korzystające z uwierzytelniania hasłem musi istnieć w ramach maszyny Wirtualnej. Można utworzyć użytkownika opartego na hasłach z funkcją [resetowania hasła](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) rozszerzenia dostępu do maszyny Wirtualnej. Wybierz **pozycję Resetuj hasło** z sekcji Pomoc **techniczna + rozwiązywanie problemów.**
- Konto platformy Azure uzyskujące dostęp do konsoli szeregowej musi mieć [rolę współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) zarówno dla maszyny wirtualnej, jak i konta magazynu [diagnostyki rozruchu](boot-diagnostics.md)

> [!NOTE]
> Wdrożenia klasyczne nie są obsługiwane. Wystąpienie zestawu skalowania maszyny wirtualnej lub maszyny wirtualnej musi używać modelu wdrażania usługi Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Wprowadzenie do konsoli szeregowej
Konsola szeregowa dla maszyn wirtualnych i zestaw skalowania maszyny wirtualnej jest dostępna tylko za pośrednictwem witryny Azure portal:

### <a name="serial-console-for-virtual-machines"></a>Konsola szeregowa dla maszyn wirtualnych
Konsola szeregowa dla maszyn wirtualnych jest tak prosta, jak kliknięcie **konsoli szeregowej** w sekcji **Pomoc techniczna + rozwiązywanie problemów** w witrynie Azure portal.
  1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

  1. Przejdź do **pozycji Wszystkie zasoby** i wybierz maszynę wirtualną. Zostanie otwarta strona przeglądu maszyny wirtualnej.

  1. Przewiń w dół do sekcji **Pomoc techniczna + rozwiązywanie problemów** i wybierz **pozycję Konsola szeregowa**. Otworzy się nowe okienko z konsolą szeregową i rozpocznie połączenie.

     ![Okno Konsoli szeregowa linuksa](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Konsola szeregowa dla zestawów skalowania maszyny wirtualnej
Konsola szeregowa jest dostępna dla zestawów skalowania maszyny wirtualnej, dostępnych dla każdego wystąpienia w zestawie skalowania. Przed obejrzeniem przycisku **Konsoli szeregowej** należy przejść do pojedynczego wystąpienia zestawu skalowania maszyny wirtualnej. Jeśli zestaw skalowania maszyny wirtualnej nie ma włączonej diagnostyki rozruchu, upewnij się, że zaktualizowano model zestawu skalowania maszyny wirtualnej, aby włączyć diagnostykę rozruchu, a następnie uaktualnić wszystkie wystąpienia do nowego modelu w celu uzyskania dostępu do konsoli szeregowej.
  1. Otwórz witrynę [Azure Portal](https://portal.azure.com).

  1. Przejdź do **pozycji Wszystkie zasoby** i wybierz zestaw skalowania maszyny wirtualnej. Zostanie otwarta strona przeglądu zestawu skalowania maszyny wirtualnej.

  1. Przejdź do **wystąpień**

  1. Wybieranie wystąpienia zestawu skalowania maszyny wirtualnej

  1. W sekcji **Pomoc techniczna + rozwiązywanie problemów** wybierz pozycję **Konsola szeregowa**. Otworzy się nowe okienko z konsolą szeregową i rozpocznie połączenie.

     ![Zestaw skalowania maszyny wirtualnej Linuksa](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Zaawansowane zastosowania konsoli szeregowych
Oprócz dostępu do konsoli do maszyny Wirtualnej, można również użyć konsoli szeregowej platformy Azure dla następujących funkcji:
* Wysyłanie [polecenia żądania systemu do maszyny Wirtualnej](./serial-console-nmi-sysrq.md)
* Wysyłanie [przerwania niemaskowania do maszyny Wirtualnej](./serial-console-nmi-sysrq.md)
* Wdzięku [ponowne uruchomienie lub moc jazdy na rowerze maszyny Wirtualnej](./serial-console-power-options.md)


## <a name="next-steps"></a>Następne kroki
Dodatkowa dokumentacja konsoli szeregowej jest dostępna na pasku bocznym.
- Więcej informacji jest dostępnych dla [konsoli szeregowych dla maszyn wirtualnych z systemem Linux](./serial-console-linux.md).
- Więcej informacji jest dostępnych dla [konsoli szeregowych dla maszyn wirtualnych z systemem Windows](./serial-console-windows.md).
