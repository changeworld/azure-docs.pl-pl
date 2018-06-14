---
title: Rozwiązywanie problemów przy użyciu dzienników operacji usługi BizTalk Services | Dokumentacja firmy Microsoft
description: Rozwiązywanie problemów z usługi BizTalk Services przy użyciu dzienników operacji. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 1081a9c6-58cc-4a76-8ac8-11e5e7a6ea27
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 7d3a357e1a3929153288a9d99e21f2379bcac891
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102022"
---
# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Usługi BizTalk Services: Rozwiązywanie problemów przy użyciu dzienników operacji

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="what-are-the-operation-logs"></a>Co to są dzienniki operacji
Dzienniki operacji to funkcja usług zarządzania, która pozwala wyświetlać historyczne dzienniki operacji wykonywanych na usługami Azure, takich jak usługi BizTalk. Dzięki temu można wyświetlić dane historyczne dotyczące operacji zarządzania w ramach subskrypcji usługi BizTalk, nawet sprzed 180 dni.

> [!NOTE]
> Ta funkcja przechwytuje tylko dzienniki dla operacji zarządzania na usługi BizTalk, np. gdy usługa została uruchomiona, kopie włączone, i tak dalej. Takie operacje są śledzone za pomocą [interfejsów API REST usługi BizTalk](http://msdn.microsoft.com/library/azure/dn232347.aspx). Aby uzyskać pełną listę działań, które są śledzone za pomocą usług zarządzania, zobacz [operacji śledzone za pomocą zarządzania usług Azure](#bizops).<br/><br/>
> To nie przechwytywania w dziennikach działania związane z środowiska uruchomieniowego usługi BizTalk (na przykład komunikatów przetwarzanych przez mostków itd.). Aby wyświetlić te dzienniki, użyj widoku śledzenia z portalu usługi BizTalk Services. Aby uzyskać więcej informacji, zobacz [komunikatów śledzenia](http://msdn.microsoft.com/library/azure/hh949805.aspx).
> 
> 

## <a name="view-biztalk-services-operation-logs"></a>Wyświetl dzienniki operacji usługi BizTalk
1. W portalu, wybierz **usług zarządzania**, a następnie wybierz **dzienniki operacji** kartę.
2. Można filtrować dzienniki na podstawie różnych parametrów, takich jak subskrypcji, zakres dat, typ usługi (np. usługi BizTalk Services), nazwę usługi lub stanu operacji (Powodzenie, niepowodzenie).
3. Wybierz znacznik wyboru, aby wyświetlić listę filtrowane. Na poniższej ilustracji przedstawiono działania związane z testbiztalkservice: ![Sprawdź dzienniki operacji][ViewLogs] 
4. Aby wyświetlić więcej o określonej operacji, zaznacz wiersz, a następnie kliknij przycisk **szczegóły** na pasku zadań u dołu.

## <a name="bizops"></a>Operacje śledzone za pomocą usług zarządzania platformy Azure
W poniższej tabeli wymieniono operacje, które są śledzone za pomocą usług zarządzania platformy Azure:

| Nazwa operacji | Zadanie |
| --- | --- |
| CreateBizTalkService |Operację, aby utworzyć nową usługę BizTalk |
| DeleteBizTalkService |Operację można usunąć usługi BizTalk |
| RestartBizTalkService |Operacja ponownego uruchomienia usługi BizTalk |
| StartBizTalkService |Operację można uruchomić usługi BizTalk |
| StopBizTalkService |Operacja zatrzymania usługi BizTalk |
| DisableBizTalkService |Operacja wyłączania usługi BizTalk |
| EnableBizTalkService |Operacja, aby włączyć usługę BizTalk |
| BackupBizTalkService |Operacja do wykonania kopii zapasowej usługi BizTalk |
| RestoreBizTalkService |Operacja przywrócenia usługi BizTalk z określonej kopii zapasowej |
| SuspendBizTalkService |Operacja wstrzymania usługi BizTalk |
| ResumeBizTalkService |Operacja, aby wznowić działanie usługi BizTalk |
| ScaleBizTalkService |Operacja skalowania usługi BizTalk w górę lub w dół |
| ConfigUpdateBizTalkService |Operacja aktualizowania konfiguracji usługi BizTalk |
| ServiceUpdateBizTalkService |Operacja uaktualnienia lub starszą wersję usługi BizTalk do innej wersji |
| PurgeBackupBizTalkService |Operacja przeczyścić usługi BizTalk poza okresem przechowywania kopii zapasowych |

## <a name="see-also"></a>Zobacz też
* [Usługi BizTalk kopii zapasowej](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Przywracanie z kopii zapasowej usługi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [Usługi BizTalk Services: Developer, podstawowa, standardowa i Premium Edition wykresu](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [Usługi BizTalk Services: Inicjowanie obsługi administracyjnej](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png

