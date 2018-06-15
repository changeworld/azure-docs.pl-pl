---
title: Pobierać i wyodrębniać Azure stosu Development Kit (ASDK) | Dokumentacja firmy Microsoft
description: Opisuje sposób pobierać i wyodrębniać Azure stosu Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 0cf389c9443a9cff477b884c277d72de27769afc
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/17/2018
ms.locfileid: "29975881"
---
# <a name="download-and-extract-the-azure-stack-development-kit-asdk"></a>Pobierać i wyodrębniać Azure stosu Development Kit (ASDK)
Po upewnieniu się, że komputer-host zestawu programowanie spełnia podstawowe wymagania dotyczące instalowania ASDK, następnym krokiem jest pobierać i wyodrębniać pakietu wdrożeniowego ASDK, aby uzyskać Cloudbuilder.vhdx.

## <a name="download-the-asdk"></a>Pobierz ASDK
1. Aby rozpocząć pobieranie, upewnij się, że komputer spełnia następujące wymagania wstępne:

  - Komputer musi mieć co najmniej 60 GB wolnego miejsca na dysku na czterech oddzielnych, identyczne logiczne dyski twarde dodatkowo do dysku systemu operacyjnego.
  - [.NET framework 4.6 (lub nowszym)](https://aka.ms/r6mkiy) musi być zainstalowany.

2. [Przejdź do strony wprowadzenie](https://azure.microsoft.com/overview/azure-stack/try/?v=try) gdy można pobrać Azure stosu Development Kit, podać swoje szczegóły dotyczące i kliknięcie **przesyłania**.
3. Pobierz i uruchom [sprawdzanie wdrożenia dla zestawu SDK usługi Azure stosu](https://go.microsoft.com/fwlink/?LinkId=828735&clcid=0x409) skryptu narzędzia sprawdzania wymagań wstępnych. Ten skrypt autonomiczny przechodzi przez kontrole wstępne wykonywane przez Instalatora dla zestawu SDK usługi Azure stosu. Zapewnia sposób upewnij się, że są spełniane wymagania dotyczące sprzętu i oprogramowania przed pobraniem większych pakietu dla zestawu SDK usługi Azure stosu.
4. W obszarze **Pobierz oprogramowanie**, kliknij przycisk **Azure stosu Development Kit**.

  > [!NOTE]
  > Pobieranie ASDK (AzureStackDevelopmentKit.exe) wynosi około 10GB.

## <a name="extract-the-asdk"></a>Wyodrębnij ASDK
1. Po zakończeniu pobierania kliknij **Uruchom** można uruchomić ASDK samorozpakowujący się plik typu (AzureStackDevelopmentKit.exe).
2. Przejrzyj i zaakceptuj umowę licencyjną wyświetlanych z **umowy licencyjnej** strony kreatora samorozpakowujący się plik typu, a następnie kliknij przycisk **dalej**.
3. Zapoznaj się z informacjami instrukcji prywatności wyświetlany na **ważna Uwaga** strony kreatora samorozpakowujący się plik typu, a następnie kliknij przycisk **dalej**.
4. Wybierz lokalizację plików instalacyjnych stosu Azure do wyodrębnienia za **wybierz lokalizację docelową** strony kreatora samorozpakowujący się plik typu, a następnie kliknij przycisk **dalej**. Domyślna lokalizacja to *bieżący folder*\Azure stosu Development Kit. 
5. Przejrzyj podsumowanie w lokalizacji docelowej **gotowy do wyodrębniania** strony kreatora samorozpakowujący się plik typu, a następnie kliknij przycisk **wyodrębnić** wyodrębnić CloudBuilder.vhdx (około 25 GB) i Pliki ThirdPartyLicenses.rtf. Ten proces trwa trochę czasu.
6. Skopiować lub przenieść pliku CloudBuilder.vhdx w katalogu głównym dysku C:\ (C:\CloudBuilder.vhdx) na komputerze hosta ASDK.

> [!NOTE]
> Po wyodrębnieniu plików, można usunąć. EXE i. BIN plików, aby odzyskać miejsce na dysku twardym. Alternatywnie można tworzyć kopie zapasowe zapasowej tych plików, dzięki czemu nie trzeba ponownie pobrać pliki, jeśli chcesz ponownie wdrożyć ASDK.


## <a name="next-steps"></a>Kolejne kroki
[Przygotuj komputer-host ASDK](asdk-prepare-host.md)