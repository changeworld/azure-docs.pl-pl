---
title: Nieprzetworzone dzienniki HTTP usługi Azure CDN
description: W tym artykule opisano nieprzetworzone dzienniki http usługi Azure CDN.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: c6e8570746ae3dd0051dbec084c89d90580d28b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371634"
---
# <a name="azure-cdn-http-raw-logs"></a>Nieprzetworzone dzienniki HTTP usługi Azure CDN
Nieprzetworzone dzienniki zawierają zaawansowane informacje o operacjach i błędach, które są ważne dla inspekcji i rozwiązywania problemów. Nieprzetworzone dzienniki różnią się od dzienników aktywności. Dzienniki aktywności zapewniają wgląd w operacje wykonywane na zasoby platformy Azure. Nieprzetworzone dzienniki zapewniają rekord operacji zasobu.

> [!IMPORTANT]
> Funkcja nieprzetworzonych dzienników HTTP jest dostępna dla usługi Azure CDN firmy Microsoft.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure portal w [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Konfigurowanie

Aby skonfigurować nieprzetworzone dzienniki usługi Azure CDN z profilu firmy Microsoft: 

1. Z menu portalu Platformy Azure wybierz **polecenie Wszystkie zasoby,** >> **\<które profil CDN>**.

2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyki**.

3. Wybierz **+ Dodaj ustawienie diagnostyczne**.

    ![Ustawienie diagnostyczne cdn](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Nieprzetworzone dzienniki są dostępne tylko na poziomie profilu, podczas gdy zagregowane dzienniki kodu stanu http są dostępne na poziomie punktu końcowego.

4. W obszarze **Ustawienia diagnostyczne**wprowadź nazwę ustawienia diagnostycznego w obszarze **Nazwa ustawień diagnostycznych**.

5. Wybierz **dziennik** i ustaw retencję w dniach.

6. Wybierz **szczegóły miejsca docelowego**. Opcje docelowe to:
    * **Wysyłanie do usługi Log Analytics**
        * Wybierz obszar **roboczy Subskrypcja** i **analiza dzienników**.
    * **Archiwizuj na koncie magazynu**
        * Wybierz **subskrypcję** i **konto magazynu**.
    * **Przesyłanie strumieniowe do centrum zdarzeń**
        * Wybierz **nazwę Subskrypcja,** **Obszar nazw Centrum zdarzeń,** **Nazwę Centrum zdarzeń (opcjonalnie)** i **Nazwę zasad Centrum zdarzeń**.

    ![Ustawienie diagnostyczne cdn](./media/cdn-raw-logs/raw-logs-02.png)

7. Wybierz **pozycję Zapisz**.

## <a name="raw-logs-properties"></a>Właściwości nieprzetworzonych dzienników

Usługa Azure CDN z usługi Microsoft service zapewnia obecnie dzienniki raw. Nieprzetworzone dzienniki zapewniają indywidualne żądania interfejsu API z każdego wpisu o następującym schemacie: 

| Właściwość              | Opis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ŚledzenieReferencja     | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez drzwiami frontowymi, również wysyłane jako nagłówek X-Azure-Ref do klienta. Wymagane do wyszukiwania szczegółów w dziennikach dostępu dla określonego żądania. |
| HttpMethod (httpmethod)            | Metoda HTTP używana przez żądanie.                                                                                                                                                                     |
| Wersja httpwersacjowa           | Typ żądania lub połączenia.                                                                                                                                                                   |
| Requesturi            | Identyfikator URI odebranego żądania.                                                                                                                                                                         |
| RequestBytes (Żądaj bajtów)          | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania.                                                                                                   |
| Odpowiedzi Bajty         | Bajty wysyłane przez serwer wewnętrznej bazy danych jako odpowiedź.                                                                                                                                                    |
| Useragent             | Typ przeglądarki, który był używany przez klienta.                                                                                                                                                               |
| ClientIp              | Adres IP klienta, który złożył żądanie.                                                                                                                                                  |
| CzasTaken             | Czas trwania akcji w milisekundach.                                                                                                                                            |
| SecurityProtocol (właśc.      | Wersja protokołu TLS/SSL używana przez żądanie lub null, jeśli nie ma szyfrowania.                                                                                                                           |
| Endpoint              | Host punktu końcowego sieci CDN skonfigurował w nadrzędnym profilu sieci CDN.                                                                                                                                   |
| Nazwa hosta wewnętrznej bazy danych     | Nazwa hosta wewnętrznej bazy danych lub źródła, w którym wysyłane są żądania.                                                                                                                                |
| Wysłane do tarczy początkowej | Jeśli to prawda, oznacza to, że żądanie zostało odebrane z pamięci podręcznej tarczy pochodzenia zamiast pop krawędzi. Tarcza pochodzenia to nadrzędna pamięć podręczna służąca do poprawy współczynnika trafień w pamięci podręcznej.                                       |
| Kod HttpStatus        | Kod stanu HTTP zwrócony z serwera proxy.                                                                                                                                                        |
| Szczegóły httpStatusDetails     | Wynikowy stan na żądanie. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołanie status.                                                                                              |
| Pop                   | Pop krawędzi, który odpowiedział na żądanie użytkownika. Skróty POP są kodami lotnisk w ich odpowiednich metrach.                                                                                   |
| Stan pamięci podręcznej          | Oznacza, jeśli obiekt został zwrócony z pamięci podręcznej lub pochodzi z pochodzenia.                                                                                                             |
> [!IMPORTANT]
> Funkcja http raw logs jest dostępna automatycznie dla wszystkich profili utworzonych lub zaktualizowanych po **25 lutego 2020.** W przypadku profilów sieci CDN utworzonych wcześniej należy zaktualizować punkt końcowy sieci CDN po skonfigurowaniu rejestrowania. Na przykład można przejść do filtrowania geograficznego w punktach końcowych sieci CDN i zablokować dowolny kraj niezreażysny z obciążeniem i zapisać trafienie. 

> [!NOTE]
> Dzienniki można wyświetlać w obszarze profilu usługi Log Analytics, uruchamiając kwerendę. Przykładowa kwerenda będzie wyglądać jak AzureDiagnostics | gdzie Kategoria == "AzureCdnAccessLog"

## <a name="next-steps"></a>Następne kroki
W tym artykule włączono nieprzetworzone dzienniki HTTP dla usługi Microsoft CDN.

Aby uzyskać więcej informacji na temat usługi Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Analizowanie](cdn-log-analysis.md) Wzorce użycia usługi Azure CDN.

* Dowiedz się więcej o [usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Konfigurowanie [analizy dzienników w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
