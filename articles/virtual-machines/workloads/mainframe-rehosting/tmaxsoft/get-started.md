---
title: Rozpoczynanie pracy z usługą TmaxSoft OpenFrame na maszynach wirtualnych platformy Azure
description: Ponowne hostowanie obciążeń mainframe firmy IBM z/OS przy użyciu środowiska TmaxSoft OpenFrame w usłudze Azure Virtual Machines (VMs).
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 408e0166e52af9efd3d4c64f1b29bddcfc1cca4c
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896471"
---
# <a name="get-started-with-tmaxsoft-openframe-on-azure"></a>Rozpoczynanie pracy z usługą TmaxSoft OpenFrame na platformie Azure

Pobrać istniejących zasobów komputera mainframe i przenieść je do systemu Microsoft Azure przy użyciu TmaxSoft OpenFrame. To popularne rozwiązanie rehosting tworzy środowisko przy emulacji na platformie Azure, dzięki któremu można szybko migracji aplikacji. Nie ponownego formatowania jest wymagana.

## <a name="openframe-rehosting-environment"></a>OpenFrame rehosting środowiska

Skonfiguruj środowisko OpenFrame na platformie Azure na potrzeby programowania, pokazów, testów lub obciążeń produkcyjnych. Jak pokazano na poniższej ilustracji, OpenFrame zawiera wiele składników tworzonych w środowisku emulacji mainframe na platformie Azure. Na przykład usług online OpenFrame zastąpić mainframe oprogramowanie pośredniczące takie jak IBM klienta informacji kontroli systemu (CICS). OpenFrame Batch z jego składników TJES zastępuje mainframe firmy IBM zadania wpis podsystemu (JES). 

![Proces rehosting OpenFrame](media/openframe-01.png)

> [!NOTE]
> Aby uruchomić środowisko OpenFrame na platformie Azure, musi mieć ważną licencję lub licencję próbną z TmaxSoft.

## <a name="openframe-components"></a>Składniki OpenFrame

Następujące składniki są częścią środowiska OpenFrame na platformie Azure:

- **Narzędzia migracji** tym OFMiner, to rozwiązanie, które analizuje zasoby mainframe i przeprowadza ich migrację do platformy Azure.
- **Kompilatory**, tym OFCOBOL, kompilator interpretuje mainframe COBOL programy. OFPLI, który interpretuje mainframe PL / I programy; i OFASM, kompilatora, który interpretuje mainframe asemblera programy.
- **Fronton** składników, w tym Java Enterprise użytkownika rozwiązania (JEUS), serwer aplikacji sieci web, który posiada certyfikat dla oprogramowania Java Enterprise Edition 6.OFGW i składnika bramy OpenFrame, który udostępnia odbiornika 3270.
- **Aplikacja** środowiska. Podstawowy OpenFrame jest oprogramowanie pośredniczące, która zarządza całego systemu. OpenFrame serwera typu C (OSC) zastępuje mainframe oprogramowanie pośredniczące i CICS firmy IBM.
- **Relacyjna baza danych**, takich jak Tibero (pokazane), Oracle Database, programu Microsoft SQL Server, IBM Db2 lub MySQL. Aplikacje OpenFrame używają protokołu Open Database Connectivity (ODBC) do komunikowania się z bazą danych.
- **Zabezpieczenia** za pośrednictwem TACF, moduł usługi, która kontroluje dostęp użytkowników do systemów i zasobów. 
- **OFManager** to rozwiązanie, które zapewnia firmy OpenFrame operacji i funkcji zarządzania w środowisku sieci web.

![Architektura OpenFrame](media/openframe-02.png)

## <a name="next-steps"></a>Kolejne kroki

- [Instalowanie TmaxSoft OpenFrame na platformie Azure](./install-openframe-azure.md)
