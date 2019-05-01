---
title: Usługa Azure Service Bus — często zadawane pytania (FAQ) | Dokumentacja firmy Microsoft
description: Odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 8461764a3f1f682ffb97420a4efdf2803f518872
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707137"
---
# <a name="service-bus-faq"></a>Service Bus — często zadawane pytania

W tym artykule omówiono niektóre często zadawane pytania dotyczące usługi Microsoft Azure Service Bus. Możesz również odwiedzić [często zadawane pytania dotyczące pomocy technicznej Azure](https://azure.microsoft.com/support/faq/) ogólne informacje dotyczące platformy Azure ceny i pomocy technicznej.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Ogólne pytania dotyczące usługi Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?
[Usługa Azure Service Bus](service-bus-messaging-overview.md) asynchronicznej obsługi komunikatów platforma chmury, która umożliwia wysyłanie danych między systemami odłączony. Firma Microsoft oferuje tę funkcję jako usługa, co oznacza, że nie trzeba do obsługi sprzętu z niego korzystać.

### <a name="what-is-a-service-bus-namespace"></a>Co to jest przestrzeń nazw usługi Service Bus?
A [przestrzeni nazw](service-bus-create-namespace-portal.md) zapewnia kontener określania zakresu na potrzeby adresowania zasobów usługi Service Bus w aplikacji. Tworzenie przestrzeni nazw jest niezbędne do korzystania z usługi Service Bus i jest jednym z pierwszych kroków w środowisku wprowadzenie.

### <a name="what-is-an-azure-service-bus-queue"></a>Co to jest kolejki usługi Azure Service Bus?
A [kolejki usługi Service Bus](service-bus-queues-topics-subscriptions.md) jest jednostką, w której są przechowywane komunikaty. Kolejki są przydatne w przypadku wielu aplikacji lub wieloma częściami aplikacji rozproszonej, które muszą komunikować się ze sobą. Kolejka jest podobne do Centrum dystrybucji, w tym wielu produktów (wiadomości) były odbierane i następnie wysyłane z tej lokalizacji.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Co to są usługi Azure Service Bus tematów i subskrypcji?
Temat mogą być wizualizowane jako kolejka i korzystając z wieloma subskrypcjami, staje się bardziej rozbudowane modelu obsługi wiadomości; zasadniczo narzędzie komunikacji jeden do wielu. W tym modelu publikowania/subskrybowania (lub *publikowania/subskrybowania*) umożliwia aplikacji, która wysyła komunikat do tematu z wieloma subskrypcjami, aby ten komunikat odebrany przez wiele aplikacji.

### <a name="what-is-a-partitioned-entity"></a>Co to jest jednostki podzielonej na partycje?
Konwencjonalne kolejki lub tematu są obsługiwane przez brokera pojedynczy komunikat i przechowywane w jeden Magazyn obsługi komunikatów. Obsługiwane tylko w przypadku warstw podstawowa i standardowa, o których wiadomości w warstwach [podzieleniu kolejki lub tematu](service-bus-partitioning.md) jest obsługiwane przez kilku brokerami i przechowywane w wiele magazynów obsługi komunikatów. Tej funkcji oznacza, że ogólną przepływność podzieleniu kolejki lub tematu nie jest już ograniczone przez wydajności pojedynczego brokera lub magazynu komunikatów. Ponadto tymczasowe awarii magazynu komunikatów nie jest renderowana podzieleniu kolejki lub tematu niedostępny.

Kolejność nie jest zapewnione przy użyciu partycjonowane jednostki. W przypadku, gdy partycja jest niedostępny, nadal można wysyłać i odbierać komunikaty z innych partycji.

 Partycjonowane jednostki nie są już obsługiwane w [jednostki SKU Premium](service-bus-premium-messaging.md). 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Które porty należy otworzyć w zaporze? 
Następujące protokoły za pomocą usługi Azure Service Bus umożliwia wysyłanie i odbieranie wiadomości:

- Advanced Message Queuing Protocol (AMQP)
- Protokół (SBMP) komunikatów usługi Service Bus
- HTTP

Zobacz poniższą tabelę dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikacji z usługą Azure Event Hubs. 

| Protokół | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [przewodnik dotyczący protokołu AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP | 9350 do 9354. | Zobacz [tryb łączności](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jakie adresy IP należy do listy dozwolonych?
Aby znaleźć odpowiednie adresy IP w celu białą listę połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie w wierszu polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres IP zwrócony `Non-authoritative answer`. Ten adres IP jest statyczne. Tylko punkt, w czasie, które go zmienić jest, jeśli można przywrócić przestrzeni nazw do innego klastra.

Jeśli używasz nadmiarowości strefy dla swojego obszaru nazw, należy wykonać kilka dodatkowych kroków: 

1. Najpierw uruchom narzędzie nslookup w przestrzeni nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w **nieautorytatywnej odpowiedzi** sekcji, która znajduje się w jednej z następujących formatów: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Uruchom narzędzie nslookup dla każdego z nich przy użyciu sufiksy s1, s2 i s3 w celu uzyskania adresów IP wszystkich trzech wystąpień w trzech strefach dostępności 


## <a name="best-practices"></a>Najlepsze praktyki
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jakie są najlepsze rozwiązania usługi Azure Service Bus?
Zobacz [najlepsze rozwiązania zwiększające wydajność przy użyciu usługi Service Bus] [ Best practices for performance improvements using Service Bus] — w tym artykule opisano sposób optymalizacji wydajności podczas wymiany komunikatów.

### <a name="what-should-i-know-before-creating-entities"></a>Co należy wiedzieć przed utworzeniem jednostki?
Następujące właściwości kolejki i tematu są niezmienne. To ograniczenie podczas aprowizowania jednostek, wziąć pod uwagę te właściwości nie można zmodyfikować bez tworzenia nowego obiektu zastępczego.

* Partycjonowanie
* Sesje
* Wykrywanie duplikatów
* Jednostki ekspresowe

## <a name="pricing"></a>Cennik
W tej sekcji odpowiedzi na niektóre często zadawane pytania dotyczące cennika struktury usługi Service Bus.

[Usługi Service Bus, cen i rozliczeń](https://azure.microsoft.com/pricing/details/service-bus/) artykule wyjaśniono liczników rozliczeń w usłudze Service Bus. Aby uzyskać szczegółowe informacje o opcje cennika usługi Service Bus, zobacz [szczegóły cennika usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

Możesz również odwiedzić [— często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/) for Azure ogólne informacje o cenach. 

### <a name="how-do-you-charge-for-service-bus"></a>Jak można opłaty za usługi Service Bus?
Aby uzyskać pełne informacje na temat cen usługi Service Bus, zobacz [szczegóły cennika usługi Service Bus][Pricing overview]. Oprócz ceny podane opłaty są naliczane w przypadku transferów danych skojarzone dla ruchu wychodzącego poza centrum danych, w którym zainicjowano aplikacji.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jakie użycia usługi Service Bus podlega transfer danych? Co to jest nie?
Każdy transfer danych w danym regionie platformy Azure znajduje się bez opłat, jak również wszelkie transfer danych przychodzących. Transfer danych poza obszarem podlega opłaty za ruch wychodzący, które można znaleźć [tutaj](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Czy usługi Service Bus opłaty za magazyn?
Nie, Usługa Service Bus nie opłaty za magazyn. Istnieje jednak limit przydziału i ograniczanie maksymalną ilość danych, który może być utrwalony na kolejki lub tematu. Zobacz następny często zadawane pytania.

## <a name="quotas"></a>Przydziały

Aby uzyskać listę usługi Service Bus, limity przydziału i ograniczenia, zobacz [Omówienie przydziałów usługi Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Usługa Service Bus ma przydziały użycia?
Domyślnie dla każdej chmury usługa Microsoft ustawia łączny miesięczny przydział użycia, który jest obliczany dla wszystkich subskrypcji klientów. Jeśli potrzebujesz więcej niż te limity, może skontaktuj się z działem obsługi klienta, w dowolnym momencie, zrozumienie potrzeb i odpowiednio dostosować te limity. W przypadku usługi Service Bus przydziału użycia agregacji jest 5 miliardów komunikatów na miesiąc.

Firma Microsoft zastrzega sobie prawo wyłączenia konta klienta, która przekroczyła swoje limity przydziału użycia w danym miesiącu, są wysyłane wiadomości e-mail z powiadomieniami i wielu prób do kontaktowania się z klientem, przed podjęciem działania. Przekroczenia tych limitów przydziału odpowiadają klienci nadal opłaty, które przekraczają limitów przydziału.

Podobnie jak w przypadku innych usług na platformie Azure, Usługa Service Bus wymusza zestaw określonych przydziałów, aby upewnić się, że istnieje uczciwe wykorzystanie zasobów. Można znaleźć więcej szczegółów na temat tych limitów przydziału w [Omówienie przydziałów usługi Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>W jaki sposób do obsługi komunikatów o rozmiarze > 1 MB?
Usług obsługi wiadomości usługi Service Bus (kolejek i tematów/subskrypcji) Zezwalaj aplikacji na wysyłanie wiadomości o rozmiarze do 256 KB (warstwa standardowa) lub 1 MB (warstwa premium). Jeśli masz do czynienia z komunikatami o rozmiarze większym niż 1 MB, użyj wzorca wyboru oświadczenia, które są opisane w [ten wpis w blogu](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern).

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć przestrzeń nazw po usunięciu jej z inną subskrypcję? 
Jeśli usuniesz przestrzeni nazw z subskrypcji, poczekaj przez 4 godziny przed odtworzenia go o tej samej nazwie w innej subskrypcji. W przeciwnym razie może być wyświetlony następujący komunikat o błędzie: `Namespace already exists`. 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Co to są wyjątki generowane przez interfejsów API usługi Azure Service Bus i ich sugerowane rozwiązania?
Aby uzyskać listę możliwych wyjątków usługi Service Bus, zobacz [Przegląd wyjątki][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co to jest sygnatura dostępu współdzielonego i języki obsługiwane przez Generowanie sygnatury?
Udostępnione sygnatur dostępu to mechanizm uwierzytelniania, na podstawie bezpiecznego skróty SHA-256 lub identyfikatory URI. Aby uzyskać informacje dotyczące generowania własnych podpisów w technologii Node.js, PHP, Java i C\#, zobacz [sygnatur dostępu współdzielonego] [ Shared Access Signatures] artykułu.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i przestrzeni nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrować przestrzeni nazw do innej subskrypcji platformy Azure?

Można przenieść przestrzeni nazw z jedną subskrypcją platformy Azure do innego, za pomocą [witryny Azure portal](https://portal.azure.com) lub poleceń programu PowerShell. Aby można było wykonać operację, przestrzeń nazw musi już być aktywne. Użytkownik, wykonując polecenia musi być administratorem subskrypcji źródłowej i docelowej.

#### <a name="portal"></a>Portal

Aby przeprowadzić migrację przestrzeni nazw usługi Service Bus do innej subskrypcji, należy użyć witryny Azure portal, wykonaj czynności podane [tutaj](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Następująca sekwencja poleceń programu PowerShell przenosi przestrzeni nazw z jedną subskrypcją platformy Azure do innego. Aby wykonać tę operację, przestrzeń nazw już musi być aktywne, a użytkownik uruchamiający poleceń programu PowerShell musi być administratorem subskrypcji źródłowej i docelowej.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat usługi Service Bus, zobacz następujące artykuły:

* [Wprowadzenie do usługi Azure Service Bus w warstwie Premium (wpis na blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Wprowadzenie do usługi Azure Service Bus w warstwie Premium (— Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Omówienie usługi Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
