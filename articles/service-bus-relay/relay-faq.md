---
title: Często zadawane pytania dotyczące programu Azure Relay | Dokumenty firmy Microsoft
description: Ten artykuł zawiera odpowiedzi na niektóre z często zadawanych pytań dotyczących usługi Azure Relay.
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
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: d5032b427316a3c4e07013af4e8214e239a6efb3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514004"
---
# <a name="azure-relay-faqs"></a>Często zadawane pytania dotyczące przekazywania platformy Azure

W tym artykule odsunie się od odpowiedzi na często zadawane pytania dotyczące [usługi Azure Relay.](https://azure.microsoft.com/services/service-bus/) Aby uzyskać ogólne informacje dotyczące cen i pomocy technicznej platformy Azure, zobacz często zadawane [pytania dotyczące pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/faq/)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-relay"></a>Co to jest usługa Azure Relay?
[Usługa Azure Relay](relay-what-is-it.md) ułatwia aplikacje hybrydowe, pomagając bezpieczniej udostępniać usługi, które znajdują się w sieci przedsiębiorstwa, w chmurze publicznej. Usługi można udostępniać bez otwierania połączenia zapory i bez konieczności natrętnych zmian w infrastrukturze sieci firmowej.

### <a name="what-is-a-relay-namespace"></a>Co to jest obszar nazw przekaźnika?
[Obszar nazw](relay-create-namespace-portal.md) jest kontenerem zakresu, którego można użyć do adresowania zasobów przekazywania w aplikacji. Aby użyć przekaźnika, należy utworzyć obszar nazw. Jest to jeden z pierwszych kroków w rozpoczęciu pracy.

### <a name="what-happened-to-service-bus-relay-service"></a>Co się stało z usługą Przekaźnik magistrali usług?
Poprzednia usługa przekazywania magistrali usług jest teraz nazywana [WCF Relay](service-bus-relay-tutorial.md). Możesz nadal korzystać z tej usługi w zwykły sposób. Funkcja Połączenia hybrydowe jest zaktualizowaną wersją usługi, która została przeszczepiona z usług Azure BizTalk Services. WCF Relay i połączenia hybrydowe są nadal obsługiwane.

## <a name="pricing"></a>Cennik
W tej sekcji odsunie się od odpowiedzi na niektóre często zadawane pytania dotyczące struktury cenowej Przekazywanie. Można również wyświetlić często zadawane [pytania dotyczące pomocy technicznej platformy Azure,](https://azure.microsoft.com/support/faq/) aby uzyskać ogólne informacje o cenach platformy Azure. Aby uzyskać pełne informacje na temat cennika usługi Relay, zobacz [Szczegóły cen usługi Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak pobierać opłaty za połączenia hybrydowe i przekaźnik WCF?
Aby uzyskać pełne informacje na temat cen przekazywania, zobacz [hybrydowe połączenia i przekaźniki WCF][Pricing overview] tabeli na stronie szczegóły cennik usługi Service Bus. Oprócz cen wymienionych na tej stronie są naliczane opłaty za skojarzone transfery danych dla wyjścia poza centrum danych, w którym aplikacja jest aprowizowana.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak rozliczane są płatności za połączenia hybrydowe?
Oto trzy przykładowe scenariusze rozliczeń dla połączeń hybrydowych:

*   Scenariusz 1.
    *   Masz jeden odbiornik, takich jak wystąpienie Menedżera połączeń hybrydowych zainstalowany i stale uruchomiony przez cały miesiąc.
    *   W ciągu miesiąca wysyłasz 3 GB danych przez połączenie. 
    *   Całkowita opłata wynosi 5 USD.
*   Scenariusz 2.
    *   Masz jeden odbiornik, takich jak wystąpienie Menedżera połączeń hybrydowych zainstalowany i stale uruchomiony przez cały miesiąc.
    *   Wysyłasz 10 GB danych przez połączenie w ciągu miesiąca.
    *   Całkowita opłata wynosi 7,50 USD. To $5 za połączenie i pierwsze 5 GB + $2.50 dla dodatkowych 5 GB danych.
*   Scenariusz 3.
    *   Masz dwa wystąpienia, A i B, Menedżera połączeń hybrydowych zainstalowany i stale uruchomiony przez cały miesiąc.
    *   W ciągu miesiąca wysyłasz 3 GB danych przez połączenie A.
    *   Wysyłasz 6 GB danych przez połączenie B w ciągu miesiąca.
    *   Całkowita opłata wynosi 10,50 USD. To $5 za połączenie A + $5 dla połączenia B + $0.50 (dla szóstego gigabajta przy połączeniu B).

Należy zauważyć, że ceny używane w przykładach mają zastosowanie tylko w okresie podglądu połączeń hybrydowych. Ceny mogą ulec zmianie w przypadku ogólnej dostępności połączeń hybrydowych.

### <a name="how-are-hours-calculated-for-relay"></a>Jak obliczane są godziny dla przekaźnika?

WCF Relay jest dostępny tylko w standardowych przestrzeniach nazw warstwy. Przydziały cen i [połączeń](../service-bus-messaging/service-bus-quotas.md) dla przekaźników w przeciwnym razie nie uległy zmianie. Oznacza to, że przekaźniki są nadal naliczane na podstawie liczby komunikatów (nie operacji) i godzin przekazywania. Aby uzyskać więcej informacji, zobacz tabelę ["Połączenia hybrydowe i przekaźniki WCF"](https://azure.microsoft.com/pricing/details/service-bus/) na stronie szczegółów cennika.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co zrobić, jeśli do określonego przekaźnika podłączono więcej niż jednego odbiornika?
W niektórych przypadkach pojedynczy przekaźnik może mieć wiele połączonych odbiorników. Przekaźnik jest uważany za otwarty, gdy podłączony jest do niego co najmniej jeden odbiornik przekaźnika. Dodanie odbiorników do otwartego przekaźnika powoduje dodatkowe godziny przekazywania. Liczba nadawców przekaźników (klientów, którzy wywołują lub wysyłają wiadomości do przekaźników), które są podłączone do przekaźnika, nie wpływa na obliczanie godzin przekazywania.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak jest obliczany miernik wiadomości dla przekaźników WCF?
(**Dotyczy to tylko przekaźników WCF. Wiadomości nie są kosztem połączeń hybrydowych.**)

Ogólnie rzecz biorąc, rozliczane wiadomości dla przekaźników są obliczane przy użyciu tej samej metody, która jest używana dla jednostek pośredniczących (kolejki, tematy i subskrypcje), opisane wcześniej. Istnieją jednak pewne znaczące różnice.

Wysyłanie wiadomości do przekaźnika usługi Service Bus jest traktowane jako "full through" wysłać do odbiornika przekaźnika, który odbiera wiadomość. Nie jest traktowany jako operacja wysyłania do przekaźnika usługi Service Bus, po którym następuje dostarczanie do odbiornika przekaźnika. Wywołanie usługi w stylu żądania i odpowiedzi (do 64 KB) względem odbiornika przekazywania powoduje dwie płatne wiadomości: jedną wiadomość rozliczaną dla żądania i jedną wiadomość podlegający rozliczaniu dla odpowiedzi (przy założeniu, że odpowiedź jest również 64 KB lub mniejsza). Jest to inna niż przy użyciu kolejki do pośredniczenia między klientem a usługą. Jeśli używasz kolejki do pośredniczenia między klientem a usługą, ten sam wzorzec żądanie odpowiedź wymaga żądania wysłać do kolejki, a następnie dequeue/dostawy z kolejki do usługi. Po tym następuje odpowiedź wysłać do innej kolejki i dequeue/dostawy z tej kolejki do klienta. Przy użyciu tych samych założeń rozmiaru w całym (do 64 KB), za pośrednictwem kolejki wzorzec wyniki w 4 podpisy. Naliczane są dwa razy więcej wiadomości, aby zaimplementować ten sam wzorzec, który można wykonać za pomocą przekazywania. Oczywiście istnieją korzyści z używania kolejek do osiągnięcia tego wzorca, takich jak trwałość i bilansowanie obciążenia. Korzyści te mogą uzasadniać dodatkowe koszty.

Przekaźniki, które są otwierane przy użyciu **powiązania netTCPRelay** WCF traktują wiadomości nie jako pojedyncze komunikaty, ale jako strumień danych przepływających przez system. Korzystając z tego powiązania, tylko nadawca i odbiornik mają wgląd w kadrowanie poszczególnych wiadomości wysłanych i odebranych. W przypadku przekaźników, które używają powiązania **netTCPRelay,** wszystkie dane są traktowane jako strumień do obliczania wiadomości podlegających rozliczaniu. W takim przypadku usługa Service Bus oblicza całkowitą ilość danych wysyłanych lub odbieranych za pośrednictwem poszczególnych przekaźników na podstawie 5 minut. Następnie dzieli tę całkowitą ilość danych przez 64 KB, aby określić liczbę wiadomości podlegać rozliczaniu dla tego przekazywania w tym okresie.

## <a name="quotas"></a>Przydziały
| Nazwa przydziału | Zakres |  Uwagi | Wartość |
| --- | --- | --- | --- |
| Współbieżne detektory na przekaźniku |Jednostka |Kolejne żądania dotyczące dodatkowych połączeń są odrzucane i wyjątek jest odbierany przez kod wywołujący. |25 |
| Równoczesne połączenia przekazywania na wszystkie punkty końcowe przekaźnika w obszarze nazw usługi |Przestrzeń nazw |- |5000 |
| Przekazywanie punktów końcowych na obszar nazw usługi |Przestrzeń nazw |- |10 000 |
| Rozmiar wiadomości dla przekaźników [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) i [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) |Przestrzeń nazw |Przychodzące wiadomości, które przekraczają te przydziały są odrzucane i wyjątek jest odbierany przez kod wywołujący. |64 KB |
| Rozmiar wiadomości dla przekaźników [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) |Przestrzeń nazw |Brak limitu rozmiaru wiadomości. |Unlimited (nieograniczony) |

### <a name="does-relay-have-any-usage-quotas"></a>Czy relay ma jakieś przydziały użycia?
Domyślnie dla każdej usługi w chmurze firma Microsoft ustawia łączny miesięczny przydział użycia, który jest obliczany we wszystkich subskrypcjach klienta. Rozumiemy, że czasami Twoje potrzeby mogą przekroczyć te limity. Możesz skontaktować się z obsługą klienta w dowolnym momencie, abyśmy mogli zrozumieć Twoje potrzeby i odpowiednio dostosować te limity. W przypadku usługi Service Bus zagregowane przydziały użycia są następujące:

* 5 miliardów wiadomości
* 2 miliony godzin przekaźników

Chociaż zastrzegamy sobie prawo do wyłączenia konta, które przekracza miesięczne limity użytkowania, dostarczamy powiadomienia e-mail i podejmujemy wiele prób skontaktowania się z klientem przed podjęciem jakichkolwiek działań. Klienci, którzy przekraczają te limity, są nadal odpowiedzialni za nadmierne opłaty.

### <a name="naming-restrictions"></a>Ograniczenia nazewnictwa
Nazwa przekaźnika musi mieć długość od 6 do 50 znaków.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i obszarem nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak przeprowadzić migrację obszaru nazw do innej subskrypcji platformy Azure?

Aby przenieść obszar nazw z jednej subskrypcji platformy Azure do innej subskrypcji, można użyć [witryny Azure portal](https://portal.azure.com) lub użyć poleceń programu PowerShell. Aby przenieść obszar nazw do innej subskrypcji, obszar nazw musi być już aktywny. Użytkownik uruchamiając polecenia musi być administratorem użytkownika zarówno w subskrypcji źródłowej i docelowej.

#### <a name="azure-portal"></a>Portal Azure

Aby użyć witryny Azure portal do migracji obszarów nazw usługi Azure Relay z jednej subskrypcji do innej subskrypcji, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md#use-the-portal). 

#### <a name="powershell"></a>PowerShell

Aby użyć programu PowerShell do przeniesienia obszaru nazw z jednej subskrypcji platformy Azure do innej subskrypcji, należy użyć następującej sekwencji poleceń. Aby wykonać tę operację, obszar nazw musi być już aktywny, a użytkownik uruchamiany polecenia programu PowerShell musi być użytkownikiem administratora zarówno w subskrypcji źródłowej, jak i docelowej.

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
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jakie są niektóre wyjątki generowane przez interfejsy API usługi Azure Relay i sugerowane akcje, które można podjąć?
Aby uzyskać opis typowych wyjątków i sugerowanych działań, które można podjąć, zobacz [Wyjątki przekazywania][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co to jest podpis dostępu współdzielonego i których języków można używać do generowania podpisu?
Sygnatury dostępu współdzielonego (SAS) to mechanizm uwierzytelniania oparty na bezpiecznych skrótach lub identyfikatorach URI SHA-256. Aby uzyskać informacje dotyczące generowania własnych podpisów w plikach Node.js, PHP, Python, Java, C i C#, zobacz [Uwierzytelnianie usługi Service Bus za pomocą sygnatur dostępu współdzielonego][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Czy możliwe jest umieszczanie na białej liście punktów końcowych przekazywania?
Tak. Klient przekazywania nawiązuje połączenia z usługą Azure Relay przy użyciu w pełni kwalifikowanych nazw domen. Klienci mogą dodać `*.servicebus.windows.net` wpis dotyczący zapór obsługiwanych przez system DNS na białej liście.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Rozpoczęcie pracy z programem .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
