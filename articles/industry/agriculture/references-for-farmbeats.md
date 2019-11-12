---
title: Odwołania do FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: efd294910531509d736dbda274406bd7c801c124
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931199"
---
# <a name="references"></a>Dokumentacja

Poniżej znajduje się kolekcja notatek i instrukcji, które stanowią zarys interfejsów API usługi Azure FarmBeats.

## <a name="rest-api"></a>Interfejs API REST

Interfejsy API usługi Azure FarmBeats udostępniają firmę rolniczą ze standardowym interfejsem RESTful z odpowiedziami opartymi na notacji JSON, które ułatwiają korzystanie z funkcji Azure FarmBeats:

- Interfejs API umożliwiający uzyskiwanie danych z czujnika, aparatu, drona, pogody, satelity i nadzoru z terenu.
- Normalizacja/contextualization danych między typowymi dostawcami danych.
- Schematized możliwości dostępu i zapytania dotyczące wszystkich pozyskanych danych.
- Automatyczne generowanie metadanych, które mogą być badane w oparciu o funkcje agronomicznych.  
- Automatycznie generowane zagregowane sekwencje czasu do szybkiego kompilowania modeli.
- Zintegrowany aparat Azure Data Factory (ADF), aby łatwo tworzyć niestandardowe potoki przetwarzania danych.

## <a name="application-development"></a>Opracowywanie aplikacji

Interfejsy API zawierają dokumentację techniczną struktury Swagger. Zobacz [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) , aby uzyskać informacje na temat wszystkich interfejsów API i odpowiadających im żądań/odpowiedzi.

To jest podsumowanie wszystkich obiektów/zasobów w FarmBeats Data Hub:

Hodowli | Farma odpowiada fizycznej lokalizacji interesu w systemie FarmBeats. Każda Farma ma nazwę farmy i unikatowy identyfikator farmy.
--- | ---|
Urządzenie  | Urządzenie odpowiada urządzeniu fizycznemu znajdującemu się w farmie. Każde urządzenie ma unikatowy identyfikator urządzenia. Urządzenie jest zazwyczaj inicjowane w farmie z IDENTYFIKATORem farmy.
DeviceModel  | DeviceModel odnosi się do meta danych urządzenia, takiego jak producent, typ urządzenia albo brama lub węzeł.
Czujnik  | Czujnik odnosi się do czujnika fizycznego, który rejestruje wartości. Czujnik jest zwykle podłączony do urządzenia z IDENTYFIKATORem urządzenia.
SensorModel  | SensorModel odnosi się do meta danych czujnika, takiego jak producent, typ czujnika albo analogowe lub cyfrowe, pomiar czujnika, taki jak temperatura otoczenia, ciśnienie itp.
Telemetria  | Funkcja telemetrii umożliwia odczytywanie komunikatów telemetrycznych dla określonego czujnika i zakresu czasu.
Zadanie  | Zadanie odnosi się do dowolnego przepływu pracy działań, które są wykonywane w systemie FarmBeats w celu uzyskania żądanych danych wyjściowych. Każde zadanie jest skojarzone z IDENTYFIKATORem zadania i typem zadania.
Typu zadania  | Typu zadania odnosi się do różnych typów zadań obsługiwanych przez system. Obejmuje to system zdefiniowany przez użytkownika & typami zadań zdefiniowanymi przez użytkowników.
ExtendedType  | Rozszerzony ElementType odpowiada liście typów systemu & zdefiniowanych przez użytkownika w systemie. Pozwala to na skonfigurowanie nowej czujnika lub typu sceny lub Scenefile w systemie FarmBeats.
Partner  | Partner odpowiada partnerowi integracji czujników/obrazów dla FarmBeats
Miejscu  | Scena odnosi się do wszystkich wygenerowanych danych wyjściowych w kontekście farmy. Każda scena ma identyfikator sceny, źródło sceny, typ sceny i skojarzony z nią identyfikator farmy. Każdemu IDENTYFIKATORowi sceny może być skojarzonych wiele plików sceny.
SceneFile |SceneFile odnosi się do wszystkich plików, które są generowane dla pojedynczej sceny. Z pojedynczym IDENTYFIKATORem sceny może być skojarzonych wiele identyfikatorów SceneFile.
Reguła  |Reguła odnosi się do warunku dla danych związanych z farmą w celu wyzwolenia alertu. Każda reguła będzie w kontekście danych farmy.
Alert  | Alert odpowiada powiadomienia, które jest generowane po spełnieniu warunku reguły. Każdy alert będzie w kontekście reguły.
Definicji  | Definicji definiuje dozwolone i niedozwolone akcje dla roli.
RoleAssignment  |RoleAssignment odpowiada przypisaniu roli do użytkownika lub nazwy głównej usługi.

**Format danych**

JSON (JavaScript Object Notation) to typowy, niezależny od języka format danych, który zapewnia prostą reprezentację dowolnych struktur danych. Aby uzyskać więcej informacji, zobacz json.org.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Żądania HTTP do interfejsu API REST są chronione za pomocą Azure Active Directory (Azure AD).
Aby wysłać żądanie do interfejsów API REST, kod klienta wymaga uwierzytelnienia z prawidłowymi poświadczeniami, zanim będzie można wywołać interfejs API. Uwierzytelnianie jest koordynowane między różnymi aktorami przez usługę Azure AD i zapewnia klientowi token dostępu jako potwierdzenie uwierzytelniania. Token jest następnie wysyłany w nagłówku autoryzacji HTTP żądań interfejsu API REST. Aby dowiedzieć się więcej o uwierzytelnianiu usługi Azure AD, zobacz [Azure Active Directory](https://portal.azure.com) dla deweloperów.

Token dostępu musi być wysyłany w kolejnych żądaniach interfejsu API w sekcji nagłówka jako:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**Nagłówki żądań HTTP**

Poniżej znajdują się najczęstsze nagłówki żądań, które należy określić podczas wywołania interfejsu API do usługi Azure FarmBeats Data Hub:


**Nagłówek** | **Opis i przykład**
--- | ---
Content-Type  | Format żądania (Content-Type: Application/<format>) dla interfejsu API usługi Azure FarmBeats Data Hub to JSON. Content-Type: Application/JSON
Autoryzacja  | Określa token dostępu wymagany do wywołania interfejsu API. **Autoryzacja: < tokenu dostępu >**
odebrać | Format odpowiedzi. W przypadku interfejsów API usługi Azure FarmBeats Data Hub formatem jest **akceptowanie JSON: Application/JSON**

**Żądania interfejsu API**

Aby wykonać żądanie interfejsu API REST, należy połączyć metodę HTTP (GET, POST, PUT lub DELETE), adres URL usługi interfejsu API, identyfikator URI do zasobu do zapytania, przesłać dane do, zaktualizować lub usunąć, a także co najmniej jeden nagłówek żądania HTTP.

Adres URL usługi API Service to adres URL centrum danych https://\<yourdatahub-Website-Name >. azurewebsites. NET opcjonalnie, można uwzględnić parametry zapytania dla wywołań GET, ograniczyć rozmiar i sortować dane w odpowiedziach.

Poniżej przykładowego żądania jest uzyskanie listy urządzeń:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

Większość wywołań GET, POST i PUT wymaga treści żądania JSON.

Poniżej przykładowego żądania jest utworzenie urządzenia (zawiera on wejściowy kod JSON z treścią żądania).

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Parametry zapytania**

W przypadku wywołań REST **Get** można filtrować, ograniczać rozmiar i sortować dane w odpowiedzi interfejsu API przez uwzględnienie co najmniej jednego parametru zapytania w identyfikatorze URI żądania. W przypadku parametrów zapytania zapoznaj się z dokumentacją interfejsu API i Zobacz poszczególne wywołania GET.
Na przykład podczas wysyłania zapytania do listy urządzeń (GET Call on/Device) można określić następujące parametry zapytania:  

![Farmy projektów](./media/for-references/query-parameters-device-1.png)

**Obsługa błędów**

Interfejs API usługi Azure FarmBeats Data Hub zwraca standardowe błędy HTTP. Najbardziej typowe kody błędów są następujące:

 |Kod błędu             | Opis |
 |---                    | --- |
 |200                    | Powodzenie |
 |201                    | Powodzenie tworzenia (post) |
 |400                    | Nieprawidłowe żądanie. Wystąpił błąd w żądaniu |
 |401                    | Próby. Obiekt wywołujący interfejsu API nie ma autoryzacji do uzyskania dostępu do zasobu |
 |404                    | Nie znaleziono zasobu |
 |5XX                    | Wewnętrzny błąd serwera. Kody błędów zaczynające się od 5XX oznacza, że na serwerze Wystąpił błąd. Aby uzyskać więcej informacji, zapoznaj się z dziennikami serwera i poniższą sekcją. |


Oprócz standardowych błędów HTTP interfejsy API usługi Azure FarmBeats Data Hub zwracają również błędy wewnętrzne w następującym formacie:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

Przykład: podczas tworzenia farmy nie określono obowiązkowego pola "name" w ładunku wejściowym. Następujący komunikat o błędzie:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Dodawanie użytkowników lub rejestracji aplikacji do Azure Active Directory

Dostęp do interfejsów API platformy Azure FarmBeats można uzyskać za pomocą użytkownika lub rejestracji aplikacji w Azure Active Directory. Aby utworzyć rejestrację aplikacji na Azure Active Directory, wykonaj następujące czynności:  

1. Przejdź do [Azure Portal](https://portal.azure.com) **Azure Active Directory, rejestracje aplikacji**> **Nowa rejestracja**. Alternatywnie możesz użyć istniejącego konta.
2. W przypadku nowego konta upewnij się, że zostały wykonane następujące czynności:

    - Wprowadź nazwę
    - Wybierz **konta tylko w tym katalogu organizacji (pojedynczy dzierżawca)**
    - Wartości domyślne w pozostałych polach
    - Wybierz pozycję **zarejestruj**

3. Na stronie Rejestracja nowej/istniejącej aplikacji — **Omówienie**wykonaj następujące czynności:

    - Przechwyć **Identyfikator klienta** i **Identyfikator dzierżawy**.
    - Przejdź do pozycji **Certyfikaty i wpisy tajne** , aby wygenerować nowy klucz tajny klienta i przechwycić **wpis tajny klienta**.
    - Wróć do omówienia i kliknij link obok pozycji **Zarządzaj aplikacją w katalogu lokalnym**
    - Przejdź do **Właściwości** , aby przechwycić **Identyfikator obiektu**

4. Przejdź do [centrum danych struktury Swagger](https://<yourdatahub>.azurewebsites.net/swagger/index.html) i wykonaj następujące czynności:
    - Przejdź do **interfejsu API RoleAssignment**
    - Wykonaj **wpis** , aby utworzyć RoleAssignment dla właśnie utworzonego **identyfikatora obiektu** . — Wejściowy kod JSON to:

  > [!NOTE]
  > Aby uzyskać więcej informacji na temat dodawania użytkowników i rejestracji w usłudze AD, zobacz [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

Po wykonaniu powyższych kroków Rejestracja aplikacji (klienta) może wywołać interfejsy API usługi Azure FarmBeats przy użyciu tokenu dostępu za pośrednictwem uwierzytelniania okaziciela.  

Użyj tokenu dostępu, aby wysłać go w kolejnych żądaniach interfejsu API w sekcji nagłówka jako:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
