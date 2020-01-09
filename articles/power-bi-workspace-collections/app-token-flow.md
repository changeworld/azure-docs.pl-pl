---
title: Uwierzytelnianie i Autoryzowanie Power BI kolekcji obszarów roboczych
description: Uwierzytelnianie i Autoryzowanie za pomocą Power BI kolekcji obszarów roboczych.
services: power-bi-workspace-collections
author: rkarlin
ms.author: rkarlin
ms.service: power-bi-embedded
ms.topic: article
ms.workload: powerbi
ms.date: 09/20/2017
ms.openlocfilehash: 8fcd7caffb041c57090d7256361421cb49a9a5fc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75427107"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Uwierzytelnianie i Autoryzowanie za pomocą kolekcji Power BI obszarów roboczych

Power BI kolekcje obszarów roboczych używają **kluczy** i **tokenów aplikacji** do uwierzytelniania i autoryzacji, zamiast jawnego uwierzytelniania użytkowników końcowych. W tym modelu aplikacja zarządza uwierzytelnianiem i autoryzacją dla użytkowników końcowych. W razie potrzeby aplikacja tworzy i wysyła tokeny aplikacji, które informują naszą usługę, aby renderować żądany raport. Ten projekt nie wymaga, aby aplikacja używała Azure Active Directory do uwierzytelniania i autoryzacji użytkowników, chociaż nadal można.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dwa sposoby uwierzytelniania

**Klucz** — można użyć kluczy dla wszystkich wywołań interfejsu API REST kolekcji obszarów roboczych Power BI. Klucze można znaleźć w **Microsoft Azure Portal** , wybierając pozycję **wszystkie ustawienia** , a następnie pozycję **klucze dostępu**. Zawsze Traktuj swój klucz, tak jakby był to hasło. Te klucze mają uprawnienia do podejmowania wszelkich wywołań interfejsu API REST w określonej kolekcji obszarów roboczych.

Aby użyć klucza w wywołaniu REST, Dodaj następujący nagłówek autoryzacji:

    Authorization: AppKey {your key}

**Token aplikacji** — tokeny aplikacji są używane dla wszystkich żądań osadzania. Są one przeznaczone do uruchamiania po stronie klienta. Token jest ograniczony do pojedynczego raportu i najlepszym rozwiązaniem do ustawienia czasu wygaśnięcia.

Tokeny aplikacji to JWT (token sieci Web JSON), który jest podpisywany przez jeden z kluczy.

Token aplikacji może zawierać następujące oświadczenia:

| Claim | Opis |    
| --- | --- |
| **ver** |Wersja tokenu aplikacji. 0.2.0 jest bieżącą wersją. |
| **AUD** |Zamierzony odbiorca tokenu. W przypadku Power BI Użyj kolekcji obszarów roboczych: *https:\//Analysis.Windows.NET/PowerBI/API*. |
| **ISS** |Ciąg wskazujący aplikację, która wystawił token. |
| **type** |Typ tworzonego tokenu aplikacji. Obecnie jedynym obsługiwanym typem jest **osadzanie**. |
| **WCN** |Nazwa kolekcji obszarów roboczych, dla której jest wystawiony token. |
| **magazynie** |Identyfikator obszaru roboczego, dla którego jest wystawiony token. |
| **objęte** |Identyfikator raportu, dla którego jest wystawiony token. |
| **Nazwa użytkownika** (opcjonalnie) |Nazwa użytkownika używana z zabezpieczeniami na poziomie wiersza jest ciągiem, który może pomóc identyfikować użytkownika podczas stosowania reguł zabezpieczeń na poziomie wiersza. |
| **role** (opcjonalnie) |Ciąg zawierający role do wybrania podczas stosowania reguł zabezpieczeń na poziomie wiersza. W przypadku przekazywania więcej niż jednej roli powinna być przekazywana jako tablica Sting. |
| **punkt połączenia** usługi (opcjonalnie) |Ciąg zawierający zakresy uprawnień. W przypadku przekazywania więcej niż jednej roli powinna być przekazywana jako tablica Sting. |
| **EXP** (opcjonalnie) |Wskazuje czas wygaśnięcia tokenu. Wartość powinna zostać przeniesiona jako sygnatury czasowe systemu UNIX. |
| **NBF** (opcjonalnie) |Wskazuje czas, w którym token zaczyna obowiązywać. Wartość powinna zostać przeniesiona jako sygnatury czasowe systemu UNIX. |

Przykładowy token aplikacji wygląda następująco:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Podczas dekodowania wygląda to następująco:

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

Istnieją metody dostępne w zestawach SDK, które ułatwiają tworzenie tokenów aplikacji. Na przykład w przypadku platformy .NET można przyjrzeć się klasie [Microsoft. PowerBI. Security. PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) i [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN) .

W przypadku zestawu .NET SDK można odwoływać się do [zakresów](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Zakresy

W przypadku korzystania z tokenów osadzania można ograniczyć użycie zasobów, do których uzyskuje się dostęp. Z tego powodu można wygenerować token z uprawnieniami o określonym zakresie.

Poniżej przedstawiono dostępne zakresy dla Power BI kolekcje obszarów roboczych.

|Zakres|Opis|
|---|---|
|Zestaw danych. odczyt|Zapewnia uprawnienie do odczytu określonego zestawu danych.|
|DataSet. Write|Zapewnia uprawnienie do zapisu w określonym zestawie danych.|
|DataSet. ReadWrite|Zapewnia uprawnienie do odczytu i zapisu do określonego zestawu danych.|
|Raport. odczyt|Zapewnia uprawnienia do wyświetlania określonego raportu.|
|Report. ReadWrite|Zapewnia uprawnienie do wyświetlania i edytowania określonego raportu.|
|Workspace. Report. Create|Umożliwia utworzenie nowego raportu w określonym obszarze roboczym.|
|Workspace. Report. Copy|Zapewnia uprawnienie do klonowania istniejącego raportu w określonym obszarze roboczym.|

Można podać wiele zakresów przy użyciu spacji między zakresami, takimi jak poniższe.

```csharp
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Wymagane oświadczenia — zakresy**

punkt SCP: {scopesClaim} scopesClaim może być ciągiem lub tablicą ciągów, zwracając listę dozwolonych uprawnień do zasobów obszaru roboczego (raport, zestaw danych itp.)

Zdekodowany token z zdefiniowanymi zakresami będzie wyglądać podobnie do:

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
|Utwórz (w pamięci) nowy raport oparty na zestawie danych.|Zestaw danych|Zestaw danych. odczyt|
|Utwórz (w pamięci) nowy raport oparty na zestawie danych i Zapisz raport.|Zestaw danych|* Zestaw danych. odczyt<br>* Workspace. Report. Create|
|Wyświetlaj i Eksploruj/Edytuj (w pamięci) istniejący raport. Raport. odczyt implikuje zestaw danych. Odczytaj. Raport. odczyt nie zezwala na zapisywanie zmian.|Raport|Raport. odczyt|
|Edytuj i Zapisz istniejący raport.|Raport|Report. ReadWrite|
|Zapisz kopię raportu (Zapisz jako).|Raport|* Raport. odczyt<br>* Workspace. Report. Copy|

## <a name="heres-how-the-flow-works"></a>Oto jak działa przepływ
1. Skopiuj klucze interfejsu API do aplikacji. Klucze można pobrać w **Azure Portal**.
   
    ![Gdzie można znaleźć klucze interfejsu API w Azure Portal](media/get-started-sample/azure-portal.png)
1. Token potwierdza wierzytelność i ma czas wygaśnięcia.
   
    ![Przepływ tokenów aplikacji — potwierdzenie tokenu](media/get-started-sample/token-2.png)
1. Token jest podpisany za pomocą kluczy dostępu interfejsu API.
   
    ![Przepływ tokenów aplikacji — token zostanie podpisany](media/get-started-sample/token-3.png)
1. Użytkownik żąda wyświetlania raportu.
   
    ![Przepływ tokenu aplikacji — liczba żądań użytkownika do wyświetlenia raportu](media/get-started-sample/token-4.png)
1. Token jest weryfikowany przy użyciu kluczy dostępu interfejsu API.
   
   ![Przepływ tokenów aplikacji — sprawdzanie poprawności tokenu](media/get-started-sample/token-5.png)
1. Power BI kolekcje obszarów roboczych wysyła raport do użytkownika.
   
   ![Przepływ tokenów aplikacji — Wyślij raport do użytkownika](media/get-started-sample/token-6.png)

Po **Power BI kolekcji obszarów roboczych** wysyła raport do użytkownika, użytkownik może wyświetlić raport w aplikacji niestandardowej. Jeśli na przykład zaimportowano przykład [Przeanalizuj dane Sales Data PBIX](https://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), przykładowa aplikacja internetowa będzie wyglądać następująco:

![Przykładowy raport osadzony w aplikacji](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Zobacz też

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN)  
[Rozpocznij pracę z przykładem kolekcji obszarów roboczych Power BI firmy Microsoft](get-started-sample.md)  
[Typowe scenariusze dotyczące kolekcji obszarów roboczych Power BI firmy Microsoft](scenarios.md)  
[Wprowadzenie do kolekcji obszarów roboczych programu Microsoft Power BI](get-started.md)  
[Power BI — CSharp repozytorium git](https://github.com/Microsoft/PowerBI-CSharp)

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](https://community.powerbi.com/)
