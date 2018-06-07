---
title: Przekaźnik Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące przekaźnika usługi Azure.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 886d2c7f-838f-4938-bd23-466662fb1c8e
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/21/2018
ms.author: sethm
ms.openlocfilehash: 1795c1ec0b4129e3c99fff3fc893148e191ce83e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34641496"
---
# <a name="azure-relay-faqs"></a>Przekaźnik Azure — często zadawane pytania

Ten artykuł zawiera odpowiedzi niektóre często zadawane pytania (FAQ) o [przekazywania Azure](https://azure.microsoft.com/services/service-bus/). Ogólne Azure cennik i pomocy technicznej informacji można uzyskać [często zadawane pytania dotyczące obsługi Azure](https://azure.microsoft.com/support/faq/).

## <a name="general-questions"></a>Pytania ogólne
### <a name="what-is-azure-relay"></a>Co to jest usługa Azure Relay?
[Usługi przekazywania Azure](relay-what-is-it.md) ułatwia hybrydowych aplikacji, pomagając więcej bezpieczne Uwidacznianie usług, które znajdują się w korporacyjnym środowisku sieciowym w chmurze publicznej. Usługi mogą uwidaczniać bez konieczności otwierania połączenia zaporę i bez wprowadzania niepożądanych zmian do infrastruktury sieci korporacyjnej.

### <a name="what-is-a-relay-namespace"></a>Co to jest obszar nazw przekazywania?
A [przestrzeni nazw](relay-create-namespace-portal.md) jest kontenerem zakresu, który służy do adresowania przekazywania zasobów w aplikacji. Konieczne jest utworzenie przestrzeni nazw, aby używać przekaźnika. Jest to jedna z pierwszym krokiem wprowadzenie.

### <a name="what-happened-to-service-bus-relay-service"></a>Co się stało z usługi Service Bus Relay?
Wcześniej nazwane usługi Service Bus Relay nosi teraz [przekazywania WCF](relay-wcf-dotnet-get-started.md). Można nadal używać tej usługi w zwykły sposób. Funkcja połączeń hybrydowych było możliwe jest zaktualizowana wersja usługi, która jest ponownie przeszczepione z usług Azure BizTalk. Przekaźnik usługi WCF i połączeń hybrydowych nadal obsługiwane.

## <a name="pricing"></a>Cennik
W tej sekcji odpowiedzi na niektóre często zadawane pytania dotyczące przekazywania cennik struktury. Również widoczne [pomocy technicznej platformy Azure — często zadawane pytania](https://azure.microsoft.com/support/faq/) Azure ogólne informacje o cenach. Aby uzyskać pełne informacje o cenach przekazywania, zobacz [szczegóły cennika usługi Service Bus][Pricing overview].

### <a name="how-do-you-charge-for-hybrid-connections-and-wcf-relay"></a>Jak można pobierać połączeń hybrydowych i przekazywania WCF?
Aby uzyskać pełne informacje o cenach przekazywania, zobacz [połączeń hybrydowych i przekaźników WCF] [ Pricing overview] tabeli usługi Service Bus cennik strony szczegółów. Oprócz ceny wymienione na tej stronie są naliczane transferów danych skojarzony za wyjście poza centrum danych, w którym aplikacja zostanie zainicjowana.

### <a name="how-am-i-billed-for-hybrid-connections"></a>Jak m rozliczane dla połączenia hybrydowe?
Poniżej przedstawiono trzy przykładowe scenariusze rozliczeń dla połączeń hybrydowych było możliwe:

*   Scenariusz 1.
    *   Masz jeden odbiornik, takich jak wystąpienie Menedżera połączeń hybrydowych zainstalowane i pracujące dla całego miesiąca.
    *   3 GB danych są wysyłane za pośrednictwem połączenia w miesiącu. 
    *   Poziom naładowania całkowita jest wynosi 5.
*   Scenariusz 2.
    *   Masz jeden odbiornik, takich jak wystąpienie Menedżera połączeń hybrydowych zainstalowane i pracujące dla całego miesiąca.
    *   10 GB danych są wysyłane za pośrednictwem połączenia w miesiącu.
    *   Poziom naładowania całkowita jest 7.50 $. To 5 $ dla połączenia i pierwsze 5 GB + $2.50 dodatkowe 5 GB danych.
*   Scenariusz 3:
    *   Masz dwa wystąpienia, A i B, zainstalowano i uruchomiono stale dla całego miesiąca Menedżera połączeń hybrydowych.
    *   3 GB danych są wysyłane za pośrednictwem połączenia A w miesiącu.
    *   6 GB danych są wysyłane za pośrednictwem połączenia B w miesiącu.
    *   Poziom naładowania całkowita jest 10.50 $. To 5 $ połączenia A + $5 dla połączenia B wysokości 0,50 USD (dla szóstego gigabajt połączenia B).

Należy pamiętać, że ceny używana w przykładach są używane tylko podczas okresu zapoznawczego połączeń hybrydowych było możliwe. Ceny mogą ulec zmianie po udostępnieniu wersji ogólnodostępnej połączeń hybrydowych.

### <a name="how-are-hours-calculated-for-relay"></a>Jak są obliczane godziny przekazywania?

Przekaźnik WCF jest dostępna tylko w przestrzeniach nazw warstwy standardowa. Cennik i [przydziały połączenia](../service-bus-messaging/service-bus-quotas.md) dla przekaźniki, w przeciwnym razie nie uległy zmianie. Oznacza to, że przekaźników nadal naliczane na podstawie liczby wiadomości (nie operacje) i godziny przekazywania. Aby uzyskać więcej informacji, zobacz ["Hybrydowego połączenia i przekaźników WCF"](https://azure.microsoft.com/pricing/details/service-bus/) tabeli na stronie szczegółów cenową.

### <a name="what-if-i-have-more-than-one-listener-connected-to-a-specific-relay"></a>Co zrobić, jeśli użytkownik ma więcej niż jeden odbiornik połączone z określonym przekaźnikiem?
W niektórych przypadkach pojedynczego przekazywania może mieć wiele odbiorników połączonych. Przekaźnik jest uznawane za otwarte, gdy co najmniej jednego przekazującego odbiornika jest podłączony do niego. Dodawanie odbiorników do przekazywania Otwórz wyniki w godzinach przekazywania dodatkowych. Liczba przekazywania nadawców (klientów, którzy wywołania lub wysyłać wiadomości do przekaźników) podłączone do przekazywania nie ma wpływu na obliczenia godziny przekazywania.

### <a name="how-is-the-messages-meter-calculated-for-wcf-relays"></a>Jak licznik wiadomości jest obliczana dla przekaźników usługi WCF
(**Dotyczy tylko przekaźników usługi WCF. Wiadomość nie jest koszt połączeń hybrydowych było możliwe.** )

Ogólnie rzecz biorąc rozliczeniowy komunikatów dla przekaźniki są obliczane przy użyciu tej samej metody, który jest używany dla jednostek obsługiwanych przez brokera (kolejki, tematy i subskrypcje), opisane wcześniej. Istnieje jednak kilka istotnych różnic.

Wysyłanie wiadomości do przekaźnika usługi Service Bus jest traktowana jako "pełnej za pośrednictwem" Wyślij do odbiornika przekazywania, który odbiera wiadomości. Nie jest traktowana jako operacji wysyłania do przekaźnika usługi Service Bus, następuje dostarczanie do odbiornika przekazywania. Żądanie odpowiedź stylu wywołania usługi (o maksymalnym rozmiarze 64 KB) przed przekaźnik odbiornika powoduje dwa komunikaty rozliczeniowy: jeden komunikat rozliczeń dla żądania i jeden rozliczeniowy komunikat odpowiedzi na (przy założeniu odpowiedzi jest także 64 KB lub mniej). To jest inny niż przy użyciu kolejki do pośredniczących między klientem a usługą. Jeśli używasz kolejki do pośredniczących między klientem a usługą tego samego wzorca "żądanie-odpowiedź" wymaga wysyłania żądań do kolejki, następuje kolejki/dostawy z kolejki z usługą. To następuje wysyłania odpowiedzi do innej kolejki i kolejki/dostawy z tej kolejki do klienta. Wzorzec udziału kolejki przy użyciu tego samego rozmiaru założeń w całym (maksymalnie 64 KB), powoduje 4 rozliczeniowy wiadomości. Czy opłaty będą naliczane za dwukrotnie liczbę komunikatów do wykonania tego samego wzorca, który przy użyciu przekaźnika. Oczywiście istnieją korzyści wynikające z używania kolejek osiągnięcie tego wzorca, takie jak trwałości i wyrównywanie obciążenia. Te korzyści mogą uzasadniać dodatkowych kosztów.

Przekaźniki, które są otwierane za pomocą **netTCPRelay** wiązania WCF Traktuj wiadomości nie jako pojedyncze wiadomości, ale jako strumień danych przepływających przez system. Korzystając z tego powiązania, tylko nadawcy i odbiornika mieć wgląd w ramek poszczególne wiadomości wysłanych i odebranych. Dla przekazuje używające **netTCPRelay** powiązanie, wszystkie dane jest traktowane jako strumień obliczania rozliczeniowy wiadomości. W takim przypadku Usługa Service Bus oblicza łączną ilość danych wysyłane lub odbierane za pośrednictwem każdej poszczególnych przekazywania na podstawie 5 minut. Następnie dzieli tego całkowitej ilości danych przez 64 KB, aby określić numer rozliczeniowy komunikatów dla tego przekazywania podczas tego okresu.

## <a name="quotas"></a>Przydziały
| Nazwa przydziału | Zakres |  Uwagi | Wartość |
| --- | --- | --- | --- |
| Współbieżne odbiorników w przekaźnik |Jednostka |Kolejne żądania dla dodatkowych połączeń są odrzucane i wyjątek jest odbierany przez kod wywołujący. |25 |
| Połączeń współbieżnych przekazywania na wszystkich przekazywania punktów końcowych w przestrzeni nazw usługi |Przestrzeń nazw |- |5000 |
| Punkty końcowe przekazywania na przestrzeni nazw usługi |Przestrzeń nazw |- |10 000 |
| Rozmiar komunikatu [NetOnewayRelayBinding](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) i [NetEventRelayBinding](/dotnet/api/microsoft.servicebus.neteventrelaybinding) przekazuje |Przestrzeń nazw |Wiadomości przychodzących, które przekraczają te przydziały są odrzucane i wyjątek jest odbierany przez kod wywołujący. |64 KB |
| Rozmiar komunikatu [HttpRelayTransportBindingElement](/dotnet/api/microsoft.servicebus.httprelaytransportbindingelement) i [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) przekazuje |Przestrzeń nazw |Brak limitu rozmiaru dla wiadomości. |Nieograniczona liczba |

### <a name="does-relay-have-any-usage-quotas"></a>Przekaźnik ma wszelkie przydziały użycia?
Domyślnie dla dowolnej usługi w chmurze firmy Microsoft ustawia agregacji miesięczne przydział użycia, która jest obliczana dla wszystkich subskrypcji klienta. Rozumiemy, że w czasie potrzeb może przekroczyć tych limitów. Tak możemy zrozumienie potrzeb i odpowiednio dostosować te limity można się z obsługą klienta w dowolnym momencie. Dla usługi Service Bus przydziały użycie agregacji są następujące:

* 5 miliardów komunikatów
* 2 miliony godzin przekazywania

Mimo że firma Microsoft zastrzega sobie prawo wyłączyć konto, które przekracza jego miesięcznego wykorzystania przydziały, firma Microsoft udostępnia powiadomienia e-mail i wykonujemy wielu podejmie próbę skontaktowania się z klientem, przed podjęciem działania. Klienci, którzy przekracza te przydziały nadal są odpowiedzialne za nadmiarowe opłat.

### <a name="naming-restrictions"></a>Ograniczenia nazewnictwa
Nazwa przestrzeni nazw przekazywania musi być od 6 do 50 znaków.

## <a name="subscription-and-namespace-management"></a>Zarządzanie subskrypcją i przestrzeni nazw
### <a name="how-do-i-migrate-a-namespace-to-another-azure-subscription"></a>Jak przeprowadzić migrację przestrzeni nazw do innej subskrypcji platformy Azure?

Aby przenieść przestrzeni nazw z jedną subskrypcją platformy Azure do innej subskrypcji, można użyć [portalu Azure](https://portal.azure.com) lub użyć poleceń programu PowerShell. Aby przenieść przestrzeni nazw do innej subskrypcji, przestrzeni nazw musi już być aktywne. Użytkownik uruchamiający polecenia musi być użytkownika administratora dla subskrypcji źródłowej i docelowej.

#### <a name="azure-portal"></a>Azure Portal

Na potrzeby migracji przestrzeni nazw przekazywania Azure z jedną subskrypcję do innej subskrypcji w portalu Azure, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md#use-portal). 

#### <a name="powershell"></a>PowerShell

Przenieś przestrzeni nazw z jedną subskrypcją platformy Azure do innej subskrypcji przy użyciu programu PowerShell, użyj następującej procedury. Do wykonania tej operacji, przestrzeń nazw już musi być aktywne, a użytkownik uruchamiający poleceń programu PowerShell musi być użytkownika administratora dla subskrypcji źródłowej i docelowej.

```azurepowershell-interactive
# Create a new resource group in the target subscription.
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move the namespace from the source subscription to the target subscription.
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="troubleshooting"></a>Rozwiązywanie problemów
### <a name="what-are-some-of-the-exceptions-generated-by-azure-relay-apis-and-suggested-actions-you-can-take"></a>Jakie są wyjątki generowane przez interfejsów API przekaźnika usługi Azure i sugerowanych akcji, które można wykonać?
Opis wyjątków typowe i wyświetlić sugerowane akcje można wykonać, zobacz [przekazywania wyjątki][Relay exceptions].

### <a name="what-is-a-shared-access-signature-and-which-languages-can-i-use-to-generate-a-signature"></a>Co to jest sygnatury dostępu współdzielonego i języki, których można użyć do generowania podpisu?
Udostępniony sygnatur dostępu (SAS) są mechanizmu uwierzytelniania na podstawie bezpiecznego wartości skrótu SHA-256 lub identyfikatorów URI. Aby uzyskać informacje o sposobie generowania własnych podpisów w węźle, PHP, Java, C i C#, zobacz [uwierzytelniania usługi Service Bus za pomocą sygnatur dostępu współdzielonego][Shared Access Signatures].

### <a name="is-it-possible-to-whitelist-relay-endpoints"></a>Czy jest możliwe punkty końcowe przekazywania listy dozwolonych adresów IP
Tak. Klient przekaźnika wysyła połączenia z usługą przekazywania Azure przy użyciu w pełni kwalifikowanych nazw domen. Klientów można dodać wpis dla `*.servicebus.windows.net` na zaporach, które obsługują listę dozwolonych podobnej DNS.

## <a name="next-steps"></a>Kolejne kroki
* [Tworzenie przestrzeni nazw](relay-create-namespace-portal.md)
* [Wprowadzenie do programu .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Wprowadzenie do programu Node](relay-hybrid-connections-node-get-started.md)

[Pricing overview]: https://azure.microsoft.com/pricing/details/service-bus/
[Relay exceptions]: relay-exceptions.md
[Shared Access Signatures]: ../service-bus-messaging/service-bus-sas.md
