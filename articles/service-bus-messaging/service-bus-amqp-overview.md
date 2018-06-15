---
title: Omówienie protokołu AMQP 1.0 w usłudze Azure Service Bus | Dokumentacja firmy Microsoft
description: Informacje o używaniu zaawansowane komunikatów usługi kolejkowania wiadomości protokołu (AMQP) 1.0 na platformie Azure.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 0e8d19cc-de36-478e-84ae-e089bbc2d515
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2018
ms.author: sethm
ms.openlocfilehash: 6d2dffd22ecfc0aaf6e338567d5cf107a2c07383
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926601"
---
# <a name="amqp-10-support-in-service-bus"></a>Obsługa protokołu AMQP 1.0 w usłudze Service Bus
Zarówno usługa w chmurze Azure Service Bus, jak i dla lokalnego [Usługa Service Bus dla systemu Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) obsługuje zaawansowane komunikatów usługi kolejkowania protokołu (protokół AMQP) 1.0. Protokół AMQP umożliwia tworzenie wielu platform, hybrydowych aplikacji przy użyciu standardowego protokołu zapewniającego otwarte. Można utworzyć aplikacji przy użyciu składników, które są tworzone przy użyciu różnych języków i struktur, które działają w różnych systemach operacyjnych. Wszystkie te składniki mogą połączenia usługi Service Bus i bezproblemowo wymiany wiadomości biznesowych strukturalnych, wydajne i w pełnej rozdzielczości.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Wprowadzenie: Co to jest protokołu AMQP 1.0 i dlaczego ważne jest?
Tradycyjnie zorientowany pośredniczącego używanych protokołów własnościowych do komunikacji między aplikacjami klienckimi i brokerów. Oznacza to, że po wybraniu brokera obsługi komunikatów dla określonego dostawcy, trzeba korzystać z danym dostawcą bibliotek nawiązać brokera tym aplikacjom klienckim. W efekcie stopień zależność od tego dostawcy, ponieważ eksportowanie aplikację do innego produktu wymaga zmian kodu w połączonych aplikacjach. 

Ponadto łączenie brokerzy wiadomości pochodzących od różnych dostawców jest istotne znaczenie. Zwykle wymaga to mostkowanie poziomie aplikacji, aby przenieść wiadomości z jednego systemu i do tłumaczenia ich formaty wiadomości zastrzeżonych. Jest to wymagane wspólnej; na przykład po musi podać nowe ujednolicony interfejs do starszych różnych systemów, lub zintegrować systemów IT po połączeniu.

Producentów oprogramowania jest firma szybko; Czasami bewildering tempem wprowadzane są nowe języki programowania i platform aplikacji. Podobnie wymagania dotyczące systemów IT ewoluują z upływem czasu i deweloperzy chcą korzystać z najnowszych funkcji platformy. Jednak czasami wybranego dostawcy obsługi wiadomości nie obsługuje tych platform. Ponieważ protokoły są unikatowe, nie jest możliwe innym osobom do zapewnienia biblioteki dla tych nowych platform. W związku z tym należy użyć metod, takich jak tworzenie bramy lub mostków umożliwiające w dalszym ciągu korzystać z produktu obsługi wiadomości.

Programowanie z zaawansowane komunikatów usługi kolejkowania wiadomości protokołu (AMQP) 1.0 został uzasadnione tych problemów. Jego występowania w Morgan Chase JP, kto, podobnie jak najbardziej finansowych usługowe, są użytkownicy zorientowany oprogramowania pośredniczącego. Celem było proste: do tworzenia protokołu komunikatów standardu open możliwe tworzenie aplikacji wiadomości przy użyciu składników utworzony przy użyciu różnych języków, struktur i systemów operacyjnych, wszystkie przy użyciu składników obsługi najlepiej z zakresu dostawcy.

## <a name="amqp-10-technical-features"></a>Funkcje techniczne protokołu AMQP 1.0
Protokół AMQP 1.0 stanowi wydajne, niezawodne i poziom przesyłania komunikatów protokół, który służy do tworzenia niezawodne, obsługujący wiele platform aplikacji do obsługi wiadomości. Protokół ma prosty cel: określenie sposobu bezpieczne, niezawodne i wydajne transferu wiadomości między dwiema stronami. Komunikaty, same są zakodowane przy użyciu reprezentację danych, umożliwiający heterogenicznych nadawcy i odbiorcy, do wymiany wiadomości biznesowych strukturalnych w pełnej rozdzielczości. Poniżej znajduje się podsumowanie najważniejszych funkcji:

* **Efektywne**: protokół AMQP 1.0 stanowi nawiązaniem połączenia protokołu, które używa kodowanie binarne dla komunikatów biznesowych i instrukcje protokołu transferu nad nim. Zawiera schematy Zaawansowane sterowanie przepływem, aby zapewnić maksymalne użycie sieci i składników połączonych. Inaczej mówiąc, protokół została zaprojektowana w celu równowagę między wydajność, elastyczność i współdziałanie.
* **Niezawodne**: protokół AMQP 1.0 umożliwia wiadomości wymieniane zakres gwarancje niezawodności, fire i zapomnij niezawodne, dokładnie — raz potwierdzenie dostarczenia.
* **Elastyczne**: protokołu AMQP 1.0 jest protokołem elastycznym, który może służyć do obsługi różnych topologiach. Ten sam protokół może służyć do komunikacji klienta do klienta, klient brokera i broker brokera.
* **Niezależne od modelu brokera**: Specyfikacja protokołu AMQP 1.0 nie powoduje żadnych wymagań dotyczących modelu obsługi wiadomości, używany przez brokera. Oznacza to, że jest można łatwo dodać obsługę protokołu AMQP 1.0 do istniejących brokerzy obsługi wiadomości.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>Protokół AMQP 1.0 stanowi standardowe (z kapitału ")
Protokół AMQP 1.0 stanowi międzynarodowe wersje standard, zatwierdzone przez ISO i IEC jako 19464:2014 ISO/IEC.

Protokołu AMQP 1.0 został rozwijany od 2008 przez grupę core więcej niż 20 firm, zarówno dostawców technologii, jak i firm użytkownika końcowego. W tym czasie firmy użytkownika przyczyniły ich wymagania biznesowe rzeczywistych i dostawców technologii usprawnionych protokołu w celu spełnienia tych wymagań. W trakcie procesu dostawców uczestniczyli w zakładów, w których współpracę do sprawdzania poprawności współdziałanie ich implementacji.

Październik 2011 projektach są przenoszone do Komitetu technicznego w firmie dla rozwoju z Structured Information Standards (OASIS) i Standard języka OASIS protokołu AMQP 1.0 został wydany w października 2012. Podczas tworzenia standardowego poniższych firmy brał udział w Komisji techniczne:

* **Technologia dostawców**: Axway oprogramowania, Huawei technologii, IIT oprogramowania, INETCO systemów, Kaazing, Microsoft, Mitre Corporation, Primeton technologii, postęp oprogramowania, Red Hat, SITA, oprogramowania AG, Solace systemów, VMware, WSO2, Zenika.
* **Firmy użytkownika**: Bank Ameryka Suisse środki, Boerse marki, Goldman Sachs, JPMorgan Chase.

Oto niektóre z najczęściej cytowane zalet otwartych standardów:

* Mniejsze ryzyko blokady w dostawcy
* Współdziałanie
* Szerokie dostępności biblioteki i narzędzia
* Ochrona przed utraty przydatności
* Dostępność personelu wiedzą
* Niższe i łatwą w obsłudze ryzyka

## <a name="amqp-10-and-service-bus"></a>Protokołu AMQP 1.0 i Service Bus
Obsługa protokołu AMQP 1.0 w Azure Service Bus oznacza, że można teraz korzystać z kolejek usługi Service Bus i publikowania/subskrypcji obsługiwanych przez brokera obsługi komunikatów funkcje z zakresu od platformy przy użyciu protokołu binarnego wydajne. Ponadto można tworzyć aplikacje obejmuje składniki utworzony przy użyciu różnych języków, struktur i systemów operacyjnych.

Na poniższym rysunku przedstawiono przykład wdrożenia, w którym Java uruchomionym w systemie Linux, napisane przy użyciu protokół standardowe Java wiadomości usługi (JMS) w interfejsu API i .NET klientów z uruchomionym systemem Windows, wymieniać komunikaty za pośrednictwem usługi Service Bus przy użyciu protokołu AMQP 1.0.

![][0]

**Rysunek 1: Przykładowy wdrożenia scenariusz przedstawiający wieloplatformowych wiadomości przy użyciu usługi Service Bus i protokołu AMQP 1.0**

W tej chwili do pracy z usługą Service Bus znane są następujące biblioteki klienta:

| Język | Biblioteka |
| --- | --- |
| Java |Klient usługi komunikat Java Apache Qpid (JMS)<br/>Klient IIT oprogramowania SwiftMQ Java |
| C |Apache Qpid protonów C |
| PHP |Apache Qpid protonów PHP |
| Python |Apache Qpid protonów Python |
| C# |Protokół AMQP .net Lite |

**Rysunek 2: Tabela bibliotek klienta protokołu AMQP 1.0**

## <a name="summary"></a>Podsumowanie
* Protokołu AMQP 1.0 jest otwarty, niezawodnej obsługi komunikatów protokołu służącego do tworzenia i platform, hybrydowych aplikacji. Protokołu AMQP 1.0 jest standardem OASIS.
* Obsługa protokołu AMQP 1.0 jest teraz dostępna w Azure Service Bus, a także Usługa Service Bus dla systemu Windows Server (Service Bus 1.1). Cennik jest taka sama jak w przypadku istniejących protokołów.

## <a name="next-steps"></a>Kolejne kroki
Chcesz dowiedzieć się więcej? Skorzystaj z następujących łączy:

* [Przy użyciu usługi Service Bus z .NET z protokołu AMQP]
* [Przy użyciu usługi Service Bus za pomocą języka Java w usłudze AMQP]
* [Instalowanie na maszynie Wirtualnej platformy Azure Linux Apache Qpid protonów C]
* [Protokół AMQP w usłudze Service Bus dla systemu Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Przy użyciu usługi Service Bus z .NET z protokołu AMQP]: service-bus-amqp-dotnet.md
[Przy użyciu usługi Service Bus za pomocą języka Java w usłudze AMQP]: service-bus-amqp-java.md
[Instalowanie na maszynie Wirtualnej platformy Azure Linux Apache Qpid protonów C]: service-bus-amqp-apache.md
[Protokół AMQP w usłudze Service Bus dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
