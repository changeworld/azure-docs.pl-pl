---
title: Komunikacja zabezpieczeń — narzędzie do modelowania zagrożeń firmy Microsoft — Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: 8534f30c17208e77adfa47ea41506a3a61d3548d
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121327"
---
# <a name="security-frame-communication-security--mitigations"></a>Ramka zabezpieczeń: Bezpieczeństwo komunikacji | Środki zaradcze 
| Produkt/usługę | Artykuł |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Zabezpieczanie komunikacji z Centrum zdarzeń za pomocą certyfikatu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegają zabezpieczeń firmy CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Użyj bramy zarządzania danymi podczas nawiązywania połączenia z lokalnego programu SQL Server w usłudze Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Tożsamość serwera** | <ul><li>[Upewnij się, że cały ruch do serwera tożsamości za pośrednictwem połączenia HTTPS](#identity-https)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Sprawdź X.509 certyfikaty używane do uwierzytelniania połączenia protokołu SSL, TLS i DTLS](#x509-ssltls)</li><li>[Konfigurowanie certyfikatu SSL dla domeny niestandardowej w usłudze Azure App Service](#ssl-appservice)</li><li>[Wymuszenie całego ruchu do usługi Azure App Service za pośrednictwem połączenia HTTPS](#appservice-https)</li><li>[Włącz zabezpieczenia Strict transportu HTTP (HSTS)](#http-hsts)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, program SQL server szyfrowania i certyfikat sprawdzania poprawności połączenia](#sqlserver-validation)</li><li>[Wymuszaj zaszyfrowane komunikacji z serwerem SQL](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że komunikacja z usługi Azure Storage przy użyciu protokołu HTTPS](#comm-storage)</li><li>[Sprawdzenie skrótu MD5 po pobraniu obiektu blob, jeśli nie można włączyć protokołu HTTPS](#md5-https)</li><li>[Upewnij się, szyfrowanie danych podczas przesyłania do udziałów plików platformy Azure za pomocą protokołu SMB 3.0 zgodne klienta](#smb-shares)</li></ul> |
| **Mobile Client** | <ul><li>[Implementowanie przypinania certyfikatu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Włącz protokół HTTPS — należy zabezpieczyć kanał transportu](#https-transport)</li><li>[USŁUGI WCF: Zabezpieczenia komunikatów poziom ochrony ma wartość EncryptAndSign](#message-protection)</li><li>[USŁUGI WCF: Użyj najmniej uprzywilejowane konto uruchamiania usługi WCF](#least-account-wcf)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Wymuszenie całego ruchu do interfejsów API sieci Web za pośrednictwem połączenia HTTPS](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Upewnij się, że komunikacja pamięć podręczna systemu Azure dla usługi Redis za pośrednictwem protokołu SSL](#redis-ssl)</li></ul> |
| **IoT Field Gateway** | <ul><li>[Zabezpieczenia urządzenia do bramy w terenie komunikacji](#device-field)</li></ul> |
| **IoT Cloud Gateway** | <ul><li>[Zabezpieczenia urządzenia komunikację bramy w chmurze przy użyciu protokołu SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Zabezpieczanie komunikacji z Centrum zdarzeń za pomocą certyfikatu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Zabezpieczanie połączenia AMQP lub HTTP z Centrum zdarzeń za pomocą certyfikatu SSL/TLS |

## <a id="priv-aspnet"></a>Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegają zabezpieczeń firmy CRM

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegają zabezpieczeń firmy CRM |

## <a id="sqlserver-factory"></a>Użyj bramy zarządzania danymi podczas nawiązywania połączenia z lokalnego programu SQL Server w usłudze Azure Data Factory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Data Factory | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | Typów połączonych usług — platforma Azure i w środowisku lokalnym |
| **Odwołania**              |[Przenoszenie danych między magazynami lokalnymi i usługi Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [bramy zarządzania danymi](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroki** | <p>Narzędzia danych bramy zarządzania (DMG) jest wymagany do nawiązania połączenia ze źródłami danych, które są chronione w sieci firmowej lub zapory.</p><ol><li>Blokowanie maszynę izoluje narzędzie DMG i uniemożliwia nieprawidłowe działanie programów z naruszenia lub podsłuchiwanie na maszynie źródłowej danych. (Np. muszą być zainstalowane najnowsze aktualizacje, Włącz minimalne wymagane porty, kontrolowane kont Inicjowanie obsługi administracyjnej inspekcji włączona, szyfrowanie dysków włączone itp.)</li><li>Klucz brama danych jest obracana w krótkich odstępach czasu lub zawsze wtedy, gdy odnawia hasło do konta usługi DMG</li><li>Tranzytu danych za pośrednictwem usługi łączy muszą być szyfrowane.</li></ol> |

## <a id="identity-https"></a>Upewnij się, że cały ruch do serwera tożsamości za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamość serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IdentityServer3 — klucze, sygnatur i kryptografii](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - wdrożenia](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | Domyślnie IdentityServer wymaga wszystkie połączenia przychodzące do dostarczane za pośrednictwem protokołu HTTPS. Jest absolutnie obowiązkowe, że komunikacja z IdentityServer odbywa się za pośrednictwem zabezpieczonej transportów tylko. Istnieją pewne scenariusze wdrażania, takie jak odciążanie protokołu SSL gdzie może zostać złagodzone to wymaganie. Zobacz stronę wdrożenia serwera tożsamości w odwołaniach, aby uzyskać więcej informacji. |

## <a id="x509-ssltls"></a>Sprawdź X.509 certyfikaty używane do uwierzytelniania połączenia protokołu SSL, TLS i DTLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Certyfikaty X.509 z jednostkami, z którymi łączą się z w pełni należy sprawdzić, aplikacje, które używają protokołu SSL, TLS i DTLS. Obejmuje to weryfikacji certyfikatów dla:</p><ul><li>Nazwa domeny</li><li>Daty ważności (daty rozpoczęcia i wygaśnięcia)</li><li>Stan odwołania</li><li>Użycie (na przykład uwierzytelnianie serwera dla serwerów, uwierzytelnianie klienta dla klientów)</li><li>Zaufaj łańcucha. Certyfikaty muszą być powiązane z głównego urzędu certyfikacji (CA), który jest zaufany przez platformę lub jawnie skonfigurowane przez administratora</li><li>Długość klucza publicznego certyfikatu musi być > 2048 bitów</li><li>Algorytm wyznaczania wartości skrótu musi być SHA256 i nowsze wersje |

## <a id="ssl-appservice"></a>Konfigurowanie certyfikatu SSL dla domeny niestandardowej w usłudze Azure App Service

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Włącz protokół HTTPS dla aplikacji w usłudze Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Kroki** | Domyślnie, system Azure już pozwala HTTPS na dla każdej aplikacji przy użyciu certyfikatu dla symbolu wieloznacznego *. azurewebsites.net domeny. Jednakże, podobnie jak wszystkie domeny z symbolami wieloznacznymi, nie jest tak bezpieczne, jak przy użyciu domeny niestandardowej za pomocą własnego certyfikatu [można znaleźć](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Zalecane jest aby włączyć protokół SSL dla domeny niestandardowej, która wdrożonej aplikacji będą uzyskiwać dostęp za pośrednictwem|

## <a id="appservice-https"></a>Wymuszenie całego ruchu do usługi Azure App Service za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Wymuszanie protokołu HTTPS w usłudze Azure App Service](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Kroki** | <p>Chociaż system Azure pozwala już HTTPS dla usług Azure app services przy użyciu certyfikat wieloznaczny dla domeny *. azurewebsites.net, nie Wymuszaj HTTPS. Goście mogą nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, który może naruszyć bezpieczeństwo aplikacji i dlatego HTTPS musi jawnie wymuszone. Skorzystaj z aplikacji ASP.NET MVC [filtr RequireHttps](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) , wymusza przesłanie niezabezpieczonego żądania HTTP do wysłania się ponownie przy użyciu protokołu HTTPS.</p><p>Alternatywnie moduł ponowne zapisywanie adresów URL, który jest dołączony do usługi Azure App Service umożliwia wymuszanie protokołu HTTPS. Moduł ponowne zapisywanie adresów URL umożliwia deweloperom Definiowanie reguł, które są stosowane do żądań przychodzących, zanim żądania są przekazywane do aplikacji. Reguły ponownego zapisywania adresów URL są zdefiniowane w pliku web.config, przechowywane w katalogu głównym aplikacji</p>|

### <a name="example"></a>Przykład
Poniższy przykład zawiera podstawową regułę ponownego zapisywania adresów URL, która wymusza cały ruch przychodzący do używania protokołu HTTPS
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Ta zasada działa, zwracając kod stanu HTTP 301 (trwałe przekierowanie) po użytkownik zgłasza żądanie strony przy użyciu protokołu HTTP. 301 przekierowuje żądanie do tego samego adresu URL jako użytkownik zażądał, lecz zastępuje części HTTP żądania przy użyciu protokołu HTTPS. Na przykład HTTP://contoso.com zostanie przekierowana HTTPS://contoso.com. 

## <a id="http-hsts"></a>Włącz zabezpieczenia Strict transportu HTTP (HSTS)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zabezpieczenia transportu Strict HTTP OWASP Ściągawka](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Kroki** | <p>Zabezpieczenia transportu HTTP ograniczeniami (HSTS) jest ulepszeniem zabezpieczeń zgłoszenie zgody na uczestnictwo w określonym przez aplikację sieci web przy użyciu nagłówka odpowiedzi specjalne. Po obsługiwanej przeglądarki odbiera tego pliku nagłówkowego tej przeglądarki uniemożliwi każdej komunikacji z wysyłanych za pośrednictwem protokołu HTTP do określonej domeny i zamiast tego będzie wysyłać całej komunikacji za pośrednictwem protokołu HTTPS. Uniemożliwia ona kliknij HTTPS za pośrednictwem monity w przeglądarkach.</p><p>Aby zaimplementować HSTS, następujący nagłówek odpowiedzi musi być skonfigurowana globalnie, dla witryny sieci Web w w kodzie lub w pliku konfiguracyjnym. Strict — Transport — Security: max-age = 300; includeSubDomains HSTS rozwiązuje następujące zagrożenia:</p><ul><li>Zakładki użytkownika, lub ręcznie typów https://example.com i podlega atakujący ataków typu man-in--middle: HSTS automatycznie przekierowuje żądania HTTP do HTTPS dla domeny docelowej</li><li>Aplikacja sieci Web, która ma być wyłącznie HTTPS przypadkowo zawiera linki do protokołu HTTP lub obsługują zawartość przy użyciu protokołu HTTP: HSTS automatycznie przekierowuje żądania HTTP do HTTPS dla domeny docelowej</li><li>Atakujący ataków typu man-in--middle próbuje przechwycić ruch z użytkownikiem ofiarą przy użyciu nieprawidłowego certyfikatu i nadzieję, że użytkownik będzie akceptować nieprawidłowy certyfikat: HSTS pozwala użytkownikowi zastąpić komunikat nieprawidłowy certyfikat</li></ul>|

## <a id="sqlserver-validation"></a>Upewnij się, program SQL server szyfrowania i certyfikat sprawdzania poprawności połączenia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Usługi SQL Azure  |
| **Atrybuty**              | Wersja SQL - V12 |
| **Odwołania**              | [Najlepsze rozwiązania dotyczące pisania bezpiecznego parametry połączenia dla bazy danych SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroki** | <p>Cała komunikacja między bazy danych SQL Database i aplikację kliencką są szyfrowane przy użyciu protokołu Secure Sockets Layer (SSL), przez cały czas. SQL Database nie obsługuje połączenia nieszyfrowanego. Aby sprawdzić poprawność certyfikatów za pomocą kod aplikacji lub narzędzia, należy jawnie żądać połączenia szyfrowanego i niezaufane certyfikaty serwera. Jeśli aplikacji, kodu lub narzędzia nie żądać połączenia szyfrowanego, będzie nadal otrzymywał szyfrowanych połączeń</p><p>Jednak nie może sprawdzić poprawności certyfikatów serwera i związku z tym będzie narażony na ataki man-in middle". Aby sprawdzić poprawność certyfikatów za pomocą ADO.NET, kod aplikacji, należy ustawić `Encrypt=True` i `TrustServerCertificate=False` w parametrach połączenia bazy danych. Aby zweryfikować certyfikaty przy użyciu programu SQL Server Management Studio, otwórz nawiązywania połączenia z serwerem, okno dialogowe. Kliknij przycisk Szyfruj połączenie na karcie właściwości połączenia</p>|

## <a id="encrypted-sqlserver"></a>Wymuszaj zaszyfrowane komunikacji z serwerem SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | OnPrem |
| **Atrybuty**              | MsSQL2014 wersji — MsSQL2012, wersja SQL — SQL wersja - MsSQL2016, SQL |
| **Odwołania**              | [Włączanie połączeń szyfrowanych z aparatem bazy danych](https://msdn.microsoft.com/library/ms191192)  |
| **Kroki** | Włączanie protokołu SSL szyfrowania kryptograficznego zwiększa bezpieczeństwo danych przesyłanych przez sieci między wystąpieniami programu SQL Server i aplikacji. |

## <a id="comm-storage"></a>Upewnij się, że komunikacja z usługi Azure Storage przy użyciu protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Szyfrowanie na poziomie transportu usługi Azure Storage — przy użyciu protokołu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroki** | Aby zapewnić bezpieczeństwo usłudze Azure Storage danych podczas przesyłania, zawsze używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwania dostępu do obiektów w magazynie. Ponadto sygnatur dostępu współdzielonego, które mogą być używane do Delegowanie dostępu do obiektów usługi Azure Storage, obejmują opcję, aby określić, że mogą być używane tylko z protokołu HTTPS, przy użyciu sygnatury dostępu współdzielonego, zapewniając każda osoba, wysyłając połączeń za pomocą tokenów sygnatur dostępu Współdzielonego będzie używać odpowiedni protokół.|

## <a id="md5-https"></a>Sprawdzenie skrótu MD5 po pobraniu obiektu blob, jeśli nie można włączyć protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Omówienie MD5 obiektów Blob platformy Azure Windows](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroki** | <p>Windows Azure Blob service udostępnia mechanizmy do zapewnienia integralności danych, zarówno w aplikacji i warstwy transportu. Jakiegokolwiek powodu, czego potrzebujesz do obsługi protokołu HTTP zamiast HTTPS i pracy z blokowymi obiektami blob, umożliwia sprawdzanie MD5 ułatwiają sprawdzanie integralności przesyłanych obiektów blob</p><p>Ułatwi to ochronę przed błędami warstwy transportu sieciowego /, ale niekoniecznie pośrednie ataków. Jeśli używasz protokołu HTTPS, który zapewnia zabezpieczenia na poziomie transportu, a następnie użyć sprawdzanie MD5 jest nadmiarowe i niepotrzebne.</p>|

## <a id="smb-shares"></a>Upewnij się, szyfrowanie danych podczas przesyłania do udziałów plików platformy Azure za pomocą protokołu SMB 3.0 zgodne klienta

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Mobile Client | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | StorageType — plik |
| **Odwołania**              | [Usługi Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [Obsługa protokołu SMB magazynu plików platformy Azure dla klientów Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroki** | Usługa Azure File Storage obsługuje protokół HTTPS, korzystając z interfejsu API REST, ale jest stosowany znacznie częściej jako udział plików SMB dołączony do maszyny Wirtualnej. Protokół SMB 2.1 nie obsługuje szyfrowania, więc połączenia są dozwolone tylko w obrębie tego samego regionu na platformie Azure. Jednak obsługuje szyfrowanie protokołu SMB 3.0 i może być używany z systemem Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10, dzięki czemu między regionami dostępu, a nawet dostępu na pulpicie. |

## <a id="cert-pinning"></a>Implementowanie przypinania certyfikatu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólne, Windows Phone |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Certyfikat i przypinanie klucza publicznego](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Kroki** | <p>Przypinanie certyfikatu systemów przed atakami typu Man-In--Middle (MITM). Przypinanie polega kojarzenie hosta z ich X509 oczekiwanego certyfikatu lub klucza publicznego. Gdy certyfikat lub klucz publiczny jest znane lub widoczny dla hosta, certyfikatu lub klucza publicznego jest skojarzony lub przypięte do hosta. </p><p>W związku z tym gdy osoba atakująca próbuje ataki SSL MITM czy, podczas uzgadniania protokołu SSL klucza z serwera osoby atakującej będzie różni się od klucza przypiętych certyfikatów i żądanie zostanie odrzucone, zapobiegając w ten certyfikat MITM przypinanie można osiągnąć przez Implementowanie ServicePointManager — jego `ServerCertificateValidationCallback` delegować.</p>|

### <a name="example"></a>Przykład
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a id="https-transport"></a>Włącz protokół HTTPS — należy zabezpieczyć kanał transportu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [wzmacnia Królestwa](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroki** | Konfiguracja aplikacji należy upewnić się, czy HTTPS jest używany przez cały dostęp do poufnych informacji.<ul><li>**OBJAŚNIENIE:** Jeśli aplikacja obsługuje informacje poufne i nie używa szyfrowania na poziomie komunikatu, następnie go powinny być dozwolone tylko do komunikacji za pośrednictwem kanału zaszyfrowanych transportu.</li><li>**ZALECENIA:** Upewnij się, że transportu HTTP jest wyłączona, a zamiast tego włączyć transportu HTTPS. Na przykład Zastąp ciąg `<httpTransport/>` z `<httpsTransport/>` tagu. Nie należy polegać na konfigurację sieci (zapora), aby zagwarantować, że aplikacja może zostać oceniony jedynie przez bezpieczny kanał. Z filozoficzne punktu widzenia aplikacji nie powinno zależeć od sieci dla jego zabezpieczenia.</li></ul><p>Z praktycznego punktu widzenia osoby, które są odpowiedzialni za zabezpieczenie sieci nie zawsze Śledź wymagania dotyczące zabezpieczeń aplikacji w miarę ich ewolucji.</p>|

## <a id="message-protection"></a>WCF: Zabezpieczenia komunikatów poziom ochrony ma wartość EncryptAndSign

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroki** | <ul><li>**OBJAŚNIENIE:** Podczas ochrony poziom jest równa "none" go spowoduje wyłączenie ochrony wiadomości. Poufności i integralności odbywa się przy użyciu odpowiedniego poziomu ustawienie.</li><li>**ZALECENIA:**<ul><li>gdy `Mode=None` — wyłącza ochronę wiadomości</li><li>gdy `Mode=Sign` -znaków, ale nie szyfruje wiadomości; Jeśli ważne jest, integralność danych, należy użyć</li><li>gdy `Mode=EncryptAndSign` -objawy i szyfrowanie wiadomości</li></ul></li></ul><p>Rozważ wyłączenie szyfrowania i tylko podpisywania wiadomości, gdy trzeba tylko sprawdzenia spójności informacji bez obaw poufności. Może to być przydatne w przypadku operacji lub zamówienia usługi, której należy dokonać weryfikacji oryginalnego nadawcy, ale żadne poufne dane są przesyłane. Podczas obniżania poziomu ochrony, należy zachować ostrożność, że komunikat nie zawiera wszelkie identyfikowalne dane osobowe (PII).</p>|

### <a name="example"></a>Przykład
Konfigurowanie usługi i operacji tylko podpisywanie komunikatów przedstawiono w poniższych przykładach. Przykład kontraktu usługi `ProtectionLevel.Sign`: Oto przykład użycia ProtectionLevel.Sign na poziomie kontraktu usługi: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Przykład
Przykład kontrakt operacji `ProtectionLevel.Sign` (Aby uzyskać szczegółową kontrolę): Oto przykład użycia `ProtectionLevel.Sign` na poziomie OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Użyj najmniej uprzywilejowane konto uruchamiania usługi WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | .NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroki** | <ul><li>**OBJAŚNIENIE:** Nie należy uruchamiać usług WCF w ramach administratora lub konta z wysokim poziomie uprawnień. w przypadku naruszenia zabezpieczeń usługi spowoduje o dużym znaczeniu.</li><li>**ZALECENIA:** Umożliwia hostowanie usługi WCF, ponieważ będzie on zmniejszyć obszar narażony na ataki aplikacji i ograniczyć potencjalne szkody, jeśli są zaatakowane najmniej uprzywilejowane konto. Jeśli konto usługi wymaga prawa dostępu do dodatkowych zasobów infrastruktury, takich jak usługi MSMQ, w dzienniku zdarzeń, liczniki wydajności i system plików, powinien być podawany odpowiednich uprawnień do tych zasobów, tak, aby pomyślnie uruchomić usługi WCF.</li></ul><p>Jeśli usługa musi uzyskać dostęp do określonych zasobów imieniu oryginalnego obiektu wywołującego, umożliwia personifikacji i delegowanie tożsamości elementu wywołującego do sprawdzania autoryzacji podrzędnego przepływu. W przypadku projektowania należy użyć konta usługi lokalnej sieci, które to specjalne konto wbudowane, które ma ograniczone uprawnienia. W scenariuszu produkcji należy utworzyć konto usługi domeny niestandardowej najmniej uprzywilejowane.</p>|

## <a id="webapi-https"></a>Wymuszenie całego ruchu do interfejsów API sieci Web za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wymuszanie protokołu SSL w kontrolerze interfejsu API sieci Web](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroki** | Jeśli aplikacja ma przy użyciu protokołu HTTPS i powiązanie HTTP, klienci nadal mogą używać protokołu HTTP dostęp do witryny. Aby tego uniknąć, użyj filtru akcji, aby upewnij się, że żądania do chronionego interfejsów API zawsze przy użyciu protokołu HTTPS.|

### <a name="example"></a>Przykład 
Poniższy kod pokazuje filtr uwierzytelniania interfejsu API sieci Web, który sprawdza, czy protokół SSL: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Dodaj filtr do wszystkich działań interfejsu API sieci Web wymaga protokołu SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Upewnij się, że komunikacja pamięć podręczna systemu Azure dla usługi Redis za pośrednictwem protokołu SSL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Cache for Redis | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Usługa Azure redis Cache Obsługa protokołu SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroki** | Redis server nie obsługuje protokołu SSL gotowych, ale nie w pamięci podręcznej Azure redis Cache. Jeśli łączysz się pamięć podręczna systemu Azure dla usługi Redis i klient obsługuje protokół SSL, takich jak StackExchange.Redis, należy używać protokołu SSL. Domyślnie port bez protokołu SSL jest wyłączony dla nowej usługi Azure Cache dla wystąpień usługi Redis. Upewnij się, czy bezpieczne ustawienia domyślne nie są zmieniane, chyba że istnieje zależność na temat obsługi protokołu SSL dla klientów usługi redis. |

Należy pamiętać, że Redis jest przeznaczona do uzyskiwał dostęp do zaufanych klientów w środowiskach zaufanych. Oznacza to, że zwykle nie jest dobry pomysł, aby udostępnić wystąpienia pamięci podręcznej Redis bezpośrednio do Internetu lub ogólnie rzecz biorąc, w środowisku, gdzie niezaufani klienci mogą uzyskać bezpośredni dostęp do portu Redis TCP lub w gnieździe systemu UNIX. 

## <a id="device-field"></a>Zabezpieczenia urządzenia do bramy w terenie komunikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Bramy w terenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | W przypadku urządzeń opartych na adresie IP protokół komunikacyjny można zwykle hermetyzowane w kanału SSL/TLS, aby chronić dane podczas przesyłania. Inne protokoły, które nie obsługują protokołu SSL/TLS można zbadać w przypadku bezpieczne wersje protokołu, które zapewniają zabezpieczeń w warstwie transportu lub komunikatu. |

## <a id="device-cloud"></a>Zabezpieczenia urządzenia komunikację bramy w chmurze przy użyciu protokołu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | IoT Cloud Gateway | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednich technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wybierz swoje protokołu komunikacyjnego](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroki** | Zabezpiecz HTTP/AMQP lub protokołów MQTT przy użyciu protokołu SSL/TLS. |
