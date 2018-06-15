---
title: Zadania, dozwolone w różnych stanów oraz Stany w usługi BizTalk Services | Dokumentacja firmy Microsoft
description: 'Akcje operacje dozwolone w inny stan MABS: Zatrzymaj, start, uruchom ponownie, wstrzymać, wznowić, Usuń, skalowania, aktualizacji konfiguracji i tworzenia kopii zapasowej'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 05470e75fc7b46603c8fce3a98c66ac6a24758a8
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102746"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Co można i nie można wykonać przy użyciu stanu usługi BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

W zależności od bieżącego stanu usługi BizTalk ma działań, które mogą lub nie można wykonać na usługi BizTalk.

Na przykład można udostępnić nową usługę BizTalk. Po pomyślnym ukończeniu, usługa BizTalk jest `active` stanu. W stanie aktywnym Zatrzymaj, Wstrzymaj, a usunąć usługi BizTalk. Jeśli należy zatrzymać usługę BizTalk i Zatrzymaj zakończy się niepowodzeniem, a następnie usługi BizTalk przechodzi do `StopFailed` stanu. W `StopFailed` stanu, można ponownie uruchomić usługę BizTalk. Jeśli spróbujesz operację, która nie jest dozwolona, takich jak wznowieniu wystąpił następujący błąd:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Możliwe stany

W poniższych tabelach przedstawiono operacje lub akcje, które można wykonać, gdy usługa BizTalk znajduje się w określonym stanie. ✔ oznacza, że operacja jest dozwolona w tym stanie. Pusty wpis oznacza, że znajduje się w tym stanie nie można wykonać operacji.

| Stan usługi | Uruchamianie | Stop | Ponowne uruchamianie | Wstrzymaj | Resume | Usuwanie | Skalowanie | Aktualizacja <br/> Konfiguracja | Tworzenie kopii zapasowych |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Aktywne |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Disabled (Wyłączony) |  |  |  |  |  | ✔ | |  |  | 
| Zawieszone |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Zatrzymane | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Aktualizacja usługi nie powiodło się |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |  | 



## <a name="see-also"></a>Zobacz też
* [Co można zrobić w kartach pulpitu nawigacyjnego, monitor i skalowania w usługi BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Pobierz Developer, podstawowa, standardowa i Premium Edition w usługi BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Jak wykonać kopię zapasową i przywrócić usługi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Ograniczanie wyjaśniono w usługi BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Pobrać usługi Service Bus i kontroli dostępu wystawcy nazwy i wystawców wartości klucza dla usługi BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

