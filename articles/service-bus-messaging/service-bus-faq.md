---
title: Usługa Azure Service Bus często zadawane pytania (często zadawane pytania) | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na niektóre z często zadawanych pytań (CZĘSTO ZADAWANE PYTANIA) dotyczące usługi Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 3cd4e69481fb452391e6dc027cb41fd6dae71b7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760253"
---
# <a name="azure-service-bus---frequently-asked-questions-faq"></a>Usługa Azure Service Bus — często zadawane pytania (często zadawane pytania)

W tym artykule omówiono niektóre często zadawane pytania dotyczące usługi Microsoft Azure Service Bus. Możesz również odwiedzić często zadawane [pytania dotyczące pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/faq/) aby uzyskać ogólne informacje dotyczące cen i pomocy technicznej platformy Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions-about-azure-service-bus"></a>Ogólne pytania dotyczące usługi Azure Service Bus
### <a name="what-is-azure-service-bus"></a>Co to jest Azure Service Bus?
[Usługa Azure Service Bus](service-bus-messaging-overview.md) to asynchroninowa platforma chmury obsługi wiadomości, która umożliwia wysyłanie danych między systemami oddzielonymi. Firma Microsoft oferuje tę funkcję jako usługę, co oznacza, że nie trzeba hostować własnego sprzętu, aby z niej korzystać.

### <a name="what-is-a-service-bus-namespace"></a>Co to jest obszar nazw usługi Service Bus?
[Obszar nazw](service-bus-create-namespace-portal.md) udostępnia kontener zakresu adresowania zasobów usługi Service Bus w aplikacji. Tworzenie obszaru nazw jest konieczne do korzystania z usługi Service Bus i jest jednym z pierwszych kroków w rozpoczęciu pracy.

### <a name="what-is-an-azure-service-bus-queue"></a>Co to jest kolejka usługi Azure Service Bus?
[Kolejka usługi Service Bus](service-bus-queues-topics-subscriptions.md) jest jednostką, w której są przechowywane wiadomości. Kolejki są przydatne, gdy masz wiele aplikacji lub wiele części aplikacji rozproszonej, które muszą komunikować się ze sobą. Kolejka jest podobna do centrum dystrybucji, w którym odbieranych jest wiele produktów (wiadomości), a następnie wysyłanych z tej lokalizacji.

### <a name="what-are-azure-service-bus-topics-and-subscriptions"></a>Jakie są tematy i subskrypcje usługi Azure Service Bus?
Temat można wizualizować jako kolejkę, a podczas korzystania z wielu subskrypcji staje się bogatszym modelem obsługi wiadomości; zasadniczo narzędzie komunikacji jeden do wielu. Ten model publikowania/subskrybowania (lub *pub/sub)* umożliwia aplikacji, która wysyła wiadomość do tematu z wieloma subskrypcjami, aby ten komunikat został odebrany przez wiele aplikacji.

### <a name="what-is-a-partitioned-entity"></a>Co to jest jednostka podzielona na partycje?
Konwencjonalna kolejka lub temat jest obsługiwana przez brokera pojedynczych wiadomości i przechowywana w jednym magazynie wiadomości. Obsługiwane tylko w warstwach wiadomości basic i standard, [kolejka partycjonowana lub temat](service-bus-partitioning.md) jest obsługiwany przez wielu brokerów wiadomości i przechowywane w wielu magazynach obsługi wiadomości. Ta funkcja oznacza, że ogólna przepływność kolejki lub tematu podzielonego na partycje nie jest już ograniczona przez wydajność jednego brokera wiadomości lub magazynu obsługi wiadomości. Ponadto tymczasowa awaria magazynu obsługi wiadomości nie powoduje, że kolejka na partycje lub temat są niedostępne.

Zamawianie nie jest zapewnione podczas korzystania z jednostek podzielonych na partycje. W przypadku, gdy partycja jest niedostępna, nadal można wysyłać i odbierać wiadomości z innych partycji.

 Jednostki podzielone na partycje nie są już obsługiwane w [jednostce premium.](service-bus-premium-messaging.md) 

### <a name="what-ports-do-i-need-to-open-on-the-firewall"></a>Jakie porty są potrzebne do otwarcia na zaporze? 
Do wysyłania i odbierania wiadomości za pomocą usługi Azure Service Bus można używać następujących protokołów:

- Zaawansowane usługi kolejkowania Protocol (AMQP)
- Protokół SBMP (Service Bus Messaging Protocol)
- HTTP

Zobacz poniższą tabelę dla portów wychodzących, które należy otworzyć, aby używać tych protokołów do komunikowania się z usługą Azure Event Hubs. 

| Protocol (Protokół) | Porty | Szczegóły | 
| -------- | ----- | ------- | 
| AMQP | 5671 i 5672 | Zobacz [przewodnik po protokole protokołu AMQP](service-bus-amqp-protocol-guide.md) | 
| SBMP (sbmp) | od 9350 do 9354 | Zobacz [tryb łączności](/dotnet/api/microsoft.servicebus.connectivitymode?view=azure-dotnet) |
| HTTP, HTTPS | 80, 443 | 

### <a name="what-ip-addresses-do-i-need-to-whitelist"></a>Jakie adresy IP są potrzebne do umieszczenia na białej liście?
Aby znaleźć odpowiednie adresy IP do białej listy połączeń, wykonaj następujące kroki:

1. Uruchom następujące polecenie z wiersza polecenia: 

    ```
    nslookup <YourNamespaceName>.servicebus.windows.net
    ```
2. Zanotuj adres `Non-authoritative answer`IP zwrócony w pliku . Ten adres IP jest statyczny. Jedynym punktem w czasie to się zmieni, jeśli przywrócić obszar nazw do innego klastra.

Jeśli używasz nadmiarowości strefy dla obszaru nazw, musisz wykonać kilka dodatkowych kroków: 

1. Najpierw uruchom nslookup w obszarze nazw.

    ```
    nslookup <yournamespace>.servicebus.windows.net
    ```
2. Zanotuj nazwę w sekcji **odpowiedzi nieuzyskanych,** która jest w jednym z następujących formatów: 

    ```
    <name>-s1.servicebus.windows.net
    <name>-s2.servicebus.windows.net
    <name>-s3.servicebus.windows.net
    ```
3. Uruchom nslookup dla każdego z przyrostkami s1, s2 i s3, aby uzyskać adresy IP wszystkich trzech wystąpień uruchomionych w trzech strefach dostępności, 


## <a name="best-practices"></a>Najlepsze rozwiązania
### <a name="what-are-some-azure-service-bus-best-practices"></a>Jakie są najlepsze rozwiązania usługi Azure Service Bus?
Zobacz [najważniejsze wskazówki dotyczące poprawy wydajności przy użyciu usługi Service Bus][Best practices for performance improvements using Service Bus] — w tym artykule opisano sposób optymalizacji wydajności podczas wymiany wiadomości.

### <a name="what-should-i-know-before-creating-entities"></a>Co należy wiedzieć przed utworzeniem encji?
Następujące właściwości kolejki i tematu są niezmienne. Należy wziąć pod uwagę to ograniczenie podczas inicjowania obsługi administracyjnej jednostek, ponieważ te właściwości nie mogą być modyfikowane bez tworzenia nowej jednostki zastępczej.

* Partycjonowanie
* Sesje
* Wykrywanie duplikatów
* Jednostka ekspresowa

## <a name="pricing"></a>Cennik
W tej sekcji odpowiedzieć na niektóre często zadawane pytania dotyczące struktury cen usługi Service Bus.

Artykuł [o cenach i rozliczeniach usługi Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) wyjaśnia liczniki rozliczeń w usłudze Service Bus. Aby uzyskać szczegółowe informacje na temat opcji cennika usługi Service [Bus,](https://azure.microsoft.com/pricing/details/service-bus/)zobacz Szczegóły cen usługi Service Bus .

Możesz również odwiedzić często zadawane [pytania dotyczące pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/faq/) aby uzyskać ogólne informacje o cenach platformy Azure. 

### <a name="how-do-you-charge-for-service-bus"></a>Jak pobierać opłaty za usługę Service Bus?
Aby uzyskać pełne informacje na temat cennika usługi Service [Bus,][Pricing overview]zobacz Szczegóły cen usługi Service Bus . Oprócz odnotowanych cen naliczana jest opłata za skojarzone transfery danych za wyjście poza centrum danych, w którym aplikacja jest aprowizowana.

### <a name="what-usage-of-service-bus-is-subject-to-data-transfer-what-is-not"></a>Jakie korzystanie z usługi Service Bus podlega transferowi danych? Co nie jest?
Wszelkie transfery danych w danym regionie platformy Azure są udostępniane bezpłatnie, a także wszelkie przychodzące transfery danych. Przesyłanie danych poza region podlega opłatom za wyjście, które można znaleźć [tutaj](https://azure.microsoft.com/pricing/details/bandwidth/).

### <a name="does-service-bus-charge-for-storage"></a>Czy usługa Service Bus pobiera opłatę za przechowywanie?
Nie, usługa Service Bus nie pobiera opłat za przechowywanie. Istnieje jednak przydział ograniczający maksymalną ilość danych, które mogą być utrwalane na kolejkę/temat. Zobacz następne często zadawane pytania.

### <a name="i-have-a-service-bus-standard-namespace-why-do-i-see-charges-under-resource-group-system"></a>Mam standardową przestrzeń nazw usługi Service Bus. Dlaczego widzę opłaty w ramach grupy zasobów "$system"?
Usługa Azure Service Bus niedawno uaktualniła składniki rozliczeń. W związku z tym, jeśli masz standardową przestrzeń nazw usługi Service Bus, w grupie zasobów "$system/$system" mogą być widoczne elementy zamówienia dla zasobu "/subscriptions/<azure_subscription_id>/resourceGroups/$system/providers/Microsoft.ServiceBus/namespaces/$system".

Opłaty te reprezentują opłatę podstawową za subskrypcję platformy Azure, która aprowizować standardowy obszar nazw usługi Service Bus. 

Ważne jest, aby pamiętać, że nie są to nowe opłaty, to znaczy, że istniały one również w poprzednim modelu rozliczeniowym. Jedyną zmianą jest to, że są one obecnie wymienione w "$system". Odbywa się to z powodu contraints w nowym systemie rozliczeniowym, który grupuje opłaty na poziomie subskrypcji, nie związane z określonego zasobu, w ramach identyfikatora zasobu "$system".

## <a name="quotas"></a>Przydziały

Aby uzyskać listę limitów i przydziałów usługi Service Bus, zobacz [omówienie przydziałów usługi Service Bus][Quotas overview].

### <a name="does-service-bus-have-any-usage-quotas"></a>Czy usługa Service Bus ma jakieś przydziały użycia?
Domyślnie dla dowolnej usługi w chmurze firma Microsoft ustawia łączny miesięczny przydział użycia, który jest obliczany we wszystkich subskrypcjach klienta. Jeśli potrzebujesz więcej niż te limity, możesz skontaktować się z obsługą klienta w dowolnym momencie, aby zrozumieć swoje potrzeby i odpowiednio dostosować te limity. W przypadku usługi Service Bus łączny przydział użycia wynosi 5 miliardów komunikatów miesięcznie.

Firma Microsoft zastrzega sobie prawo do wyłączenia konta klienta, które przekroczyło przydziały użycia w danym miesiącu, wysyłane są powiadomienia e-mail i podejmowane są wielokrotne próby skontaktowania się z klientem przed podjęciem jakichkolwiek działań. Klienci przekraczający te przydziały są nadal odpowiedzialni za opłaty, które przekraczają przydziały.

Podobnie jak w przypadku innych usług na platformie Azure, usługa Service Bus wymusza zestaw określonych przydziałów, aby zapewnić, że istnieje uczciwe użycie zasobów. Więcej informacji na temat tych przydziałów można znaleźć w [omówienie przydziałów usługi Service Bus][Quotas overview].

### <a name="how-to-handle-messages-of-size--1-mb"></a>Jak obsługiwać wiadomości o rozmiarze > 1 MB?
Usługi obsługi wiadomości usługi Service Bus (kolejki i tematy/subskrypcje) umożliwiają aplikacji wysyłanie wiadomości o rozmiarze do 256 KB (warstwa standardowa) lub 1 MB (warstwa premium). Jeśli masz do czynienia z wiadomościami o rozmiarze większym niż 1 MB, użyj wzorca sprawdzania oświadczeń opisanego w [tym wpisie](https://www.serverless360.com/blog/deal-with-large-service-bus-messages-using-claim-check-pattern)w blogu .

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="why-am-i-not-able-to-create-a-namespace-after-deleting-it-from-another-subscription"></a>Dlaczego nie mogę utworzyć obszaru nazw po usunięciu jej z innej subskrypcji? 
Po usunięciu obszaru nazw z subskrypcji, poczekaj 4 godziny przed odtworzeniem go o tej samej nazwie w innej subskrypcji. W przeciwnym razie może zostać `Namespace already exists`wyświetlony następujący komunikat o błędzie: . 

### <a name="what-are-some-of-the-exceptions-generated-by-azure-service-bus-apis-and-their-suggested-actions"></a>Jakie są niektóre wyjątki generowane przez interfejsy API usługi Azure Service Bus i ich sugerowane akcje?
Aby uzyskać listę możliwych wyjątków usługi Service Bus, zobacz [Omówienie wyjątków][Exceptions overview].

### <a name="what-is-a-shared-access-signature-and-which-languages-support-generating-a-signature"></a>Co to jest podpis dostępu współdzielonego i które języki obsługują generowanie podpisu?
Sygnatury dostępu współdzielonego to mechanizm uwierzytelniania oparty na bezpiecznych skrótach lub identyfikatorach URI typu SHA-256. Aby uzyskać informacje dotyczące generowania własnych podpisów w plikach Node.js, PHP, Java, Python i C#, zobacz artykuł [o podpisach dostępu współdzielonego.][Shared Access Signatures]

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i obszarem nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak przeprowadzić migrację obszaru nazw do innej subskrypcji platformy Azure?

Obszar nazw można przenieść z jednej subskrypcji platformy Azure do innej przy użyciu poleceń [portalu Azure](https://portal.azure.com) lub programu PowerShell. Aby wykonać operację, obszar nazw musi być już aktywny. Użytkownik wykonujący polecenia musi być administratorem zarówno w subskrypcji źródłowej, jak i docelowej.

#### <a name="portal"></a>Portal

Aby użyć portalu Azure do migracji obszarów nazw usługi Service Bus do innej subskrypcji, postępuj zgodnie ze wskazówkami [tutaj](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Następująca sekwencja poleceń programu PowerShell przenosi obszar nazw z jednej subskrypcji platformy Azure do innej. Aby wykonać tę operację, obszar nazw musi być już aktywny, a użytkownik uruchamiany polecenia programu PowerShell musi być administratorem zarówno w subskrypcji źródłowej, jak i docelowej.

```powershell
# Create a new resource group in target subscription
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o usłudze Service Bus, zobacz następujące artykuły:

* [Wprowadzenie usługi Azure Service Bus Premium (wpis w blogu)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
* [Wprowadzenie usługi Azure Service Bus Premium (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
* [Omówienie usługi Service Bus](service-bus-messaging-overview.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)

[Best practices for performance improvements using Service Bus]: service-bus-performance-improvements.md
[Best practices for insulating applications against Service Bus outages and disasters]: service-bus-outages-disasters.md
[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Quotas overview]: service-bus-quotas.md
[Exceptions overview]: service-bus-messaging-exceptions.md
[Shared Access Signatures]: service-bus-sas.md
