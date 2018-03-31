---
title: Uruchom i debugowania usługi w chmurze platformy Azure na komputerze lokalnym przy użyciu emulatora Express | Dokumentacja firmy Microsoft
description: Uruchom i debugowania usługi w chmurze na komputerze lokalnym przy użyciu emulatora Express
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
ms.openlocfilehash: 638aa005321963ce8bdcbb7df4317fc4bdec74ae
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2018
---
# <a name="using-emulator-express-to-run-and-debug-an-azure-cloud-service-on-a-local-machine"></a>Uruchom i debugowania usługi w chmurze platformy Azure na komputerze lokalnym przy użyciu emulatora Express
Przy użyciu emulatora Express, można testowanie i debugowanie usługi w chmurze bez konieczności uruchamiania programu Visual Studio jako administrator. Można ustawić ustawienia projektu, aby użyć emulatora Express lub pełnego emulatora, w zależności od wymagań usługi w chmurze. Aby uzyskać więcej informacji na temat pełnego emulatora, zobacz [uruchomić aplikację Azure w emulatorze obliczeniowe](storage/common/storage-use-emulator.md).

## <a name="using-emulator-express-in-visual-studio"></a>Przy użyciu ekspresowej wersji emulatora w programie Visual Studio
Podczas tworzenia projektu platformy Azure w wersji 2.3 zestawu SDK platformy Azure lub nowszym Express emulatora automatycznie jest używany. Dla istniejących projektów, które zostały utworzone przy użyciu starszej wersji zestawu SDK platformy Azure wykonaj następujące kroki, aby wybrać Emulator Express:

1. Utwórz lub Otwórz projekt usługi w chmurze platformy Azure w programie Visual Studio.

1. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz z menu kontekstowego **właściwości**.

1. Na stronach właściwości projektów wybierz **Web** kartę.

    ![Właściwości projektu usługi w chmurze Azure](./media/vs-azure-tools-emulator-express-debug-run/web-properties.png)

1. W obszarze **lokalnego serwera wdrożeniowego**, wybierz pozycję **opcję Użyj usługi IIS Express**.

1. W obszarze **emulatora**, wybierz pozycję **Express emulatora użyj**.
   
1. Aby uruchomić Express emulatora, uruchom następujące polecenie w wierszu polecenia: 

    ```
    csrun.exe /useemulatorexpress
    ```

## <a name="emulator-express-limitations"></a>Ograniczenia Express emulatora
Znane ograniczenia Express emulatora następujące problemy: 

- Ekspresowej wersji emulatora nie jest zgodny z serwera sieci Web usług IIS.
- Usługi w chmurze może zawierać wiele ról, ale jest ograniczony do jednego wystąpienia każdej roli.
- Nie można uzyskać dostępu do numerów portów poniżej 1000. Jeśli używasz dostawcę uwierzytelniania, która zwykle używa portu poniżej 1000, konieczne może zmienić tę wartość na numer portu, który jest ponad 1000.
- Ograniczenia dotyczące emulatora obliczeniowe Azure dotyczą również emulatora Express. Na przykład nie może mieć więcej niż 50 wystąpień roli dla wdrożenia. Aby uzyskać więcej informacji na temat obliczeniowe emulatora usługi Azure, zobacz [uruchomić aplikację Azure w emulatorze obliczeniowe](http://go.microsoft.com/fwlink/p/?LinkId=623050).

## <a name="next-steps"></a>Kolejne kroki
[Debugowanie usług w chmurze Azure](https://msdn.microsoft.com/library/azure/ee405479.aspx)
