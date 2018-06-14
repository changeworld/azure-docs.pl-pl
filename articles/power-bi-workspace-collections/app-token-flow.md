---
title: Uwierzytelniania i autoryzacji z kolekcjami obszaru roboczego programu Power BI | Dokumentacja firmy Microsoft
description: Uwierzytelniania i autoryzacji z kolekcjami obszaru roboczego programu Power BI.
services: power-bi-embedded
documentationcenter: ''
author: markingmyname
manager: kfile
editor: ''
tags: ''
ROBOTS: NOINDEX
ms.assetid: 1c1369ea-7dfd-4b6e-978b-8f78908fd6f6
ms.service: power-bi-embedded
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: powerbi
ms.date: 09/20/2017
ms.author: maghan
ms.openlocfilehash: 74d34e708fb74daa295642d50643b78af8f6cb7a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31412189"
---
# <a name="authenticating-and-authorizing-with-power-bi-workspace-collections"></a>Uwierzytelniania i autoryzacji z kolekcjami obszaru roboczego programu Power BI

Power BI obszaru roboczego kolekcje użycia **klucze** i **tokenów aplikacji** do uwierzytelniania i autoryzacji, zamiast jawnego użytkownika końcowego uwierzytelniania. W tym modelu aplikacji zarządza uwierzytelniania i autoryzacji dla użytkowników końcowych. Jeśli to konieczne, aplikacja tworzy i wysyła tokenów aplikacji, które informują naszej usługi do renderowania żądanego raportu. Ten projekt nie wymaga aplikacji usługi Azure Active Directory na potrzeby uwierzytelniania i autoryzacji, użytkowników, mimo że nadal można.

> [!IMPORTANT]
> Kolekcje obszarów roboczych usługi Power BI są przestarzałe i będą dostępne do czerwca 2018 roku lub do daty podanej w kontrakcie. Zachęcamy do zaplanowania migracji do usługi Power BI Embedded, aby uniknąć przerw w działaniu aplikacji. Aby uzyskać informacje dotyczące sposobu przeprowadzenia migracji danych do usługi Power BI Embedded, zobacz [How to migrate Power BI Workspace Collections content to Power BI Embedded (Migrowanie zawartości kolekcji obszarów roboczych usługi Power BI do usługi Power BI Embedded)](https://powerbi.microsoft.com/documentation/powerbi-developer-migrate-from-powerbi-embedded/).

## <a name="two-ways-to-authenticate"></a>Dwa sposoby uwierzytelniania

**Klucz** — można używać kluczy dla wszystkich wywołań interfejsu API REST kolekcje Power BI obszaru roboczego. Klucze można znaleźć w **portalu Microsoft Azure** wybierając **wszystkie ustawienia** , a następnie **klucze dostępu**. Zawsze należy traktować klucz, tak, jakby jego są hasła. Te klucze mają uprawnienia do wprowadzania żadnych wywołać w kolekcji obszarów roboczych danego interfejsu API REST.

Aby użyć klucza na wywołanie interfejsu REST, Dodaj następujący nagłówek autoryzacji:

    Authorization: AppKey {your key}

**Tokenu aplikacji** -tokenów aplikacji są używane dla wszystkich żądań osadzania. Służą one do uruchomienia po stronie klienta. Token jest ograniczone do pojedynczego raportu i jego najlepszym rozwiązaniem jest ustawienie czasu wygaśnięcia.

Tokenów aplikacji są JWT (JSON Web Token), który jest podpisany przez jeden z kluczy.

Token aplikacji może zawierać następujące oświadczeń:

| Claim | Opis |
| --- | --- |
| **VER** |Wersja tokenu aplikacji. 0.2.0 jest bieżącej wersji. |
| **lub** |Zamierzonym odbiorcą tokenu. Dla kolekcji programu Power BI obszaru roboczego: "https://analysis.windows.net/powerbi/api." |
| **iss** |Ciąg wskazujący aplikacji, która wystawiła token. |
| **type** |Typ tokenu aplikacji, która jest tworzona. Bieżący jest jedynym obsługiwanym typem **osadzić**. |
| **WCN** |Nazwa kolekcji obszaru roboczego token zostało wystawione. |
| **bazy danych WID** |Identyfikator obszaru roboczego token zostało wystawione. |
| **Identyfikator RID** |Identyfikator raportu token zostało wystawione. |
| **Nazwa użytkownika** (opcjonalnie) |Używane zabezpieczenia na poziomie wiersza, nazwa użytkownika jest ciągiem, który może ułatwić identyfikację użytkownika, stosując zasady zabezpieczenia na poziomie wiersza. |
| **role** (opcjonalnie) |Ciąg zawierający role, aby wybrać podczas stosowania zasad zabezpieczeń na poziomie wiersza. Przekazywanie więcej niż jednej roli, powinien zostać przekazany jako tablica ciągu. |
| **punkt połączenia usługi** (opcjonalnie) |Ciąg zawierający zakresy uprawnień. Przekazywanie więcej niż jednej roli, powinien zostać przekazany jako tablica ciągu. |
| **EXP** (opcjonalnie) |Wskazuje czas, w którym token jest ważny. Wartość powinien być przekazany jako sygnatur czasowych systemu Unix. |
| **NBF** (opcjonalnie) |Wskazuje godzinę, w której token zaczyna się ważny. Wartość powinien być przekazany jako sygnatur czasowych systemu Unix. |

Przykładowy token aplikacji wygląda następująco:

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ2ZXIiOiIwLjIuMCIsInR5cGUiOiJlbWJlZCIsIndjbiI6Ikd1eUluQUN1YmUiLCJ3aWQiOiJkNGZlMWViMS0yNzEwLTRhNDctODQ3Yy0xNzZhOTU0NWRhZDgiLCJyaWQiOiIyNWMwZDQwYi1kZTY1LTQxZDItOTMyYy0wZjE2ODc2ZTNiOWQiLCJzY3AiOiJSZXBvcnQuUmVhZCIsImlzcyI6IlBvd2VyQklTREsiLCJhdWQiOiJodHRwczovL2FuYWx5c2lzLndpbmRvd3MubmV0L3Bvd2VyYmkvYXBpIiwiZXhwIjoxNDg4NTAyNDM2LCJuYmYiOjE0ODg0OTg4MzZ9.v1znUaXMrD1AdMz6YjywhJQGY7MWjdCR3SmUSwWwIiI
```

Gdy zdekodowany, wygląda na to wyglądać mniej więcej tak:

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

Brak dostępnych metod w obrębie zestawów SDK, które ułatwia tworzenie tokenów aplikacji. Na przykład dla platformy .NET można przyjrzeć się [Microsoft.PowerBI.Security.PowerBIToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken) klasy i [CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_) metody.

Dla zestawu SDK .NET mogą odwoływać się do [zakresy](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.scopes).

## <a name="scopes"></a>Zakresy

Osadź tokeny, można ograniczyć użycie zasobów, które zapewniają dostęp do. Z tego powodu można wygenerować token z zakresu uprawnień.

Poniżej przedstawiono dostępne zakresy dla kolekcji programu Power BI obszaru roboczego.

|Zakres|Opis|
|---|---|
|Dataset.Read|Udostępnia uprawnienie do odczytu określonego zestawu danych.|
|Dataset.Write|Udostępnia uprawnienie do zapisu do określonego zestawu danych.|
|Dataset.ReadWrite|Udostępnia uprawnienie do odczytu i zapisu do określonego zestawu danych.|
|Report.Read|Zawiera uprawnienia do wyświetlania określonego raportu.|
|Report.ReadWrite|Udostępnia uprawnienie do wyświetlania i edytowania określonego raportu.|
|Workspace.Report.Create|Udostępnia uprawnienie do tworzenia nowego raportu w ramach określonego obszaru roboczego.|
|Workspace.Report.Copy|Udostępnia uprawnienie do Sklonowanie istniejącego raportu w ramach określonego obszaru roboczego.|

Przy użyciu spacji między zakresy podobne do poniższych można dostarczyć wiele zakresów.

```
string scopes = "Dataset.Read Workspace.Report.Create";
```

**Wymagane oświadczenia - zakresów**

punkt połączenia usługi: scopesClaim {scopesClaim} może być ciąg lub tablica ciągów, biorąc pod uwagę dozwolone uprawnienia do zasobów obszaru roboczego (raport, zestaw danych itp.)

Token dekodowane z zakresami zdefiniowane, będzie wyglądać podobnie do:

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

|Operacja|Zasób docelowy|Token uprawnień|
|---|---|---|
|Tworzenie nowego raportu oparte na zestawie danych (w pamięci).|Zestaw danych|Dataset.Read|
|Tworzenie nowego raportu oparte na zestawie danych (w pamięci) i Zapisz raport.|Zestaw danych|* Dataset.Read<br>* Workspace.Report.Create|
|Wyświetlanie i Eksploruj/edytowanie (w pamięci) istniejącego raportu. Report.Read oznacza Dataset.Read. Report.Read nie zezwala na zapisywanie zmian.|Raport|Report.Read|
|Edytuj i zapisuj istniejącego raportu.|Raport|Report.ReadWrite|
|Zapisz kopię raportu (Zapisz jako).|Raport|* Report.Read<br>* Workspace.Report.Copy|

## <a name="heres-how-the-flow-works"></a>Oto, jak działa przepływu
1. Kopiowanie kluczy interfejsu API do aplikacji. Można pobrać kluczy **portalu Azure**.
   
    ![Gdzie można znaleźć klucze interfejsu API w portalu Azure](media/get-started-sample/azure-portal.png)
1. Token deklaracji rozkazujących oświadczenia i ma czasu wygaśnięcia.
   
    ![Przepływie tokenu aplikacji - potwierdzeń tokenu oświadczeń](media/get-started-sample/token-2.png)
1. Pobiera podpisany token kluczy dostępu do interfejsu API.
   
    ![Pobiera podpisany token przepływu aplikacji - token](media/get-started-sample/token-3.png)
1. Żądania użytkowników dotyczące wyświetlania raportu.
   
    ![Przepływie tokenu aplikacji — użytkownik zażąda Aby wyświetlić raport](media/get-started-sample/token-4.png)
1. Token została zweryfikowana za pomocą kluczy dostępu do interfejsu API.
   
   ![Sprawdzania poprawności tokenu przepływu aplikacji - token](media/get-started-sample/token-5.png)
1. Power BI obszaru roboczego kolekcje wysyła raport do użytkownika.
   
   ![Przepływie tokenu aplikacji - usługi wysłać raport do użytkownika](media/get-started-sample/token-6.png)

Po **Power BI obszaru roboczego kolekcje** wysyła raport do użytkownika, użytkownik może wyświetlić raport w aplikacji niestandardowej. Na przykład po zaimportowaniu [analizowania sprzedaży PBIX dane przykładowe](http://download.microsoft.com/download/1/4/E/14EDED28-6C58-4055-A65C-23B4DA81C4DE/Analyzing_Sales_Data.pbix), przykładową aplikację sieci web powinien wyglądać tak:

![Przykładowy raport osadzone w aplikacji](media/get-started-sample/sample-web-app.png)

## <a name="see-also"></a>Zobacz też

[CreateReportEmbedToken](https://docs.microsoft.com/dotnet/api/microsoft.powerbi.security.powerbitoken?redirectedfrom=MSDN#methods_)  
[Rozpoczęcie pracy z przykładem Microsoft Power BI obszaru roboczego kolekcje](get-started-sample.md)  
[Typowe scenariusze Microsoft Power BI obszaru roboczego kolekcje](scenarios.md)  
[Wprowadzenie do programu Microsoft Power BI obszaru roboczego kolekcje](get-started.md)  
[Usługa Power BI CSharp repozytorium Git](https://github.com/Microsoft/PowerBI-CSharp)

Masz więcej pytań? [Dołącz do społeczności użytkowników usługi Power BI](http://community.powerbi.com/)