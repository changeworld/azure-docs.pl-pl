---
title: Usługa Azure Relay — często zadawane pytania | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Relay.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: spelluru
ms.openlocfilehash: 2433f4b3563cc8b301d1815cccf5ab24406e8662
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045581"
---
# <a name="azure-relay-faqs"></a>Usługa Azure Relay — często zadawane pytania

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania (FAQ) w o [usługi Azure Relay](https://azure.microsoft.com/services/service-bus/). Ogólne informacje dotyczące platformy Azure ceny i pomocy technicznej, zobacz [często zadawane pytania dotyczące pomocy technicznej Azure](https://azure.microsoft.com/support/faq/).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-relay"></a>Co to jest usługa Azure Relay?
[Usługi Azure Relay](relay-what-is-it.md) ułatwia tworzenie hybrydowych aplikacji, pomagając Wam więcej bezpieczne Uwidacznianie usług znajdujących się w korporacyjnej sieci firmowej do chmury publicznej. Usługi można ujawnić, bez konieczności otwierania połączenia przez zaporę i bez wprowadzania niepożądanych zmian do infrastruktury sieci korporacyjnej.

### <a name="what-is-a-relay-namespace"></a>Co to jest przestrzeń nazw usługi Relay?
A [przestrzeni nazw](relay-create-namespace-portal.md) jest kontenerem określania zakresu, służących do adresowania zasobów usługi Relay w aplikacji. Należy utworzyć przestrzeni nazw, aby używać usługi Relay. Jest to jedna z pierwszych kroków w środowisku wprowadzenie.

### <a name="what-happened-to-service-bus-relay-service"></a>Co się stało z usługi service Bus Relay?
Wcześniej nazwaną usługę Service Bus Relay jest teraz nazywana [przekaźnika WCF](relay-wcf-dotnet-get-started.md). Można nadal używać tej usługi w zwykły sposób. Funkcja połączeń hybrydowych jest zaktualizowana wersja usługi, która jest ponownie przeszczepione z usługi Azure BizTalk Services. Przekaźnik WCF i połączeń hybrydowych nadal są obsługiwane.

## <a name="pricing"></a>Cennik
W tej sekcji odpowiedzi na niektóre często zadawane pytania dotyczące usługi Relay struktury cenowej. Możesz również zobaczyć [— często zadawane pytania dla pomocy technicznej Azure](https://azure.microsoft.com/support/faq/) for Azure ogólne informacje o cenach. Aby uzyskać pełne informacje o cenach usługi Relay, zobacz [szczegóły cennika usługi Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak można opłaty za połączenia hybrydowe i WCF Relay?
Aby uzyskać pełne informacje o cenach usługi Relay, zobacz [połączenia hybrydowe i przekaźniki WCF] [ Pricing overview] tabeli na stronie szczegółów cennika usługi Service Bus. Oprócz ceny podane na tej stronie opłaty są naliczane w przypadku transferów danych skojarzone dla ruchu wychodzącego poza centrum danych, w którym zainicjowano aplikacji.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak są rozliczane dla połączeń hybrydowych?
Poniżej przedstawiono trzy przykładowe scenariusze rozliczeń dla połączeń hybrydowych:

*   Scenariusz 1:
    *   Masz pojedynczego odbiornika, takiego jak wystąpienie Menedżera połączeń hybrydowych zainstalowane i działające nieprzerwanie przez cały miesiąc.
    *   Wysyłasz 3 GB danych za pośrednictwem połączenia w miesiącu. 
    *   Łączna opłata wynosi $5.
*   Scenariusz 2:
    *   Masz pojedynczego odbiornika, takiego jak wystąpienie Menedżera połączeń hybrydowych zainstalowane i działające nieprzerwanie przez cały miesiąc.
    *   Wysyłasz 10 GB danych za pośrednictwem połączenia w miesiącu.
    *   Łączna opłata jest 7.50 $. To 5 USD za połączenia i pierwsze 5 GB + 2,50 USD za dodatkowe 5 GB danych.
*   Scenariusz 3:
    *   Masz dwa wystąpienia, A i B, Menedżera połączeń hybrydowych zainstalowane i działające nieprzerwanie przez cały miesiąc.
    *   Wysyłasz 3 GB danych za pośrednictwem połączenia A w ciągu miesiąca.
    *   Wysyłasz 6 GB danych za pośrednictwem połączenia B w miesiącu.
    *   Łączna opłata jest 10,50 $. To 5 USD za połączenie A + 5 USD za połączenie B + 0,50 USD (w przypadku szósty GB połączenia B).

Należy zauważyć, że ceny użyte w tych przykładach mają zastosowanie tylko w trakcie okresu zapoznawczego, połączenia hybrydowe. Ceny są może ulec zmianie po ogólnym udostępnieniu połączeń hybrydowych.

### <a name="how-are-hours-calculated-for-relay"></a>Jak obliczane są godziny przekazywania?

Przekaźnik WCF to dostępne tylko w przestrzeni nazw w warstwie standardowa. Ceny i [przydziały połączenia](../service-bus-messaging/service-bus-quotas.md) dla przekaźniki, w przeciwnym razie nie uległy zmianie. Oznacza to, że przekaźniki w dalszym ciągu naliczane na podstawie liczby komunikatów (nie operacji) i godzin przekazywania. Aby uzyskać więcej informacji, zobacz ["Hybrydowe połączeń i przekaźniki WCF"](https://azure.microsoft.com/pricing/details/service-bus/) tabeli na stronie szczegółów cennika.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co się stanie, jeśli mam więcej niż jeden odbiornik, nawiązanie połączenia z określonym przekaźnikiem?
W niektórych przypadkach pojedynczego przekazywania może mieć wiele odbiorników połączonych. Wystąpienie usługi przekazywania jest uznawane za otwarte, gdy jest z nią połączona co najmniej jeden odbiornik. Dodawania detektorów do wyników otwartego przekazywania w godziny przekazywania dodatkowych. Numer nadawcy przekazywania (liczba klientów, które wywołania lub wysyłać komunikaty do przekaźników) podłączone do przekazywania nie ma wpływu na obliczenia godzin przekazywania.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak jest obliczana wartość licznika komunikaty dla przekaźniki WCF?
(**Dotyczy to tylko przekaźniki WCF. Komunikaty nie są kosztu dla połączeń hybrydowych.** )

Ogólnie rzecz biorąc płatnych komunikatów dla przekaźniki są obliczane przy użyciu tej samej metody, która służy do jednostek obsługiwanych przez brokera (kolejki, tematy i subskrypcje), opisanych powyżej. Istnieją jednak pewne istotne różnice.

Wysyłanie wiadomości do przekaźnika usługi Service Bus jest traktowany jako "pełnej za pośrednictwem" wysyłać odbiornik usługi relay, który odbiera wiadomości. Nie jest ona traktowana jako operacji wysyłania przekaźnika usługi Service Bus, następuje dostarczania na odbiornik usługi relay. Żądanie odpowiedź stylu wywołania usługi (o rozmiarze do 64 KB) względem przekaźnik odbiornika wyniki w dwóch płatnych komunikatów: jeden płatny komunikat żądania i jeden płatny komunikat odpowiedzi (zakładając, że odpowiedź jest również 64 KB lub mniej). Stanowi to odmianę pośredniczy między klientem a usługą za pomocą kolejki. Jeśli używasz kolejki do pośredniczy między klientem a usługą tego samego wzorca "żądanie-odpowiedź" wymaga wysyłania żądań do kolejki, a następnie usuwania z kolejki/dostarczania z kolejki w usłudze. Następnie przez wysłanie odpowiedzi do kolejnej kolejki i usuwania z kolejki/dostarczania z tej kolejki do klienta. Wzorzec kolejki udziału za pomocą tego samego założenia rozmiar w całym (maksymalnie 64 KB), powoduje 4 płatnych komunikatów. Rachunek będzie dotyczył dwa razy liczba komunikatów do wykonania tego samego wzorca, który można wykonać przy użyciu przekazywania. Oczywiście istnieją korzyści z korzystania z kolejek do osiągnięcia tego wzorca, takich jak niezawodność i wyrównywanie obciążenia. Te korzyści może uzasadniać dodatkowych kosztów.

Przekaźniki, które są otwarte przy użyciu **netTCPRelay** wiązania WCF traktować wiadomości, nie jako pojedyncze wiadomości, ale jako strumień dane przepływają przez system. Korzystając z tego powiązania, tylko nadawcy i odbiornika widzą ramek poszczególne wiadomości wysłanych i odebranych. Dla przekazuje używające **netTCPRelay** powiązania, wszystkie dane jest traktowany jako strumień do obliczania płatnych komunikatów. W tym przypadku usługi Service Bus oblicza łączna ilość danych wysyłanych lub odbieranych przez wystąpienie usługi relay poszczególnych na podstawie 5-minutowych. Następnie dzieli tego łączna ilość danych, przez 64 KB, aby określić liczbę płatnych komunikatów dla tej usługi relay w tym okresie czasu.

## <a name="quotas"></a>Przydziały
| Nazwa limitu przydziału | Zakres |  Uwagi | Wartość |
| --- | --- | --- | --- |
| Współbieżne odbiorniki przekaźnik |Jednostka |Kolejne żądania dla dodatkowych połączeń są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |25 |
| Połączeń współbieżnych przekazywania na wszystkich punktem końcowym przekaźnika w przestrzeni nazw usługi |Przestrzeń nazw |- |5000 |
| Punktów końcowych przestrzeni nazw usługi Relay |Przestrzeń nazw |- |10 000 |
| Rozmiar komunikatu [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) i [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) przekazuje |Przestrzeń nazw |Wiadomości przychodzących, które wykraczają poza te przydziały są odrzucane, a wyjątek jest odbierany przez kod wywołujący. |64 KB |
| Rozmiar komunikatu [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) przekazuje |Przestrzeń nazw |Brak limitu rozmiaru wiadomości. |Nieograniczona liczba |

### <a name="does-relay-have-any-usage-quotas"></a>Przekaźnik ma przydziały użycia?
Domyślnie dla dowolnej usługi w chmurze firmy Microsoft ustawia łączny miesięczny przydział użycia, który jest obliczany dla wszystkich subskrypcji klientów. Rozumiemy, że czasami potrzeb może przekroczenia limitów. Dlatego firma Microsoft jest zrozumienie potrzeb i odpowiednio dostosować te limity można skontaktować się działem obsługi klienta w dowolnym momencie. Service Bus przydziały użycia agregacji są następujące:

* 5 miliardów komunikatów
* 2 miliony godzin przekazywania

Mimo że firma Microsoft zastrzega sobie prawo wyłączenia konta, które przekracza jego miesięczne przydziały użycia, firma Microsoft zapewnia powiadomienie e-mail i wprowadzania wielu próbuje nawiązać połączenie klienta przed podjęciem działania. Klienci, którzy przekroczenia tych limitów przydziału nadal są odpowiedzialne za naliczania opłat.

### <a name="naming-restrictions"></a>Ograniczenia nazewnictwa
Nazwa przestrzeni nazw usługi Relay musi być od 6 do 50 znaków.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i przestrzeni nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak migrować przestrzeni nazw do innej subskrypcji platformy Azure?

Aby przenieść przestrzeni nazw z jedną subskrypcją platformy Azure do innej subskrypcji, można użyć [witryny Azure portal](https://portal.azure.com) lub za pomocą poleceń programu PowerShell. Aby przenieść przestrzeni nazw do innej subskrypcji, przestrzeń nazw musi być aktywne. Użytkownik uruchamiający polecenia musi być użytkownika administratora dla subskrypcji źródłowej i docelowej.

#### <a name="azure-portal"></a>Azure Portal

Aby przeprowadzić migrację przestrzeni nazw usługi Azure Relay z jednej subskrypcji do innej subskrypcji, należy użyć witryny Azure portal, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Przenieś przestrzeni nazw z jedną subskrypcją platformy Azure do innej subskrypcji przy użyciu programu PowerShell, użyj następującej procedury. Aby wykonać tę operację, przestrzeń nazw już musi być aktywne, a użytkownik uruchamiający poleceń programu PowerShell musi być użytkownika administratora dla subskrypcji źródłowej i docelowej.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jakie są niektóre wyjątki generowane przez interfejsy API usługi Azure Relay i sugerowanych akcji, które można wykonać?
Opis typowych wyjątków i sugerowane akcje można wykonać, zobacz [przekazywania wyjątki][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co to jest sygnatura dostępu współdzielonego i języków, których można użyć do generowania podpisu?
Udostępnione sygnatur dostępu (SAS) to mechanizm uwierzytelniania, na podstawie bezpiecznego skróty SHA-256 lub identyfikatory URI. Aby uzyskać informacje o tym, jak wygenerować własne podpisów w Node, PHP, Java, C i C#, zobacz [uwierzytelniania usługi Service Bus przy użyciu sygnatury dostępu współdzielonego][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Czy możliwe jest punktem końcowym przekaźnika dozwolonych?
Tak. Klient przekazujący sprawia, że połączenia z usługą Azure Relay przy użyciu w pełni kwalifikowanych nazw domen. Klientów można dodać wpisu `*.servicebus.windows.net` na zaporach, które obsługują DNS umieszczania na białej liście.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do platformy .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Rozpoczynanie pracy z węzłem](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
