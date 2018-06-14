---
title: Dane poufne — narzędzie Microsoft Threat modelowania - Azure | Dokumentacja firmy Microsoft
description: środki zaradcze w przypadku zagrożeń widoczne w narzędziu modelowania zagrożeń
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 8d7189ea4b01d43cea709e3300d8ed71d266f5c9
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/24/2018
ms.locfileid: "28019419"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Ramka zabezpieczeń: Dane poufne | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Granic zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne są zaciemniona, jeśli zawierają one informacje poufne](#binaries-info)</li><li>[Należy rozważyć przy użyciu systemu szyfrowania plików (EFS) jest używany do ochrony danych poufnych specyficzne dla użytkownika](#efs-user)</li><li>[Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany.](#filesystem)</li></ul> | 
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, poufnej zawartości nie jest buforowana w przeglądarce](#cache-browser)</li><li>[Szyfrowanie sekcji pliki konfiguracji aplikacji sieci Web, które zawierają dane poufne](#encrypt-data)</li><li>[Jawnie Wyłącz Autouzupełnianie atrybutu HTML w formularzach poufnych i dane wejściowe](#autocomplete-input)</li><li>[Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są wyświetlane jako znaki](#data-mask)</li></ul> | 
| **Baza danych** | <ul><li>[Implementowanie dynamiczne maskowanie Aby ograniczyć użytkowników narażenia z systemem innym niż uprzywilejowany danych poufnych danych](#dynamic-users)</li><li>[Upewnij się, że hasła są przechowywane w formacie solone wyznaczania wartości skrótu](#salted-hash)</li><li>[Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane](#db-encrypted)</li><li>[Upewnij się, że włączone jest szyfrowanie tej bazy danych na poziomie (funkcji TDE)](#tde-enabled)</li><li>[Upewnij się, że kopie zapasowe bazy danych są szyfrowane.](#backup)</li></ul> | 
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że poufne dane związane z interfejsu API sieci Web nie są przechowywane w pamięci masowej w przeglądarce](#api-browser)</li></ul> | 
| Dokumentów w usłudze Azure DB | <ul><li>[Szyfrowania poufnych danych przechowywanych w usłudze Azure DB rozwiązania Cosmos](#encrypt-docdb)</li></ul> | 
| **Maszyna wirtualna platformy Azure IaaS granicy zaufania** | <ul><li>[Użyj szyfrowania dysków Azure do szyfrowania dysków używanych przez maszyny wirtualne](#disk-vm)</li></ul> | 
| **Granic zaufania sieci szkieletowej usług** | <ul><li>[Szyfrowanie kluczy tajnych w aplikacji sieci szkieletowej usług](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Wykonaj modelowania zabezpieczeń i użyć zespołów jednostki biznesowe w przypadku, gdy wymagane](#modeling-teams)</li><li>[Minimalizowanie dostęp do udziału funkcji na jednostkach krytyczne](#entities)</li><li>[Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i dobrej rozwiązania](#good-practices)</li><li>[Programowanie regułę standardy, proscribing konfiguracji ze szczegółowymi informacjami w Zarządzanie wyjątkami dołączania](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Użyj szyfrowanie usługi Magazyn Azure (SSE) dla danych magazynowanych (wersja zapoznawcza)](#sse-preview)</li><li>[Użyj szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage](#client-storage)</li></ul> | 
| **Klientów urządzeń przenośnych** | <ul><li>[Szyfrowanie liter lub dane osobowe dane zapisywane w lokalnej pamięci masowej telefonów](#pii-phones)</li><li>[Zasłaniają wygenerowanych plików binarnych przed rozpoczęciem dystrybuowania użytkownikom końcowym](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ustaw właściwość clientCredentialType certyfikatu lub systemu Windows](#cert)</li><li>[Nie jest włączony tryb zabezpieczeń programu WCF](#security)</li></ul> | 

## <a id="binaries-info"></a>Upewnij się, że pliki binarne są zaciemniona, jeśli zawierają one informacje poufne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że pliki binarne są zaciemniona, jeśli zawierają one informacje poufne, takich jak tajemnice handlowe logiki biznesowej poufnych, które powinny nie wycofane. To, aby zatrzymać odtwarzanie zestawów. Narzędzi, takich jak `CryptoObfuscator` mogą być używane w tym celu. |

## <a id="efs-user"></a>Należy rozważyć przy użyciu systemu szyfrowania plików (EFS) jest używany do ochrony danych poufnych specyficzne dla użytkownika

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Należy rozważyć przy użyciu systemu szyfrowania plików (EFS) jest używany do chronić poufne dane specyficzne dla użytkownika z atakujący mający fizyczny dostęp do komputera. |

## <a id="filesystem"></a>Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany.

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany (np. przy użyciu DPAPI), jeśli nie można wymusić systemu szyfrowania plików |

## <a id="cache-browser"></a>Upewnij się, poufnej zawartości nie jest buforowana w przeglądarce

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, Web Forms, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Przeglądarki może przechowywać informacje na potrzeby buforowania i historii. Te buforowane pliki są przechowywane w folderze, takie jak folder tymczasowych plików internetowych, w przypadku programu Internet Explorer. Te strony są ponownie, określonym w przeglądarce pojawi się je z pamięci podręcznej. Jeśli informacje poufne jest wyświetlany użytkownikowi (na przykład ich adres karty kredytowej, numer ubezpieczenia społecznego albo nazwa użytkownika), te informacje może być przechowywane w pamięci podręcznej przeglądarki i w związku z tym pobieranie poprzez badanie pamięci podręcznej przeglądarki lub po prostu naciskając przycisk "Wstecz" w przeglądarce. Wartość nagłówka cache-control odpowiedzi nagłówka do "nie-store" dla wszystkich stron. |

### <a name="example"></a>Przykład
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-cache" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Przykład
Mogą to być wykonywane przez filtr. Może służyć następujący przykład: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a id="encrypt-data"></a>Szyfrowanie sekcji pliki konfiguracji aplikacji sieci Web, które zawierają dane poufne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Porady: Szyfrowanie sekcji konfiguracyjnych w DPAPI za pomocą programu ASP.NET 2.0](https://msdn.microsoft.com/library/ff647398.aspx), [określenie dostawcę konfiguracji chronione](https://msdn.microsoft.com/library/68ze1hb2.aspx), [przy użyciu usługi Azure Key Vault ochronę kluczy tajnych w aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracji, takich jak plik Web.config, appsettings.json są często używane do przechowywania informacji poufnych, w tym nazwy użytkownika, hasła, parametry połączenia bazy danych i kluczy szyfrowania. Jeśli nie będzie chronić te informacje, aplikacja jest narażony na ataki lub złośliwym użytkownikom uzyskania poufne informacje, takie jak nazwy kont i hasła, nazwy bazy danych i nazwy serwera. Oparty na typie wdrożenia (azure/lokalnego), szyfrowanie sekcji poufnych plików konfiguracji przy użyciu DPAPI lub usługi, takie jak usługi Azure Key Vault. |

## <a id="autocomplete-input"></a>Jawnie Wyłącz Autouzupełnianie atrybutu HTML w formularzach poufnych i dane wejściowe

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN: atrybut autocomplete](http://msdn.microsoft.com/library/ms533486(VS.85).aspx), [przy użyciu funkcji AutoComplete w formacie HTML](http://msdn.microsoft.com/library/ms533032.aspx), [luki w zabezpieczeniach ich oczyszczania HTML](http://technet.microsoft.com/security/bulletin/MS10-071), [Autocomplete., ponownie?](http://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroki** | Atrybut autocomplete Określa, czy formularz powinien mieć autouzupełniania lub wyłącz. Po włączeniu funkcji autocomplete przeglądarka automatycznie wypełnić wartości na podstawie wartości, które użytkownik wprowadził przed. Na przykład po wprowadzeniu nowej nazwy i hasła w postaci i przesłaniu formularza, przeglądarka zapyta, czy hasło ma zostać zapisany. Następnie podczas wyświetlania formularza, nazwę i hasło są wypełniane automatycznie lub zostały zakończone, ponieważ nazwa jest wprowadzona. Osoba atakująca z dostępem do lokalnego może uzyskać hasła nieszyfrowanego z pamięci podręcznej przeglądarki. Funkcji autocomplete jest domyślnie włączona, a musi jawnie wyłączone. |

### <a name="example"></a>Przykład
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są wyświetlane jako znaki

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Dane poufne, takie jak hasła, numery kart kredytowych, itp. SSN powinien maskowane, gdy wyświetlane na ekranie. Ma to zapobiec próbom nieautoryzowanego dostępu do danych (np. hasła ramię przeglądanie, pomocy technicznej wyświetlanie SSN liczby użytkowników). Upewnij się, że te elementy danych nie są widoczne w postaci zwykłego tekstu i odpowiednio są ukryte. Musi to być wykonane szczególną uwagę podczas akceptowania ich jako danych wejściowych (np. wprowadzania type = "password") oraz wyświetlanie wstecz na ekranie (np. wyświetlania tylko ostatnich 4 cyfr numeru karty kredytowej). |

## <a id="dynamic-users"></a>Implementowanie dynamiczne maskowanie Aby ograniczyć użytkowników narażenia z systemem innym niż uprzywilejowany danych poufnych danych

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Sql Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 wersji — w wersji 12, wersja programu SQL — SQL |
| **Odwołania**              | [Maskowanie danych dynamicznych](https://msdn.microsoft.com/library/mt130841) |
| **Kroki** | Dynamiczne maskowanie danych służy do ograniczania ujawnianie danych wrażliwych, zapobiegając użytkowników, którzy nie powinni mieć dostęp do danych z wyświetlania jej. Dynamiczne maskowanie danych nie ma na celu aby uniemożliwić użytkownikom bazy danych połączenie bezpośrednio z bazy danych i uruchamiania wyczerpujący zapytań, które udostępniają fragmentów danych poufnych. Maskowanie danych dynamicznych jest uzupełnienie innych funkcji zabezpieczeń programu SQL Server (inspekcji, szyfrowania, zabezpieczeń na poziomie wiersza...) i zdecydowanie zaleca się stosowania tej funkcji w połączeniu z nimi dodatkowo aby lepiej chronić poufne dane w bazie danych. Należy pamiętać, że ta funkcja jest obsługiwana tylko przez program SQL Server w programie 2016 i bazy danych SQL Azure. |

## <a id="salted-hash"></a>Upewnij się, że hasła są przechowywane w formacie solone wyznaczania wartości skrótu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Hashing hasła przy użyciu interfejsów API architektury .NET kryptograficznego](http://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroki** | Hasła nie powinny być przechowywane w magazynie użytkownika niestandardowego w bazach danych. Hasła powinny być przechowywane wartości zaburzającej zamiast tego. Upewnij się, soli dla użytkownika jest zawsze unikatowa i zastosować b-crypt, s-crypt lub PBKDF2 przed zapisaniem hasła, wraz z liczbą minimalna pracy współczynnik iteracji pętli 150 000, aby wyeliminować możliwość wymuszania atak.| 

## <a id="db-encrypted"></a>Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Wersja programu SQL — wszystkie |
| **Odwołania**              | [Szyfrowania poufnych danych w programie SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [porady: szyfrowanie kolumny danych w programie SQL Server](https://msdn.microsoft.com/library/ms179331), [Szyfruj certyfikatu](https://msdn.microsoft.com/library/ms188061) |
| **Kroki** | Dane poufne, takie jak numer karty kredytowej musi być szyfrowane w bazie danych. Dane mogą być szyfrowane za pomocą szyfrowania na poziomie kolumny lub funkcję aplikacji przy użyciu funkcji szyfrowania. |

## <a id="tde-enabled"></a>Upewnij się, że włączone jest szyfrowanie tej bazy danych na poziomie (funkcji TDE)

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Opis programu SQL Server przezroczystego szyfrowania danych (funkcji TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroki** | Przezroczystego szyfrowania danych (funkcji TDE) funkcji w programie SQL server ułatwia szyfrowania poufnych danych w bazie danych i ochronę kluczy, które są używane do szyfrowania danych przy użyciu certyfikatu. Zapobiega to osobom niemającym kluczy przy użyciu danych. Funkcji TDE chroni dane "magazynowane", co oznacza plików danych i dziennika. Zapewnia możliwość wykonania wielu ustawowych, wykonawczych i wskazówkami w różnych branżach. |

## <a id="backup"></a>Upewnij się, że kopie zapasowe bazy danych są szyfrowane.

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | SQL Azure, OnPrem |
| **Atrybuty**              | MsSQL2014 wersji — w wersji 12, wersja programu SQL — SQL |
| **Odwołania**              | [Szyfrowanie kopii zapasowej bazy danych SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroki** | Program SQL Server ma możliwość szyfrowania danych podczas tworzenia kopii zapasowej. Określając algorytm szyfrowania i modułu szyfrującego (certyfikatu lub klucza asymetrycznego) podczas tworzenia kopii zapasowej, można utworzyć zaszyfrowanego pliku kopii zapasowej. |

## <a id="api-browser"></a>Upewnij się, że poufne dane związane z interfejsu API sieci Web nie są przechowywane w pamięci masowej w przeglądarce

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC 5, MVC 6 |
| **Atrybuty**              | Dostawca tożsamości dostawca — usługi AD FS, tożsamość — usługi Azure AD |
| **Odwołania**              | ND  |
| **Kroki** | <p>W niektórych implementacjach poufnych artefaktów odpowiednie do uwierzytelniania składnika Web API są przechowywane w magazynie lokalnym w przeglądarce. Np. artefakty uwierzytelniania usługi Azure AD, takich jak adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key itp.</p><p>Wszystkie te artefakty są dostępne nawet po zakończeniu Wyloguj lub zamknięcie okna przeglądarki. Jeśli atakujący dokonuje uzyskuje dostęp do tych artefaktów, może on korzystać z nich dostępu do chronionych zasobów (API). Upewnij się, że wszystkich poufnych artefaktów związanych z interfejsu API sieci Web nie są przechowywane w magazynie w przeglądarce. W przypadkach, gdy niezbędne jest magazynu po stronie klienta (np. jednej strony aplikacji JEDNOSTRONICOWEJ zwiększają przepływów niejawne OpenIdConnect/OAuth potrzeba przechowywania tokenów dostępu lokalnie), użyj opcji magazynu z braku trwałości. np. preferowane SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Przykład
Poniżej JavaScript fragment jest z biblioteki uwierzytelniania niestandardowego, której są przechowywane artefakty uwierzytelniania w magazynie lokalnym. Należy unikać takich implementacji. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a id="encrypt-docdb"></a>Szyfrowania poufnych danych przechowywanych w bazie danych rozwiązania Cosmos

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dokumentów w usłudze Azure DB | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Szyfrowania poufnych danych na poziomie aplikacji przed przekazaniem dokumentu bazy danych lub przechowywać żadnych poufnych danych w innych rozwiązań pamięci masowej, takich jak usługi Azure Storage lub Azure SQL| 

## <a id="disk-vm"></a>Użyj szyfrowania dysków Azure do szyfrowania dysków używanych przez maszyny wirtualne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyna wirtualna platformy Azure IaaS granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Przy użyciu szyfrowania dysków Azure do szyfrowania dysków używanych przez maszyny wirtualne](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroki** | <p>Szyfrowanie dysków Azure to nowa funkcja, która jest obecnie w przeglądzie. Ta funkcja umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszyny wirtualne IaaS. W systemie Windows dyski są szyfrowane za pomocą technologii szyfrowania BitLocker standardowych. Dla systemu Linux dyski są szyfrowane za pomocą technologii DM-Crypt. To jest zintegrowany z usługą Azure Key Vault, co pozwala na kontrolowanie i zarządzać kluczami szyfrowania dysku. Rozwiązania szyfrowania dysków Azure obsługuje następujące scenariusze szyfrowania trzy klienta:</p><ul><li>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie szyfrowane przez klienta plików VHD i kluczy szyfrowania klienta, które są przechowywane w usłudze Azure Key Vault.</li><li>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone w witrynie Azure Marketplace.</li><li>Włącz szyfrowanie dla istniejących maszyn wirtualnych IaaS już uruchomione na platformie Azure.</li></ul>| 

## <a id="fabric-apps"></a>Szyfrowanie kluczy tajnych w aplikacji sieci szkieletowej usług

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granic zaufania sieci szkieletowej usług | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Zarządzanie kluczy tajnych w aplikacji sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroki** | Klucze tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. Magazyn kluczy Azure umożliwia zarządzanie kluczy i kluczy tajnych w aplikacji sieci szkieletowej usług. |

## <a id="modeling-teams"></a>Wykonaj modelowania zabezpieczeń i użyć zespołów jednostki biznesowe w przypadku, gdy wymagane

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj modelowania zabezpieczeń i użyć zespołów jednostki biznesowe w przypadku, gdy wymagane |

## <a id="entities"></a>Minimalizowanie dostęp do udziału funkcji na jednostkach krytyczne

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Minimalizowanie dostęp do udziału funkcji na jednostkach krytyczne |

## <a id="good-practices"></a>Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i dobrej rozwiązania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i dobrej rozwiązania |

## <a id="exception-mgmt"></a>Programowanie regułę standardy, proscribing konfiguracji ze szczegółowymi informacjami w Zarządzanie wyjątkami dołączania

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Obejmują reguły standardów programowanie proscribing konfiguracji ze szczegółowymi informacjami w Zarządzanie wyjątkami poza programowanie. Testowanie jako część przeglądy kodu lub okresowy.|

## <a id="sse-preview"></a>Użyj szyfrowanie usługi Magazyn Azure (SSE) dla danych magazynowanych (wersja zapoznawcza)

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Szyfrowanie usługi Magazyn Azure dla danych magazynowanych (wersja zapoznawcza)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroki** | <p>Azure magazynu usługi szyfrowania (SSE) dla danych magazynowanych pomaga chronić i ochrony danych w celu spełnienia organizacji bezpieczeństwa i zgodności zobowiązań. Przy użyciu tej funkcji usługi Magazyn Azure szyfruje dane przed wprowadzeniem trwałych do magazynu i automatycznie odszyfrowuje przed pobierania. Szyfrowania, odszyfrowywania i zarządzania kluczami jest całkowicie niewidoczne dla użytkowników. SSE dotyczy tylko blokowe obiekty BLOB, stronicowe obiekty BLOB i uzupełnialnych obiektów blob. Typy danych, w tym tabel, kolejek i plików, nie będą szyfrowane.</p><p>Szyfrowanie i odszyfrowywanie przepływu pracy:</p><ul><li>Klient włącza szyfrowanie na konto magazynu</li><li>Gdy klient zapisuje nowych danych (umieszczanie obiektu Blob, umieść bloku, umieść stronę itp.) do magazynu obiektów Blob; każdego zapisu jest szyfrowana przy użyciu szyfrowania AES 256-bitowego jednego najwyższy dostępny szyfrów bloku</li><li>Gdy klient musi mieć dostęp do danych (Pobierz obiekt Blob itp.), dane są automatycznie odszyfrowane przed powrotem do użytkownika</li><li>Jeśli szyfrowanie jest wyłączone, nowe zapisy nie są szyfrowane i zaszyfrowane dane pozostaną zaszyfrowane, dopóki nie ulegną przez użytkownika. Gdy włączone jest szyfrowanie, będą szyfrowane zapisuje dane w magazynie obiektów Blob. Nie zmienia stanu danych użytkownika przełączania się między włączenie/wyłączenie szyfrowania dla konta magazynu</li><li>Wszystkie klucze szyfrowania są przechowywane, szyfrowane i zarządzany przez firmę Microsoft</li></ul><p>Należy pamiętać, że w tym momencie, klucze szyfrowania są zarządzane przez firmę Microsoft. Firma Microsoft generuje klucze pierwotnie i zarządzanie bezpieczny magazyn kluczy, a także regularne obrotu zgodnie z definicją w wewnętrznych zasad firmy Microsoft. W przyszłości, klienci otrzymają zdolność do zarządzania własnych > klucze szyfrowania i podaj ścieżkę migracji z kluczy zarządzany przez firmę Microsoft do kluczy zarządzany przez klienta.</p>| 

## <a id="client-storage"></a>Użyj szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Szyfrowanie po stronie klienta i usługi Azure Key Vault dla magazynu Microsoft Azure](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [samouczek: szyfrowanie i odszyfrowywanie obiektów blob w magazynie platformy Microsoft Azure przy użyciu usługi Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [bezpieczne przechowywanie danych w magazynie obiektów Blob Azure z rozszerzeniami szyfrowania Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroki** | <p>Biblioteka klienta magazynu Azure dla pakietu Nuget programu .NET obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage i odszyfrowywania danych podczas pobierania do klienta. Biblioteka obsługuje również integrację z magazynem kluczy Azure do zarządzania kluczami konta magazynu. Poniżej przedstawiono krótki opis działania szyfrowania po stronie klienta:</p><ul><li>Klient usługi Azure Storage SDK generuje klucz szyfrowania zawartości (CEK), jest jeden jednorazowej użycia klucza symetrycznego</li><li>Dane klienta są zaszyfrowane za pomocą tego CEK</li><li>CEK jest następnie opakowane (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i można parę klucza asymetrycznego lub klucza symetrycznego i można być zarządzany lokalnie lub przechowywane w usłudze Azure Key Vault. Klienta magazynu nigdy nie ma dostępu do klucza KEK. Wywołuje po prostu algorytm klucza zawijania zapewnianej przez usługi Key Vault. Klientów można wybrać opcję użycia niestandardowych dostawców dla zawijania/odkodowywania chcący klucza</li><li>Zaszyfrowane dane są następnie przekazywane do usługi Magazyn Azure. Sprawdź łącza w sekcji odwołań dla szczegóły implementacji niskiego poziomu.</li></ul>|

## <a id="pii-phones"></a>Szyfrowanie liter lub dane osobowe dane zapisywane w lokalnej pamięci masowej telefonów

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klientów urządzeń przenośnych | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Generic, Xamarin  |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zarządzanie ustawieniami i funkcjami na urządzeniach przy użyciu zasad usługi Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies#create-a-configuration-policy), [Valet łańcucha kluczy](https://components.xamarin.com/view/square.valet) |
| **Kroki** | <p>Jeśli aplikacja zapisuje informacje poufne, na przykład PII użytkownika (poczty e-mail, numer telefonu, imię, nazwisko, preferencje itp.) — w systemie plików na telefon komórkowy, następnie go powinny być szyfrowane przed ich zapisaniem w lokalnym systemie plików. Jeśli aplikacja jest aplikacja przedsiębiorstwa, skorzystaj z możliwości publikowania aplikacji za pomocą usługi Windows Intune.</p>|

### <a name="example"></a>Przykład
Usługa Intune można skonfigurować następujące zasady zabezpieczeń w celu ochrony danych poufnych: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Przykład
Jeśli aplikacja nie jest aplikacja przedsiębiorstwa, następnie za pomocą magazynu kluczy platformy, pod warunkiem, może być wykonana pęki kluczy w celu przechowywania kluczy szyfrowania, przy użyciu których operacji kryptograficznych w systemie plików. Poniższy fragment kodu przedstawia sposób dostępu do klucza z łańcucha kluczy za pomocą platformy xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a id="binaries-end"></a>Zasłaniają wygenerowanych plików binarnych przed rozpoczęciem dystrybuowania użytkownikom końcowym

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klientów urządzeń przenośnych | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zaciemnienie kryptograficznych dla platformy .net](http://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroki** | Zatrzymać odtwarzanie zestawów powinny być zaciemniona wygenerowanych plików binarnych (zestawy w apk). Narzędzi, takich jak `CryptoObfuscator` mogą być używane w tym celu. |

## <a id="cert"></a>Ustaw właściwość clientCredentialType certyfikatu lub systemu Windows

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uzyskania zawartości](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Kroki** | Przy użyciu UsernameToken za pomocą hasła w postaci zwykłego tekstu przez kanał niezaszyfrowany przedstawia hasło, aby osoby atakujące, które można wyśledzić wiadomości SOAP. Dostawcy usług, używanego UsernameToken może zaakceptować wysyłania haseł w postaci zwykłego tekstu. Wysyłanie hasła w postaci zwykłego tekstu przez kanał niezaszyfrowany mogą uwidaczniać poświadczenia, aby osoby atakujące, które można wyśledzić komunikatu protokołu SOAP. | 

### <a name="example"></a>Przykład
UsernameToken korzysta z następujących konfiguracji dostawcy usługi WCF: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ustaw właściwość clientCredentialType certyfikatu lub systemu Windows. 

## <a id="security"></a>Nie jest włączony tryb zabezpieczeń programu WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, .NET Framework 3 |
| **Atrybuty**              | Zabezpieczenia trybu — transportu, trybu zabezpieczeń — komunikat |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/vulncat/index.html), [Magazine kodu podstawowe informacje na temat zabezpieczeń programu WCF](http://www.codemag.com/article/0611051) |
| **Kroki** | Brak zabezpieczeń transportu lub komunikat został zdefiniowany. Aplikacje, które przesyłania komunikatów zabezpieczeń nie może zagwarantować integralności i poufności komunikatów lub komunikatów bez transportu. Powiązania WCF zabezpieczeń ma wartość None, zabezpieczenia transportowe i wiadomości są wyłączone. |

### <a name="example"></a>Przykład
Następująca konfiguracja ustawia tryb zabezpieczeń None. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Przykład
Tryb zabezpieczeń we wszystkich powiązań usługi są pięć tryby możliwe zabezpieczeń: 
* Brak. Wyłącza zabezpieczeń. 
* Transport. Używa transportu zabezpieczeń do wzajemnego uwierzytelniania i komunikat ochrony. 
* Komunikat. Używa zabezpieczeń wiadomości do wzajemnego uwierzytelniania i komunikat ochrony. 
* Oba. Umożliwia określanie wartości ustawień dla transportu i zabezpieczenia na poziomie komunikatu (tylko usługi MSMQ obsługuje tę funkcję). 
* TransportWithMessageCredential. Poświadczenia są przekazywane z komunikatu i ochrony wiadomości i uwierzytelniania serwera są udostępniane przez warstwę transportu. 
* TransportCredentialOnly. Poświadczenia klienta są przekazywane z warstwy transportowej i stosowane jest Brak ochrony wiadomości. Użyć zabezpieczeń transportu i komunikat do ochrony integralności i poufności informacji. Konfiguracja poniżej informuje usługi, aby użyć zabezpieczeń transportu z poświadczeniami komunikatu.
```
<system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
</system.serviceModel> 
```
