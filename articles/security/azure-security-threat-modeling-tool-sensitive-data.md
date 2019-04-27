---
title: Dane poufne — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
description: środki zaradcze dotyczące zagrożeń, widoczne w narzędziu do modelowania zagrożeń
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.openlocfilehash: 27028903daeaf62a25584300944538341a861c80
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60610563"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Ramka zabezpieczeń: Danych poufnych | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Maszyny granicy zaufania** | <ul><li>[Upewnij się, że pliki binarne są zaciemniony plikach, jeśli zawierają one informacje poufne](#binaries-info)</li><li>[Należy wziąć pod uwagę, że za pomocą systemu szyfrowania plików (EFS) jest używany do ochrony danych poufnych specyficzne dla użytkownika](#efs-user)</li><li>[Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany](#filesystem)</li></ul> | 
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, że poufnej zawartości nie jest buforowana w przeglądarce](#cache-browser)</li><li>[Szyfruj sekcje pliki konfiguracji aplikacji sieci Web, które zawierają dane poufne](#encrypt-data)</li><li>[Jawnie Wyłącz Autouzupełnianie atrybutu HTML w formularzach poufnych i dane wejściowe](#autocomplete-input)</li><li>[Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są wyświetlane jako znaki](#data-mask)</li></ul> | 
| **Baza danych** | <ul><li>[Implementowanie dynamiczne maskowanie danych Aby ograniczyć użytkowników bez ujawniania uprzywilejowany poufnych danych](#dynamic-users)</li><li>[Upewnij się, że hasła są przechowywane w formacie solone wyznaczania wartości skrótu](#salted-hash)</li><li>[Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane](#db-encrypted)</li><li>[Upewnij się, że włączone jest szyfrowanie tego poziomu bazy danych (TDE)](#tde-enabled)</li><li>[Upewnij się, że są szyfrowane kopie zapasowe bazy danych](#backup)</li></ul> | 
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że dane poufne, które są istotne dla interfejsu API sieci Web nie są przechowywane w pamięci masowej w przeglądarce](#api-browser)</li></ul> | 
| Azure Document DB | <ul><li>[Szyfruj poufne dane przechowywane w usłudze Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Granicy zaufania maszyn wirtualnych IaaS platformy Azure** | <ul><li>[Usługa Azure Disk Encryption umożliwia szyfrowanie dysków używanych przez maszyny wirtualne](#disk-vm)</li></ul> | 
| **Granicy zaufania usługi Service Fabric** | <ul><li>[Szyfrowanie kluczy tajnych w aplikacji usługi Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Wykonaj bezpieczeństwa modelowania i użyj zespoły jednostki biznesowe w przypadku, gdy wymagane](#modeling-teams)</li><li>[Minimalizacja dostępu, aby udostępnić funkcję na jednostkach krytyczne](#entities)</li><li>[Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i rozwiązania w zakresie zabezpieczeń](#good-practices)</li><li>[Obejmują reguły standardy programowania proscribing przedstawiający szczegóły konfiguracji w Zarządzanie wyjątkami](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Użyj usługi Azure Storage Service Encryption (SSE) dla danych magazynowanych (wersja zapoznawcza)](#sse-preview)</li><li>[Użyj szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage](#client-storage)</li></ul> | 
| **Mobile Client** | <ul><li>[Szyfruj poufne lub dane osobowe dane zapisane w magazynie lokalnym telefonów](#pii-phones)</li><li>[Zaciemniania wygenerowanych plików binarnych, przed rozpoczęciem dystrybuowania dla użytkowników końcowych](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ustaw właściwości ClientCredentialType o wartości certyfikatu lub Windows](#cert)</li><li>[Nie jest włączony tryb zabezpieczeń programu WCF](#security)</li></ul> | 

## <a id="binaries-info"></a>Upewnij się, że pliki binarne są zaciemniony plikach, jeśli zawierają one informacje poufne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że pliki binarne są zaciemniony plikach, jeśli zawierają one poufne informacje, takie jak tajemnice handlowe, logiki biznesowej poufnych, które należy wycofać nie. To, aby zatrzymać odtwarzanie zestawów. Narzędzia takie jak `CryptoObfuscator` mogą być używane do tego celu. |

## <a id="efs-user"></a>Należy wziąć pod uwagę, że za pomocą systemu szyfrowania plików (EFS) jest używany do ochrony danych poufnych specyficzne dla użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Należy wziąć pod uwagę, że za pomocą systemu szyfrowania plików (EFS) jest używany do chronić poufne dane specyficzne dla użytkownika z przeciwników mających fizyczny dostęp do komputera. |

## <a id="filesystem"></a>Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Maszyny granicy zaufania | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików jest zaszyfrowany (np. przy użyciu interfejsu DPAPI), jeśli nie można wymusić systemu szyfrowania plików |

## <a id="cache-browser"></a>Upewnij się, że poufnej zawartości nie jest buforowana w przeglądarce

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, Web Forms, MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Przeglądarki może przechowywać informacje na potrzeby buforowania i historię. Te buforowane pliki są przechowywane w folderze, takich jak folderu tymczasowych plików internetowych, w przypadku programu Internet Explorer. Strony te są ponownie, określonym w przeglądarce pojawi się je z pamięci podręcznej. Jeśli poufnych informacji jest wyświetlany użytkownikowi (na przykład ich adres karty kredytowej, numer ubezpieczenia społecznego i nazwa użytkownika), następnie tych informacji może być przechowywane w pamięci podręcznej przeglądarki i w związku z tym pobieranie poprzez badanie pamięci podręcznej przeglądarki lub za pomocą Wystarczy nacisnąć przycisk "Wstecz", w przeglądarce. Ustaw wartość nagłówka cache-control w odpowiedzi do "no-store" dla wszystkich stron. |

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
Może to być implementowane za pomocą filtru. Poniższy przykład może zostać użyty: 
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

## <a id="encrypt-data"></a>Szyfruj sekcje pliki konfiguracji aplikacji sieci Web, które zawierają dane poufne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Instrukcje: Szyfrowanie sekcji konfiguracyjnych w ASP.NET 2.0 przy użyciu interfejsu DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Określanie dostawcę konfiguracji chronionych](https://msdn.microsoft.com/library/68ze1hb2.aspx), [przy użyciu usługi Azure Key Vault do ochrony wpisu tajnego aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracji, takie jak pliku Web.config, appsettings.json są często używane do przechowywania informacji poufnych, w tym nazwy użytkownika, hasła, parametry połączenia bazy danych i kluczy szyfrowania. Jeśli te informacje nie jest chroniony, aplikacja jest narażony na ataki lub złośliwym użytkownikom uzyskania poufne informacje, takie jak nazwy użytkownika konta i hasła, nazwy baz danych i serwerów nazw. Oparte na dany typ wdrożenia (azure/lokalnych), szyfrowanie poufnych części plików konfiguracji przy użyciu interfejsu DPAPI lub usług, takich jak usługi Azure Key Vault. |

## <a id="autocomplete-input"></a>Jawnie Wyłącz Autouzupełnianie atrybutu HTML w formularzach poufnych i dane wejściowe

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN: autouzupełniania atrybut](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [za pomocą automatycznego uzupełniania w języku HTML](https://msdn.microsoft.com/library/ms533032.aspx), [luki w zabezpieczeniach Sanityzacji HTML](https://technet.microsoft.com/security/bulletin/MS10-071), [Autouzupełnianie., ponownie?!](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroki** | Atrybut autouzupełniania Określa, czy formularz powinien mają autouzupełniania, lub wyłączyć. Gdy autouzupełniania jest włączona, przeglądarki automatycznego ukończenia wartości na podstawie wartości, które użytkownik wprowadził przed. Na przykład po wprowadzeniu nowej nazwy i hasła w postaci i przesłaniu formularza, przeglądarka zapyta, czy można zapisywać hasła. Następnie podczas wyświetlania formularza, nazwę i hasło są wypełniane automatycznie lub odbywa się zgodnie z jej nazwa jest wpisana. Osoba atakująca z lokalnym dostępem można uzyskać zwykłego tekstu w pamięci podręcznej przeglądarki. Autouzupełnianie jest domyślnie włączona i musi jawnie wyłączone. |

### <a name="example"></a>Przykład
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a id="data-mask"></a>Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są wyświetlane jako znaki

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Dane poufne, takie jak hasła, numerów kart kredytowych SSN itp. należy maskowane, gdy na ekranie. To uniemożliwia nieautoryzowanym próbom uzyskanie dostępu do danych (np. hasła ramię przeglądanie, personel pomocy technicznej, wyświetlając numer PESEL liczby użytkowników). Upewnij się, że te elementy danych nie są widoczne w postaci zwykłego tekstu i odpowiednio są maskowane. Należy uwzględnić podczas akceptowania je jako dane wejściowe (np. typu danych wejściowych = "password") oraz wyświetlanie na ekranie (np. wyświetlania tylko 4 ostatnie cyfry numeru karty kredytowej). |

## <a id="dynamic-users"></a>Implementowanie dynamiczne maskowanie danych Aby ograniczyć użytkowników bez ujawniania uprzywilejowany poufnych danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Sql Azure, OnPrem |
| **Atrybuty**              | MsSQL2016 wersja — wersja V12, wersja SQL — SQL |
| **Odwołania**              | [Dynamiczne maskowanie danych](https://msdn.microsoft.com/library/mt130841) |
| **Kroki** | Dynamiczne maskowanie danych ma na celu ograniczania ujawniania poufnych danych, co uniemożliwia użytkownikom, którzy nie powinni mieć dostęp do danych z wyświetlania go. Dynamiczne maskowanie danych nie ma na celu aby uniemożliwić użytkownikom bazy danych bezpośrednio z bazą danych i uruchamiania pełnego zapytań, które udostępniają elementy danych poufnych. Dynamiczne maskowanie danych jest uzupełnieniem inne funkcje zabezpieczeń programu SQL Server (inspekcja, szyfrowanie, zabezpieczenia na poziomie wiersza...) i zdecydowanie zaleca się korzystać z tej funkcji w połączeniu z ich także w celu zapewnienia lepszej ochrony danych poufnych w Baza danych. Należy pamiętać, że ta funkcja jest obsługiwana tylko przez program SQL Server, począwszy od 2016 i Azure SQL Database. |

## <a id="salted-hash"></a>Upewnij się, że hasła są przechowywane w formacie solone wyznaczania wartości skrótu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Hashing hasła przy użyciu interfejsów API kryptografii platformy .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroki** | Hasła nie powinien być przechowywany w użytkownika niestandardowego magazynu w bazach danych. Skróty haseł powinny być przechowywane przy użyciu wartości zaburzającej zamiast tego. Upewnij się, ziarna dla użytkownika jest zawsze unikatowa i zastosować b-crypt, s-crypt lub PBKDF2 przed zapisaniem hasło, wraz z liczbą pracy minimalny współczynnik iteracji 150 000 pętli, aby wyeliminować możliwości ataków siłowych.| 

## <a id="db-encrypted"></a>Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Wersja SQL — wszystkie |
| **Odwołania**              | [Szyfrowanie poufnych danych w programie SQL server](https://technet.microsoft.com/library/ff848751(v=sql.105).aspx), [jak: Szyfrowanie kolumny danych w programie SQL Server](https://msdn.microsoft.com/library/ms179331), [szyfrowania przy certyfikatu](https://msdn.microsoft.com/library/ms188061) |
| **Kroki** | Dane poufne, takie jak numer karty kredytowej musi być szyfrowane w bazie danych. Dane mogą być szyfrowane za pomocą szyfrowania na poziomie kolumny lub funkcję aplikacji za pomocą funkcji szyfrowania. |

## <a id="tde-enabled"></a>Upewnij się, że włączone jest szyfrowanie tego poziomu bazy danych (TDE)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Omówienie programu SQL Server Transparent Data Encryption (TDE)](https://technet.microsoft.com/library/bb934049(v=sql.105).aspx) |
| **Kroki** | Przezroczysty funkcji szyfrowania danych (TDE) w programie SQL server pomaga w szyfrowania poufnych danych w bazie danych i chronić klucze, które są używane do szyfrowania danych przy użyciu certyfikatu. Zapobiega to każda osoba, bez kluczy przy użyciu danych. Funkcja TDE chroni dane "w stanie spoczynku" oznacza plików danych i dziennika. Daje możliwość wykonania wielu prawem, regulacjami i wskazówkami w różnych branżach. |

## <a id="backup"></a>Upewnij się, że są szyfrowane kopie zapasowe bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | SQL Azure, OnPrem |
| **Atrybuty**              | MsSQL2014 wersja — wersja V12, wersja SQL — SQL |
| **Odwołania**              | [Szyfrowanie kopii zapasowej bazy danych SQL](https://msdn.microsoft.com/library/dn449489) |
| **Kroki** | Program SQL Server ma możliwość szyfrowania danych podczas tworzenia kopii zapasowej. Określając algorytm szyfrowania i modułu szyfrującego (certyfikatu lub klucza asymetrycznego) podczas tworzenia kopii zapasowej, można utworzyć zaszyfrowanego pliku kopii zapasowej. |

## <a id="api-browser"></a>Upewnij się, że dane poufne, które są istotne dla interfejsu API sieci Web nie są przechowywane w pamięci masowej w przeglądarce

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC 5, MVC 6 |
| **Atrybuty**              | Dostawca tożsamości dostawcę — usługi AD FS, tożsamości — usłudze Azure AD |
| **Odwołania**              | ND  |
| **Kroki** | <p>W niektórych implementacjach poufnych artefaktów odpowiednie do internetowego interfejsu API uwierzytelniania są przechowywane w magazynie lokalnym w przeglądarce. Np. artefakty uwierzytelniania usługi Azure AD, takie jak adal.idtoken adal.nonce.idtoken, adal.access.token.key, adal.token.keys, adal.state.login, adal.session.state, adal.expiration.key itp.</p><p>Wszystkie te artefakty są dostępne nawet po zakończeniu Wyloguj lub nastąpi zamknięcie okna przeglądarki. Jeśli osoba atakująca uzyskuje dostęp do tych artefaktów, osoba ta może korzystać z nich uzyskać dostęp do chronionych zasobów (API). Upewnij się, że wszystkich poufnych artefaktów związanych z interfejsu API sieci Web nie są przechowywane w magazynie w przeglądarce. W przypadku gdy magazyn klienta jest nieuniknione (np. jednej strony aplikacji (SPA) korzystających z przepływów niejawne OpenIdConnect/OAuth wymaga przechowywania tokenów dostępu lokalnie), użyj opcji magazynu przy użyciu nie mają stanu trwałego. np. wolą SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Przykład
Poniżej JavaScript fragment jest z biblioteki uwierzytelniania niestandardowego, który artefaktów uwierzytelniania są przechowywane w magazynie lokalnym. Należy unikać takich implementacji. 
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

## <a id="encrypt-docdb"></a>Szyfruj poufne dane przechowywane w usłudze Cosmos DB

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Document DB | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Szyfruj poufne dane na poziomie aplikacji przed zapisaniem w dokumencie bazy danych lub przechowywania poufnych danych w innych rozwiązaniach magazynu, takich jak usługa Azure Storage lub Azure SQL| 

## <a id="disk-vm"></a>Usługa Azure Disk Encryption umożliwia szyfrowanie dysków używanych przez maszyny wirtualne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania maszyn wirtualnych IaaS platformy Azure | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Do szyfrowania dysków używanych przez maszyny wirtualne przy użyciu usługi Azure Disk Encryption](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) |
| **Kroki** | <p>Usługa Azure Disk Encryption jest nową funkcję, która jest obecnie dostępna w wersji zapoznawczej. Ta funkcja umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS. Dla Windows dyski są szyfrowane przy użyciu technologii szyfrowania funkcją BitLocker będące standardami branżowymi. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Ta lokalizacja jest zintegrowana z usługą Azure Key Vault umożliwia kontrolowanie i zarządzać kluczami szyfrowania dysków. Rozwiązanie Azure Disk Encryption obsługuje następujące scenariusze szyfrowania trzech klientów:</p><ul><li>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone na podstawie szyfrowane klienta plików VHD i klucze szyfrowania dostarczone do klienta, które są przechowywane w usłudze Azure Key Vault.</li><li>Włącz szyfrowanie dla nowych maszyn wirtualnych IaaS utworzone w portalu Azure Marketplace.</li><li>Włącz szyfrowanie dla istniejących maszyn wirtualnych IaaS już działające na platformie Azure.</li></ul>| 

## <a id="fabric-apps"></a>Szyfrowanie kluczy tajnych w aplikacji usługi Service Fabric

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granicy zaufania usługi Service Fabric | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Środowisko — platformy Azure |
| **Odwołania**              | [Zarządzanie wpisami tajnymi w aplikacji usługi Service Fabric](https://azure.microsoft.com/documentation/articles/service-fabric-application-secret-management/) |
| **Kroki** | Wpisy tajne można poufne informacje, takie jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być traktowane w postaci zwykłego tekstu. Użyj usługi Azure Key Vault do zarządzania kluczami i wpisami tajnymi w aplikacje usługi Service fabric. |

## <a id="modeling-teams"></a>Wykonaj bezpieczeństwa modelowania i użyj zespoły jednostki biznesowe w przypadku, gdy wymagane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Wykonaj bezpieczeństwa modelowania i użyj zespoły jednostki biznesowe w przypadku, gdy wymagane |

## <a id="entities"></a>Minimalizacja dostępu, aby udostępnić funkcję na jednostkach krytyczne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Minimalizacja dostępu, aby udostępnić funkcję na jednostkach krytyczne |

## <a id="good-practices"></a>Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i rozwiązania w zakresie zabezpieczeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Szkolenie użytkowników na ryzyko związane z funkcji programu Dynamics CRM udziału i rozwiązania w zakresie zabezpieczeń |

## <a id="exception-mgmt"></a>Obejmują reguły standardy programowania proscribing przedstawiający szczegóły konfiguracji w Zarządzanie wyjątkami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Obejmują reguły standardy programowania proscribing przedstawiający szczegóły konfiguracji w Zarządzanie wyjątkami poza rozwoju. To sprawdzić w ramach przeglądów kodu lub okresowej kontroli.|

## <a id="sse-preview"></a>Użyj usługi Azure Storage Service Encryption (SSE) dla danych magazynowanych (wersja zapoznawcza)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Szyfrowanie usługi Azure Storage dla danych magazynowanych (wersja zapoznawcza)](https://azure.microsoft.com/documentation/articles/storage-service-encryption/) |
| **Kroki** | <p>Azure szyfrowanie usługi Storage (SSE) dla danych w spoczynku pomaga chronić dane zgodnie z wymaganiami co do bezpieczeństwa organizacji i zobowiązaniami w zakresie zgodności. Przy użyciu tej funkcji usługa Azure Storage automatycznie szyfruje dane przed trwałym wprowadzeniem ich do magazynu i odszyfrowuje je przed pobraniem. Szyfrowanie, odszyfrowywanie i zarządzanie kluczami jest całkowicie niewidoczne dla użytkowników. Usługa SSE dotyczy tylko blokowe obiekty BLOB, stronicowe obiekty BLOB, obiektów blob i uzupełnialnych obiektów blob. Inne rodzaje danych, w tym tabel, kolejek i plików, nie będą szyfrowane.</p><p>Szyfrowanie i odszyfrowywanie przepływu pracy:</p><ul><li>Klient włącza szyfrowanie na koncie magazynu</li><li>Gdy klient zapisuje nowe dane (umieszczanie obiektu Blob, umieszczania bloku, umieść strony itp.) do magazynu obiektów Blob; każdego zapisu jest szyfrowana przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych</li><li>Gdy klient musi uzyskać dostęp do danych (Pobieranie obiektów Blob, itp.), dane są automatycznie odszyfrowany przed zwróceniem do użytkownika</li><li>Jeśli szyfrowanie jest wyłączone, nowe zapisy nie są szyfrowane, a istniejące zaszyfrowane dane pozostają zaszyfrowane do momentu przepisany przez użytkownika. Gdy jest włączone szyfrowanie, będą szyfrowane zapisów do magazynu obiektów Blob. Stan danych nie zmienia się z użytkownikiem przełączania się między Włączanie/wyłączanie szyfrowania dla konta magazynu</li><li>Wszystkie klucze szyfrowania są przechowywane, szyfrowane, a zarządzany przez firmę Microsoft</li></ul><p>Należy pamiętać, że w tej chwili klucze używane do szyfrowania są zarządzane przez firmę Microsoft. Firma Microsoft generuje klucze pierwotnie i zarządzać bezpieczny magazyn kluczy, a także regularne obrotu, zgodnie z definicją wewnętrznych zasad firmy Microsoft. W przyszłości, klienci otrzymają możliwość zarządzania własnych > klucze szyfrowania i podaj ścieżkę migracji z kluczy zarządzanych przez firmę Microsoft do kluczy zarządzanych przez klienta.</p>| 

## <a id="client-storage"></a>Użyj szyfrowania po stronie klienta do przechowywania poufnych danych w usłudze Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Szyfrowanie po stronie klienta i Azure magazynu kluczy dla usługi Microsoft Azure Storage](https://azure.microsoft.com/documentation/articles/storage-client-side-encryption/), [samouczka: Szyfrowanie i odszyfrowywanie obiektów blob w Microsoft Azure Storage przy użyciu usługi Azure Key Vault](https://azure.microsoft.com/documentation/articles/storage-encrypt-decrypt-blobs-key-vault/), [bezpieczne przechowywanie danych w usłudze Azure Blob Storage za pomocą rozszerzenia szyfrowania usługi Azure](https://blogs.msdn.microsoft.com/partnercatalystteam/2015/06/17/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions/) |
| **Kroki** | <p>Biblioteki klienta usługi Azure Storage dla pakietu Nuget programu .NET obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage oraz odszyfrowywanie danych pobraniem do klienta. Biblioteka obsługuje również integrację z magazynem kluczy Azure do zarządzania kluczami konta magazynu. Poniżej przedstawiono krótki opis sposobu działania szyfrowania po stronie klienta:</p><ul><li>Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), czyli jeden jednorazowej Użyj klucza symetrycznego</li><li>Dane klienta są szyfrowane przy użyciu tego CEK</li><li>CEK jest następnie opakowywany (zaszyfrowane) przy użyciu klucza szyfrowania klucza (KEK). Klucza KEK jest identyfikowana przez identyfikator klucza i można asymetryczną parę kluczy lub klucz symetryczny i mogą być zarządzane lokalnie lub przechowywane w usłudze Azure Key Vault. Klienta usługi Storage, sama nigdy nie ma dostępu do klucza KEK. Po prostu wywołuje algorytmu zawijanie kluczy, który znajduje się za pomocą usługi Key Vault. Klienci mogą wybrać opcję użycia niestandardowych dostawców klucza zawijania/rozpakowanie chcący</li><li>Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Sprawdź linków w sekcji odwołań dla szczegółów implementacji niskiego poziomu.</li></ul>|

## <a id="pii-phones"></a>Szyfruj poufne lub dane osobowe dane zapisane w magazynie lokalnym telefonów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Mobile Client | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Generic, Xamarin  |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zarządzanie ustawieniami i funkcjami na urządzeniach zasady w usłudze Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/manage-settings-and-features-on-your-devices-with-microsoft-intune-policies), [pęku kluczy Portiera](https://components.xamarin.com/view/square.valet) |
| **Kroki** | <p>Jeśli aplikacja zapisuje informacje poufne, takie jak dane osobowe użytkownika, (wiadomości e-mail, numer telefonu, imienia, nazwiska, preferencje itp.) — w systemie plików na telefon komórkowy, następnie go mają być szyfrowane przed zapisaniem w lokalnym systemie plików. Jeśli aplikacja znajduje się aplikacja dla przedsiębiorstw, zapoznaj się możliwość publikowania aplikacji za pomocą usługi Windows Intune.</p>|

### <a name="example"></a>Przykład
Usługa Intune można skonfigurować następujące zasady zabezpieczeń w celu ochrony poufnych danych: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Przykład
Jeśli aplikacja nie jest aplikacja dla przedsiębiorstw, następnie za pomocą platformy podany magazyn kluczy, łańcuchy kluczy w celu przechowywania kluczy szyfrowania, za pomocą której operacja kryptograficzna procedury może wykonać w systemie plików. Poniższy fragment kodu pokazuje, jak uzyskać dostęp do klucza z łańcucha kluczy za pomocą platformy xamarin: 
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

## <a id="binaries-end"></a>Zaciemniania wygenerowanych plików binarnych, przed rozpoczęciem dystrybuowania dla użytkowników końcowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Mobile Client | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zasłanianie kryptograficznych dla platformy .net](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroki** | Wygenerowane pliki binarne (zestawów w pliku apk) powinny być zaciemniona Aby zatrzymać odtwarzanie zestawów. Narzędzia takie jak `CryptoObfuscator` mogą być używane do tego celu. |

## <a id="cert"></a>Ustaw właściwości ClientCredentialType o wartości certyfikatu lub Windows

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wzmacnia](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroki** | Za pomocą UsernameToken za pomocą hasła w postaci zwykłego tekstu przez kanał niezaszyfrowany udostępnia hasło, aby osoby atakujące, które można wyśledzić komunikaty protokołu SOAP. Dostawcy usług, używanego przez UsernameToken przyjąć hasła wysyłane w postaci zwykłego tekstu. Wysyłanie hasła w postaci jawnej przez kanał niezaszyfrowany można ujawnić poświadczenia aby osoby atakujące, które można wyśledzić komunikatu protokołu SOAP. | 

### <a name="example"></a>Przykład
UsernameToken korzysta z następujących konfiguracji dostawcy usługi WCF: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ustaw właściwości ClientCredentialType o wartości do certyfikatu lub Windows. 

## <a id="security"></a>Nie jest włączony tryb zabezpieczeń programu WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólne, .NET Framework 3 |
| **Atrybuty**              | Zabezpieczenia trybu — transportu, tryb zabezpieczeń — komunikat |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwo](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [podstawy zabezpieczeń WCF kodu Magazyn](https://www.codemag.com/article/0611051) |
| **Kroki** | Bez zabezpieczeń transportu lub komunikat został zdefiniowany. Aplikacje, które przesyłają komunikaty bez transportu lub zabezpieczeń nie może zagwarantować integralności i poufność komunikatów przesyłanych wiadomości. Powiązanie zabezpieczeń WCF jest ustawiona na wartość None, zabezpieczeń transportu i wiadomości są wyłączone. |

### <a name="example"></a>Przykład
Następująca konfiguracja ustawia tryb zabezpieczeń na wartość None. 
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
Tryb zabezpieczeń we wszystkich powiązań usługi są pięć tryby możliwych zabezpieczeniami: 
* Brak. Wyłączenie zabezpieczeń. 
* Transport. Zastosowań transportu zabezpieczeń do wzajemnego uwierzytelniania i komunikat ochrony. 
* Komunikat. Używa zabezpieczeń wiadomości do wzajemnego uwierzytelniania i komunikat ochrony. 
* Oba. Umożliwia określanie wartości ustawień transportu i zabezpieczenia na poziomie komunikatu (tylko usługi MSMQ obsługuje tę funkcję). 
* TransportWithMessageCredential. Poświadczenia są przekazywane z komunikatem i ochrona wiadomości oraz uwierzytelnianie serwera są dostarczane przez warstwy transportowej. 
* TransportCredentialOnly. Poświadczenia klienta są przekazywane z warstwy transportowej i ma zabezpieczenia wiadomości są stosowane. Użyj zabezpieczeń transportu i komunikat, aby chronić integralności i poufności informacji. Informuje usługę do korzystania z zabezpieczeń transportu przy użyciu poświadczeń wiadomości o konfiguracji poniżej.
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
