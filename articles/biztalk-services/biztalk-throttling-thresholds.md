---
title: Więcej informacji na temat ograniczania przepustowości w usłudze BizTalk Services | Dokumentacja firmy Microsoft
description: Więcej informacji o wartościach progowych ograniczania przepustowości i wynikowa zachowaniami usługi BizTalk Services. Ograniczanie opiera się na użycie pamięci i liczbę komunikatów. MABS, WABS
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
ms.openlocfilehash: daab61a0ea9321b0fb918c60688215c80088e0bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243355"
---
# <a name="biztalk-services-throttling"></a>BizTalk Services: Throttling (Usługa BizTalk Services: ograniczanie przepływności)

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Usługa Azure BizTalk Services implementuje ograniczenie usługi oparte na dwa warunki: użycie pamięci i liczbę jednoczesnych przetwarzania komunikatów. W tym temacie wymieniono wartościach progowych ograniczania przepustowości i opisano zachowanie środowiska uruchomieniowego, w przypadku wystąpienia warunku ograniczenia.

## <a name="throttling-thresholds"></a>Wartości progowe ograniczania przepustowości
W poniższej tabeli wymieniono ograniczenia przepustowości źródłowej i progów:

|  | Opis | Dolna wartość progowa | Górny próg |
| --- | --- | --- | --- |
| Memory (Pamięć) |procent całkowitej system pamięci dostępne/PageFileBytes. <p><p>Łączna liczba dostępnych PageFileBytes to około 2 razy pamięć RAM systemu. |60% |70% |
| Przetwarzanie komunikatów |Liczba wiadomości jednocześnie przetwarzania |40 * liczba rdzeni |100 * Liczba rdzeni |

Po osiągnięciu progu wysokiego rozpoczyna ograniczania usługi Azure BizTalk Services. Ograniczanie zatrzymuje po osiągnięciu progu dolnego. Na przykład usługa używa 65% pamięci systemowej. W takiej sytuacji usługa nie ograniczenie przepustowości. Twoja usługa jest uruchamiana przy użyciu 70% pamięci systemowej. W takiej sytuacji usługa ogranicza i ograniczać, dopóki usługa korzysta z pamięci systemowej (dolna wartość progowa) 60% w dalszym ciągu.

Usługa Azure BizTalk Services umożliwia śledzenie stanu ograniczania (normalny stan i stan ograniczone) i ograniczania czasu trwania.

## <a name="runtime-behavior"></a>Zachowanie środowiska uruchomieniowego
Gdy usługa Azure BizTalk Services przechodzi do stanu ograniczania przepustowości, zostaną wykonane następujące zadania:

* Ograniczanie przepływności jest dla każdego wystąpienia roli. Na przykład:<br/>
  RoleInstanceA jest ograniczanie. Nie ogranicza RoleInstanceB. W takiej sytuacji wiadomości w RoleInstanceB są przetwarzane zgodnie z oczekiwaniami. Komunikaty w RoleInstanceA są odrzucane, a kończy się niepowodzeniem z powodu następującego błędu:<br/><br/>
  **Serwer jest zajęty. Spróbuj ponownie później.**<br/><br/>
* Żadne źródło ściągnięcia nie sondowania lub pobierania wiadomości. Na przykład:<br/>
  Potok baza danych ściąga wiadomości z zewnętrznego źródła FTP. Pobiera wystąpienie roli, wykonując ściągnięcia do ograniczania stanu. W takiej sytuacji potoku zatrzymuje się pobieranie dodatkowych komunikatów, dopóki wystąpienie roli zatrzymuje, ograniczanie przepustowości.
* Odpowiedź jest wysyłana do klienta, dzięki czemu klient może ponownie przesłać wiadomość.
* Należy poczekać, aż ograniczenie nie zostanie rozwiązany. W szczególności należy poczekać, aż do osiągnięcia progu dolnego.

## <a name="important-notes"></a>Ważne uwagi
* Nie można wyłączyć ograniczenia przepustowości.
* Nie można zmodyfikować wartości progowe ograniczania przepustowości.
* Ograniczanie przepustowości jest implementowany całego systemu.
* Serwer bazy danych SQL Azure ma również wbudowane ograniczenia przepustowości.

## <a name="additional-azure-biztalk-services-topics"></a>Dodatkowe tematy usługi Azure BizTalk Services
* [Instalowanie zestawu SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
* [Samouczki: Azure BizTalk Services —](https://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
* [Jak rozpocząć pracę z zestawem SDK usługi Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
* [Usługa Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Zobacz też
* [Usługa BizTalk Services: Deweloper, podstawowa, standardowa i Premium Editions Chart](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [BizTalk Services: Provisioning Status Chart (Usługa BizTalk Services: aprowizowanie wykresu stanu)](https://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
* [BizTalk Services: Dashboard, Monitor and Scale tabs (Usługa BizTalk Services: karty Pulpit nawigacyjny, Monitor i Skalowanie)](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [BizTalk Services: Backup and Restore (Usługa BizTalk Services: tworzenie kopii zapasowej i przywracanie)](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [BizTalk Services: Issuer Name and Issuer Key (Usługa BizTalk Services: nazwa i klucz wydawcy)](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>

