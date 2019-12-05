---
title: Wskazówki dotyczące ograniczania usługi Azure Key Vault
description: Ograniczania usługi Key Vault ogranicza liczbę równoczesnych wywołań, aby zapobiec nadmiernemu zużyciu zasobów.
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: 28e79dffb206e8a62410bf3b4e0e239879b51224
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806681"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania usługi Azure Key Vault

Ograniczenie to proces zainicjowanych przez, który ogranicza liczbę równoczesnych wywołań do usługi Azure zapobiega nadmiernemu zużyciu zasobów. Usługa Azure Key Vault (AKV) została zaprojektowana do obsługi dużej liczby żądań. W przypadku zbyt dużej liczby żądań ograniczanie żądań klienta pomaga utrzymać optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania różnią się zależnie od scenariusza. Na przykład jeśli przeprowadzasz dużej liczby zapisów możliwości ograniczania jest wyższa niż jeśli są wykonywane tylko operacje odczytu.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak usługa Key Vault obsługuje zmuszenia?

Limity usługi w Key Vault uniemożliwiają niewłaściwe użycie zasobów i zapewniają jakość usługi dla wszystkich klientów Key Vault. W przypadku przekroczenia progu usługi Key Vault ogranicza wszelkie dalsze żądania od tego klienta przez pewien czas, zwraca kod stanu HTTP 429 (zbyt wiele żądań) i żądanie kończy się niepowodzeniem. Żądania zakończone niepowodzeniem, które zwracają liczbę 429 do limitów dławienia śledzonych przez Key Vault. 

Key Vault pierwotnie zaprojektowano, aby służyć do przechowywania i pobierania wpisów tajnych w czasie wdrażania.  Świat został rozwijający, a Key Vault jest używany w czasie wykonywania w celu przechowywania i pobierania wpisów tajnych, a często aplikacje i usługi chcą używać Key Vault, takich jak baza danych.  Bieżące limity nie obsługują stawek o dużej przepływności.

Key Vault został pierwotnie utworzony z limitami określonymi w [limitach usługi Azure Key Vault](key-vault-service-limits.md).  Aby zmaksymalizować Key Vault dzięki stawkom za użycie usługi Put, poniżej przedstawiono kilka zalecanych wytycznych/najlepszych rozwiązań dotyczących maksymalizowania przepływności:
1. Upewnij się, że nastąpiło ograniczenie wydajności.  Klient musi przestrzegać zasad wycofywania wykładniczego dla 429 i upewnić się, że wykonujesz ponowną próbę zgodnie z poniższymi wskazówkami.
1. Podziel ruch Key Vault między wiele magazynów a różnymi regionami.   Użyj oddzielnego magazynu dla każdej domeny zabezpieczeń/dostępności.   Jeśli masz pięć aplikacji, z których każda znajduje się w dwóch regionach, zalecamy 10 magazynów zawierających wpisy tajne unikatowe dla aplikacji i regionu.  Limit całej subskrypcji dla wszystkich typów transakcji jest pięciu razy większy niż pojedynczy limit magazynu kluczy. Na przykład usługi HSM — inne transakcje na subskrypcję są ograniczone do 5 000 transakcji w ciągu 10 sekund na subskrypcję. Rozważ buforowanie wpisu tajnego w ramach usługi lub aplikacji, aby zmniejszyć RPS pliku bezpośrednio do magazynu kluczy i/lub obsłużyć ruch na podstawie serii.  Możesz również podzielić ruch między różnymi regionami, aby zminimalizować opóźnienia i korzystać z innej subskrypcji/magazynu.  Nie wysyłaj więcej niż limitu subskrypcji do usługi Key Vault w jednym regionie platformy Azure.
1. Buforowanie kluczy tajnych pobieranych z Azure Key Vault w pamięci i wielokrotnego użytku z pamięci, gdy jest to możliwe.  Odczytaj Azure Key Vault tylko wtedy, gdy buforowana kopia przestanie działać (na przykład, ponieważ została obrócona na źródło). 
1. Key Vault jest zaprojektowana dla własnych wpisów tajnych usług.   Jeśli przechowujesz wpisy tajne klientów (szczególnie w przypadku scenariuszy magazynu kluczy o wysokiej przepływności), Rozważ umieszczenie kluczy w bazie danych lub koncie magazynu przy użyciu szyfrowania i przechowywanie tylko klucza głównego w Azure Key Vault.
1. Szyfrowanie, zawijanie i weryfikowanie operacji w kluczu publicznym może być wykonywane bez dostępu do Key Vault, co zmniejsza ryzyko ograniczania przepustowości, ale również zwiększa niezawodność (o ile materiał klucza publicznego jest prawidłowo buforowany).
1. Jeśli używasz Key Vault do przechowywania poświadczeń dla usługi, sprawdź, czy usługa obsługuje uwierzytelnianie w usłudze AAD do bezpośredniego uwierzytelniania. Zmniejsza to obciążenie Key Vault, zwiększa niezawodność i upraszcza kod, ponieważ Key Vault może teraz korzystać z tokenu usługi AAD.  Wiele usług zostało przeniesionych do korzystania z uwierzytelniania usługi AAD.  Zapoznaj się z bieżącą listą [usług, która obsługuje zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources).
1. Rozważ rozłożenie obciążenia/wdrożenia w dłuższym czasie, aby zachować w ramach bieżących limitów RPS pliku.
1. Jeśli aplikacja składa się z wielu węzłów, które muszą odczytywać te same tajne dane, należy rozważyć użycie wzorca wentylatorów, gdzie jedna jednostka odczytuje wpis tajny z Key Vault, a następnie wymusić między wszystkimi węzłami.   Buforuj pobrane wpisy tajne tylko w pamięci.
Jeśli okaże się, że powyższy kod nadal nie spełnia Twoich potrzeb, Wypełnij poniższą tabelę i skontaktuj się z nami, aby określić, jakie dodatkowe możliwości można dodać (przykład poniżej w celach ilustracyjnych).

| Nazwa magazynu | Region magazynu | Typ obiektu (klucz tajny, klucz lub certyfikat) | Operacje: * | Typ klucza | Długość lub krzywa klucza | Klucz HSM?| Wymagany RPS pliku stanu | Wymagany RPS pliku szczytu |
|--|--|--|--|--|--|--|--|--|
| https://mykeyvault.vault.azure.net/ | | Klucz | Zaloguj | EC | P-256 | Nie | 200 | 1000 |

Aby uzyskać pełną listę możliwych wartości, zapoznaj się z tematem [Azure Key Vault operations](/rest/api/keyvault/key-operations)\*.

W przypadku zatwierdzenia dodatkowej pojemności należy zwrócić uwagę na następujące kwestie w wyniku wzrostu pojemności:
1. Zmiany modelu spójności danych. Gdy magazyn jest dozwolony na liście z dodatkową przepływność, usługa Key Vault gwarancja spójności danych usług (niezbędna do spełnienia wyższego RPS pliku woluminu, ponieważ nie można zatrzymać podstawowej usługi magazynu platformy Azure).  W Nutshell:
  1. **Bez zezwolenia na listę**: usługa Key Vault będzie odzwierciedlała wyniki operacji zapisu (np. SecretSet, CreateKey) natychmiast w kolejnych wywołaniach (np. SecretGet, znak.
  1. **Z opcją Zezwalaj na listę**: usługa Key Vault będzie odzwierciedlała wyniki operacji zapisu (np. SecretSet, CreateKey) w ciągu 60 sekund w kolejnych wywołaniach (np. SecretGet, znak.
1. Kod klienta musi przestrzegać zasad wycofywania dla 429 ponownych prób. Kod klienta wywołujący usługę Key Vault nie może natychmiast ponowić próby Key Vault żądań, gdy odbierze kod odpowiedzi 429.  Wskazówki dotyczące ograniczania przepustowości Azure Key Vault opublikowane w tym miejscu zalecają zastosowanie wykładniczej wycofywania podczas otrzymywania kodu odpowiedzi HTTP 429.

Jeśli masz biznesowe przypadku wyższych limitów ograniczania przepływności, skontaktuj się z nami.

## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczyć aplikację w odpowiedzi na limity usługi

Poniżej przedstawiono **najlepsze rozwiązania** , które należy zaimplementować w przypadku dławienia usługi:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Należy unikać bezpośredniego ponownych prób. 
    - Wszystkie żądania są naliczane względem limitów użycia.

Podczas implementowania obsługi błędów aplikacji, należy użyć kod błędu HTTP 429 można wykrywać ograniczania po stronie klienta. Jeśli żądanie nie powiedzie się ponownie z kodem błędu HTTP 429, nadal występują ograniczenia usług platformy Azure. Nadal używać zalecanych klienta ograniczania metody, ponowieniem próby żądania, aż do skutku.

Poniżej przedstawiono kod, który implementuje wykładniczego wycofywania. 
```
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
    var client = new SecretClient(new Uri(https://keyVaultName.vault.azure.net"), new DefaultAzureCredential(),options);
                                 
    //Retrieve Secret
    secret = client.GetSecret(secretName);
```


Korzystanie z tego kodu w aplikacji C# klienckiej jest proste. 

### <a name="recommended-client-side-throttling-method"></a>Zalecana metoda ograniczania przepływności po stronie klienta

Na kod błędu HTTP 429 Rozpocznij ograniczanie klienta przy użyciu podejścia wykładniczego wycofywania:

1. Poczekaj 1 sekundę, ponów próbę żądania
2. Jeśli nadal ograniczona poczekaj 2 sekundy, ponów próbę wykonania żądania
3. Jeśli nadal ograniczona oczekiwania 4 sekundy, ponów próbę wykonania żądania
4. Jeśli nadal ograniczona oczekiwania 8 sekund, ponów próbę wykonania żądania
5. Jeśli nadal ograniczona oczekiwania 16 sekund, ponów próbę wykonania żądania

W tym momencie użytkownik powinien nie uzyskać kody odpowiedzi HTTP 429.

## <a name="see-also"></a>Zobacz także

Dla bardziej orientację ograniczania przepustowości na Microsoft Cloud, zobacz [wzorzec ograniczania](https://docs.microsoft.com/azure/architecture/patterns/throttling).

