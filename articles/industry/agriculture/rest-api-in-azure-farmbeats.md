---
title: Dokumentacja
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 717a09d8377a7b95fe24300cc65222f307e7419f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437520"
---
# <a name="references"></a>Dokumentacja

W tym artykule opisano interfejsy API azure farmbeats.

## <a name="rest-api"></a>Interfejs API REST

Interfejsy API Azure FarmBeats zapewniają firmom rolniczym standardowy interfejs RESTful z odpowiedziami opartymi na JSON, które ułatwiają korzystanie z funkcji Azure FarmBeats, takich jak:

- Interfejsy API, aby uzyskać czujnik, kamerę, drona, pogodę, satelitę i wyselekcjonowane dane naziemne.
- Normalizacja i kontekstowanie danych we wspólnych dostawcach danych.
- Schematyzowane możliwości dostępu i zapytań na wszystkich pogoń za pomocą danych.
- Automatyczne generowanie metadanych, które mogą być wyszukiwane, na podstawie funkcji agronomicznych.
- Automatycznie generowane agregacje sekwencji czasu dla szybkiego budowania modelu.
- Zintegrowany aparat usługi Azure Data Factory do łatwego tworzenia niestandardowych potoków przetwarzania danych.

## <a name="application-development"></a>Opracowywanie aplikacji

Interfejsy API FarmBeats zawierają dokumentację techniczną Swagger. Aby uzyskać informacje na temat wszystkich interfejsów API i odpowiadających im żądań lub odpowiedzi, zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

W poniższej tabeli podsumowano wszystkie obiekty i zasoby w obszarze Dane FarmBeats Datahub:

| Obiekty i zasoby | Opis
--- | ---|
Gospodarstwa | Farm odpowiada fizycznej lokalizacji zainteresowania w systemie FarmBeats. Każda farma ma nazwę farmy i unikatowy identyfikator gospodarstwa. |
Urządzenie  | Urządzenie odpowiada fizycznemu urządzeniu obecnemu w gospodarstwie. Każde urządzenie ma unikatowy identyfikator urządzenia. Urządzenie jest zazwyczaj aprowizacji do farmy z identyfikatorem farmy.
DeviceModel  | DeviceModel odpowiada metadanym urządzenia, takie jak producent i typ urządzenia, które jest bramą lub węzłem.
Czujnik  | Czujnik odpowiada czujnikowi fizycznemu, który rejestruje wartości. Czujnik jest zazwyczaj podłączony do urządzenia z identyfikatorem urządzenia.
CzujnikModel  | SensorModel odpowiada metadane czujnika, takie jak producent, typ czujnika, który jest analogowy lub cyfrowy, oraz pomiar czujnika, taki jak temperatura otoczenia i ciśnienie.
Telemetria  | Telemetria umożliwia odczytywanie komunikatów telemetrycznych dla określonego czujnika i zakresu czasu.
Zadanie  | Zadanie odpowiada wszelkiego przepływu pracy działań, które są wykonywane w systemie FarmBeats, aby uzyskać żądane dane wyjściowe. Każde zadanie jest skojarzone z identyfikatorem zadania i typem zadania.
Typ zadania  | JobType odpowiada różnych typów zadań obsługiwanych przez system. Uwzględniane są typy zadań zdefiniowane przez system i zdefiniowane przez użytkownika.
Rozszerzony typ  | ExtendedType odpowiada liście typów zdefiniowanych przez system i użytkownika w systemie. ExtendedType pomaga skonfigurować nowy typ pliku czujnika, sceny lub sceny w systemie FarmBeats.
Partner  | Partner odpowiada partnerowi integracji czujników i obrazów dla FarmBeats.
Sceny  | Scena odpowiada wszelkim wygenerowanym wynikom w kontekście farmy. Każda scena ma skojarzony identyfikator sceny, źródło sceny, typ sceny i identyfikator farmy. Z każdym identyfikatorem sceny może być skojarzonych wiele plików scen.
Plik sceny |SceneFile odpowiada wszystkim plikom generowanym dla pojedynczej sceny. Identyfikator pojedynczej sceny może mieć skojarzone z nią wiele identyfikatorów SceneFile.
Reguła  |Reguła odpowiada warunkowi dla danych związanych z gospodarstwem, aby wyzwolić alert. Każda reguła jest w kontekście danych farmy.
Alerty  | Alert odpowiada powiadomieniu, które jest generowane po spełnieniu warunku reguły. Każdy alert jest w kontekście reguły.
Definicja roli  | Definicja funkcji Określanie funkcji definiuje dozwolone i niedozwolone akcje dla roli.
Znak ról  |RoleAssignment odpowiada przypisaniu roli użytkownikowi lub podmiotowi usługi.

### <a name="data-format"></a>Format danych

JSON jest wspólnym formatem danych niezależnym od języka, który zapewnia prostą reprezentację tekstową dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz [witrynę JSON](https://www.json.org/)w sieci Web .

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Żądania HTTP do interfejsu API REST są chronione za pomocą usługi Azure Active Directory (Azure AD).
Aby wykonać uwierzytelnione żądanie do interfejsów API REST, kod klienta wymaga uwierzytelniania z prawidłowymi poświadczeniami przed wywołaniem interfejsu API. Uwierzytelnianie jest koordynowane między różnymi podmiotami przez usługę Azure AD. Zapewnia klientowi token dostępu jako dowód uwierzytelniania. Token jest następnie wysyłany w nagłówku autoryzacji HTTP żądań interfejsu API REST. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz [Usługa Azure Active Directory](https://portal.azure.com) dla deweloperów.

Token dostępu musi być wysyłany w kolejnych żądaniach interfejsu API, w sekcji nagłówka, jak:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>Nagłówki żądań HTTP

Oto najczęstsze nagłówki żądań, które należy określić podczas wywoływania interfejsu API do usługi Azure FarmBeats Datahub.


**Nagłówka** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Typ zawartości:<format>aplikacja/ ). W przypadku interfejsów API usługi Azure FarmBeats Datahub format jest JSON. Typ zawartości: aplikacja/json
Autoryzacja  | Określa token dostępu wymagany do wywołania interfejsu API. Autoryzacja:> tokenu dostępu na okaziciela <
Zaakceptuj | Format odpowiedzi. W przypadku interfejsów API usługi Azure FarmBeats Datahub format jest JSON. Zaakceptuj: wniosek/json

### <a name="api-requests"></a>Żądania interfejsu API

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST, PUT lub DELETE), adres URL do usługi interfejsu API, identyfikator URI z zasobem, aby wykonać kwerendę, przesłać dane, zaktualizować lub usunąć, a następnie dodać jeden lub więcej nagłówków żądań HTTP.

Adres URL usługi interfejsu API to adres URL witryny\<Datahub, na przykład https:// nazwa witryny yourdatahub>.azurewebsites.net.

Opcjonalnie można uwzględnić parametry kwerendy w wywołaniach GET, aby filtrować, ograniczać rozmiar i sortować dane w odpowiedziach.

Następujące przykładowe żądanie jest używane do uzyskania listy urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Następujące przykładowe żądanie tworzy urządzenie. To żądanie ma wejście JSON z treścią żądania.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Parametry zapytania

W przypadku wywołań REST GET można filtrować, ograniczać rozmiar i sortować dane w odpowiedzi interfejsu API, dołączając co najmniej jeden parametr zapytania w identyfikatorze URI żądania. Parametry kwerendy można znaleźć w dokumentacji interfejsu API i poszczególnych wywołań GET.
Na przykład podczas kwerendy listy urządzeń (GET call on /Device), można określić następujące parametry kwerendy:

![Lista urządzeń](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Obsługa błędów

Interfejsy API usługi Azure FarmBeats Datahub zwracają standardowe błędy HTTP. Najczęstsze kody błędów są następujące:

 |Kod błędu             | Opis |
 |---                    | --- |
 |200                    | Powodzenie |
 |201                    | Tworzenie (post) sukces |
 |400                    | Zła prośba. Wystąpił błąd w żądaniu. |
 |401                    | Nieautoryzowanych. Osoba wywołująca interfejsu API nie jest autoryzowana do uzyskiwania dostępu do zasobu. |
 |404                    | Nie znaleziono zasobu |
 |5XX (polski)                    | Wewnętrzny błąd serwera. Kody błędów zaczynające się od 5XX oznaczają, że na serwerze występuje pewien błąd. Aby uzyskać więcej informacji, zapoznaj się z dziennikami serwera i poniższą sekcją. |


Oprócz standardowych błędów HTTP interfejsy API usługi Azure FarmBeats Datahub zwracają również błędy wewnętrzne w następującym formacie:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

W tym przykładzie podczas tworzenia farmy pole obowiązkowe "Nazwa" nie zostało określone w ładunku wejściowym. Wynikowy komunikat o błędzie będzie:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Dodawanie użytkowników lub rejestracji aplikacji do usługi Azure Active Directory

Interfejsy API usługi Azure FarmBeats są dostępne dla użytkownika lub rejestracji aplikacji w usłudze Azure Active Directory. Aby utworzyć rejestrację aplikacji w usłudze Azure Active Directory, wykonaj następujące kroki:

1. Przejdź do [witryny Azure portal](https://portal.azure.com)i wybierz pozycję**Rejestracje** > aplikacji **usługi Azure Active Directory** > **Nowa rejestracja**. Alternatywnie można użyć istniejącego konta.
2. W przypadku nowego konta wykonaj następujące czynności:

    - Wprowadź nazwę.
    - Wybierz **pozycję Konta w tym katalogu organizacyjnym (tylko pojedyncza dzierżawa).**
    - Użyj wartości domyślnych w pozostałych polach.
    - Wybierz pozycję **Zarejestruj**.

3. W nowym i istniejącym **okienku przegląd rejestracji** aplikacji wykonaj następujące czynności:

    - Przechwyć **identyfikator klienta** i **identyfikator dzierżawy**.
    - Przejdź do **certyfikatów i wpisów tajnych,** aby wygenerować nowy klucz tajny klienta i przechwycić **klucz tajny klienta.**
    - Wróć do **pozycji Przegląd**i wybierz łącze obok pozycji **Zarządzaj aplikacją w katalogu lokalnym**.
    - Przejdź do **właściwości,** aby przechwycić **identyfikator obiektu**.

4. Przejdź do witryny Datahub Swagger<yourdatahub>(https:// .azurewebsites.net/swagger/index.html) i wykonaj następujące czynności:
    - Przejdź do **interfejsu API przypisania roli**.
    - Wykonaj post, aby utworzyć obiekt **roleassignment** dla właśnie utworzonego **identyfikatora obiektu.**

  > [!NOTE]
  > Aby uzyskać więcej informacji na temat dodawania użytkowników i rejestracji usługi Active Directory, zobacz [Usługa Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

Po zakończeniu poprzednich kroków rejestracji aplikacji (klient) można wywołać interfejsów API Azure FarmBeats przy użyciu tokenu dostępu za pośrednictwem uwierzytelniania na okaziciela.

Użyj tokenu dostępu, aby wysłać go w kolejnych żądaniach interfejsu API w sekcji nagłówka w następujący sposób:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
