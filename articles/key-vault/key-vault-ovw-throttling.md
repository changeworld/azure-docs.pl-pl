---
title: Wskazówki dotyczące ograniczania usługi Azure Key Vault
description: Ograniczania usługi Key Vault ogranicza liczbę równoczesnych wywołań, aby zapobiec nadmiernemu zużyciu zasobów.
services: key-vault
documentationcenter: ''
author: msmbaldwin
manager: barbkess
tags: ''
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: mbaldwin
ms.openlocfilehash: 0f8aafce4c4feeed742504db84664e4dfd472ca6
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59787553"
---
# <a name="azure-key-vault-throttling-guidance"></a>Wskazówki dotyczące ograniczania usługi Azure Key Vault

Ograniczenie to proces zainicjowanych przez, który ogranicza liczbę równoczesnych wywołań do usługi Azure zapobiega nadmiernemu zużyciu zasobów. Usługa Azure Key Vault (AKV) została zaprojektowana do obsługi dużej liczby żądań. W przypadku zbyt dużej liczby żądań ograniczanie żądań klienta pomaga utrzymać optymalną wydajność i niezawodność usługi AKV.

Limity ograniczania różnią się zależnie od scenariusza. Na przykład jeśli przeprowadzasz dużej liczby zapisów możliwości ograniczania jest wyższa niż jeśli są wykonywane tylko operacje odczytu.

## <a name="how-does-key-vault-handle-its-limits"></a>Jak usługa Key Vault obsługuje zmuszenia?

Limity usługi w usłudze Key Vault, istnieją zapobiegania niewłaściwemu użyciu zasobów i zapewnienia dobrej jakości usługi dla wszystkich klientów usługi Key Vault. Po przekroczeniu progu usługi Key Vault ogranicza wszystkie dalsze żądania z tego klienta w okresie czasu. W takiej sytuacji usługa Key Vault zwraca kod stanu HTTP 429 (zbyt wiele żądań), i Niepowodzenie żądania. Ponadto żądań zakończonych niepowodzeniem, które zwracają 429 liczba kierunku limitów ograniczania przepływności śledzone przez usługę Key Vault. 

Jeśli masz biznesowe przypadku wyższych limitów ograniczania przepływności, skontaktuj się z nami.


## <a name="how-to-throttle-your-app-in-response-to-service-limits"></a>Jak ograniczyć aplikację w odpowiedzi na limity usługi

Poniżej przedstawiono **najlepsze praktyki** powinny implementować, gdy jest ograniczany usługi:
- Zmniejsz liczbę operacji na żądanie.
- Zmniejsz częstotliwość żądań.
- Należy unikać bezpośredniego ponownych prób. 
    - Wszystkie żądania są naliczane względem limitów użycia.

Podczas implementowania obsługi błędów aplikacji, należy użyć kod błędu HTTP 429 można wykrywać ograniczania po stronie klienta. Jeśli żądanie nie powiedzie się ponownie z kodem błędu HTTP 429, nadal występują ograniczenia usług platformy Azure. Nadal używać zalecanych klienta ograniczania metody, ponowieniem próby żądania, aż do skutku.

Poniżej przedstawiono kod, który implementuje wykładniczego wycofywania. 
```
    public sealed class RetryWithExponentialBackoff
    {
        private readonly int maxRetries, delayMilliseconds, maxDelayMilliseconds;

        public RetryWithExponentialBackoff(int maxRetries = 50,
            int delayMilliseconds = 200,
            int maxDelayMilliseconds = 2000)
        {
            this.maxRetries = maxRetries;
            this.delayMilliseconds = delayMilliseconds;
            this.maxDelayMilliseconds = maxDelayMilliseconds;
        }

        public async Task RunAsync(Func<Task> func)
        {
            ExponentialBackoff backoff = new ExponentialBackoff(this.maxRetries,
                this.delayMilliseconds,
                this.maxDelayMilliseconds);
            retry:
            try
            {
                await func();
            }
            catch (Exception ex) when (ex is TimeoutException ||
                ex is System.Net.Http.HttpRequestException)
            {
                Debug.WriteLine("Exception raised is: " +
                    ex.GetType().ToString() +
                    " –Message: " + ex.Message +
                    " -- Inner Message: " +
                    ex.InnerException.Message);
                await backoff.Delay();
                goto retry;
            }
        }
    }

    public struct ExponentialBackoff
    {
        private readonly int m_maxRetries, m_delayMilliseconds, m_maxDelayMilliseconds;
        private int m_retries, m_pow;

        public ExponentialBackoff(int maxRetries, int delayMilliseconds,
            int maxDelayMilliseconds)
        {
            m_maxRetries = maxRetries;
            m_delayMilliseconds = delayMilliseconds;
            m_maxDelayMilliseconds = maxDelayMilliseconds;
            m_retries = 0;
            m_pow = 1;
        }

        public Task Delay()
        {
            if (m_retries == m_maxRetries)
            {
                throw new TimeoutException("Max retry attempts exceeded.");
            }
            ++m_retries;
            if (m_retries < 31)
            {
                m_pow = m_pow << 1; // m_pow = Pow(2, m_retries - 1)
            }
            int delay = Math.Min(m_delayMilliseconds * (m_pow - 1) / 2,
                m_maxDelayMilliseconds);
            return Task.Delay(delay);
        }
    }
```


Przy użyciu tego kodu w kliencie C\# aplikacji jest bardzo proste. W poniższym przykładzie przedstawiono sposób używania klasy HttpClient.

```csharp
public async Task<Cart> GetCartItems(int page)
{
    _apiClient = new HttpClient();
    //
    // Using HttpClient with Retry and Exponential Backoff
    //
    var retry = new RetryWithExponentialBackoff();
    await retry.RunAsync(async () =>
    {
        // work with HttpClient call
        dataString = await _apiClient.GetStringAsync(catalogUrl);
    });
    return JsonConvert.DeserializeObject<Cart>(dataString);
}
```

Należy pamiętać, że ten kod nadaje się tylko jako weryfikacji koncepcji. 

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

