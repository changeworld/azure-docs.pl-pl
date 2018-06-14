---
title: Dowiedz się więcej o ograniczaniu przepustowości w usługi BizTalk Services | Dokumentacja firmy Microsoft
description: Więcej informacji na temat ograniczania progów i wynikowa zachowanie w czasie wykonywania dla usługi BizTalk Services. Ograniczanie opiera się na użycie pamięci i liczbę komunikatów. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: f6663cf2-cda4-4bac-855e-27d2ad5c4fa4
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 39fc5ef36bb581c3a81c9948fda048f6cb75eb7e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2017
ms.locfileid: "24102090"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Usługi BizTalk Azure implementuje ograniczania usługi na podstawie dwóch warunków: użycie pamięci i liczbę jednoczesnych przetwarzania komunikatów. W tym temacie wymieniono progi ograniczania przepustowości i opisano zachowania w czasie wykonywania, jeśli dławienia stan występuje.

## <a name="throttling-thresholds"></a>Progi ograniczania przepustowości
W poniższej tabeli wymieniono progi i ograniczania przepustowości źródła:

|  | Opis | Dolna wartość progowa | Górny próg |
| --- | --- | --- | --- |
| Memory (Pamięć) |% całkowitej systemu pamięci dostępne/PageFileBytes. <p><p>Całkowita liczba dostępnych PageFileBytes wynosi około 2 razy pamięci RAM systemu. |60% |70% |
| Przetwarzanie komunikatów |Liczba komunikatów przetwarzania jednocześnie |40 * liczba rdzeni |100 * Liczba rdzeni |

Po osiągnięciu progu wysokiego usług BizTalk Azure rozpoczyna ograniczenie przepustowości. Ograniczanie zatrzymuje po osiągnięciu progu dolnego. Na przykład usługi używa 65% pamięci systemowej. W takiej sytuacji usługa nie ograniczenie przepustowości. Usługa uruchamia przy użyciu 70% pamięci systemowej. W takiej sytuacji usługa ogranicza i ograniczenie przepustowości, dopóki usługa korzysta z pamięci systemowej (progu dolnego) 60% w dalszym ciągu.

Usługi BizTalk Azure śledzi stan ograniczenia przepustowości (normalnym stanie a stanu ograniczeniem przepustowości) i czas trwania ograniczania przepustowości.

## <a name="runtime-behavior"></a>Zachowania w czasie wykonywania
Gdy usługi BizTalk Azure przechodzi do stanu ograniczania przepustowości, są następujące operacje:

* Ograniczenie jest dla każdego wystąpienia roli. Na przykład:<br/>
  RoleInstanceA jest ograniczanie. Nie jest ograniczanie RoleInstanceB. W takim przypadku komunikatów w RoleInstanceB są przetwarzane zgodnie z oczekiwaniami. Komunikaty w RoleInstanceA zostaną odrzucone i zakończyć się niepowodzeniem z powodu następującego błędu:<br/><br/>
  **Serwer jest zajęty. Spróbuj ponownie.**<br/><br/>
* Wszystkie źródła ściągania nie sondowania lub pobierania wiadomości. Na przykład:<br/>
  Potok ściąga komunikatów ze źródła zewnętrznego FTP. Pobiera wystąpienia roli, podczas replikacji ściąganej stan ograniczania przepustowości. W takiej sytuacji potoku zatrzymuje pobieranie dodatkowych komunikatów, do momentu wystąpienia roli zatrzymuje ograniczania.
* Odpowiedź jest wysyłane do klienta, dzięki czemu klient może ponownie przesłać wiadomości.
* Musisz poczekać, aż ograniczenie został rozwiązany. W szczególności należy poczekać, aż do osiągnięcia dolna wartość progowa.

## <a name="important-notes"></a>Ważne uwagi
* Nie można wyłączyć ograniczenia przepustowości.
* Nie można zmodyfikować ograniczania progów.
* Ograniczanie zaimplementowano całego systemu.
* Serwer bazy danych SQL Azure ma również wbudowane ograniczenia przepustowości.

## <a name="additional-azure-biztalk-services-topics"></a>Dodatkowe tematy Azure BizTalk Services
* [Instalowanie usług Azure BizTalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Samouczki: Usługi Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Usługi Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Zobacz też
* [Usługi BizTalk Services: Developer, podstawowa, standardowa i Premium Edition wykresu](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

