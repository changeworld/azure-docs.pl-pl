---
title: Omówienie protokołu AMQP 1.0 w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Dowiedz się więcej o korzystaniu z zaawansowanych wiadomości Queuing Protocol (AMQP) 1.0 na platformie Azure.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 34829482e570354c1ab1e1fd6cec0c96b993cd83
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403930"
---
# <a name="amqp-10-support-in-service-bus"></a>Obsługa protokołu AMQP 1.0 w usłudze Service Bus
Usługa Azure Service Bus w chmurze i lokalnych [Usługa Service Bus dla systemu Windows Server (Usługa Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) obsługuje zaawansowane komunikat Kolejkowanie Protocol (AMQP) 1.0. Protokół AMQP umożliwia tworzenie dla różnych platform aplikacji hybrydowej za pomocą otwartego protokołu. Można skonstruować aplikacji przy użyciu składników, które są tworzone przy użyciu różnych języków i platform i działających w różnych systemach operacyjnych. Wszystkie te składniki można połączyć z usługą Service Bus oraz zapewniające bezproblemową wymiany wiadomości ze strukturą biznesowych, wydajne i w pełnej rozdzielczości.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Wprowadzenie: Co to jest protokołu AMQP 1.0, i dlaczego jest ważna?
Tradycyjnie oprogramowanie pośredniczące ukierunkowane produktów używanych protokołów własnościowych do komunikacji między aplikacjami klienta i brokerów. Oznacza to, po wybraniu brokera obsługi komunikatów dla określonego dostawcy należy użyć tego dostawcy bibliotek do łączenia się z tym brokera aplikacjom klienckim. Spowoduje to pewien stopień zależności od tego dostawcy, ponieważ przenoszenie aplikacji do innego produktu wymaga zmian w kodzie w połączonych aplikacjach. 

Ponadto łączenie brokery pochodzących od różnych dostawców jest trudne. Zwykle wymaga to poziom aplikacji mostkowanie przenoszenie wiadomości z jednego systemu do drugiego i tłumaczenie ich formaty własności wiadomości. To jest typowym wymogiem; na przykład, gdy możesz musi Podaj nowy, ujednolicony interfejs do starszych systemów różnych lub integrowanie systemów IT po połączeniu.

Branżę oprogramowania jest zmieniających firm; Czasami bewildering tempie wprowadzono nowe języki programowania i platform aplikacji. Podobnie z czasem ewoluować wymagania systemy IT i deweloperzy chcą korzystać z zalet najnowszych funkcji platformy. Jednak czasami wybranego dostawcy obsługi wiadomości nie obsługuje tych platform. Ponieważ protokoły są zastrzeżone, nie jest możliwe w innym osobom udostępniają biblioteki dla tych nowych platform. W związku z tym należy użyć metody, na przykład tworzenia bram lub mostków, które umożliwiają kontynuowanie korzystania z produktu obsługi komunikatów.

Programowanie z zaawansowanymi komunikat Queuing Protocol (AMQP) 1.0 leżała tych problemów. Pochodzi na JP Morgan Chase, którzy, takich jak najbardziej finansowych firm oferujących usługi Użytkownicy ukierunkowane oprogramowania pośredniczącego. Celem było proste: Aby utworzyć protokołu obsługi komunikatów otwartym standardem możliwe oparta na komunikatach za pomocą tworzyć aplikacje utworzone przy użyciu różnych języków, struktur i systemów operacyjnych, składniki wszystkich korzystasz z wykorzystaniem najlepszych składników z zakresu dostawcy.

## <a name="amqp-10-technical-features"></a>Funkcje techniczne protokołu AMQP 1.0
Protokołu AMQP 1.0 to wydajny, niezawodny i protokół sieciowy niskiego poziomu obsługi komunikatów, która umożliwia tworzenie niezawodnych i platform, aplikacji do obsługi komunikatów. Protokół ma prosty cel: do definiowania sposobu działania bezpieczne, niezawodne i efektywne przesyłanie wiadomości między dwiema stronami. Komunikaty, samodzielnie są zakodowane przy użyciu reprezentacji danych, umożliwiająca heterogenicznych nadawców i odbiorców, do wymiany wiadomości ze strukturą biznesowych w pełnej rozdzielczości. Poniżej przedstawiono podsumowanie najważniejszych funkcji:

* **Wydajne**: Protokół AMQP 1.0 stanowi protokołem połączeniowy używa kodowania pliku binarnego instrukcje protokołu i firm wiadomości przesyłanych przez go. Zawiera zaawansowane sterowanie przepływem schematów to maksymalne zwiększenie wykorzystania sieci oraz połączonych składników. Inaczej mówiąc, protokół zaprojektowano tak, aby zachować równowagę pomiędzy wydajność, elastyczność i współdziałania.
* **Niezawodne**: Protokół AMQP 1.0 umożliwia komunikatów wymienianych z szerokim zakresem gwarancje niezawodności, fire i zapomnij niezawodne, dokładnie — raz potwierdzenia dostarczenia.
* **Elastyczne**: Protokołu AMQP 1.0 jest protokołem elastycznym, który może służyć do obsługi różnych topologii. Ten sam protokół może służyć do komunikacji klienta do klienta, broker do brokera i klienta do brokera.
* **Niezależnie od modelu brokera**: Specyfikacja protokołu AMQP 1.0 nie powoduje żadnych wymagań dotyczących modelu obsługi komunikatów, używany przez brokera. Oznacza to, że możliwe jest łatwe dodawanie obsługę protokołu AMQP 1.0 do istniejących brokery.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>Protokół AMQP 1.0 stanowi standardowy (z wielkiej litery ")
Protokół AMQP 1.0 stanowi międzynarodowego standardu, zatwierdzone przez ISO i IEC jako 19464:2014 ISO/IEC.

Protokołu AMQP 1.0 został rozwijany od 2008 przez grupę core ponad 20 firmy zarówno dostawców technologii, jak i użytkowników końcowych przedsiębiorstwa. W tym czasie przedsiębiorstwa użytkownika opracowali swoje wymagania biznesowe rzeczywistych i dostawców technologii usprawniły protokołu w celu spełnienia tych wymagań. W całym procesie dostawców uczestniczyli w warsztatach, w których współpracy zastosowania do sprawdzania poprawności współdziałanie ich implementacji.

W październiku 2011 roku tworzonym przeniesieni do Komitet techniczny w firmie dla zawodowego of Structured Information Standards (OASIS) i standardowa OASIS protokołu AMQP 1.0 został wydany w października 2012. Następujące przedsiębiorstwa w nich nie uczestniczył Komitetu Technicznego podczas tworzenia standardowych:

* **Dostawców technologii**: Oprogramowanie Axway, Huawei technologii, IIT oprogramowania, INETCO systemów, Kaazing, Microsoft, Mitre Corporation, Primeton technologii, postęp oprogramowania, Red Hat, SITA, oprogramowanie AG, Solace systemów, VMware, WSO2 Zenika.
* **Przedsiębiorstwa użytkownika**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Często wspominane korzyści wynikające z otwartych standardów, należą:

* Mniejsze ryzyko blokady w dostawcy
* Współdziałanie
* Szeroka dostępności bibliotek i narzędzi
* Ochrona przed starzenie
* Dostępność personelu wiedzę
* Niższe i łatwe w zarządzaniu ryzykiem

## <a name="amqp-10-and-service-bus"></a>Protokół AMQP 1.0 lub usługi Service Bus
Obsługę protokołu AMQP 1.0 w usłudze Azure Service Bus oznacza, że można teraz korzystać z kolejek usługi Service Bus i publikowania/subskrybowania funkcji komunikatów obsługiwanych przez brokera z szeroką gamę platform za pomocą protokołu binarne wydajne. Ponadto możesz tworzyć aplikacje, które obejmuje składniki utworzone przy użyciu różnych języków, platform i systemów operacyjnych.

Na poniższym rysunku przedstawiono przykład wdrożenia, w którym klienci Java systemu Linux, napisane przy użyciu standardowego języka Java wiadomości usługi JMS () interfejsu API i systemem Windows, klientów programu .NET wymieniać komunikaty za pośrednictwem usługi Service Bus przy użyciu protokołu AMQP 1.0.

![][0]

**Rysunek 1: Przykładowy scenariusz wdrażania przedstawiający dla wielu platform komunikatów za pomocą usługi Service Bus i protokołu AMQP 1.0**

W tej chwili znane podanych niżej bibliotek klienta do pracy z usługą Service Bus:

| Język | Biblioteka |
| --- | --- |
| Java |Klient Apache Qpid Java wiadomości usługi (JMS)<br/>Klient IIT oprogramowania SwiftMQ Java |
| C |Apache Qpid protonów C |
| PHP |Apache Qpid protonów — PHP |
| Python |Apache Qpid protonów — Python |
| C# |AMQP .NET Lite |

**Rysunek 2: Tabela bibliotek klienta protokołu AMQP 1.0**

## <a name="summary"></a>Podsumowanie
* Protokół AMQP 1.0 stanowi otwarte, niezawodnej obsługi komunikatów protokołu używanego do tworzenia dla różnych platform aplikacji hybrydowych. Protokół AMQP 1.0 stanowi standardem języka OASIS.
* Obsługa protokołu AMQP 1.0 jest teraz dostępna w usłudze Azure Service Bus, a także Usługa Service Bus dla systemu Windows Server (Usługa Service Bus 1.1). Ceny są takie same jak dla istniejących protokołów.

## <a name="next-steps"></a>Kolejne kroki
Czy chcesz dowiedzieć się więcej? Skorzystaj z następujących linków:

* [Za pomocą usługi Service Bus z platformy .NET z obsługą protokołu AMQP]
* [Za pomocą usługi Service Bus w języku Java z obsługą protokołu AMQP]
* [Instalowanie Apache Qpid protonów C na maszynie Wirtualnej systemu Linux platformy Azure]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Za pomocą usługi Service Bus z platformy .NET z obsługą protokołu AMQP]: service-bus-amqp-dotnet.md
[Za pomocą usługi Service Bus w języku Java z obsługą protokołu AMQP]: service-bus-amqp-java.md
[Instalowanie Apache Qpid protonów C na maszynie Wirtualnej systemu Linux platformy Azure]: service-bus-amqp-apache.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
