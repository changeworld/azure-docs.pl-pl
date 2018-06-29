---
title: Komunikacja — narzędzie Microsoft Threat modelowania — zabezpieczeń Azure | Dokumentacja firmy Microsoft
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
ms.openlocfilehash: c361f74147862585074f3c4475209ba6eb0c1e0c
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37029802"
---
# <a name="security-frame-communication-security--mitigations"></a>Ramka zabezpieczeń: Zabezpieczenia komunikacji | Środki zaradcze 
| Produktów i usług | Artykuł |
| --------------- | ------- |
| **Azure Event Hub** | <ul><li>[Bezpieczna komunikacja do Centrum zdarzeń za pomocą protokołu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegać CRM zabezpieczeń](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Brama zarządzania danymi używana podczas łączenia z fabryką danych Azure na serwerze SQL — wersja Premium](#sqlserver-factory)</li></ul> |
| **Tożsamości serwera** | <ul><li>[Upewnij się, że cały ruch do tożsamości serwera jest za pośrednictwem połączenia HTTPS](#identity-https)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Sprawdź X.509 certyfikaty używane do uwierzytelniania połączeń SSL, TLS i DTLS](#x509-ssltls)</li><li>[Konfigurowanie certyfikatu SSL dla domen niestandardowych w usłudze Azure App Service](#ssl-appservice)</li><li>[Wymuś cały ruch do usługi Azure App Service za pośrednictwem połączenia HTTPS](#appservice-https)</li><li>[Włącz zabezpieczenie Strict transportu HTTP (HSTS)](#http-hsts)</li></ul> |
| **Baza danych** | <ul><li>[Upewnij się, SQL server szyfrowania i certyfikatu sprawdzania poprawności połączenia](#sqlserver-validation)</li><li>[Wymuś zaszyfrowana komunikacja z programem SQL server](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Sprawdź, czy komunikacji z magazynem Azure za pośrednictwem protokołu HTTPS](#comm-storage)</li><li>[Sprawdzenie skrótu MD5 po pobraniu obiektów blob, jeśli nie można włączyć protokołu HTTPS](#md5-https)</li><li>[Upewnij się, szyfrowanie danych podczas przesyłania danych do udziałów plików Azure za pomocą klienta zgodne protokołu SMB 3.0](#smb-shares)</li></ul> |
| **Klientów urządzeń przenośnych** | <ul><li>[Implementowanie funkcji przypinania certyfikatu](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Włącz protokół HTTPS - bezpieczny kanał transportu](#https-transport)</li><li>[WCF: Zabezpieczenia komunikatów zestawu poziom ochrony EncryptAndSign](#message-protection)</li><li>[WCF: Użyj najmniej uprzywilejowane konto uruchamiania usługi WCF](#least-account-wcf)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Wymuś cały ruch do interfejsów API sieci Web za pośrednictwem połączenia HTTPS](#webapi-https)</li></ul> |
| **Azure Redis Cache** | <ul><li>[Sprawdź, czy komunikacji z pamięcią podręczną Redis Azure za pośrednictwem protokołu SSL](#redis-ssl)</li></ul> |
| **Pole IoT bramy** | <ul><li>[Bezpieczne urządzenie do komunikacji między bramą pola](#device-field)</li></ul> |
| **Brama chmury IoT** | <ul><li>[Bezpieczne urządzenia bramy chmury komunikacji przy użyciu protokołu SSL/TLS](#device-cloud)</li></ul> |

## <a id="comm-ssltls"></a>Bezpieczna komunikacja do Centrum zdarzeń za pomocą protokołu SSL/TLS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Uwierzytelnianie i zabezpieczenia modelu Omówienie usługi Event Hubs](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Bezpiecznych połączeń HTTP lub protokołu AMQP do Centrum zdarzeń za pomocą protokołu SSL/TLS |

## <a id="priv-aspnet"></a>Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegać CRM zabezpieczeń

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Sprawdź uprawnienia konta usługi i sprawdź, czy niestandardowych usług lub stron ASP.NET przestrzegać CRM zabezpieczeń |

## <a id="sqlserver-factory"></a>Brama zarządzania danymi używana podczas łączenia z fabryką danych Azure na serwerze SQL — wersja Premium

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Data Factory | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | Typy połączonych usług - Azure i lokalnej |
| **Odwołania**              |[ Przenoszenie danych między o lokalnej i fabryki danych Azure](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [brama zarządzania danymi](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroki** | <p>Narzędzie danych zarządzania bramy (DMG) jest wymagane do połączenia ze źródłami danych, które są chronione za corpnet lub zaporą.</p><ol><li>Blokowanie komputera izoluje narzędzia DMG i uniemożliwia nieprawidłowo programy z uszkodzenia lub śledzenie na maszynie źródłowej danych. (Np. musi być zainstalowane najnowsze aktualizacje, Włącz minimalne wymagane porty, kontrolowanego kont inicjowania obsługi administracyjnej, inspekcji włączona, szyfrowanie dysków włączone itp.)</li><li>Klucz bramy danych jest obracana odstępach częste lub gdy hasło konta usługi DMG odnawia</li><li>Tranzytu danych za pośrednictwem łącza usługi musi być zaszyfrowany.</li></ol> |

## <a id="identity-https"></a>Upewnij się, że cały ruch do tożsamości serwera jest za pośrednictwem połączenia HTTPS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Tożsamości serwera | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [IdentityServer3 - kluczy, podpisy i szyfrowania](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 — wdrożenia](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | Domyślnie IdentityServer wymaga wszystkie połączenia przychodzące do trybu za pośrednictwem protokołu HTTPS. Jest absolutnie obowiązkowe, że komunikacja z IdentityServer odbywa się za pośrednictwem tylko zabezpieczonych transportów. Brak niektórych scenariuszy wdrażania, takie jak odciążanie protokołu SSL gdzie to wymaganie można rozluźnić. Odwiedź stronę wdrożenia tożsamości serwera w odwołaniach, aby uzyskać więcej informacji. |

## <a id="x509-ssltls"></a>Sprawdź X.509 certyfikaty używane do uwierzytelniania połączeń SSL, TLS i DTLS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | <p>Certyfikaty X.509 jednostek, które łączą się z pełni należy sprawdzić, aplikacje używające protokołu SSL, TLS i DTLS. Obejmuje to weryfikacji certyfikatów dla:</p><ul><li>Nazwa domeny</li><li>Daty ważności (początku i na wygaśnięcie daty)</li><li>Stan odwołania</li><li>Sposób użycia (na przykład uwierzytelniania serwera dla serwerów, uwierzytelnianie klienta dla klientów)</li><li>Zaufania łańcucha. Certyfikaty muszą być powiązane z do głównego urzędu certyfikacji (CA), który jest uważany za zaufany przez platformę lub jawnie skonfigurowane przez administratora</li><li>Długość klucza publicznego certyfikatu musi być > 2048 bitów</li><li>Algorytm wyznaczania wartości skrótu musi być SHA256 i powyżej. |

## <a id="ssl-appservice"></a>Konfigurowanie certyfikatu SSL dla domen niestandardowych w usłudze Azure App Service

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Włącz protokół HTTPS dla aplikacji w usłudze aplikacji Azure](../app-service/app-service-web-tutorial-custom-ssl.md) |
| **Kroki** | Domyślnie program Azure po już umożliwia HTTPS dla każdej aplikacji przy użyciu symboli wieloznacznych certyfikatu dla *. azurewebsites.net domeny. Jednakże, podobnie jak wszystkie domeny symboli wieloznacznych, nie jest należycie zabezpieczone za pomocą domeny niestandardowej z własnym certyfikatem [zobacz](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Zaleca się włączenie protokołu SSL dla domeny niestandardowej, która wdrożonej aplikacji będą uzyskiwać dostęp za pośrednictwem|

## <a id="appservice-https"></a>Wymuś cały ruch do usługi Azure App Service za pośrednictwem połączenia HTTPS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | EnvironmentType - Azure |
| **Odwołania**              | [Wymuszanie protokołu HTTPS z usługi aplikacji Azure](../app-service/app-service-web-tutorial-custom-ssl.md#enforce-https) |
| **Kroki** | <p>Chociaż Azure umożliwia już HTTPS dla usług aplikacji Azure z certyfikatem symboli wieloznacznych dla domeny *. azurewebsites.net, nie Wymuszaj HTTPS. Odwiedzający mogą nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, który może naruszyć bezpieczeństwo aplikacji i dlatego HTTPS ma mają być egzekwowane jawnie. Należy użyć aplikacji ASP.NET MVC [filtru RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) który wymusza niezabezpieczonego żądania HTTP do ponownego wysyłania za pośrednictwem protokołu HTTPS.</p><p>Moduł ponowne zapisywanie adresów URL, który znajduje się w usłudze Azure App Service można również wymusić HTTPS. Moduł ponowne zapisywanie adresów URL umożliwia deweloperom definiują reguły, które są stosowane do żądań przychodzących, przed żądania są przekazywane do aplikacji. Ponowne zapisywanie adresów URL reguły są zdefiniowane w pliku web.config, przechowywane w katalogu głównym aplikacji</p>|

### <a name="example"></a>Przykład
Poniżej przedstawiono przykład zawierający podstawowe reguły ponowne zapisywanie adresów URL, która wymusza cały ruch przychodzący do używania protokołu HTTPS
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
Ta zasada działa zwróciła kod stanu HTTP 301 (Stałe przekierowanie) gdy użytkownik zażąda strony przy użyciu protokołu HTTP. 301 przekierowuje żądanie do tego samego adresu URL jako obiekt odwiedzający żądanie, ale zastępuje części HTTP żądania HTTPS. Na przykład HTTP://contoso.com zostanie przekierowana HTTPS://contoso.com. 

## <a id="http-hsts"></a>Włącz zabezpieczenie Strict transportu HTTP (HSTS)

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Zabezpieczenia transportu Strict HTTP OWASP Ściągawka](https://www.owasp.org/index.php/HTTP_Strict_Transport_Security_Cheat_Sheet) |
| **Kroki** | <p>Zabezpieczenia transportu HTTP ograniczeniami (HSTS) jest ulepszeniem opcjonalnych zabezpieczeń, określonym przez aplikację sieci web przy użyciu nagłówka odpowiedzi specjalnych. Kiedy obsługiwanej przeglądarki odbiera ten nagłówek przeglądarka uniemożliwi komunikację z są wysyłane za pośrednictwem protokołu HTTP z określoną domeną i zamiast tego wyśle cała komunikacja za pośrednictwem protokołu HTTPS. Uniemożliwia także kliknij HTTPS za pomocą monity w przeglądarkach.</p><p>Aby zaimplementować HSTS, następujący nagłówek odpowiedzi musi być skonfigurowana globalnie, witryny sieci Web w w kodzie lub w pliku konfiguracyjnym. Strict —-TLS: maksymalny — wiek = 300; includeSubDomains HSTS dotyczy następujących zagrożeniami:</p><ul><li>Zakładki użytkownika, lub ręcznie typy http://example.com i podlega atakująca man-in--middle: HSTS automatyczne przekierowanie żądania HTTP do HTTPS dla domeny docelowej</li><li>Aplikacja sieci Web, która powinna być całkowicie HTTPS przypadkowo zawiera łącza HTTP lub obsługują zawartość za pośrednictwem protokołu HTTP: HSTS automatyczne przekierowanie żądania HTTP do HTTPS dla domeny docelowej</li><li>Atakujący man-in--middle próbuje przechwytywać ruch od użytkownika ofiara przy użyciu nieprawidłowego certyfikatu i nadzieję, użytkownik będzie akceptować zły certyfikat: HSTS nie zezwala użytkownikowi przesłanianie komunikat nieprawidłowy certyfikat</li></ul>|

## <a id="sqlserver-validation"></a>Upewnij się, SQL server szyfrowania i certyfikatu sprawdzania poprawności połączenia

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Usługi SQL Azure  |
| **Atrybuty**              | Wersja programu SQL — wersja 12 |
| **Odwołania**              | [Najlepsze rozwiązania dla pisma bezpiecznego parametry połączenia dla bazy danych SQL](http://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroki** | <p>Cała komunikacja między bazy danych SQL i aplikacją klienta są szyfrowane przy użyciu protokołu Secure Sockets Layer (SSL) przez cały czas. Baza danych SQL nie obsługuje połączeń niezaszyfrowanych. Aby sprawdzić poprawność certyfikatów przy użyciu kodu aplikacji lub narzędzi, jawnie żądania połączenia szyfrowanego i nie masz zaufania certyfikatów serwera. Jeśli z kodu aplikacji lub narzędzia połączenie szyfrowane nie jest żądanie, będzie nadal otrzymywał szyfrowanych połączeń</p><p>Jednak nie może zweryfikować certyfikaty serwera i w związku z tym będzie narażony na ataki "man w środku". Aby sprawdzić poprawność certyfikatów przy użyciu kodu aplikacji ADO.NET, ustaw `Encrypt=True` i `TrustServerCertificate=False` w ciągu połączenia bazy danych. Aby sprawdzić poprawność certyfikatów za pomocą programu SQL Server Management Studio, otwórz Połącz z serwerem, okno dialogowe. Kliknij przycisk połączenie Szyfruj na karcie właściwości połączenia</p>|

## <a id="encrypted-sqlserver"></a>Wymuś zaszyfrowana komunikacja z programem SQL server

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Database (Baza danych) | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | OnPrem |
| **Atrybuty**              | MsSQL2014 wersji — MsSQL2012, wersja programu SQL — SQL wersja - MsSQL2016, SQL |
| **Odwołania**              | [Włączanie połączeń szyfrowanych z aparatem bazy danych](https://msdn.microsoft.com/library/ms191192)  |
| **Kroki** | Włączanie protokołu SSL szyfrowania zwiększa bezpieczeństwo danych przesyłanych przez sieci między wystąpieniami programu SQL Server i aplikacji. |

## <a id="comm-storage"></a>Sprawdź, czy komunikacji z magazynem Azure za pośrednictwem protokołu HTTPS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Szyfrowanie na poziomie transportu usługi Azure Storage — przy użyciu protokołu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroki** | Aby zapewnić bezpieczeństwo usługi Azure Storage danych podczas przesyłania, zawsze używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwanie dostępu do obiektów w magazynie. Ponadto sygnatury dostępu współdzielonego, które mogą być używane, aby delegować dostęp do obiektów usługi Azure Storage, obejmują opcję, aby określić, że mogą być używane tylko z protokołu HTTPS przy użyciu sygnatury dostępu współdzielonego, zapewnienie każdy wysyłanie linków z tokenami SAS będzie używać odpowiedni protokół.|

## <a id="md5-https"></a>Sprawdzenie skrótu MD5 po pobraniu obiektów blob, jeśli nie można włączyć protokołu HTTPS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | StorageType — obiekt Blob |
| **Odwołania**              | [Omówienie MD5 obiektów Blob platformy Azure dla systemu Windows](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroki** | <p>Usługa Windows Azure Blob zawiera mechanizmy do zapewnienia integralności danych, zarówno w aplikacji i warstwy transportu. Przyczyn potrzebnych do obsługi protokołu HTTP zamiast HTTPS i pracy z blokowych obiektów blob, umożliwia sprawdzanie MD5 weryfikowania integralności transferowanych obiektów blob</p><p>Pomoże to ochrony z sieci/transport layer błędów, ale niekoniecznie pośredniczące ataków. Jeśli używasz protokołu HTTPS, który zapewnia zabezpieczeń na poziomie transportu, następnie przy użyciu algorytmu MD5 sprawdzanie jest nadmiarowe i niepotrzebne.</p>|

## <a id="smb-shares"></a>Upewnij się, szyfrowanie danych podczas przesyłania danych do udziałów plików na platformę Azure za pomocą protokołu SMB 3.0 zgodny klienta

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klientów urządzeń przenośnych | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | StorageType — plik |
| **Odwołania**              | [Magazyn plików Azure](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [plików na platformę Azure magazynu SMB obsługę klientów systemu Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroki** | Magazyn plików Azure obsługuje protokół HTTPS przy użyciu interfejsu API REST, ale jest więcej powszechnie używany jako udział plików SMB dołączony do maszyny Wirtualnej. Protokół SMB 2.1 nie obsługuje szyfrowania, więc połączeń są dozwolone tylko w obrębie tego samego regionu platformy Azure. Jednak protokół SMB 3.0 obsługuje szyfrowanie i może być używany z systemem Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10, umożliwiając między region dostępu, a nawet dostępu na pulpicie. |

## <a id="cert-pinning"></a>Implementowanie funkcji przypinania certyfikatu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny, Windows Phone |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Certyfikat i przypinanie klucza publicznego](https://www.owasp.org/index.php/Certificate_and_Public_Key_Pinning#.Net) |
| **Kroki** | <p>Przypinanie certyfikatu defends przed atakami typu Man-In--Middle (MITM). Przypinanie to proces kojarzenia hosta z ich oczekiwanej X509 certyfikatu lub klucza publicznego. Po wiadomo lub widoczny dla hosta certyfikatu lub klucza publicznego certyfikatu lub klucza publicznego jest skojarzony lub przypięte do hosta. </p><p>W związku z tym podczas próby atakujący dokonuje czy ataki MITM protokołu SSL, podczas uzgadniania protokołu SSL klucza od osoby atakującej serwera będzie inny niż klucz przypiętych certyfikatów i żądanie zostanie odrzucone, uniemożliwiający certyfikatu MITM przypinanie można osiągnąć przez Implementowanie przez element ServicePointManager `ServerCertificateValidationCallback` delegowanie.</p>|

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

## <a id="https-transport"></a>Włącz protokół HTTPS - bezpieczny kanał transportu

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | NET Framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [uzyskania zawartości Królestwo](https://vulncat.fortify.com/en/detail?id=desc.semantic.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroki** | Konfiguracja aplikacji powinien upewnić się, że HTTPS jest używany przez wszystkie dostęp do poufnych informacji.<ul><li>**Wyjaśnienie:** Jeśli aplikacja obsługuje poufne informacje i nie używa szyfrowania na poziomie wiadomości, a następnie powinno być dozwolone tylko do komunikacji za pośrednictwem kanału zaszyfrowanych transportu.</li><li>**ZALECENIA:** upewnij się, że transportu HTTP jest wyłączona, a zamiast tego włączyć transportu HTTPS. Na przykład zastąpić `<httpTransport/>` z `<httpsTransport/>` tagu. Nie należy polegać na konfigurację sieci (zapory), aby zagwarantować, że aplikacja jest możliwy tylko za pośrednictwem bezpiecznego kanału. Z filozoficzne punktu widzenia aplikacji nie należy uwzględniać sieci na potrzeby jego zabezpieczeń.</li></ul><p>Z praktycznego punktu widzenia osoby odpowiedzialne za zabezpieczania sieci nie zawsze Śledź wymagania dotyczące zabezpieczeń aplikacji ich ewolucji.</p>|

## <a id="message-protection"></a>WCF: Zabezpieczenia komunikatów zestawu poziom ochrony EncryptAndSign

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroki** | <ul><li>**Wyjaśnienie:** podczas ochrony poziom ma ustawioną wartość "none" go spowoduje wyłączenie ochrony wiadomości. Poufność i integralności odbywa się z poziomu odpowiednie ustawienia.</li><li>**ZALECENIA:**<ul><li>gdy `Mode=None` — wyłącza ochronę wiadomości</li><li>gdy `Mode=Sign` -znaki ale szyfruje komunikat; jeśli ważne jest, integralność danych, należy użyć</li><li>gdy `Mode=EncryptAndSign` -znaki i szyfrowanie wiadomości</li></ul></li></ul><p>Rozważ wyłączenie szyfrowania i tylko podpisywania wiadomości, gdy musisz tylko sprawdzenia spójności informacji bez obaw poufności. Może to być przydatne w przypadku operacji lub usługi umów której należy dokonać weryfikacji adres oryginalnego nadawcy, ale są przesyłane żadne dane poufne. Podczas obniżania poziomu ochrony, należy zachować ostrożność, wiadomości nie zawiera żadnych informacji osobistych (dane osobowe).</p>|

### <a name="example"></a>Przykład
Konfigurowanie usługi i operację, aby się zarejestrować tylko komunikat jest wyświetlany w poniższych przykładach. Przykład kontraktu usługi `ProtectionLevel.Sign`: poniżej przedstawiono przykład użycia ProtectionLevel.Sign na poziomie kontraktu usługi: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Przykład
Przykład kontrakt operacji `ProtectionLevel.Sign` (Aby uzyskać szczegółową kontrolę): poniżej przedstawiono przykład użycia `ProtectionLevel.Sign` na poziomie OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a id="least-account-wcf"></a>WCF: Użyj najmniej uprzywilejowane konto uruchamiania usługi WCF

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | .NET framework 3 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroki** | <ul><li>**Wyjaśnienie:** nie są uruchamiane usługi WCF w ramach administratora lub konta wysokiego poziomu uprawnień. w przypadku naruszenia zabezpieczeń usługi spowoduje duże znaczenie.</li><li>**ZALECENIA:** używane konto uprzywilejowane najmniejszej do hostów usługi WCF, ponieważ jego zmniejsza możliwości ataku aplikacji i ograniczyć potencjalne szkody, jeśli są ataku. Jeśli konto usługi wymaga praw dodatkowe dostępu do zasobów infrastruktury, takich jak usługi MSMQ, dziennika zdarzeń, liczników wydajności i systemu plików, odpowiednich uprawnień należy podać dla tych zasobów, aby pomyślnie uruchomić usługę WCF.</li></ul><p>Jeśli usługa wymaga dostępu do określonych zasobów w imieniu oryginalny obiekt wywołujący, użyj personifikacji i delegowanie przepływ tożsamości obiektu wywołującego sprawdzanie autoryzacji podrzędne. W przypadku rozwoju należy użyć konta usługi sieci lokalnej, co jest specjalnym kontem wbudowanych, który ma ograniczone uprawnienia. W środowisku produkcyjnym należy utworzyć domeny niestandardowej najmniej uprzywilejowane konta usługi.</p>|

## <a id="webapi-https"></a>Wymuś cały ruch do interfejsów API sieci Web za pośrednictwem połączenia HTTPS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | MVC5, MVC6 |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wymuszanie protokołu SSL w kontrolerze interfejsu API sieci Web](http://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroki** | Jeśli aplikacja ma HTTPS i powiązanie HTTP, klienci mogą nadal używać protokołu HTTP dostęp do witryny. Aby tego uniknąć, należy użyć filtr akcji do zapewnienia żądań do interfejsów API chronionych zawsze przy użyciu protokołu HTTPS.|

### <a name="example"></a>Przykład 
Poniższy kod przedstawia filtr uwierzytelniania interfejsu API sieci Web, który sprawdza, czy protokół SSL: 
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
Dodaj filtr do wszystkich akcji interfejsu API sieci Web, które wymagają protokołu SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a id="redis-ssl"></a>Sprawdź, czy komunikacji z pamięcią podręczną Redis Azure za pośrednictwem protokołu SSL

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Redis Cache | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Pomoc techniczna platformy Azure Redis protokołu SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroki** | Redis serwer nie obsługuje bez protokołu SSL, ale nie w pamięci podręcznej Redis Azure. Jeśli łączysz się z pamięcią podręczną Redis Azure i klient obsługuje protokół SSL, takich jak programie StackExchange.Redis, należy użyć protokołu SSL. Domyślnie port bez protokołu SSL jest wyłączona dla nowego wystąpienia pamięci podręcznej Redis Azure. Upewnij się, że bezpieczny wartości domyślne nie są zmieniane, chyba że istnieje zależność na obsługę protokołu SSL dla klientów pamięci podręcznej redis. |

Należy pamiętać, że Redis zaprojektowano w celu uzyskiwał zaufanych klientów w środowiskach zaufanych. Oznacza to, że zwykle nie jest dobrym rozwiązaniem do udostępnienia wystąpienia pamięci podręcznej Redis bezpośrednio z Internetem lub, ogólnie rzecz biorąc, do środowiska, gdzie niezaufani klienci mogą bezpośredni dostęp do portu Redis TCP lub gniazda systemu UNIX. 

## <a id="device-field"></a>Bezpieczne urządzenie do komunikacji między bramą pola

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Pole IoT bramy | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | ND  |
| **Kroki** | Dla urządzeń na podstawie adresu IP protokół komunikacyjny można zwykle hermetyzowany w kanału SSL/TLS do ochrony przesyłanych danych. Dla innych protokołów, które nie obsługują protokołu SSL/TLS Sprawdź, czy istnieją bezpieczne wersje protokołu zapewniające zabezpieczeń w warstwie transportu lub wiadomości. |

## <a id="device-cloud"></a>Bezpieczne urządzenia bramy chmury komunikacji przy użyciu protokołu SSL/TLS

| Stanowisko                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama chmury IoT | 
| **Faza SDL**               | Kompilacja |  
| **Zastosowanie technologii** | Ogólny |
| **Atrybuty**              | ND  |
| **Odwołania**              | [Wybierz użytkownika protokołu komunikacyjnego](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroki** | Secure HTTP/AMQP lub protokołów MQTT przy użyciu protokołu SSL/TLS. |
