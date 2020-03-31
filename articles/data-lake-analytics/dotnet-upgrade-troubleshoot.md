---
title: Jak rozwiązywać problemy z błędami zadań U-SQL usługi Azure Data Lake Analytics z powodu uaktualnienia programu .NET Framework 4.7.2
description: Rozwiązywanie problemów z błędami zadań U-SQL z powodu uaktualnienia do programu .NET Framework 4.7.2.
services: data-lake-analytics
author: guyhay
ms.author: guyhay
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: troubleshooting
ms.workload: big-data
ms.date: 10/11/2019
ms.openlocfilehash: f909419810cbd837e57b19a13b2df6ae9ad2ee97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213584"
---
# <a name="azure-data-lake-analytics-is-upgrading-to-the-net-framework-v472"></a>Usługa Azure Data Lake Analytics uaktualnia się do platformy .NET Framework w wersji 4.7.2

Domyślne środowisko uruchomieniowe usługi Azure Data Lake Analytics jest uaktualniane z platformy .NET Framework w wersji 4.5.2 do .NET Framework w wersji 4.7.2. Ta zmiana wprowadza niewielkie ryzyko podziału zmian, jeśli kod U-SQL używa zestawów niestandardowych, a te zestawy niestandardowe używają bibliotek .NET.

To uaktualnienie z programu .NET Framework 4.5.2 do wersji 4.7.2 oznacza, że program .NET Framework wdrożony w czasie wykonywania U-SQL (domyślne środowisko wykonawcze) będzie teraz zawsze 4.7.2. Nie ma opcji side-by-side dla wersji programu .NET Framework.

Po zakończeniu tego uaktualnienia do programu .NET Framework 4.7.2 kod zarządzany systemu będzie działał w wersji 4.7.2, biblioteki dostarczone przez użytkownika, takie jak zestawy niestandardowe U-SQL, będą działać w trybie zgodnym z wsteczem, odpowiednim dla wersji, w którą został generowane dla.

- Jeśli biblioteki DLL zestawu są generowane dla wersji 4.5.2, wdrożona struktura będzie traktować je jako biblioteki 4.5.2, zapewniając (z kilkoma wyjątkami) semantykę 4.5.2.
- Teraz można użyć zestawów niestandardowych U-SQL, które korzystają z funkcji w wersji 4.7.2, jeśli jest to docelowe .NET Framework 4.7.2.

Z powodu tego uaktualnienia do platformy .NET Framework 4.7.2 istnieje możliwość wprowadzenia przełomowych zmian w zadaniach U-SQL, które używają zestawów niestandardowych platformy .NET. Zalecamy sprawdzenie problemów ze zgodnością z do tyłu, korzystając z poniższej procedury.

## <a name="how-to-check-for-backwards-compatibility-issues"></a>Jak sprawdzić problemy ze zgodnością z przesiewem

Sprawdź, czy nie można rozwiązać problemów z łamaniem zgodności z powrotem, uruchamiając testy zgodności platformy .NET w kodzie platformy .NET w zestawach niestandardowych U-SQL.

> [!Note]
> Narzędzie nie wykrywa rzeczywistych zmian podziału. identyfikuje tylko o nazwie .NET INTERFEJSÓW API, które mogą (dla niektórych danych wejściowych) powodować problemy. Jeśli otrzymasz powiadomienie o problemie, kod może być nadal w porządku, jednak należy sprawdzić więcej szczegółów.

1. Uruchom moduł sprawdzania zgodności z do tyłu na bibliotekach DLL platformy .NET
   1. Korzystanie z rozszerzenia programu Visual Studio w [programie .NET Portability Analyzer Visual Studio Extension](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer)
   1. Pobieranie i korzystanie z autonomicznego narzędzia z [GitHub dotnetapiport](https://github.com/microsoft/dotnet-apiport). Instrukcje dotyczące uruchamiania samodzielnego narzędzia znajdują się w [gitHub dotnetapiport, które przełamują zmiany](https://github.com/microsoft/dotnet-apiport/blob/dev/docs/HowTo/BreakingChanges.md)
   1. Dla 4.7.2. zgodności, `read isRetargeting == True` identyfikuje możliwe problemy.
2. Jeśli narzędzie wskazuje, czy na kod może mieć wpływ którykolwiek z możliwych niezgodności wstecznych (niektóre typowe przykłady niezgodności są wymienione poniżej), można
   1. Analizowanie kodu i identyfikowanie, czy kod jest przekazywanie wartości do interfejsów API wpływ
   1. Wykonaj sprawdzanie środowiska uruchomieniowego. Wdrożenie środowiska uruchomieniowego nie jest wykonywane obok siebie w umowy ADLA. Można wykonać sprawdzanie środowiska uruchomieniowego przed uaktualnieniem, przy użyciu uruchomienia lokalnego VisualStudio z lokalnym .NET Framework 4.7.2 względem reprezentatywnego zestawu danych.
3. Jeśli rzeczywiście są dotknięte wstecznie niezgodności, należy podjąć niezbędne kroki, aby go naprawić (na przykład naprawy danych lub logiki kodu).

W większości przypadków nie powinny mieć wpływu na zgodność wstecznych.

## <a name="timeline"></a>Oś czasu

Można sprawdzić, czy wdrożenie nowego środowiska uruchomieniowego w tym miejscu [Rozwiązywanie problemów](runtime-troubleshoot.md)i patrząc na wszelkie wcześniejsze pomyślne zadanie.

### <a name="what-if-i-cant-get-my-code-reviewed-in-time"></a>Co zrobić, jeśli nie mogę uzyskać przeglądu kodu na czas

Można przesłać zadanie względem starej wersji środowiska uruchomieniowego (która jest zbudowana z kierowania 4.5.2), jednak ze względu na brak możliwości .NET Framework side-by-side, nadal będzie działać tylko w trybie zgodności 4.5.2. Z powodu tego zachowania mogą nadal występnąć niektóre problemy ze zgodnością z wstecznymi.

### <a name="what-are-the-most-common-backwards-compatibility-issues-you-may-encounter"></a>Jakie są najczęstsze problemy ze zgodnością z do tyłu, które mogą wystąpić

Najczęstsze niezgodności wsteczne, które prawdopodobnie zidentyfikowano moduł sprawdzania, to (wygenerowaliśmy tę listę, uruchamiając moduł sprawdzania na naszych wewnętrznych zadaniach ADLA), których dotyczy problem bibliotek (uwaga: możesz wywołać biblioteki tylko pośrednio, dlatego ważne jest podjęcie wymaganych działań #1, aby sprawdzić, czy zadania są zagrożone) i możliwe działania, aby rozwiązać problem. Uwaga: W prawie wszystkich przypadkach dla naszych własnych miejsc pracy, ostrzeżenia okazały się fałszywymi alarmami ze względu na wąski charakter większości przełomowych zmian.

- IAsyncResult.CompletedSynchronously Właściwość musi być poprawna dla wynikowego zadania do wykonania
  - Podczas wywoływania TaskFactory.FromAsync, implementacja IAsyncResult.CompletedSynchronously właściwość musi być poprawna dla wynikowego zadania do wykonania. Oznacza to, że właściwość musi zwracać true if i tylko wtedy, gdy implementacja została ukończona synchronicznie. Uprzednio ta właściwość nie była sprawdzana.
  - Biblioteki, na które ma wpływ: mscorlib, System.Threading.Tasks
  - Sugerowana akcja: Upewnij się, że TaskFactory.FromAsync zwraca poprawnie

- DataObject.GetData pobiera teraz dane jako UTF-8
  - W przypadku aplikacji przeznaczonych dla programu .NET Framework 4 lub uruchomionych w programie .NET Framework 4.5.1 lub starszych wersjach plik DataObject.GetData pobiera dane w formacie HTML jako ciąg ASCII. W rezultacie znaki inne niż ASCII (znaki, których kody ASCII są większe niż 0x7F) są reprezentowane przez dwa losowe znaki.#N##N#For aplikacje przeznaczone dla programu .NET Framework 4.5 lub nowszego i uruchamiane w programie .NET Framework 4.5.2, `DataObject.GetData` pobierają dane w formacie HTML jako UTF-8, który reprezentuje znaki większe niż 0x7F poprawnie.
  - Biblioteki, na które ma wpływ: Glo
  - Sugerowana akcja: Upewnij się, że pobrane dane są w żądanym formacie

- XmlWriter rzuca na nieprawidłowe pary zastępcze
  - W przypadku aplikacji przeznaczonych dla platformy .NET Framework 4.5.2 lub poprzednich wersji, pisanie nieprawidłowej pary zastępczej przy użyciu obsługi rezerwowej wyjątku nie zawsze zgłasza wyjątek. W przypadku aplikacji przeznaczonych dla programu .NET Framework 4.6 próba `ArgumentException`zapisania nieprawidłowej pary zastępczej powoduje rzut .
  - Biblioteki, na które ma wpływ: System.Xml, System.Xml.ReaderWriter
  - Sugerowana akcja: Upewnij się, że nie piszesz nieprawidłowej pary zastępczej, która spowoduje wyjątek argumentów

- HtmlTextWriter nie `<br/>` renderuje elementu poprawnie
  - Począwszy od programu .NET Framework 4.6, `HtmlTextWriter.RenderBeginTag()` wywołanie i `HtmlTextWriter.RenderEndTag()` `<BR />` element poprawnie wstawić tylko jeden `<BR />` (zamiast dwóch)
  - Biblioteki, na które ma wpływ: System.Web
  - Sugerowane działanie: Upewnij się, że `<BR />` wstawiasz oczekiwaną ilość, aby zobaczyć, aby w zadaniu produkcyjnym nie było przypadkowego zachowania

- Wywołanie CreateDefaultAuthorizationContext z argumentem null został zmieniony
  - Implementacja AuthorizationContext zwrócony przez wywołanie `CreateDefaultAuthorizationContext(IList<IAuthorizationPolicy>)` z null authorizationPolicies argument zmienił jego implementacji w .NET Framework 4.6.
  - Biblioteki, na które ma wpływ: System.IdentityModel
  - Sugerowana akcja: Upewnij się, że obsługujesz nowe oczekiwane zachowanie, gdy istnieją zasady autoryzacji zerowej
  
- RSACng teraz poprawnie ładuje klawisze RSA niestandardowego rozmiaru klucza
  - W wersjach programu .NET Framework przed wersją 4.6.2 klienci o niestandardowych rozmiarach kluczy dla certyfikatów RSA nie mogą uzyskać dostępu do tych kluczy za pośrednictwem metod `GetRSAPublicKey()` rozszerzenia i. `GetRSAPrivateKey()` A `CryptographicException` z komunikatem "Żądany rozmiar klucza nie jest obsługiwany" jest generowany. W przypadku programu .NET Framework 4.6.2 ten problem został rozwiązany. Podobnie, `RSA.ImportParameters()` a `RSACng.ImportParameters()` teraz pracować z niestandardowymi rozmiarami `CryptographicException`kluczy bez rzucania 's.
  - Biblioteki, na które wpływ wpłynęły: mscorlib, System.Core
  - Sugerowane działanie: Upewnij się, że klucze RSA działają zgodnie z oczekiwaniami

- Kontrole dwukropek ścieżki są bardziej rygorystyczne
  - W .NET Framework 4.6.2 wprowadzono szereg zmian w celu obsługi wcześniej nieobsługiconych ścieżek (zarówno pod względem długości, jak i formatu). Sprawdzanie prawidłowej składni separatora dysku (dwukropek) było bardziej poprawne, co miało efekt uboczny blokowania niektórych ścieżek URI w kilku wybranych interfejsach API ścieżki, w których były tolerowane.
  - Biblioteki, na które ma wpływ: mscorlib, System.Runtime.Extensions
  - Sugerowane działanie:

- Wywołania konstrukcji ClaimsIdentity
  - Począwszy od .NET Framework 4.6.2, istnieje `T:System.Security.Claims.ClaimsIdentity` zmiana w `T:System.Security.Principal.IIdentity` sposób `P:System.Security.Claims.ClaimsIdentify.Actor` konstruktorów z parametrem ustawić właściwość. Jeśli `T:System.Security.Principal.IIdentity` argument jest `T:System.Security.Claims.ClaimsIdentity` obiektem, `P:System.Security.Claims.ClaimsIdentify.Actor` a `T:System.Security.Claims.ClaimsIdentity` właściwość `null`tego `P:System.Security.Claims.ClaimsIdentify.Actor` obiektu nie jest , `M:System.Security.Claims.ClaimsIdentity.Clone` właściwość jest dołączona przy użyciu metody. W framework 4.6.1 i wcześniejszych `P:System.Security.Claims.ClaimsIdentify.Actor` wersjach właściwość jest dołączona jako istniejące odwołanie. Z `P:System.Security.Claims.ClaimsIdentify.Actor` powodu tej zmiany, począwszy od .NET Framework 4.6.2, właściwość nowego `T:System.Security.Claims.ClaimsIdentity` obiektu nie jest równa `P:System.Security.Claims.ClaimsIdentify.Actor` właściwości argumentu konstruktora. `T:System.Security.Principal.IIdentity` W .NET Framework 4.6.1 i wcześniejszych wersjach jest równa.
  - Biblioteki, na które wpływ wpłynęła: mscorlib
  - Sugerowana akcja: Upewnij się, że claimsidentity działa zgodnie z oczekiwaniami w nowym czasie wykonywania

- Serializacja znaków sterujących za pomocą datacontractJsonSerializer jest teraz zgodna z ecmascript V6 i V8
  - W ramach .NET framework 4.6.2 i wcześniejszych wersjach datacontractJsonSerializer nie serizował niektórych znaków sterujących specjalnych, takich jak \b, \f i \t, w sposób zgodny ze standardami ECMAScript V6 i V8. Począwszy od programu .NET Framework 4.7, serializacja tych znaków sterujących jest zgodna z ecmascript V6 i V8.
  - Biblioteki, na które ma wpływ: System.Runtime.Serialization.Json
  - Sugerowana akcja: Zapewnienie tego samego zachowania za pomocą datacontractJsonSerializer
