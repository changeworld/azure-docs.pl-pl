---
title: Omówienie usługi AMQP 1.0 w usłudze Azure Service Bus
description: Dowiedz się, jak usługa Azure Service Bus obsługuje protokół AMQP (Advanced Message Usłudze kolejkowania) ( open usytuować protokół).
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
ms.openlocfilehash: 50d21cfe8136b9c794eae5104bbb34e28f7c1661
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759316"
---
# <a name="amqp-10-support-in-service-bus"></a>Obsługa usługi AMQP 1.0 w magistrali usług
Zarówno usługa w chmurze usługi Azure Service Bus, jak i lokalna [usługa Service Bus dla systemu Windows Server (Service Bus 1.1)](https://msdn.microsoft.com/library/dn282144.aspx) obsługują protokół AMQP (Advanced Message Queueing Protocol) 1.0. Protokół AMQP umożliwia tworzenie wieloplatformowych, hybrydowych aplikacji przy użyciu otwartego standardowego protokołu. Można konstruować aplikacje przy użyciu składników, które są tworzone przy użyciu różnych języków i struktur i które działają w różnych systemach operacyjnych. Wszystkie te składniki mogą łączyć się z usługą Service Bus i bezproblemowo wymieniać uporządkowane komunikaty biznesowe wydajnie i z pełną wiernością.

## <a name="introduction-what-is-amqp-10-and-why-is-it-important"></a>Wprowadzenie: Co to jest AMQP 1.0 i dlaczego jest to ważne?
Tradycyjnie produkty pośredniczące zorientowane na wiadomości używały zastrzeżonych protokołów do komunikacji między aplikacjami klienckimi a brokerami. Oznacza to, że po wybraniu brokera obsługi wiadomości określonego dostawcy należy użyć bibliotek tego dostawcy, aby połączyć aplikacje klienckie z tym brokerem. Powoduje to stopień zależności od tego dostawcy, ponieważ przeniesienie aplikacji do innego produktu wymaga zmian kodu we wszystkich połączonych aplikacjach. 

Ponadto łączenie brokerów wiadomości od różnych dostawców jest trudne. Zazwyczaj wymaga to pomostowania na poziomie aplikacji, aby przenosić wiadomości z jednego systemu do drugiego i tłumaczyć między ich zastrzeżonymi formatami wiadomości. Jest to wspólny wymóg; na przykład, gdy należy podać nowy ujednolicony interfejs do starszych różnych systemów lub zintegrować systemy INFORMATYCZNE po połączeniu.

Przemysł oprogramowania jest szybko rozwijającym się biznesem; nowe języki programowania i struktury aplikacji są wprowadzane w niekiedy zaskakującym tempie. Podobnie wymagania systemów IT ewoluują wraz z czasem, a deweloperzy chcą korzystać z najnowszych funkcji platformy. Jednak czasami wybrany dostawca obsługi wiadomości nie obsługuje tych platform. Ponieważ protokoły obsługi wiadomości są zastrzeżone, nie jest możliwe dla innych, aby zapewnić biblioteki dla tych nowych platform. W związku z tym należy użyć metod, takich jak budowanie bram lub mostów, które umożliwiają dalsze korzystanie z urządzenia do obsługi wiadomości.

Opracowanie protokołu AMQP (Advanced Message Queuing Protocol) 1.0 było motywowane tymi problemami. Pochodzi z JP Morgan Chase, który, podobnie jak większość firm świadczących usługi finansowe, są ciężkimi użytkownikami oprogramowania pośredniczącego zorientowanego na wiadomości. Cel był prosty: stworzyć otwarty protokół przesyłania wiadomości, który umożliwił tworzenie aplikacji opartych na wiadomościach przy użyciu komponentów zbudowanych przy użyciu różnych języków, struktur i systemów operacyjnych, wszystkie przy użyciu najlepszych w swojej klasie składników z różnych Dostawców.

## <a name="amqp-10-technical-features"></a>Funkcje techniczne AMQP 1.0
PROTOKÓŁ AMQP 1.0 to wydajny, niezawodny protokół obsługi wiadomości na poziomie przewodowym, którego można używać do tworzenia niezawodnych, wieloplatformowych aplikacji do obsługi wiadomości. Protokół ma prosty cel: zdefiniować mechanikę bezpiecznego, niezawodnego i efektywnego transferu wiadomości między dwiema stronami. Same wiadomości są kodowane przy użyciu przenośnej reprezentacji danych, która umożliwia heterogenicznych nadawców i odbiorców do wymiany uporządkowanych komunikatów biznesowych z pełną wiernością. Poniżej znajduje się podsumowanie najważniejszych funkcji:

* **Efficient**: AMQP 1.0 to protokół zorientowany na połączenie, który używa kodowania binarnego dla instrukcji protokołu i wiadomości biznesowych przesyłanych za nim. Zawiera zaawansowane schematy kontroli przepływu, aby zmaksymalizować wykorzystanie sieci i podłączonych komponentów. Mimo to protokół został zaprojektowany w celu znalezienia równowagi między wydajnością, elastycznością i interoperacyjnością.
* **Niezawodność:** Protokół AMQP 1.0 umożliwia wymianę wiadomości z szeregiem gwarancji niezawodności, od ognia i zapomnienia po niezawodne, dokładnie raz potwierdzone dostarczanie.
* **Elastyczny:** AMQP 1.0 to elastyczny protokół, który może być używany do obsługi różnych topologii. Ten sam protokół może być używany do komunikacji klient-klient-klient, klient-broker i broker-broker.
* **Niezależny od brokera model:** Specyfikacja AMQP 1.0 nie zawiera żadnych wymagań dotyczących modelu obsługi wiadomości używanego przez brokera. Oznacza to, że można łatwo dodać obsługę AMQP 1.0 do istniejących brokerów wiadomości.

## <a name="amqp-10-is-a-standard-with-a-capital-s"></a>AMQP 1.0 jest standardem (z wielkim "S")
AMQP 1.0 jest międzynarodową normą, zatwierdzoną przez ISO i IEC jako ISO/IEC 19464:2014.

AMQP 1.0 jest rozwijany od 2008 roku przez podstawową grupę ponad 20 firm, zarówno dostawców technologii, jak i firm użytkowników końcowych. W tym czasie firmy użytkowników przyczyniły się do ich rzeczywistych wymagań biznesowych, a dostawcy technologii ewoluowali protokół, aby spełnić te wymagania. Przez cały proces sprzedawcy uczestniczyli w warsztatach, w których współpracowali w celu potwierdzenia interoperacyjności między ich wdrożeniami.

W październiku 2011 r. prace rozwojowe przeszły do komitetu technicznego w ramach Organizacji Rozwoju Standardów Informacji Strukturalnej (OASIS), a OASIS AMQP 1.0 Standard został wydany w październiku 2012 r. Następujące firmy uczestniczyły w pracach komitetu technicznego podczas opracowywania normy:

* **Dostawcy technologii:** Axway Software, Huawei Technologies, IIT Software, INETCO Systems, Kaazing, Microsoft, Mitre Corporation, Primeton Technologies, Progress Software, Red Hat, SITA, Software AG, Solace Systems, VMware, WSO2, Zenika.
* **Firmy użytkowników**: Bank of America, Credit Suisse, Deutsche Boerse, Goldman Sachs, JPMorgan Chase.

Niektóre z powszechnie cytowanych korzyści z otwartych standardów obejmują:

* Mniejsza szansa na zablokowanie dostawcy
* Współdziałanie
* Szeroka dostępność bibliotek i narzędzi
* Ochrona przed starzeniem się
* Dostępność kompetentnego personelu
* Mniejsze i możliwe do zarządzania ryzyko

## <a name="amqp-10-and-service-bus"></a>AMQP 1.0 i magistrala serwisowa
Obsługa protokołu AMQP 1.0 w usłudze Azure Service Bus oznacza, że można teraz korzystać z funkcji kolejkowania usługi Service Bus i publikować/subskrybować funkcje obsługi wiadomości z różnych platform przy użyciu wydajnego protokołu binarnego. Ponadto można tworzyć aplikacje składające się ze składników utworzonych przy użyciu kombinacji języków, struktur i systemów operacyjnych.

Na poniższym rysunku przedstawiono przykładowe wdrożenie, w którym klienci java działający w systemie Linux, napisany przy użyciu standardowego interfejsu API usługi Java Message Service (JMS) i klientów .NET uruchomionych w systemie Windows, wymieniają wiadomości za pośrednictwem usługi Service Bus przy użyciu protokołu AMQP 1.0.

![][0]

**Rysunek 1: Przykładowy scenariusz wdrażania przedstawiający wiadomości między platformami przy użyciu usługi Service Bus i amqp 1.0**

W tej chwili znane są następujące biblioteki klientów do pracy z usługą Service Bus:

| Język | Biblioteka |
| --- | --- |
| Java |Klient usługi apache Qpid Java Message Service (JMS)<br/>IIT Software SwiftMQ Java client |
| C |Apache Qpid Proton-C |
| PHP |Apache Qpid Proton-PHP |
| Python |Apache Qpid Proton-Python |
| C# |AMQP .NET Lite |

**Rysunek 2: Tabela bibliotek klienckich AMQP 1.0**

## <a name="summary"></a>Podsumowanie
* PROTOKÓŁ AMQP 1.0 to otwarty, niezawodny protokół obsługi wiadomości, którego można używać do tworzenia wieloplatformowych, hybrydowych aplikacji. AMQP 1.0 jest standardem OASIS.
* Obsługa usługi AMQP 1.0 jest teraz dostępna w usłudze Azure Service Bus oraz w usłudze Service Bus dla systemu Windows Server (service bus 1.1). Ceny są takie same jak w przypadku istniejących protokołów.

## <a name="next-steps"></a>Następne kroki
Chcesz dowiedzieć się więcej? Odwiedź następujące linki:

* [Korzystanie z usługi Service Bus z platformy .NET z usługą AMQP]
* [Korzystanie z usługi Service Bus firmy Java z usługą AMQP]
* [Instalowanie apache Qpid Proton-C na maszynie wirtualnej systemu Azure Linux]
* [Usługa AMQP w magistrali usług dla systemu Windows Server]

[0]: ./media/service-bus-amqp-overview/service-bus-amqp-1.png
[Korzystanie z usługi Service Bus z platformy .NET z usługą AMQP]: service-bus-amqp-dotnet.md
[Korzystanie z usługi Service Bus firmy Java z usługą AMQP]: service-bus-amqp-java.md
[Instalowanie apache Qpid Proton-C na maszynie wirtualnej systemu Azure Linux]: service-bus-amqp-apache.md
[Usługa AMQP w magistrali usług dla systemu Windows Server]: https://msdn.microsoft.com/library/dn574799.aspx
