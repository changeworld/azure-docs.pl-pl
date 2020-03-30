---
title: Poufne dane — narzędzie microsoft do modelowania zagrożeń — Azure | Dokumenty firmy Microsoft
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: ab22e9843ca133274361838eeb49abbe326588dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502232"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Ramka zabezpieczeń: Poufne dane | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne są zaciemnione, jeśli zawierają poufne informacje](#binaries-info)</li><li>[Rozważ użycie systemu szyfrowania plików (EFS) służy do ochrony poufnych danych specyficznych dla użytkownika](#efs-user)</li><li>[Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane](#filesystem)</li></ul> | 
| **Aplikacja internetowa** | <ul><li>[Upewnij się, że zawartość wrażliwa nie jest buforowana w przeglądarce](#cache-browser)</li><li>[Szyfrowanie sekcji plików konfiguracyjnych aplikacji Web App zawierających poufne dane](#encrypt-data)</li><li>[Jawnie wyłączaj atrybut HTML autouzupełniania w poufnych formularzach i danych wejściowych](#autocomplete-input)</li><li>[Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są maskowane](#data-mask)</li></ul> | 
| **baza danych** | <ul><li>[Wdrażanie dynamicznego maskowania danych w celu ograniczenia narażenia na poufne dane użytkowników nieuprzywilejowanych](#dynamic-users)</li><li>[Upewnij się, że hasła są przechowywane w formacie solonym skrótem](#salted-hash)</li><li>[Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane](#db-encrypted)</li><li>[Upewnij się, że szyfrowanie na poziomie bazy danych (TDE) jest włączone](#tde-enabled)</li><li>[Upewnij się, że kopie zapasowe bazy danych są szyfrowane](#backup)</li></ul> | 
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że poufne dane istotne dla interfejsu API sieci Web nie są przechowywane w pamięci masowej przeglądarki](#api-browser)</li></ul> | 
| Baza danych dokumentów platformy Azure | <ul><li>[Szyfrowanie poufnych danych przechowywanych w usłudze Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Granica zaufania maszyny Wirtualnej usługi Azure IaaS** | <ul><li>[Szyfrowanie dysków platformy Azure służy do szyfrowania dysków używanych przez maszyny wirtualne](#disk-vm)</li></ul> | 
| **Granica zaufania sieci usług** | <ul><li>[Szyfrowanie wpisów tajnych w aplikacjach sieci szkieletowej usług](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z jednostek/zespołów biznesowych](#modeling-teams)</li><li>[Minimalizowanie dostępu do funkcji udostępniania w krytycznych jednostkach](#entities)</li><li>[Szkolenie użytkowników w zakresie zagrożeń związanych z funkcją Dynamics CRM Share i dobrymi praktykami w zakresie bezpieczeństwa](#good-practices)</li><li>[Dołącz regułę standardów rozwoju, która zawiera informacje o szczegółach konfiguracji w zarządzaniu wyjątkami](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Użyj szyfrowania usługi Azure Storage Service (SSE) dla danych w spoczynku (wersja zapoznawcza)](#sse-preview)</li><li>[Używanie szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage](#client-storage)</li></ul> | 
| **Klient mobilny** | <ul><li>[Szyfrowanie poufnych lub umożliwiających identyfikację danych zapisanych w lokalnej pamięci masowej telefonów](#pii-phones)</li><li>[Zaciemnianie wygenerowanych plików binarnych przed dystrybucją do użytkowników końcowych](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ustawianie typu clientCredentialType na certyfikat lub system Windows](#cert)</li><li>[Tryb WCF-Security nie jest włączony](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Upewnij się, że pliki binarne są zaciemnione, jeśli zawierają poufne informacje

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że pliki binarne są zaciemnione, jeśli zawierają poufne informacje, takie jak tajemnice handlowe, poufne logiki biznesowej, które nie powinny być odwrócone. Ma to na celu zatrzymanie inżynierii odwrotnej zespołów. Narzędzia `CryptoObfuscator` takie jak mogą być używane do tego celu. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Rozważ użycie systemu szyfrowania plików (EFS) służy do ochrony poufnych danych specyficznych dla użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Należy rozważyć użycie systemu szyfrowania plików (EFS) służy do ochrony poufnych danych specyficznych dla użytkownika przed przeciwnikami z fizycznym dostępem do komputera. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane (np. przy użyciu DPAPI), jeśli nie można wymusić systemu szyfrowania plików |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Upewnij się, że zawartość wrażliwa nie jest buforowana w przeglądarce

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, Formularze internetowe, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Przeglądarki mogą przechowywać informacje w celu buforowania i historii. Te buforowane pliki są przechowywane w folderze, takim jak folder Tymczasowe pliki internetowe w przypadku programu Internet Explorer. Gdy te strony są odsyłane ponownie, przeglądarka wyświetla je z pamięci podręcznej. Jeśli użytkownikowi są wyświetlane poufne informacje (takie jak adres, dane karty kredytowej, numer ubezpieczenia społecznego lub nazwa użytkownika), informacje te mogą być przechowywane w pamięci podręcznej przeglądarki, a zatem można je pobrać poprzez sprawdzenie pamięci podręcznej przeglądarki lub po prostu naciskając przycisk "Wstecz" przeglądarki. Ustaw wartość nagłówka odpowiedzi kontroli pamięci podręcznej na "no-store" dla wszystkich stron. |

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
Może to zostać zaimplementowane za pomocą filtru. Można użyć następującego przykładu: 
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

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Szyfrowanie sekcji plików konfiguracyjnych aplikacji Web App zawierających poufne dane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Jak: Szyfrowanie sekcji konfiguracji w ASP.NET 2.0 Za pomocą DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Określanie dostawcy konfiguracji chronionej](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Używanie usługi Azure Key Vault do ochrony wpisów tajnych aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracyjne, takie jak Web.config, appsettings.json są często używane do przechowywania poufnych informacji, w tym nazw użytkowników, haseł, ciągów połączeń bazy danych i kluczy szyfrowania. Jeśli te informacje nie będą chronione, aplikacja jest narażona na osoby atakujące lub złośliwych użytkowników uzyskujących poufne informacje, takie jak nazwy użytkowników kont i hasła, nazwy baz danych i nazwy serwerów. Na podstawie typu wdrożenia (azure/on-prem) zaszyfruj poufne sekcje plików konfiguracyjnych przy użyciu dpapi lub usług, takich jak usługa Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Jawnie wyłączaj atrybut HTML autouzupełniania w poufnych formularzach i danych wejściowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN: atrybut autouzupełnianie](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Korzystanie z autouzupełniania w HTML](https://msdn.microsoft.com/library/ms533032.aspx), Luka w [zabezpieczeniach związany z dezynfekcją HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [Autouzupełnianie.,znowu?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroki** | Atrybut autouzupełniania określa, czy formularz powinien być włączony czy wyłączony. Gdy autouzupełnienie jest włączone, przeglądarka automatycznie uzupełnia wartości na podstawie wartości wprowadzonych wcześniej przez użytkownika. Na przykład po wprowadzeniu nowej nazwy i hasła w formularzu i przesłaniu formularza przeglądarka pyta, czy hasło powinno zostać zapisane. Następnie, gdy formularz jest wyświetlany, nazwa i hasło są wypełniane automatycznie lub są wypełniane po wprowadzeniu nazwy. Osoba atakująca z dostępem lokalnym może uzyskać hasło zwykłego tekstu z pamięci podręcznej przeglądarki. Domyślnie autouzupełnienie jest włączone i musi być jawnie wyłączone. |

### <a name="example"></a>Przykład
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są maskowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Poufne dane, takie jak hasła, numery kart kredytowych, SSN itp. Ma to na celu uniemożliwienie nieautoryzowanemu personelowi dostępu do danych (np. hasła do surfowania po ramionach, personel pomocy technicznej przeglądający numery SSN użytkowników). Upewnij się, że te elementy danych nie są widoczne w postaci zwykłego tekstu i są odpowiednio maskowane. Należy o to zadbać, akceptując je jako wkład (np. input type="password") oraz wyświetlanie z powrotem na ekranie (np. wyświetlanie tylko ostatnich 4 cyfr numeru karty kredytowej). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Wdrażanie dynamicznego maskowania danych w celu ograniczenia narażenia na poufne dane użytkowników nieuprzywilejowanych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Sql Azure, OnPrem |
| **Atrybuty**              | Wersja SQL - Wersja V12, WERSJA SQL - MsSQL2016 |
| **Odwołania**              | [Dynamiczne maskowanie danych](https://msdn.microsoft.com/library/mt130841) |
| **Kroki** | Celem dynamicznego maskowania danych jest ograniczenie ekspozycji poufnych danych, uniemożliwiając użytkownikom, którzy nie powinni mieć dostępu do danych, ich wyświetlanie. Dynamiczne maskowanie danych nie ma na celu uniemożliwienia użytkownikom bazy danych łączenia się bezpośrednio z bazą danych i uruchamiania wyczerpujących zapytań, które ujawniają fragmenty poufnych danych. Dynamiczne maskowanie danych jest uzupełnieniem innych funkcji zabezpieczeń programu SQL Server (inspekcja, szyfrowanie, zabezpieczenia na poziomie wiersza...) i zdecydowanie zaleca się korzystanie z tej funkcji w połączeniu z nimi w celu lepszej ochrony poufnych danych w Bazy danych. Należy pamiętać, że ta funkcja jest obsługiwana tylko przez program SQL Server począwszy od 2016 i usługi Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Upewnij się, że hasła są przechowywane w formacie solonym skrótem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Haszowanie haseł przy użyciu interfejsów API kryptograficznego platformy .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroki** | Hasła nie powinny być przechowywane w niestandardowych bazach danych magazynu użytkowników. Skróty haseł powinny być przechowywane z wartościami soli. Upewnij się, że sól dla użytkownika jest zawsze unikatowa i zastosujesz b-crypt, s-crypt lub PBKDF2 przed przechowywaniem hasła, z minimalną liczbą iteracji współczynnika pracy 150 000 pętli, aby wyeliminować możliwość brutalnego wymuszania.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wersja SQL - Wszystkie |
| **Odwołania**              | [Szyfrowanie poufnych danych na serwerze SQL](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [Jak: Szyfrowanie kolumny danych w programie SQL Server](https://msdn.microsoft.com/library/ms179331), [Szyfrowanie według certyfikatu](https://msdn.microsoft.com/library/ms188061) |
| **Kroki** | Poufne dane, takie jak numery kart kredytowych, muszą być szyfrowane w bazie danych. Dane mogą być szyfrowane przy użyciu szyfrowania na poziomie kolumny lub przez funkcję aplikacji przy użyciu funkcji szyfrowania. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Upewnij się, że szyfrowanie na poziomie bazy danych (TDE) jest włączone

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Opis szyfrowania danych przezroczystych programu SQL Server (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroki** | Funkcja przezroczystego szyfrowania danych (TDE) na serwerze SQL pomaga w szyfrowaniu poufnych danych w bazie danych i chroni klucze, które są używane do szyfrowania danych za pomocą certyfikatu. Zapobiega to nikomu bez kluczy z wykorzystaniem danych. TDE chroni dane "w spoczynku", czyli pliki danych i dzienników. Zapewnia możliwość przestrzegania wielu przepisów ustawowych, wykonawczych i wytycznych ustanowionych w różnych branżach. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Upewnij się, że kopie zapasowe bazy danych są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, OnPrem |
| **Atrybuty**              | Wersja SQL - Wersja V12, WERSJA SQL - MsSQL2014 |
| **Odwołania**              | [Szyfrowanie kopii zapasowych bazy danych SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroki** | SQL Server ma możliwość szyfrowania danych podczas tworzenia kopii zapasowej. Określając algorytm szyfrowania i szyfrujący (certyfikat lub klucz asymetryczny) podczas tworzenia kopii zapasowej, można utworzyć zaszyfrowany plik kopii zapasowej. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Upewnij się, że poufne dane istotne dla interfejsu API sieci Web nie są przechowywane w pamięci masowej przeglądarki

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5, MVC 6 |
| **Atrybuty**              | Dostawca tożsamości — usługa ADFS, dostawca tożsamości — usługa Azure AD |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>W niektórych implementacjach poufne artefakty istotne dla uwierzytelniania interfejsu API sieci Web są przechowywane w magazynie lokalnym przeglądarki. Na przykład artefakty uwierzytelniania usługi Azure AD, takie jak adal.idtoken, adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key itp.</p><p>Wszystkie te artefakty są dostępne nawet po wylogowywanie lub zamknięcie przeglądarki. Jeśli przeciwnik uzyska dostęp do tych artefaktów, może użyć ich ponownie, aby uzyskać dostęp do chronionych zasobów (interfejsów API). Upewnij się, że wszystkie poufne artefakty związane z interfejsem API sieci Web nie są przechowywane w magazynie przeglądarki. W przypadkach, gdy magazyn po stronie klienta jest nieuniknione (np. aplikacje jednostronicowe (SPA), które wykorzystują niejawne przepływy OpenIdConnect/OAuth muszą przechowywać tokeny dostępu lokalnie), użyj opcji magazynu z nie mają trwałości. na przykład, preferuj SessionStorage od LocalStorage.</p>| 

### <a name="example"></a>Przykład
Poniższy fragment kodu JavaScript pochodzi z niestandardowej biblioteki uwierzytelniania, która przechowuje artefakty uwierzytelniania w magazynie lokalnym. Należy unikać takich implementacji. 
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

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Szyfrowanie poufnych danych przechowywanych w usłudze Cosmos DB

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Szyfrowanie poufnych danych na poziomie aplikacji przed przechowywaniem w bazy danych dokumentu lub przechowywanie poufnych danych w innych rozwiązaniach magazynu, takich jak Usługa Azure Storage lub Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Szyfrowanie dysków platformy Azure służy do szyfrowania dysków używanych przez maszyny wirtualne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny Wirtualnej usługi Azure IaaS | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Szyfrowanie dysków platformy Azure do szyfrowania dysków używanych przez maszyny wirtualne](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroki** | <p>Szyfrowanie dysków platformy Azure to nowa funkcja, która jest obecnie w wersji zapoznawczej. Ta funkcja umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS. W systemie Windows dyski są szyfrowane przy użyciu standardowej w branży technologii szyfrowania funkcją BitLocker. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Jest to zintegrowane z usługą Azure Key Vault, aby umożliwić kontrolowanie kluczy szyfrowania dysku i zarządzanie nimi. Rozwiązanie szyfrowania dysków Azure obsługuje następujące trzy scenariusze szyfrowania klienta:</p><ul><li>Włącz szyfrowanie na nowych maszynach wirtualnych IaaS utworzonych na podstawie zaszyfrowanych przez klienta plików VHD i kluczy szyfrowania dostarczonych przez klienta, które są przechowywane w usłudze Azure Key Vault.</li><li>Włącz szyfrowanie na nowych maszynach wirtualnych IaaS utworzonych na podstawie portalu Azure Marketplace.</li><li>Włącz szyfrowanie na istniejących maszynach wirtualnych IaaS już uruchomionych na platformie Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Szyfrowanie wpisów tajnych w aplikacjach sieci szkieletowej usług

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania sieci usług | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Platforma Azure |
| **Odwołania**              | [Zarządzanie wpisami tajnymi w aplikacjach sieci szkieletowej usług](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroki** | Wpisy tajne mogą być wszelkie poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. Usługa Azure Key Vault umożliwia zarządzanie kluczami i wpisami tajnymi w aplikacjach sieci szkieletowej usług. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z jednostek/zespołów biznesowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | W razie potrzeby przeprowadzaj modelowanie zabezpieczeń i korzystaj z jednostek/zespołów biznesowych |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimalizowanie dostępu do funkcji udostępniania w krytycznych jednostkach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Minimalizowanie dostępu do funkcji udostępniania w krytycznych jednostkach |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Szkolenie użytkowników w zakresie zagrożeń związanych z funkcją Dynamics CRM Share i dobrymi praktykami w zakresie bezpieczeństwa

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Szkolenie użytkowników w zakresie zagrożeń związanych z funkcją Dynamics CRM Share i dobrymi praktykami w zakresie bezpieczeństwa |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Dołącz regułę standardów rozwoju, która zawiera informacje o szczegółach konfiguracji w zarządzaniu wyjątkami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | wdrażania |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Dołącz regułę standardów rozwoju, która zawiera informacje o szczegółach konfiguracji w zarządzaniu wyjątkami poza programowaniem. Przetestuj to w ramach przeglądów kodu lub okresowej kontroli.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Użyj szyfrowania usługi Azure Storage Service (SSE) dla danych w spoczynku (wersja zapoznawcza)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ magazynu — obiekt blob |
| **Odwołania**              | [Szyfrowanie usługi Azure Storage dla danych w spoczynku (wersja zapoznawcza)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroki** | <p>Szyfrowanie usługi Azure Storage Service (SSE) dla danych w spoczynku pomaga chronić i chronić dane, aby spełnić zobowiązania dotyczące zabezpieczeń i zgodności organizacji. Przy użyciu tej funkcji usługa Azure Storage automatycznie szyfruje dane przed trwałym wprowadzeniem ich do magazynu i odszyfrowuje je przed pobraniem. Szyfrowanie, odszyfrowywanie i zarządzanie kluczami jest całkowicie przejrzyste dla użytkowników. SSE ma zastosowanie tylko do blokować obiekty blob, stronicowe obiekty blob i dołączać obiekty blob. Inne typy danych, w tym tabele, kolejki i pliki, nie będą szyfrowane.</p><p>Przepływ pracy szyfrowania i odszyfrowywania:</p><ul><li>Klient umożliwia szyfrowanie na koncie magazynu</li><li>Gdy klient zapisuje nowe dane (PUT Blob, PUT Block, PUT Page, itp.) do magazynu obiektów Blob; każdy zapis jest szyfrowany przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych dostępnych szyfrów blokowych</li><li>Gdy klient musi uzyskać dostęp do danych (GET Blob itp.), dane są automatycznie odszyfrowywane przed powrotem do użytkownika</li><li>Jeśli szyfrowanie jest wyłączone, nowe zapisy nie są już szyfrowane, a istniejące zaszyfrowane dane pozostają zaszyfrowane do momentu ich ponownego zapisania przez użytkownika. Gdy szyfrowanie jest włączone, zapisy w magazynie obiektów Blob będą szyfrowane. Stan danych nie zmienia się wraz z przełączaniem użytkownika między włączaniem/wyłączaniem szyfrowania dla konta magazynu</li><li>Wszystkie klucze szyfrowania są przechowywane, szyfrowane i zarządzane przez firmę Microsoft</li></ul><p>Należy pamiętać, że obecnie klucze używane do szyfrowania są zarządzane przez firmę Microsoft. Firma Microsoft generuje klucze pierwotnie i zarządza bezpiecznym magazynem kluczy, a także regularną rotacją zgodnie z definicją wewnętrzną zasadą firmy Microsoft. W przyszłości klienci otrzymają możliwość zarządzania własnymi kluczami szyfrowania >i udostępnią ścieżkę migracji z kluczy zarządzanych przez firmę Microsoft do kluczy zarządzanych przez klienta.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Używanie szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [samouczek: szyfrowanie i odszyfrowywanie obiektów blob w usłudze Microsoft Azure Storage przy użyciu usługi Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), bezpieczne przechowywanie danych w [usłudze Azure Blob Storage z rozszerzeniami szyfrowania platformy Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroki** | <p>Pakiet Azure Storage Client Library for .NET Nuget obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage i odszyfrowywanie danych podczas pobierania do klienta. Biblioteka obsługuje również integrację z magazynem kluczy Azure do zarządzania kluczami konta magazynu. Oto krótki opis działania szyfrowania po stronie klienta:</p><ul><li>Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest kluczem symetrycznym jednorazowym</li><li>Dane klienta są szyfrowane przy użyciu tego CEK</li><li>CEK jest następnie zawijany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być asymetryczną parą kluczy lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w usłudze Azure Key Vault. Sam klient magazynu nigdy nie ma dostępu do KEK. Po prostu wywołuje algorytm zawijania kluczy, który jest dostarczany przez magazyn klucza. Klienci mogą wybrać niestandardowe dostawców do zawijania kluczy / rozpakowywania, jeśli chcą</li><li>Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Sprawdź łącza w sekcji odwołania, aby uzyskać szczegółowe informacje o implementacji niskiego poziomu.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Szyfrowanie poufnych lub umożliwiających identyfikację danych zapisanych w lokalnej pamięci masowej telefonów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, Xamarin  |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zarządzanie ustawieniami i funkcjami na urządzeniach za pomocą zasad usługi Microsoft Intune](https://docs.microsoft.com/mem/intune/configuration/), [Kluczyk valet](https://components.xamarin.com/view/square.valet) |
| **Kroki** | <p>Jeśli aplikacja zapisuje poufne informacje, takie jak informacje umożliwiające użytkownika (adres e-mail, numer telefonu, imię, nazwisko, preferencje itp.) - w mobilnym systemie plików, należy go zaszyfrować przed zapisaniem do lokalnego systemu plików. Jeśli aplikacja jest aplikacją dla przedsiębiorstw, zapoznaj się z możliwością publikowania aplikacji przy użyciu usługi Windows Intune.</p>|

### <a name="example"></a>Przykład
Usługę Intune można skonfigurować za pomocą następujących zasad zabezpieczeń w celu ochrony poufnych danych: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Przykład
Jeśli aplikacja nie jest aplikacją dla przedsiębiorstw, użyj magazynu kluczy dostarczonych przez platformę, kluczy do przechowywania kluczy szyfrowania, przy użyciu operacji kryptograficznej, która może być wykonywana w systemie plików. Poniższy fragment kodu pokazuje, jak uzyskać dostęp do klucza z pęku kluczy przy użyciu platformy xamarin: 
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

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Zaciemnianie wygenerowanych plików binarnych przed dystrybucją do użytkowników końcowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Crypto Zaciemnienie dla .Net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroki** | Wygenerowane pliki binarne (zestawy w apk) powinny być zaciemnione, aby zatrzymać inżynierię odwrotną zestawów. Narzędzia `CryptoObfuscator` takie jak mogą być używane do tego celu. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Ustawianie typu clientCredentialType na certyfikat lub system Windows

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wzmocnić](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroki** | Za pomocą UsernameToken z hasłem w postaci zwykłego tekstu na niezaszyfrowanym kanale udostępnia hasło do atakujących, którzy mogą wąchać wiadomości PROTOKOŁU SOAP. Dostawcy usług korzystający z usernameToken mogą akceptować hasła wysyłane w postaci zwykłego tekstu. Wysyłanie haseł w postaci zwykłego tekstu za pomocą niezaszyfrowanego kanału może narazić poświadczenia na osoby atakujące, które mogą wąchać komunikat SOAP. | 

### <a name="example"></a>Przykład
Następująca konfiguracja dostawcy usług WCF używa usernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ustaw clientCredentialType na certyfikat lub system Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>Tryb WCF-Security nie jest włączony

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, .NET Framework 3 |
| **Atrybuty**              | Tryb zabezpieczeń — transport, tryb zabezpieczeń — komunikat |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Kingdom](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [Podstawy WCF Security CoDe Magazine](https://www.codemag.com/article/0611051) |
| **Kroki** | Nie zdefiniowano zabezpieczeń transportu ani wiadomości. Aplikacje, które przesyłają wiadomości bez zabezpieczeń transportu lub wiadomości, nie mogą zagwarantować integralności lub poufności wiadomości. Gdy powiązanie zabezpieczeń WCF jest ustawiona na Brak, zabezpieczenia transportu i wiadomości są wyłączone. |

### <a name="example"></a>Przykład
Następująca konfiguracja ustawia tryb zabezpieczeń na Brak. 
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
Tryb zabezpieczeń We wszystkich powiązaniach usługi dostępnych jest pięć możliwych trybów zabezpieczeń: 
* Brak. Wyłącza zabezpieczenia. 
* Transportu. Używa zabezpieczeń transportu do wzajemnego uwierzytelniania i ochrony wiadomości. 
* Komunikat. Używa zabezpieczeń wiadomości do wzajemnego uwierzytelniania i ochrony wiadomości. 
* Zarówno. Umożliwia podanie ustawień zabezpieczeń transportu i zabezpieczeń na poziomie wiadomości (obsługuje to tylko usługa MSMQ). 
* Transportwithmessagecredential. Poświadczenia są przekazywane z ochrony wiadomości i wiadomości i uwierzytelniania serwera są dostarczane przez warstwę transportu. 
* TransportCredentialOnly. Poświadczenia klienta są przekazywane z warstwą transportu i nie jest stosowana ochrona wiadomości. Użyj zabezpieczeń transportu i wiadomości, aby chronić integralność i poufność wiadomości. Poniższa konfiguracja informuje usługę, aby używać zabezpieczeń transportu z poświadczeniami wiadomości.
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
