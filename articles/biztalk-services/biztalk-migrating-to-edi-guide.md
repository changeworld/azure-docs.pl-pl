---
title: Migrowanie rozwiązań EDI BizTalk Server do usług BizTalk — podręcznik techniczny | Dokumentacja firmy Microsoft
description: Migrowanie EDI do MABS; Usługi BizTalk Services dla platformy Microsoft Azure
services: biztalk-services
documentationcenter: na
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 61c179fa-3f37-495b-8016-dee7474fd3a6
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 1b70fc3d199d7f1521acb534dafec8fb3e69500e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23843490"
---
# <a name="migrating-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrowanie rozwiązań EDI BizTalk Server do usługi BizTalk Services: Podręcznik techniczny

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Wieman Timowi i Nitin Mehrotra

Recenzenci: Karthik Bharthy

Napisane przy użyciu: usługi BizTalk platformy Microsoft Azure — luty 2014 wersji.

## <a name="introduction"></a>Wprowadzenie
(Lub grupy użytkowników) jest jednym z najbardziej rozpowszechnionych oznacza przez dane programu exchange, których firm elektronicznie, określane również jako Business-to-Business lub B2B transakcji. BizTalk Server wystąpiła Obsługa EDI za pośrednictwem dekadę od początkowego wydania BizTalk Server. Z usługi BizTalk Services Microsoft kontynuuje obsługę EDI rozwiązania na platformie Microsoft Azure. Transakcje B2B są prawie spoza organizacji, a stąd jest łatwiejsze do wdrożenia, jeśli został wdrożony na platformie chmury. Microsoft Azure obsługuje tę funkcję za pomocą usługi BizTalk Services.

Gdy niektórzy klienci przyjrzeć się usługi BizTalk Services jako platforma "greenfield" dla nowych rozwiązań EDI, wielu klientów ma bieżącego rozwiązania BizTalk Server EDI, które chcą migrowania do platformy Azure. Ponieważ EDI usługi BizTalk została zaprojektowana na podstawie tego samego klucza jednostki jako architektura BizTalk Server EDI (handlowymi partnerów, jednostek, umów), jest możliwe przeprowadzenie migracji do usługi BizTalk Services BizTalk Server EDI artefaktów.

W tym dokumencie omówiono niektóre różnice związane z artefaktami BizTalk Server EDI migracji do usługi BizTalk Services. Ten dokument zakłada praktyczną znajomość i umów z partnerami handlowymi BizTalk Server EDI przetwarzania. Aby uzyskać więcej informacji dotyczących BizTalk Server EDI, zobacz [handlowymi partnera zarządzania przy użyciu BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Która wersja artefakty BizTalk Server EDI można poddać migracji do usługi BizTalk Services?
BizTalk Server EDI modułu została znacznie ulepszona dla BizTalk Server 2010, gdy została ona remodeled partnerów, profilów i umów. Usługi BizTalk Services używa tego samego modelu do organizowania partnerami handlowymi i działów biznesowych w ramach tych partnerzy handlowi. W związku z tym migrowania artefakty EDI BizTalk Server 2010 i nowszych wersjach do usługi BizTalk, jest procesem bardziej bezpośrednio do przodu. Aby przeprowadzić migrację EDI artefakty skojarzone z wersji przed BizTalk Server 2010, musi najpierw przeprowadzić uaktualnienie do programu BizTalk Server 2010, a następnie przeprowadzić migrację z artefakty EDI do usługi BizTalk Services.

## <a name="scenariosmessage-flow"></a>Przepływ scenariusze/komunikatów
Jako z BizTalk Server EDI przetwarzania w usługi BizTalk Services opiera się rozwiązania zarządzania handlowymi partnera (TPM). Rozwiązanie TPM ma następujące najważniejsze składniki:

* Partnerami handlowymi, które reprezentują organizacji w ramach transakcji B2B.
* Profile, które reprezentują oddziałami partnerem handlowym.
* Handlowymi umów z partnerami (lub umów) reprezentujący umowy biznesowych między dwoma partnerami/profile.

Na poniższej ilustracji przedstawiono podobieństwa, a także różnice między rozwiązań BizTalk Server EDI i EDI usługi BizTalk rozwiązania:

![][EDImessageflow]

Podstawowe różnice i podobieństwa przepływ rozwiązania EDI w BizTalk Server i usługi BizTalk Services są:

* Tak samo, jak BizTalk Server używa procesu EDIReceive odbierać wiadomości EDI i potoku EDISend do wysyłania wiadomości EDI, usługi BizTalk Services używa odbierania EDI mostek do odbierania i wysyłania EDI mostek do wysłania wiadomości EDI. BizTalk Server potoki są skojarzone z umowy przy użyciu wysyłania lub portów odbioru. W usługi BizTalk Services samej Umowy oznacza wysyłanie lub odbieranie mostek.
* BizTalk Server po potoku EDIReceive przetwarza wiadomości EDI wiadomość jest utworzyć zrzutu z bazą danych programu SQL Server. Potok EdiSend następnie przejmuje wiadomości z bazy danych programu SQL Server, procesy i wysyła on z partnerem handlowym.
  
    W usługi BizTalk Services po odbierania EDI Mostek przetwarza wiadomości EDI, kieruje komunikat procesu zewnętrznego. Proces zewnętrzny może być uruchomiona w Microsoft Azure lub lokalnie. Proces zewnętrzny należy kierować wiadomości do Mostek wysyłania EDI; Mostek wysyłania z założenia nie będzie ściągać wiadomości. Po przetworzeniu komunikatu, mostek wysyłania EDI trasy wiadomości z partnerem handlowym.

Usługi BizTalk Services zapewnia łatwy w użyciu konfiguracji szybkie tworzenie i wdrażanie umowę B2B między partnerami handlowymi bez konfigurowania żadnych Microsoft rozwiązań usługi obliczenia Azure wystąpienia (role sieci Web lub procesu roboczego), żadnych baz danych SQL Azure firmy Microsoft lub dowolną Konta magazynu Microsoft Azure. Wymaga bardziej złożonymi scenariuszami wiązania w przepływach pracy lub innego typu przetwarzania usługi "wokół krawędzi" umowę partnera handlowych, przed lub po zakończeniu przetwarzania Mostek handlowymi EDI umowę partnera. Szczegółowo poniższa sekwencja zdarzeń wystąpić podczas przetwarzania w usługi BizTalk Services komunikatów EDI.

1. Komunikat EDI zostało odebrane od partnera, Fabrikam handlowego.  Odbieranie komunikatów EDI z partnerami handlowymi, protokołów, takich jak FTP, SFTP AS2 i HTTP/S. obsługuje usługi BizTalk Services
2. Handlowym przetwarzania po stronie odbierającej umowę partnera rozkłada wiadomości EDI do formatu XML.  Asemblerze wiadomości EDI (w formacie XML) może kierować do punktów końcowych usługi Service Bus, takie jak punkt końcowy przekaźnik magistrali usług, temat magistrali usług, kolejką usługi Service Bus lub mostka usługi BizTalk Services.
3. Następnie można odbierać asemblerze wiadomości XML z punktu końcowego w celu dalszego przetwarzania niestandardowego.  Te punkty końcowe może być przetworzone przez składnik lokalnymi lub wystąpienia programu Microsoft Azure obliczeniowe do procesu dalszych komunikatów w usłudze Windows przepływu pracy (WF) lub usługi Windows Communication Foundation (WCF), np.
4. "Przetwarzania po stronie wysyłania" z partnerem handlowym umowy następnie składana wiadomości XML do formatu EDI i wysyła je do partnerem handlowym Contoso.  Do wysyłania wiadomości EDI do partnerami handlowymi, usługi BizTalk Services obsługuje protokoły są używane na potrzeby odbierania wiadomości EDI.

Dodatkowo ten dokument zawiera ogólne wskazówki na migracji niektórych różnych artefaktów BizTalk Server EDI do usługi BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Wyślij/Odbierz portów do partnerami handlowymi
BizTalk Server należy zdefiniować lokalizacje odbierania i porty Odbierz do odbierania wiadomości EDI/XML z partnerami handlowymi i Ustaw porty wysyłania do wysyłania wiadomości EDI/XML do partnerem handlowym. Następnie powiązać się te porty mają umowy z partnerem handlowym za pomocą konsoli administracyjnej serwera BizTalk. W lokalizacji otrzymywania wiadomości z partnerami handlowymi BizTalk usług i gdzie wysłać wiadomości do partnerami handlowymi są skonfigurowane w ramach umowy z partnerem handlowym sam (jako część ustawienia transportu) w portalu usługi BizTalk.  Dlatego nie naprawdę masz pojęcie "Wyślij porty" i "otrzymywać lokalizacje", per se w usługi BizTalk Services. Aby uzyskać więcej informacji, zobacz [Tworzenie umów](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Potoki (mostków)
BizTalk Server EDI potoki są jednostki przetwarzania komunikatów, które mogą również obejmować niestandardową logikę na potrzeby przetwarzania specyficznego dla funkcji, co jest wymagane przez aplikację. Usługi BizTalk odpowiednik czy Mostek EDI. Jednak w usługi BizTalk Services obecnie mostków EDI są "zamknięte".  Oznacza to, że nie można dodać własne niestandardowe działania mostka EDI. Poza Mostek EDI w aplikacji należy wszelkie niestandardowe przetwarzania przed lub po Mostek skonfigurowany jako część umowę partnera handlowymi wprowadza wiadomości. Mostków EAI dostępna opcja wykonuje przetwarzanie niestandardowych. Jeśli chcesz przetwarzania niestandardowego służy mostków EAI przed lub po komunikat jest przetwarzany przez mostek EDI. Aby uzyskać więcej informacji, zobacz [jak dołączyć kod niestandardowy w mostków](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Możesz wstawić przepływem publikowania/subskrypcji z niestandardowych kodów i/lub przy użyciu usługi Service Bus do obsługi komunikatów kolejek i tematów przed umowy z partnerem handlowym odbiera wiadomości lub po umowę przetwarzania komunikatu i kieruje je do punktu końcowego usługi Service Bus.

Zobacz **przepływ scenariusze/komunikatów** w tym temacie przeznaczonym dla przepływu komunikatów.

## <a name="agreements"></a>Umowy
Jeśli znasz BizTalk Server 2010 handlowi partnera umowy używanych na potrzeby przetwarzania EDI usługi BizTalk Services umów z partnerami handlowymi wyglądać bardzo znajomo. Większość ustawień umowy są takie same i używać tego samego terminologii. W niektórych przypadkach ustawienia umowy są znacznie w porównaniu do tych samych ustawień w BizTalk Server. Usługi BizTalk platformy Microsoft Azure obsługuje X12, EDIFACT i AS2 transportu.

Usługi BizTalk platformy Microsoft Azure udostępnia **migracji danych modułu TPM** narzędzia do migracji handlowym partnerów i umów z modułu partnera handlowymi BizTalk Server do portalu usługi BizTalk. Narzędzie do migracji danych modułu TPM jest dostępna jako część pakietu narzędzi, który można pobrać z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057). Ten pakiet zawiera również plik readme, który zawiera instrukcje dotyczące sposobu używania narzędzia i podstawowe informacje dotyczące rozwiązywania problemów dla tego narzędzia.

## <a name="schemas"></a>Schematy
Usługi BizTalk Services zawiera schematów EDI, które mogą być używane w rozwiązaniach usługi BizTalk Services.  Ponadto schematów EDI serwera BizTalk można również można użyć z usługi BizTalk Services, ponieważ węzeł główny schematów EDI jest taka sama w BizTalk Server, a także usługi BizTalk. W związku z tym można bezpośrednio zająć Twojej schematów EDI serwera BizTalk i używać ich w rozwiązaniach EDI, tworzonych przy użyciu usługi BizTalk Services. Możesz również pobrać schematów z [MABS SDK](http://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Mapy (transformacji)
Mapy w BizTalk Server są nazywane transformacje w usługi BizTalk Services. Migrowanie map z BizTalk Server do usługi BizTalk Services może być jednym z bardziej złożonych zadań w celu osiągnięcia (w zależności od złożoności mapy). Narzędzie mapowania używane dla usługi BizTalk Services jest inna niż mapowania BizTalk. Nawet jeśli mapowania wygląda przede wszystkim, format mapy źródłowej jest inny. Functoids (o nazwie **operacji mapy** w usługi BizTalk Services) dostępne dla użytkowników są także różne.  W efekcie nie mogą bezpośrednio używać mapy BizTalk w usługach BizTalk. Ponadto nie wszystkie dostępne w BizTalk Server functoids są dostępne jako operacje mapy w usługi BizTalk Services.

### <a name="new-transform-operations"></a>Nowe operacje transformacji
Chociaż listę operacji mapy przekształcania mogą być zupełnie różne od mapowania BizTalk Server, przekształca usługi BizTalk ma nowych sposobów tych samych zadań. Na przykład mieć przekształca usługi BizTalk **listę operacji** dostępne. To nie jest dostępna w mapowania BizTalk.  **Listę operacji** umożliwiają tworzenie i działają na "List", w przypadku, gdy lista jest zbiór elementów (znanej także jako "wiersze"), a każdy element może mieć wiele elementów członkowskich (znanej także jako "kolumny").  Można sortować listę, wybierz elementy, na podstawie warunku itp.

Innym przykładem nowych funkcji w programie przekształca usługi BizTalk są **operacji pętli**.  Jest trudne do tworzenia pętli zagnieżdżonych w mapowania BizTalk Server.  W związku z tym operacji mapy pętli zostaną dodane przekształca usługi BizTalk.

Jeszcze innym przykładem jest **If-to-inaczej** operacji mapy wyrażenia.  Wykonuje operację if to inaczej było możliwe w mapowania BizTalk, ale wymaga ona wiele functoids pozornie proste zadania.

### <a name="migrating-biztalk-server-maps"></a>Migrowanie serwera BizTalk mapy
Usługi BizTalk platformy Microsoft Azure udostępnia narzędzia do migracji serwera BizTalk mapy do przekształcenia usługi BizTalk Services. **BTMMigrationTool** jest dostępna jako część **narzędzia** pakietu podaną w [pobierania zestawu SDK usługi BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkId=235057). Aby uzyskać więcej informacji o tym narzędziu, zobacz [przekonwertować mapy BizTalk do przekształcania usługi BizTalk](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Można również przyjrzeć się próbkę przez Sandro Pereira, BizTalk MVP na temat [migracji map BizTalk Server do usługi BizTalk Services transformacje](http://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orchestrations
Jeśli trzeba migrować aranżacji BizTalk Server przetwarzania do systemu Microsoft Azure, orchestrations potrzebny do ponownego napisania ponieważ Microsoft Azure nie obsługuje uruchomionych orchestrations BizTalk Server.  Można zmodyfikować funkcji aranżacji w usłudze Windows Workflow Foundation 4.0 (WF4).  Powinien to być ukończone ponownego zapisywania, ponieważ nie ma obecnie nie migracja z orchestrations BizTalk Server do WF4. Oto kilka zasobów dla przepływu pracy systemu Windows:

* [*Integrowanie usługi przepływu pracy WCF z tematów i kolejek usługi Service Bus* ](https://msdn.microsoft.com/library/azure/hh709041.aspx) przez Paolo Salvatori. 
* [*Kompilowanie aplikacji za pomocą programu Windows Workflow Foundation i Azure* sesji](http://go.microsoft.com/fwlink/p/?LinkId=237314) z konferencji 2011 kompilacji.
* [*Centrum deweloperów systemu Windows Workflow Foundation* ](http://go.microsoft.com/fwlink/p/?LinkId=237315) w witrynie MSDN.
* [*Dokumentacja Windows Workflow Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) w witrynie MSDN.

## <a name="other-considerations"></a>Inne zagadnienia
Poniżej przedstawiono kilka kwestii, które należy wykonać podczas korzystania z usługi BizTalk Services.

### <a name="fallback-agreements"></a>Rezerwowy umów
BizTalk Server EDI przetwarzania ma pojęcie "Umów powrotu".  Usługi BizTalk Services jest **nie** ma koncepcji umowy powrotu do tej pory.  Zobacz tematy dokumentacji BizTalk [roli umów w przetwarzaniu EDI](http://go.microsoft.com/fwlink/p/?LinkId=237317) i [Konfigurowanie globalnym, ani właściwości umowy powrotu](https://msdn.microsoft.com/library/bb245981.aspx) Aby uzyskać informacje dotyczące używania umów rezerwowe w BizTalk Serwer.

### <a name="routing-to-multiple-destinations"></a>Routing do wielu miejsc docelowych
Usługi BizTalk Services mostków, w jego bieżącym stanie nie obsługuje rozesłania wiadomości do wielu miejsc docelowych przy użyciu publikowanie-subskrypcji modelu. Zamiast tego można przekierować wiadomości z mostka usługi BizTalk Services do tematu usługi Service Bus, która następnie może mieć wiele subskrypcji komunikat w więcej niż jednym punkcie końcowym.

## <a name="conclusion"></a>Podsumowanie
Usługi BizTalk platformy Microsoft Azure są aktualizowane w regularnych punkty kontrolne, aby dodać więcej funkcji i możliwości. Przy każdej aktualizacji mamy nadzieję do obsługi zwiększonej funkcje ułatwiające tworzenie rozwiązań end-to-end przy użyciu usługi BizTalk Services i innych technologii Azure.

## <a name="see-also"></a>Zobacz też
[Tworzenie aplikacji przedsiębiorstwa z platformy Azure](https://msdn.microsoft.com/library/azure/hh674490.aspx)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
