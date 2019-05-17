---
title: Pracy przy użyciu istniejących lokalnych serwerów proxy i usługą Azure AD | Dokumentacja firmy Microsoft
description: Opisano sposób pracy z istniejących serwerów proxy w środowisku lokalnym.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25b8669e5ca2abe2d763d9bc14f27ed9c4460886
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825953"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Praca z istniejących serwerów proxy w środowisku lokalnym

W tym artykule opisano sposób konfigurowania łączników serwera Proxy aplikacji usługi Azure Active Directory (Azure AD) do pracy z serwerami proxy ruchu wychodzącego. Jest ona przeznaczona dla klientów o środowiskach sieciowych, które mają istniejące serwery proxy.

Rozpoczniemy pracę, analizując następujących scenariuszy wdrażania główne:
* Skonfiguruj konektory w celu obejścia proxy ruchu wychodzącego usługi w środowisku lokalnym.
* Skonfiguruj konektory w celu dostępu serwera Proxy aplikacji usługi Azure AD do przy użyciu serwera proxy ruchu wychodzącego.

Aby uzyskać więcej informacji na temat działania łączników, zobacz [łączników serwera Proxy aplikacji usługi AD Azure zrozumienie](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Obejścia proxy ruchu wychodzącego

Łączniki mają podstawowych składników systemu operacyjnego, które żądania wychodzącego. Te składniki są automatycznie podejmować próby zlokalizowania serwera proxy w sieci przy użyciu autowykrywania serwera Proxy sieci Web (WPAD).

Składniki systemu operacyjnego próba zlokalizować serwer proxy, przeprowadzając wyszukiwania DNS dla wpad.domainsuffix. Jeśli wyszukiwanie jest rozpoznawany jako w systemie DNS, żądanie HTTP jest podejmowana wpad.dat adresu IP. To żądanie staje się skryptu konfiguracji serwera proxy w danym środowisku. Łącznik używa tego skryptu, aby wybrać serwer proxy ruchu wychodzącego. Jednak ruch łącznika może być nadal nie powiodło się, ze względu na dodatkowe ustawienia konfiguracji potrzebne na serwerze proxy.

Można skonfigurować łącznik Aby pominąć serwer proxy w środowisku lokalnym, aby upewnić się, że używa on bezpośredniego połączenia z usługami platformy Azure. Zalecamy takie podejście, tak długo, jak zasady sieci umożliwia on, ponieważ oznacza, że masz co mniej konfigurację, aby zachować.

Aby wyłączyć użycie serwera proxy ruchu wychodzącego dla łącznika, Edytuj plik C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy Connector\ApplicationProxyConnectorService.exe.config i Dodaj *przestrzeni nazw system.net* sekcji pokazano, w tym przykładowym kodzie:

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
Aby upewnić się, że usługa aktualizator łącznika pomija również serwer proxy, należy wprowadzić podobne zmianę do pliku ApplicationProxyConnectorUpdaterService.exe.config. Ten plik znajduje się w aktualizator łącznika serwera Proxy aplikacji usługi AAD C:\Program Files\Microsoft.

Upewnij się utworzyć kopie oryginalnych plików w przypadku, gdy konieczne jest przywrócenie domyślnych plików .config.

## <a name="use-the-outbound-proxy-server"></a>Użyj serwera proxy ruchu wychodzącego

Niektóre środowiska wymagają cały ruch wychodzący za pośrednictwem serwera proxy ruchu wychodzącego, bez wyjątku. W rezultacie pomijanie serwera proxy nie jest opcją.

Można skonfigurować łącznik ruch za pośrednictwem serwera proxy ruchu wychodzącego, jak pokazano na poniższym diagramie:

 ![Konfigurowanie łącznika ruchu, przechodzić przez serwer proxy ruchu wychodzącego serwera proxy aplikacji usługi Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

Wyniku mających tylko ruchu wychodzącego, nie ma potrzeby konfigurowania dostępu ruchu przychodzącego za pośrednictwem zapory.

>[!NOTE]
>Serwer Proxy aplikacji nie obsługuje uwierzytelniania do innych serwerów proxy. Konta usług sieciowych aktualizator łącznika/powinny być możliwe nawiązanie połączenia z serwerem proxy bez jest kwestionowana do uwierzytelniania.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurowanie łącznika i powiązanych usług za pośrednictwem serwera proxy ruchu wychodzącego

Jeśli WPAD jest włączona w środowisku i odpowiednio skonfigurowane, łącznik automatycznie odnajduje serwera proxy ruchu wychodzącego i niepomyślna próba użycia go. Jednak jawnie skonfigurujesz łącznik za pośrednictwem serwera proxy ruchu wychodzącego.

Aby to zrobić, Edytuj plik C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy Connector\ApplicationProxyConnectorService.exe.config i Dodaj *przestrzeni nazw system.net* sekcji pokazano, w tym przykładzie kodu. Zmiana *proxyserver:8080* w celu uwzględnienia nazwy serwera proxy w lokalnych lub adres IP i portu nasłuchiwania na. Wartość musi zawierać prefiks http://, nawet wtedy, gdy używasz adresu IP.

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

Następnie skonfiguruj usługi aktualizator łącznika w celu używania serwera proxy, wprowadzając zmiany podobne do pliku C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy łącznika Updater\ApplicationProxyConnectorUpdaterService.exe.config.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Skonfiguruj serwer proxy, aby zezwolić na ruch z łącznika i powiązanych usług przepływ

Istnieją cztery aspektów do uwzględnienia w serwera proxy ruchu wychodzącego:
* Reguły ruchu wychodzącego serwera proxy
* Uwierzytelnianie serwera proxy
* Porty serwera proxy
* Inspekcji połączenia SSL

#### <a name="proxy-outbound-rules"></a>Reguły ruchu wychodzącego serwera proxy
Zezwól na dostęp do następujących adresów URL:

| Adres URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Platforma Azure używa tych adresów URL do weryfikacji certyfikatów |
| login.windows.net<br>login.microsoftonline.com | Łącznik używa tych adresów URL podczas procesu rejestracji. |

Jeśli zapora lub serwer proxy umożliwia umieszczanie nazw DNS na białej liście, możesz dodać do niej połączenia z adresami URL \*msappproxy.net i \*servicebus.windows.net. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Zakresy adresów IP są aktualizowane co tydzień.


Jeśli nie można zezwolić na połączenie z w pełni kwalifikowaną nazwę domeny, należy określić zakresy adresów IP zamiast tego należy użyć tych opcji:

* Zezwalaj na dostęp ruchu wychodzącego łącznika do wszystkich miejsc docelowych.
* Zezwalaj na dostęp ruchu wychodzącego łącznika do wszystkich [zakresy IP centrów danych platformy Azure](https://www.microsoft.com//download/details.aspx?id=41653). Wyzwanie przy użyciu listy zakresów adresów IP centrum danych platformy Azure polega na tym, że jest aktualizowana co tydzień. Należy umieścić procesu w celu zapewnienia reguł dostępu są odpowiednio aktualizowane. Tylko za pomocą podzestawu adresów IP może spowodować konfigurację tak, aby przerwać.

#### <a name="proxy-authentication"></a>Uwierzytelnianie serwera proxy

Uwierzytelnianie serwera proxy nie jest obecnie obsługiwane. Nasz bieżący zaleca się zezwolić na dostęp anonimowy łącznika do miejsc docelowych w Internecie.

#### <a name="proxy-ports"></a>Porty serwera proxy

Łącznik sprawia, że połączenia wychodzące opartym na protokole SSL przy użyciu metody CONNECT. Ta metoda konfiguruje zasadniczo tunel za pośrednictwem serwera proxy ruchu wychodzącego. Konfiguracja serwera proxy, aby umożliwić tunelowania do porty 443 i 80.

>[!NOTE]
>Po uruchomieniu usługi Service Bus przy użyciu protokołu HTTPS używa portu 443. Domyślnie Usługa Service Bus podejmuje próbę bezpośredniego połączenia TCP i nastąpi powrót do protokołu HTTPS, tylko wtedy, gdy połączenie bezpośrednie nie powiedzie się.

#### <a name="ssl-inspection"></a>Inspekcji połączenia SSL
Nie należy używać inspekcji połączenia SSL dla ruchu łącznika, ponieważ będzie powodował problemy dla ruchu łącznika. Łącznik używa certyfikatu do uwierzytelniania serwera Proxy aplikacji usługi i certyfikat mogą zostać utracone podczas inspekcji połączenia SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Rozwiązywanie problemów z łącznika serwera proxy i problemy z połączeniem usługi
Teraz powinien być widoczny całego ruchu odbywającego się za pośrednictwem serwera proxy. Jeśli masz problemy, powinny pomóc następujące informacje dotyczące rozwiązywania problemów.

Najlepszym sposobem na identyfikowanie i rozwiązywanie problemów z łącznością łącznika jest przechwytywanie sieci podczas uruchamiania usługi łącznika. Poniżej przedstawiono kilka porad na przechwytywaniu i filtrowanie danych śledzenia sieci.

Można użyć narzędzia do monitorowania wybranych przez użytkownika. Do celów ten artykuł użyliśmy Microsoft Message Analyzer. Możesz [ją pobrać z Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Poniższe przykłady są specyficzne dla analizatora komunikatów, ale zasady mogą być stosowane do dowolnego narzędzia do analizy.

### <a name="take-a-capture-of-connector-traffic"></a>Przechwytywanie ruchu łącznika

Do rozwiązywania problemów początkowej, wykonaj następujące czynności:

1. Za pomocą polecenia services.msc należy zatrzymać usługę łącznika serwera Proxy aplikacji w usłudze Azure AD.

   ![Usługa łącznika serwera Proxy aplikacji usługi AD w pliku services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Message Analyzer Uruchom jako administrator.
3. Wybierz **Rozpocznij śledzenie lokalnych**.

   ![Rozpocznij rejestrowanie sieci](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Uruchom usługę łącznika serwera Proxy aplikacji w usłudze Azure AD.
4. Zatrzymaj przechwytywanie sieci.

   ![Zatrzymaj przechwytywanie sieci](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Sprawdź, jeśli łącznik ruch pomija proxy ruchu wychodzącego

Skonfigurowanie łącznika serwera Proxy aplikacji obejść serwerów proxy i połączyć się bezpośrednio z usługą serwera Proxy aplikacji mają do przeszukania przechwytywania sieci dla zakończonych niepowodzeniem prób połączenia TCP. 

Użyj filtru analizatora komunikatów, aby zidentyfikować te próby. Wprowadź `property.TCPSynRetransmit` w polu filtru i wybierz pozycję **Zastosuj**. 

Pakiet SYN jest pierwszym pakietów wysyłanych do ustanawiania połączenia protokołu TCP. Jeśli ten pakiet nie zwracają odpowiedzi, jest ponowieniu SYN. Poprzedni filtr umożliwia Zobacz wszystkie syn ponownie przesłane. Następnie możesz sprawdzić, czy te syn odnoszą się do dowolnego ruchu związane z łącznika.

Jeśli oczekujesz, że łącznik do nawiązywania bezpośredniego połączenia z usługami platformy Azure, SynRetransmit odpowiedzi na porcie 443 są wskazanie, że masz problem z siecią lub zaporą.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Sprawdź, jeśli ruch łącznik używa serwerów proxy ruchu wychodzącego

Jeśli skonfigurowano ruchu łącznika serwera Proxy aplikacji za pośrednictwem serwerów proxy, chcesz poszukaj połączenia https nie powiodło się z serwera proxy. 

Aby filtrować przechwytywania sieci dla tych próby nawiązania połączenia, wprowadź `(https.Request or https.Response) and tcp.port==8080` w filtrze Message Analyzer, zastępując 8080 portów usługi serwera proxy. Wybierz **Zastosuj** wyników filtru. 

Poprzedni filtru zawiera tylko żądania HTTPs i odpowiedzi z port serwera proxy. Szukasz żądań CONNECT, które pokazują komunikacji z serwerem proxy. W razie powodzenia otrzymasz odpowiedź HTTP OK (200).

Jeśli zobaczysz inne kody odpowiedzi, takie jak 407 lub 502, oznacza to, że serwer proxy wymaga uwierzytelniania lub innego powodu nie zezwala na ruch. W tym momencie angażować się z zespołem pomocy technicznej serwera proxy.

## <a name="next-steps"></a>Kolejne kroki

- [Omówienie łączników serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)

- Jeśli masz problemy z problemów z łącznością łącznika, Zadaj pytanie [forum usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) lub Utwórz bilet z naszym zespołem pomocy technicznej.
