---
title: Wprowadzenie do TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure
description: Hostuj ponownie obciążenia komputerów mainframe IBM z/OS przy użyciu środowiska TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure(VM).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "61485549"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Wprowadzenie do tmaxsoft OpenFrame na platformie Azure

Zabierz istniejące zasoby mainframe i przenieś je na platformę Microsoft Azure za pomocą TmaxSoft OpenFrame. To popularne rozwiązanie do ponownego hostowania tworzy środowisko emulacji na platformie Azure, umożliwiając szybkie migrowanie aplikacji. Nie jest wymagane formatowanie.

## <a name="openframe-rehosting-environment"></a>Środowisko ponownego hostowania OpenFrame

Skonfiguruj środowisko OpenFrame na platformie Azure w celu tworzenia, tworzenia, testowania lub tworzenia obciążeń. Jak pokazano na poniższej ilustracji, OpenFrame zawiera wiele składników, które tworzą środowisko emulacji mainframe na platformie Azure. Na przykład usługi online OpenFrame zastępują oprogramowanie pośredniczące typu mainframe, takie jak IBM Customer Information Control System (CICS). OpenFrame Batch, ze swoim komponentem TJES, zastępuje podsystem wejścia zadań IBM mainframe (JES). 

![Proces ponownego hostowania OpenFrame](media/openframe-01.png)

> [!NOTE]
> Aby uruchomić środowisko OpenFrame na platformie Azure, musisz mieć ważną licencję produktu lub licencję próbną firmy TmaxSoft.

## <a name="openframe-components"></a>Składniki OpenFrame

Następujące składniki są częścią środowiska OpenFrame na platformie Azure:

- **Narzędzia migracji,** w tym OFMiner, rozwiązanie, które analizuje zasoby komputerów mainframe, a następnie migruje je na platformę Azure.
- **Kompilatory**, w tym OFCOBOL, kompilator, który interpretuje programy COBOL komputera mainframe; OFPLI, który interpretuje mainframe pl / I programów; i OFASM, kompilator, który interpretuje programy asemblera mainframe.
- **Składniki frontonu,** w tym Rozwiązanie użytkownika Firmy Java Enterprise (JEUS), serwer aplikacji sieci web, który jest certyfikowany dla wersji Java Enterprise Edition 6.OFGW, oraz składnik bramy OpenFrame, który zapewnia odbiornik 3270.
- **Środowisko aplikacji.** OpenFrame Base to oprogramowanie pośredniczące, które zarządza całym systemem. OpenFrame Server Type C (OSC) zastępuje oprogramowanie pośredniczące mainframe i IBM CICS.
- **Relacyjnej bazy danych**, takich jak Tibero (pokazano), Oracle Database, Microsoft SQL Server, IBM Db2 lub MySQL. Aplikacje OpenFrame używają protokołu OPEN Database Connectivity (ODBC) do komunikowania się z bazą danych.
- **Zabezpieczenia** za pośrednictwem TACF, modułu usługi, który kontroluje dostęp użytkowników do systemów i zasobów. 
- **OFManager** to rozwiązanie, które zapewnia funkcje działania i zarządzania OpenFrame w środowisku internetowym.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Następne kroki

- [Instalowanie programu TmaxSoft OpenFrame na platformie Azure](./install-openframe-azure.md)
