---
title: Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI | Dokumentacja firmy Microsoft
description: Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-workspace-collections
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 5fbe08bb9ebc355074a790f7732c9de10eaad859
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707108"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Uwierzytelnianie i autoryzowanie za pomocą kolekcji obszarów roboczych usługi Power BI

Power użycia kolekcji obszarów roboczych usługi Power BI **klucze** i **tokenów aplikacji** uwierzytelniania i autoryzacji, zamiast jawnego uwierzytelniania użytkowników końcowych. W tym modelu aplikacja zarządza uwierzytelniania i autoryzacji dla użytkowników końcowych. Gdy jest to konieczne, aplikacja tworzy i wysyła tokenów aplikacji, które informują naszej usługi w celu renderowania żądanego raportu. Ten projekt nie wymaga aplikację do używania usługi Azure Active Directory do uwierzytelniania i autoryzacji, użytkowników, chociaż można nadal.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dwa sposoby uwierzytelniania

**Klucz** — można użyć kluczy dla wszystkich wywołań interfejsu API REST kolekcji usługi Power BI obszaru roboczego. Klucze można znaleźć w **portalu Microsoft Azure** , wybierając **wszystkie ustawienia** i następnie **klucze dostępu**. Klucz jest zawsze traktowany tak, jakby była hasła. Te klucze mają uprawnienia do wprowadzania żadnych wywołanie w kolekcji obszarów roboczych danego interfejsu API REST.

Aby użyć klucza na wywołanie interfejsu REST, należy dodać następujący nagłówek autoryzacji:

    Authorization: AppKey {your key}

**Tokenu aplikacji** -tokenów aplikacji są używane dla wszystkich żądań osadzania. Służą one do uruchomienia po stronie klienta. Token jest ograniczone do pojedynczego raportu i jego najlepszym rozwiązaniem jest ustawienie czasu wygaśnięcia.

Tokenów aplikacji są JWT (JSON Web Token), który jest podpisany za pomocą jednej z Twoich kluczy.

Twój token aplikacji może zawierać następujących oświadczeń:

| Claim | Opis |    
| --- | --- |
| **VER** |Wersja tokenu aplikacji. 0.2.0 stanowi bieżącą wersję. |
| **AUD** |Adresat tokenu. Aby użyć kolekcji obszarów roboczych usługi Power BI: *https:\//analysis.windows.net/powerbi/api*. |
| **iss** |Ciąg wskazujący aplikacji, który wystawił token. |
| **type** |Typ tokenu aplikacji, która jest tworzona. Bieżący jest jedynym obsługiwanym typem **osadzić**. |
| **wcn** |Nazwa kolekcji obszarów roboczych token zostało wystawione. |
| **bazy danych WID** |Identyfikator obszaru roboczego token zostało wystawione. |
| **Identyfikator RID** |Identyfikator raportu token zostało wystawione. |
| **Nazwa użytkownika** (opcjonalnie) |Używane z zabezpieczeń na poziomie wiersza, nazwa użytkownika jest ciąg, który może ułatwić identyfikację użytkownika podczas stosowania reguł zabezpieczeń na poziomie wiersza. |
| **role** (opcjonalnie) |Ciąg zawierający role do wybrania podczas stosowania reguł zabezpieczeń na poziomie wiersza. W przypadku przekazywania więcej niż jednej roli, należy je przekazywać jako tablicę stingu. |
| **punkt połączenia usługi** (opcjonalnie) |Ciąg zawierający zakresy uprawnień. W przypadku przekazywania więcej niż jednej roli, należy je przekazywać jako tablicę stingu. |
| **EXP** (opcjonalnie) |Wskazuje czas, w którym token jest ważny. Wartość powinien być przekazywany w jako systemu Unix sygnatur czasowych. |
| **NBF** (opcjonalnie) |Wskazuje czas, w której token rozpoczyna się ważny. Wartość powinien być przekazywany w jako systemu Unix sygnatur czasowych. |

Tokenu aplikacji przykładowej wygląda następująco:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Gdy dekodowana, wyglądają podobnie:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

Metody są dostępne w ramach zestawów SDK, które ułatwiają tworzenie tokenów aplikacji. Na przykład dla platformy .NET można sprawdzić [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) klasy i [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) metody.

Dla zestawu .NET SDK mogą odwoływać się do [zakresy](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Zakresy

Korzystając z tokenów osadzania, można ograniczyć użycie zasobów, które zapewniają dostęp do. Z tego powodu można wygenerować token z uprawnieniami o określonym zakresie.

Poniżej przedstawiono dostępne zakresy dla kolekcji obszarów roboczych usługi Power BI.

|Zakres|Opis|
|---|---|
|Dataset.Read|Udostępnia uprawnienie do odczytu określonego zestawu danych.|
|Dataset.Write|Zapewnia uprawnienia do zapisu do określonego zestawu danych.|
|Dataset.ReadWrite|Zapewnia uprawnienia do odczytu i zapisu do określonego zestawu danych.|
|Report.Read|Zapewnia uprawnienia do wyświetlania określonego raportu.|
|Report.ReadWrite|Udostępnia uprawnienie do wyświetlania i edytowania określonego raportu.|
|Workspace.Report.Create|Udostępnia uprawnienie do tworzenia nowego raportu w ramach określonego obszaru roboczego.|
|Workspace.Report.Copy|Udostępnia uprawnienie do Sklonowanie istniejącego raportu w ramach określonego obszaru roboczego.|

Możesz podać wiele zakresów, za pomocą spacji między zakresami, jak pokazano poniżej.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Wymagane oświadczenia - zakresami**

punkt połączenia usługi: {scopesClaim} scopesClaim może być ciąg lub tablicę ciągów, biorąc pod uwagę dozwolone uprawnienia do zasobów obszaru roboczego (raportu, zestawu danych itp.)

Token zdekodowany przy użyciu zakresy definiowane, powinien wyglądać podobnie do:

```
Header

{
    typ: "JWT",
    alg: "HS256:
}

Body

{
  "ver": "0.2.0",
  "wcn": "SupportDemo",
  "wid": "ca675b19-6c3c-4003-8808-1c7ddc6bd809",
  "rid": "96241f0f-abae-4ea9-a065-93b428eddb17",
  "scp": "Report.Read",
  "iss": "PowerBISDK",
  "aud": "https://analysis.windows.net/powerbi/api",
  "exp": 1360047056,
  "nbf": 1360043456
}

```

### <a name="operations-and-scopes"></a>Operacje i zakresy

|Operacja|Zasób docelowy|Uprawnienia tokenu|
|---|---|---|
|Tworzenie nowego raportu na podstawie zestawu danych na (w pamięci).|Zestaw danych|Dataset.Read|
|Tworzenie nowego raportu, w oparciu o zestaw danych (w pamięci) i Zapisz raport.|Zestaw danych|* Dataset.Read<br>* Workspace.Report.Create|
|Wyświetlanie i Poznaj/edytowanie (w pamięci) istniejącego raportu. Report.Read oznacza Dataset.Read. Report.Read nie zezwala na zapisywanie zmian.|Raport|Report.Read|
|Edytuj i Zapisz istniejący raport.|Raport|Report.ReadWrite|
|Zapisz kopię raportu (Zapisz jako).|Raport|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Poniżej przedstawiono, jak działa przepływ
1. Kopiowanie kluczy interfejsu API do aplikacji. Możesz pobrać klucze w ramach **witryny Azure portal**.
   
    ![Gdzie można znaleźć klucze interfejsu API w witrynie Azure portal](media/get-started-sample/azure-portal.png)
1. Token potwierdzenia oświadczenia i ma czas wygaśnięcia.
   
    ![Przepływie tokenu aplikacji — token potwierdzenia oświadczeń](media/get-started-sample/token-2.png)
1. Token jest podpisany przy użyciu kluczy dostępu interfejsu API.
   
    ![Pobiera podpisany token przepływu aplikacji - token](media/get-started-sample/token-3.png)
1. Żądania użytkowników, aby wyświetlić raport.
   
    ![Przepływie tokenu aplikacji — użytkownik zażąda Aby wyświetlić raport](media/get-started-sample/token-4.png)
1. Token została zweryfikowana za pomocą kluczy dostępu interfejsu API.
   
   ![Token przepływu aplikacji — tokenu jest weryfikowana.](media/get-started-sample/token-5.png)
1. Kolekcje obszarów roboczych usługi Power BI wysyła raport do użytkownika.
   
   ![Przepływie tokenu aplikacji — usługa wysłać raport do użytkownika](media/get-started-sample/token-6.png)

Po **kolekcji obszarów roboczych usługi Power BI** wysyła raport do użytkownika, użytkownik może wyświetlić raport w aplikacji niestandardowej. Na przykład, jeśli został zaimportowany [analizowanie pliku danych sprzedaży PBIX próbka](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), przykładową aplikację sieci web powinien wyglądać tak:

![Przykładowy raport osadzone w aplikacji](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Zobacz też

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Rozpoczęcie pracy z przykładem kolekcji obszarów roboczych pakietu Microsoft Power BI](get-started-sample.md)  
[Typowe scenariusze kolekcji obszarów roboczych pakietu Microsoft Power BI](scenarios.md)  
[Rozpoczynanie pracy z kolekcji obszarów roboczych pakietu Microsoft Power BI](get-started.md)  
[Repozytorium Git języka CSharp usługi Power BI](https://github.com/Microsoft/PowerBI-CSharp)

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
