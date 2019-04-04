---
title: Migrowanie rozwiązań EDI serwera BizTalk Server do usługi BizTalk Services | Dokumentacja firmy Microsoft
description: Dowiedz się, jak można przeprowadzić migrację rozwiązań EDI serwera BizTalk Server firmy Microsoft do firmy Microsoft Azure BizTalk Services (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: e6f0b11c99cbe8778b51024c418ffba70da61a77
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58917383"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrowanie rozwiązań EDI serwera BizTalk Server do usługi BizTalk Services: Podręcznik techniczny

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Tim Wieman i Nitin Mehrotra

Recenzenci: Karthik Bharthy

Napisane przy użyciu:  Microsoft Azure BizTalk Services — wersji z lutego 2014 r.

## <a name="introduction"></a>Wprowadzenie
Elektronicznej wymiany danych (EDI) jest jednym z najbardziej rozpowszechnionych oznacza, że przez dane programu exchange, które firmom elektronicznie, również określane jako transakcje Business-to-Business lub B2B. BizTalk Server miał Obsługa ponad dziesięciu lat od czasu wydania wstępnego programu BizTalk Server EDI. Dzięki usłudze BizTalk Services firma Microsoft nadal obsługę rozwiązań EDI na platformie Microsoft Azure. Transakcji B2B są prawie zewnętrzne w stosunku do organizacji i dlatego jest łatwiejsze do wdrożenia, jeśli został wdrożony na platformie w chmurze. Microsoft Azure zapewnia tę funkcję za pomocą usługi BizTalk Services.

Chociaż niektórzy klienci przyjrzyj usługi BizTalk Services jako platforma "green field" dla nowych rozwiązań EDI, wielu klientów ma bieżącego rozwiązań EDI serwera BizTalk Server, które chcą migrować na platformę Azure. Ponieważ EDI usługi BizTalk została zaprojektowana na podstawie tych samych jednostkach kluczowych jako architektura EDI serwera BizTalk Server (handlowymi partnerów, jednostki, umowy), jest możliwe do migracji artefaktów EDI serwera BizTalk Server do usługi BizTalk.

W tym dokumencie omówiono niektóre różnice związane z migrowaniem artefaktów EDI serwera BizTalk Server do usługi BizTalk Services. Ten dokument zakłada praktyczną wiedzę na temat umów z partnerami handlowymi i przetwarzania EDI serwera BizTalk Server. Aby uzyskać więcej informacji na temat EDI serwera BizTalk Server, zobacz [handlowymi partnera zarządzania za pomocą programu BizTalk Server](/biztalk/core/trading-partner-management-using-biztalk-server).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Którą wersję artefaktów EDI serwera BizTalk Server, można przeprowadzić migrację do usługi BizTalk Services?
Moduł EDI serwera BizTalk Server zostały znacznie rozszerzone dla programu BizTalk Server 2010, gdy został on remodeled obejmujący partnerów, profilów i umowy. Usługa BizTalk Services używa tego samego modelu do organizowania partnerów handlowych i działów firmy, w ramach tych partnerów handlowych. W wyniku migracji EDI artefaktów z programu BizTalk Server 2010 i nowszych wersji do usługi BizTalk Services, jest procesem bardziej proste. Do migracji artefaktów EDI skojarzony w wersjach wcześniejszych niż BizTalk Server 2010, należy najpierw przeprowadzić uaktualnienie do programu BizTalk Server 2010 i następnie przeprowadzić migrację artefaktów EDI do usługi BizTalk Services.

## <a name="scenariosmessage-flow"></a>Przepływ scenariuszy/komunikatów
Jak za pomocą programu BizTalk Server EDI przetwarzania w usłudze BizTalk Services opiera się na rozwiązanie handlowymi partnera zarządzania (TPM). Rozwiązanie modułu TPM ma następujące najważniejsze składniki:

* Partnerów handlowych, które reprezentują organizacji w ramach transakcji B2B.
* Profile, które reprezentują oddziałami partnerem handlowym.
* Handlowymi umów z partnerami (lub umowy), które reprezentują umowie biznesowej między dwoma partnerami/profile.

Poniższa ilustracja przedstawia podobieństwa, a także różnice między rozwiązań EDI serwera BizTalk Server i BizTalk Services EDI rozwiązania:

![][EDImessageflow]

Podstawowe różnice i podobieństwa EDI przepływu rozwiązania w programie BizTalk Server i BizTalk Services są:

* Podobnie programu BizTalk Server używa potoku EDIReceive do odbierania komunikatów EDI i potoku EDISend wysyłanie komunikatów EDI, usługi BizTalk Services używa otrzymywać EDI mostka do odbierania i wysyłania EDI Mostek wysyłanie komunikatów EDI. W programie BizTalk Server potoki nie są skojarzone z umową za pomocą wysyłania lub odbierania portów. W usłudze BizTalk Services samej Umowy oznacza wysyłania lub odbierania mostka.
* W programie BizTalk Server po potoku EDIReceive przetwarzania komunikatów EDI komunikat jest zrzucany do bazy danych programu SQL Server. Potok EdiSend następnie pobiera komunikat z bazy danych programu SQL Server, przetwarza je i wysyła je z partnerem handlowym.
  
    W usłudze BizTalk Services po otrzymaniu EDI Mostek przetwarzania komunikatów EDI, kieruje wiadomości procesu zewnętrznego. Proces zewnętrzny, może być uruchomiony na Microsoft Azure lub lokalnie. Proces zewnętrzny powinien kierować wiadomości do mostka wysyłania EDI; Mostek wysyłania nie ściąga natury wiadomości. Po przetworzeniu komunikatu, Most wysyłania EDI trasy wiadomości z partnerem handlowym.

Usługa BizTalk Services oferuje możliwość konfiguracji łatwy w użyciu szybkie tworzenie i wdrażanie umowę B2B między partnerami handlowymi bez konieczności konfigurowania żadnych wystąpień obliczeniowych Azure firmy Microsoft (role sieć Web lub proces roboczy), wszystkie bazy danych SQL Azure firmy Microsoft lub dowolnego Kont usługi Microsoft Azure storage. Wiązanie w przepływach pracy lub inne procesy przetwarzania usługi wymaga bardziej złożonych scenariuszy "wokół krawędzi" Umowy partnerów handlowych, oznacza to, że przed lub po zakończeniu przetwarzania Mostek handlowymi EDI umowę partnera. Poniższa sekwencja zdarzeń szczegółowo, występują w trakcie przetwarzania komunikatów EDI w usłudze BizTalk Services.

1. Z obrotu partner, firma Fabrikam odebraniu komunikatu EDI.  Do odbierania komunikatów EDI z partnerami handlowymi, usługi BizTalk Services obsługuje protokoły transportu, takie jak FTP, SFTP, AS2 i HTTP/s
2. Handlowym przetwarzania po stronie odbierającej umowę partnera dezasembluje komunikatów EDI w formacie XML.  Dezasemblowany komunikatów EDI (w formacie XML) można kierować do punktów końcowych usługi Service Bus, takie jak punkt końcowy usługi Service Bus Relay, tematu usługi Service Bus, kolejki usługi Service Bus lub Most usługi BizTalk Services.
3. Następnie być odbierane dezasemblowany komunikaty XML z punktu końcowego w celu dalszego przetwarzania niestandardowego.  Te punkty końcowe można przetworzyć przez składnik w środowisku lokalnym lub wystąpienia programu Microsoft Azure Compute do procesu dalszych komunikatów w usłudze Windows przepływu pracy (WF) lub Windows Communication Foundation (WCF), na przykład.
4. "Przetwarzania po stronie wysyłania" z partnerem handlowym umowy następnie składa komunikat XML do formatu EDI i wysyła je do partnerów handlowych, Contoso.  Wysyłanie komunikatów EDI do partnerów handlowych, usługi BizTalk Services obsługuje protokoły używane do odbierania komunikatów EDI.

Ten dokument dalsze zawiera ogólne wskazówki na migracji niektórych różne artefakty EDI serwera BizTalk Server do usługi BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Funkcję wysyłania i odbierania portów do partnerów handlowych
W programie BizTalk Server skonfigurowaniu lokalizacji odbioru usługi i portów odbioru w celu odbierania komunikatów EDI/XML z partnerami handlowymi i Ustaw porty wysyłania do wysyłania komunikatów EDI/XML do partnerów handlowych. Następnie powiązać te porty do Umowy z partnerem handlowym za pomocą konsoli administrowania programem BizTalk Server. W usłudze BizTalk Services, lokalizacji, w których chcesz otrzymywać wiadomości od partnerów handlowych i gdzie wysłać wiadomości do partnerów handlowych są skonfigurowane jako część umowy z partnerem handlowym sam (jako część ustawień Transport) w portalu usługi BizTalk Services.  Tak naprawdę braku koncepcji "Wyślij portów" i "otrzymywać lokalizacje", per se w usłudze BizTalk Services. Aby uzyskać więcej informacji, zobacz [Tworzenie umów](/previous-versions/azure/hh689908(v=azure.100)).

## <a name="pipelines-bridges"></a>Potoki (mostków)
W EDI serwera BizTalk Server potoki są jednostki przetwarzania komunikatów, które mogą również obejmować niestandardowej logiki możliwości przetwarzania specyficznego, zgodnie z wymaganiami aplikacji. Usługi BizTalk Services to równoważne będzie Mostek EDI. Jednak w usłudze BizTalk Services teraz mostków integracji EDI są "zamknięte".  Oznacza to, że nie można dodać działania niestandardowe do mostka EDI. Dowolne niestandardowe procesy przetwarzania musi odbywać się poza Mostek EDI w Twojej aplikacji, przed lub po komunikat przechodzi most, skonfigurowany jako część umowy partnerów handlowych. Mostków integracji EAI mogą wykonywać niestandardowe przetwarzanie. Chcąc niestandardowych, można użyć mostków integracji EAI, przed lub po komunikat jest przetwarzany przez mostek EDI. Aby uzyskać więcej informacji, zobacz [jak dołączyć kod niestandardowy w mostków](/previous-versions/azure/dn232389(v=azure.100)).

Możesz wstawić przepływ publikowania/subskrybowania za pomocą kodu niestandardowego i/lub za pomocą usługi Service Bus, obsługi komunikatów kolejek i tematów, zanim umowy z partnerem handlowym otrzyma wiadomość lub po umowy przetwarza komunikat i kieruje je do punktu końcowego usługi Service Bus.

Zobacz **przepływu scenariuszy/wiadomości** w tym artykule dla wzorca przepływu wiadomości.

## <a name="agreements"></a>Umowy
Osoby zaznajomione z programu BizTalk Server 2010 handlowi umów z partnerami używana na potrzeby przetwarzania EDI usługi BizTalk Services umów z partnerami handlowymi wyglądać znajomo. Większość ustawień umowy są takie same i użyj tych samych terminologii. W niektórych przypadkach ustawienia umowy są dużo prostsze w porównaniu do tych samych ustawień w programie BizTalk Server. Microsoft Azure BizTalk Services obsługuje X12, EDIFACT i AS2 transportu.

Microsoft Azure BizTalk Services udostępnia również **migracji danych modułu TPM** narzędzie do migracji umowami i partnerami handlowymi z programu BizTalk Server handlowymi Partner modułu do portalu usługi BizTalk Services. Narzędzie migracji danych modułu TPM jest dostępne jako część pakietu narzędzi, który można pobrać z [zestawu SDK serwera usługi Mab](https://go.microsoft.com/fwlink/p/?LinkId=235057). Ten pakiet zawiera również plik readme, który zawiera instrukcje dotyczące sposobu używania narzędzia i podstawowe informacje dotyczące rozwiązywania problemów dla narzędzia.

## <a name="schemas"></a>Schematy
Usługa BizTalk Services oferuje schematów EDI, które mogą być używane w rozwiązaniach usługi BizTalk Services.  Ponadto schematów EDI serwera BizTalk Server umożliwia także z usługą BizTalk Services, ponieważ węzeł główny schematów EDI jest taka sama dla programu BizTalk Server, a także usługi BizTalk Services. W związku z tym można bezpośrednio z schematów EDI serwera BizTalk Server oraz używać ich w rozwiązań EDI, które tworzysz przy użyciu usługi BizTalk Services. Możesz również pobrać schematów z [zestawu SDK serwera usługi Mab](https://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapy (przekształcenia)
Mapy w programie BizTalk Server, są nazywane transformacji w usłudze BizTalk Services. Migrowanie mapy z programu BizTalk Server do usługi BizTalk Services może być jednym z bardziej skomplikowanych zadaniach w celu osiągnięcia (w zależności od złożoności mapy). Narzędzie mapowania używane dla usługi BizTalk Services jest inna niż mapowania BizTalk. Mimo że usługę mapowania przede wszystkim wygląda tak samo, podstawowy format mapy jest inny. Functoids (o nazwie **operacje mapy** w usłudze BizTalk Services) dostępne dla użytkowników różnią się także.  W efekcie nie możesz bezpośrednio użyć mapy BizTalk w usłudze BizTalk Services. Ponadto nie wszystkie dostępne w programie BizTalk Server functoids są dostępne jako mapa operacje w usłudze BizTalk Services.

### <a name="new-transform-operations"></a>Nowe operacje transformacji
Podczas listę operacji mapy przekształcania może wydawać się całkowicie różnią się od mapowania programu BizTalk Server, BizTalk Services przekształca się nowych sposobów wykonywania zadań. Na przykład przekształca usługi BizTalk mieć **operacje dotyczące List** dostępne. To nie jest dostępna w mapowania BizTalk.  **Operacje dotyczące List** pozwalają tworzyć i obsługiwać na "List", w przypadku, gdy lista jest zestaw elementów (znany także jako "wiersze"), a w przypadku, gdy każdy element może mieć wiele elementów członkowskich (znany także jako "kolumny").  Można sortować listę, wybierz elementy, na podstawie warunku, itp.

Inny przykład nowa funkcja usługi BizTalk Services przekształcenia są **operacje pętli**.  Jest trudne do tworzenia pętli zagnieżdżonych w mapowania programu BizTalk Server.  Operacje mapy pętli są więc dodawane do przekształcenia usługi BizTalk.

Innym przykładem jest jeszcze **If-Then-Else** operacji mapy wyrażenia.  Wykonanie operacji if-then-else było możliwe w mapowania BizTalk, ale wymaga ona wiele functoids pozornie proste zadania.

### <a name="migrating-biztalk-server-maps"></a>Migrowanie serwera BizTalk mapy
Microsoft Azure BizTalk Services zapewnia narzędzia do migracji z programu BizTalk Server mapuje do przekształcenia usługi BizTalk Services. **BTMMigrationTool** jest dostępny jako część **narzędzia** pakietów przy użyciu [pobierania zestawu SDK usługi BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkId=235057). Aby uzyskać więcej informacji o tym narzędziu, zobacz [przekonwertować mapę BizTalk do przekształcania usługi BizTalk](/previous-versions/azure/hh949812(v=azure.100)).

Można również przeglądać przykładu przez Sandro Pereira, BizTalk MVP [migracji map programu BizTalk Server do usługi BizTalk Services przekształcenia](https://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Aranżacji
Jeśli chcesz przeprowadzić migrację aranżacji programu BizTalk Server, przetwarzanie w systemie Microsoft Azure, aranżacji musi zostać przepisane, ponieważ Microsoft Azure nie obsługuje uruchamianie aranżacji programu BizTalk Server.  Można napisać ponownie funkcji aranżacji w usłudze Windows Workflow Foundation 4.0 (WF4).  Powinien to być pełne ponowne zapisywanie adresów, ponieważ nie ma obecnie nie migracja z programu BizTalk Server aranżacji WF4. Poniżej przedstawiono niektóre zasoby dla przepływu pracy Windows:

* [*Jak zintegrować usługę przepływu pracy WCF z kolejek usługi Service Bus i tematy* ](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/) przez Paolo Salvatori. 
* [*Tworzenie aplikacji za pomocą programu Windows Workflow Foundation i na platformie Azure* sesji](https://go.microsoft.com/fwlink/p/?LinkId=237314) z konferencji Build 2011.
* [*Centrum deweloperów systemu Windows Workflow Foundation*](https://docs.microsoft.com/previous-versions/dotnet/articles/ee342461(v=msdn.10)).
* [*Dokumentacja systemu Windows Workflow Foundation 4 (WF4)* ](/dotnet/framework/windows-workflow-foundation/index) w witrynie MSDN.

## <a name="other-considerations"></a>Inne zagadnienia
Poniżej przedstawiono kilka istotnych kwestii, które należy wykonać podczas korzystania z usługi BizTalk Services.

### <a name="fallback-agreements"></a>Umowy dotyczące rezerwowego
Przetwarzanie EDI serwera BizTalk Server korzysta z koncepcji "Rezerwowe umowy".  Usługa BizTalk Services jest **nie** ma koncepcji umowy powrotu do tej pory.  Zobacz tematy dokumentacji BizTalk [roli umów integracji EDI przetwarzania](https://go.microsoft.com/fwlink/p/?LinkId=237317) i [Konfigurowanie globalnych lub właściwości umowy rezerwowe](/biztalk/core/configuring-global-or-fallback-agreement-properties) instrukcje dotyczące używania umów rezerwowe w BizTalk Serwer.

### <a name="routing-to-multiple-destinations"></a>Routing do wielu miejsc docelowych
Mostki usługi BizTalk Services w jego bieżącym stanie nie obsługuje routingu komunikatów do wielu miejsc docelowych przy użyciu funkcji publikowania-subskrypcji modelu. Zamiast tego można przesyłać wiadomości z mostka usługi BizTalk Services do tematu usługi Service Bus, które następnie mogą mieć wiele subskrypcji w więcej niż jeden punkt końcowy wyświetlony komunikat.

## <a name="see-also"></a>Zobacz też
[Rozwiązania BIZNESOWE na platformie Azure](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
