---
title: Zadania, dozwolone w różnych stanach lub stanów usługi BizTalk Services | Dokumentacja firmy Microsoft
description: 'Dozwolone w inny stan serwera usługi Mab akcji/operacje: Zatrzymaj, start, ponowne uruchomienie, wstrzymać, wznowić, Usuń, skalowanie, aktualizowanie konfiguracji i tworzenia kopii zapasowej'
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
ms.openlocfilehash: 71463b523f0afdf63a7c89ff2b3137ae8d091b3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086704"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Tym, co można, a czego nie można wykonać przy użyciu stanu usługi BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

W zależności od bieżącego stanu usługi BizTalk istnieją operacje, które mogą lub nie można wykonać w usłudze BizTalk.

Na przykład możesz aprowizować nową usługę BizTalk. Po pomyślnym zakończeniu, usługa BizTalk jest `active` stanu. W stanie aktywnym można zatrzymać, wstrzymać i usuwać usługi BizTalk. Jeśli należy zatrzymać usługę BizTalk i Zatrzymaj zakończy się niepowodzeniem, a następnie usługa BizTalk przechodzi do `StopFailed` stanu. W `StopFailed` stanu, można ponownie uruchomić usługę BizTalk service. Jeśli spróbujesz operacji, która nie jest dozwolona, np. Wznów wystąpi następujący błąd:

`Operation not allowed`

## <a name="view-the-possible-states"></a>Możliwe stany

W poniższej tabeli wymieniono operacje lub akcje, które mogą realizować, gdy usługa BizTalk Services jest w określonym stanie. ✔ oznacza, że ta operacja jest dozwolona w tym stanie. Pusty element oznacza, że nie można wykonać operacji znajduje się w tym stanie.

| Stan usługi | Uruchamianie | Stop | Ponowne uruchamianie | Wstrzymaj | Resume | Usuwanie | Skalowanie | Aktualizacja <br/> Konfigurowanie | Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Aktywne |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Disabled (Wyłączony) |  |  |  |  |  | ✔ | |  |  | 
| Wstrzymane |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Zatrzymano | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Aktualizacja usługi nie powiodło się |  |  |  |  |  | ✔ | |  |  | 
| DisableFailed |  |  |  |  |  | ✔ | |  |  | 
| EnableFailed |  |  |  |  |  | ✔ | |  |  | 
| StartFailed <br/> StopFailed <br/> RestartFailed | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| SuspendedFailed <br/> ResumeFailed|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| CreatedFailed <br/> RestoreFailed |  |  |  |  |  | ✔ | |  |  | 
| ConfigUpdateFailed  |  |  | ✔ |  |  | ✔ | |✔ | |
| ScaleFailed |  |  |  |  |  | ✔ |✔ | |  |



## <a name="see-also"></a>Zobacz też
* [Co można zrobić na kartach pulpit nawigacyjny, monitor i skalowanie w usłudze BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Co można uzyskać za pomocą wersji Developer, Basic, Standard i Premium w usłudze BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Jak utworzyć kopię zapasową i przywrócić usługi BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Ograniczanie wyjaśnione w usłudze BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Pobieranie usługi Service Bus i Access Control wystawcy nazwy i Wystawca klucza wartości dla usługi BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

