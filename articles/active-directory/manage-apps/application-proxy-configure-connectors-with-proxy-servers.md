---
title: Praca z istniejącymi lokalnymi serwerami proxy i usługą Azure AD | Dokumenty firmy Microsoft
description: Obejmuje sposób pracy z istniejącymi lokalnymi serwerami proxy.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe3a63e119fed6825982b9de13bc78cb7da5415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481402"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Praca z istniejącymi lokalnymi serwerami proxy

W tym artykule wyjaśniono, jak skonfigurować łączniki serwera proxy aplikacji usługi Azure Active Directory (Azure AD) do pracy z wychodzącymi serwerami proxy. Ten artykuł jest przeznaczony dla klientów mających serwery proxy w swoich środowiskach sieciowych.

Zaczynamy od przyjrzenia się tym głównym scenariuszom wdrażania:

* Skonfiguruj łączniki, aby pominąć lokalne wychodzące serwery proxy.
* Konfigurowanie łączników do używania wychodzącego serwera proxy w celu uzyskania dostępu do serwera proxy aplikacji usługi Azure AD.

Aby uzyskać więcej informacji na temat działania łączników, zobacz [Understand Azure AD Application Proxy connectors (Omówienie łączników serwerów proxy aplikacji usługi Azure AD)](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Pomijanie serwerów proxy ruchu wychodzącego

Łączniki mają bazowe składniki systemu operacyjnego, które wysyłają żądania wychodzące. Te składniki automatycznie podejmują próby zlokalizowania serwera proxy w sieci przy użyciu usługi autowykrywania serwera proxy w sieci (WPAD).

Składniki systemu operacyjnego próbują zlokalizować serwer proxy, przeprowadzając wyszukiwania DNS dla domeny wpad.domainsuffix. Jeśli wyszukiwanie zostanie rozwiązane w systemie DNS, wysyłane jest żądanie HTTP na adres IP domeny wpad.dat. To żądanie staje się skryptem konfiguracji serwera proxy w danym środowisku. Łącznik używa tego skryptu w celu wybrania serwera proxy ruchu wychodzącego. Jednak ruch łącznika może nadal nie być przepuszczany z powodu braku dodatkowych ustawień konfiguracji wymaganych na tym serwerze proxy.

Łącznik można skonfigurować w taki sposób, aby pomijał lokalny serwer proxy w celu zapewnienia korzystania z bezpośredniej łączności z usługami platformy Azure. Zalecamy takie podejście, jeśli tylko zezwalają na to zasady sieci, ponieważ w ten sposób zostanie zmniejszona o jedną liczba obsługiwanych konfiguracji.

Aby wyłączyć użycie wychodzącego serwera proxy dla łącznika, edytuj plik C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config i dodaj *sekcję system.net* pokazaną w tym przykładzie kodu:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Aby upewnić się, że usługa updater łącznika również pomija serwer proxy, należy wprowadzić podobną zmianę do pliku ApplicationProxyConnectorUpdaterService.exe.config. Ten plik znajduje się pod adresem C:\Program Files\Microsoft AAD App Proxy Connector Updater.

Pamiętaj o utworzeniu kopii oryginalnych plików. W ten sposób będzie można przywrócić domyślne pliki .config, jeśli zajdzie taka potrzeba.

## <a name="use-the-outbound-proxy-server"></a>Korzystanie z wychodzącego serwera proxy

Niektóre środowiska wymagają całego ruchu wychodzącego, aby przejść przez wychodzący serwer proxy, bez wyjątku. W rezultacie ominięcie serwera proxy nie jest opcją.

Ruch łącznika można skonfigurować tak, aby przechodził przez wychodzący serwer proxy, jak pokazano na poniższym diagramie:

 ![Konfigurowanie ruchu łącznika w celu przechodzenia przez wychodzący serwer proxy do serwera proxy aplikacji usługi Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

W wyniku posiadania tylko ruchu wychodzącego nie ma potrzeby konfigurowania dostępu przychodzącego za pośrednictwem zapór.

> [!NOTE]
> Serwer proxy aplikacji nie obsługuje uwierzytelniania innych serwerów proxy. Konta usługi sieciowej łącznika/aktualatora sieci powinny mieć możliwość łączenia się z serwerem proxy bez kwestionowania uwierzytelniania.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurowanie łącznika i powiązanych usług, aby przejść przez wychodzący serwer proxy

Jeśli WPAD jest włączona w środowisku i odpowiednio skonfigurowane, łącznik automatycznie odnajduje wychodzący serwer proxy i próbuje go użyć. Można jednak jawnie skonfigurować łącznik, aby przejść przez wychodzący serwer proxy.

W tym celu należy edytować plik C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config i dodać *sekcję system.net* pokazaną w tym przykładzie kodu. Zmień *serwer proxy:8080,* aby odzwierciedlić nazwę lokalnego serwera proxy lub adres IP oraz port, na który nasłuchuje. Wartość musi mieć prefiks http:// nawet jeśli używasz adresu IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Następnie skonfiguruj usługę Connector Updater do używania serwera proxy, wprowadzać podobną zmianę do pliku C:\Program Files\Microsoft AAD App Proxy Connector Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Skonfiguruj serwer proxy, aby umożliwić przepływ ruchu ze łącznika i powiązanych usług

Istnieją cztery aspekty, które należy wziąć pod uwagę w wychodzącym serwerze proxy:

* Reguły wychodzące serwera proxy
* Uwierzytelnianie serwera proxy
* Porty proxy
* Inspekcja TLS

#### <a name="proxy-outbound-rules"></a>Reguły wychodzące serwera proxy

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Łącznik używa tych adresów URL do weryfikacji certyfikatów |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*microsoftonline.com<br>*.microsoftonline-p.com<br>*msauth.net<br>*.msauthimages.net .msauthimages.net<br>*msecnd.net<br>*.msftauth.net<br>*msftauthimages.net<br>*.phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Łącznik używa tych adresów URL podczas procesu rejestracji. |

Jeśli zapora lub serwer proxy umożliwia konfigurowanie list dozwolonych \*dns, można \*zezwolić na połączenia z msappproxy.net i servicebus.windows.net. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Zakresy adresów IP są aktualizowane co tydzień.

Jeśli nie możesz zezwolić na łączność przez nazwę FQDN i zamiast tego musisz określić zakresy adresów IP, użyj następujących opcji:

* Zezwalaj łącznikowi na dostęp wychodzący do wszystkich miejsc docelowych.
* Zezwól łącznikowi na dostęp wychodzący do wszystkich [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com//download/details.aspx?id=41653). Wyzwaniem przy użyciu listy zakresów adresów IP centrum danych platformy Azure jest to, że jest aktualizowana co tydzień. Należy wprowadzić proces, aby upewnić się, że reguły dostępu są odpowiednio aktualizowane. Tylko przy użyciu podzbioru adresów IP może spowodować przerwanie konfiguracji.

#### <a name="proxy-authentication"></a>Uwierzytelnianie serwera proxy

Uwierzytelnianie serwera proxy nie jest obecnie obsługiwane. Naszym obecnym zaleceniem jest umożliwienie łącznikowi anonimowego dostępu do miejsc docelowych w Internecie.

#### <a name="proxy-ports"></a>Porty proxy

Łącznik wykonuje wychodzące połączenia oparte na TLS przy użyciu metody CONNECT. Ta metoda zasadniczo konfiguruje tunel za pośrednictwem wychodzącego serwera proxy. Skonfiguruj serwer proxy, aby zezwolił na tunelowanie do portów 443 i 80.

> [!NOTE]
> Gdy usługa Service Bus jest uruchamiana przez protokół HTTPS, używa portu 443. Jednak domyślnie usługa Service Bus próbuje kierować połączenia TCP i powraca do protokołu HTTPS tylko wtedy, gdy łączność bezpośrednia nie powiedzie się.

#### <a name="tls-inspection"></a>Inspekcja TLS

Nie należy używać inspekcji TLS dla ruchu łącznika, ponieważ powoduje to problemy dla ruchu łącznika. Łącznik używa certyfikatu do uwierzytelniania w usłudze Proxy aplikacji, a ten certyfikat może zostać utracony podczas inspekcji TLS.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Rozwiązywanie problemów z serwerem proxy łączników i problemy z łącznością usługi

Teraz powinieneś zobaczyć cały ruch przepływający przez serwer proxy. Jeśli masz problemy, następujące informacje dotyczące rozwiązywania problemów powinny pomóc.

Najlepszym sposobem identyfikowania i rozwiązywania problemów z łącznością łącznika jest przejęcie sieci podczas uruchamiania usługi łącznika. Oto kilka szybkich wskazówek dotyczących przechwytywania i filtrowania śladów sieci.

Możesz użyć wybranego narzędzia do monitorowania. Na potrzeby tego artykułu użyliśmy microsoft message analyzer. Możesz [go pobrać z firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Poniższe przykłady są specyficzne dla analizatora komunikatów, ale zasady mogą być stosowane do dowolnego narzędzia analizy.

### <a name="take-a-capture-of-connector-traffic"></a>Przechwytywanie ruchu łącznika

Aby rozwiązać problemy z badaniem na początku, wykonaj następujące czynności:

1. Z services.msc zatrzymaj usługę Łącznik serwera proxy aplikacji usługi Azure AD.

   ![Usługa łącznika serwera proxy aplikacji usługi Azure AD w pliku services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Uruchom analizator komunikatów jako administrator.
1. Wybierz **pozycję Rozpocznij śledzenie lokalne**.
1. Uruchom usługę Łącznik serwera proxy aplikacji usługi Azure AD.
1. Zatrzymaj przechwytywanie sieci.

   ![Zrzut ekranu przedstawiający przycisk Zatrzymaj przechwytywanie sieci](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Sprawdź, czy ruch łącznika omija wychodzące serwery proxy

Jeśli łącznik serwera proxy aplikacji został skonfigurowany do omijania serwerów proxy i łączenia się bezpośrednio z usługą Serwera proxy aplikacji, należy wyszukać w przechwytywaniu sieci nieudane próby połączenia TCP.

Użyj filtru Analizator wiadomości, aby zidentyfikować te próby. Wprowadź `property.TCPSynRetransmit` w polu filtru i wybierz pozycję **Zastosuj**.

Pakiet SYN jest pierwszym pakietem wysłanym w celu nawiązania połączenia TCP. Jeśli ten pakiet nie zwraca odpowiedzi, SYN jest ponownie próbowany. Za pomocą poprzedniego filtru można wyświetlić wszystkie retransmitowane numery SYN. Następnie można sprawdzić, czy te numery SY odpowiadają ruchowi związanemu ze łącznikiem.

Jeśli oczekujesz, że łącznik do nawiązywać bezpośrednie połączenia z usługami platformy Azure, Odpowiedzi SynRetransmit na porcie 443 są wskazanie, że masz problem z siecią lub zaporą.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Sprawdź, czy ruch łącznika używa wychodzących serwerów proxy

Jeśli skonfigurowano ruch łącznika serwera proxy aplikacji, aby przejść przez serwery proxy, chcesz wyszukać nieudane połączenia https z serwerem proxy.

Aby filtrować przechwytywanie sieci dla `(https.Request or https.Response) and tcp.port==8080` tych prób połączenia, wprowadź filtr Analizator komunikatów, zastępując 8080 portem usługi serwera proxy. Wybierz **zastosuj,** aby wyświetlić wyniki filtru.

Poprzedni filtr pokazuje tylko żądania HTTPs i odpowiedzi do/z portu serwera proxy. Szukasz żądań CONNECT, które pokazują komunikację z serwerem proxy. Po sukcesie otrzymasz odpowiedź HTTP OK (200).

Jeśli widzisz inne kody odpowiedzi, takie jak 407 lub 502, oznacza to, że serwer proxy wymaga uwierzytelniania lub nie zezwala na ruch z innego powodu. W tym momencie można zaangażować zespół pomocy technicznej serwera proxy.

## <a name="next-steps"></a>Następne kroki

* [Opis łączników serwera proxy aplikacji usługi Azure AD](application-proxy-connectors.md)
* Jeśli masz problemy z łącznością łącznika, zadaj pytanie na [forum usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) lub utwórz bilet z naszym zespołem pomocy technicznej.
