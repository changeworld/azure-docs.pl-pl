---
title: Za pomocą Emulator Express uruchamianie i debugowanie usługi w chmurze platformy Azure na maszynie lokalnej | Dokumentacja firmy Microsoft
description: Za pomocą Emulator Express uruchamianie i debugowanie usługi w chmurze na komputerze lokalnym
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 73108f98-a552-4817-b7a1-551367b71906
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/06/2017
ms.author: mikejo
ms.openlocfilehash: 3432e8778b2c5b5fd4db53a82ca23b55d75bcac5
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969538"
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Za pomocą Emulator Express uruchamianie i debugowanie usługi w chmurze platformy Azure na maszynie lokalnej
Za pomocą Emulator Express, można przetestować i debugowanie usługi w chmurze bez konieczności uruchamiania programu Visual Studio jako administrator. Możesz ustawić ustawienia projektu, aby użyć Emulator Express lub pełnego emulatora, w zależności od wymagań usługi w chmurze. Aby uzyskać więcej informacji na temat pełnego emulatora, zobacz [uruchamiania aplikacji platformy Azure w emulatorze obliczeń](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Za pomocą Emulator Express w programie Visual Studio
Podczas tworzenia projektu platformy Azure w systemie Azure SDK 2.3 lub nowszym, Emulator Express automatycznie jest używana. W przypadku istniejących projektów, które zostały utworzone we wcześniejszej wersji zestawu SDK platformy Azure wykonaj następujące kroki, aby wybrać Emulator Express:

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz z menu kontekstowego **właściwości**.

1. Na stronach właściwości projektów, wybierz **Web** kartę.

    ![Właściwości projektu usługi w chmurze platformy Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. W obszarze **lokalnego serwera wdrożeniowego**, wybierz opcję **opcję Użyj usług IIS Express**.

1. W obszarze **emulatora**, wybierz opcję **Użyj Emulator Express**.
   
1. Aby uruchomić Emulator Express, uruchom następujące polecenie w wierszu polecenia: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Emulator Express ograniczenia
Znane ograniczenia Emulator Express następujące problemy: 

- Emulator Express nie jest zgodna z serwerem sieci Web usług IIS.
- Usługi w chmurze może zawierać wiele ról, ale każda rola jest ograniczona do jednego wystąpienia.
- Nie masz dostępu do numerów portów poniżej 1000. Jeśli używasz dostawcy uwierzytelniania, który zwykle używa portu poniżej 1000, może być konieczne Zmień tę wartość na numer portu, który jest ponad 1000.
- Wszelkie ograniczenia, które są stosowane do emulatora obliczeń platformy Azure dotyczą również Emulator Express. Na przykład nie może mieć więcej niż 50 wystąpień roli na wdrożenie. Aby uzyskać więcej informacji o emulatorze obliczeń systemu Azure, zobacz [uruchamiania aplikacji platformy Azure w emulatorze obliczeń](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie usług Azure cloud services](https://msdn.microsoft.com/library/azure/ee405479.aspx)
