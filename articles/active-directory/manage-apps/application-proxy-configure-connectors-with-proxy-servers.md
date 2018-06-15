---
title: Praca z istniejącym lokalnych serwerów proxy i Azure AD | Dokumentacja firmy Microsoft
description: Uwzględniono również sposób pracy z istniejących serwerów proxy lokalnymi.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 0456a46262c6ad27b19487a6e4b2d1b6a139bbc3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
ms.locfileid: "34162029"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Praca z istniejącym lokalnych serwerów proxy

W tym artykule opisano sposób konfigurowania serwera Proxy aplikacji usługi Azure Active Directory (Azure AD) łączniki do pracy z serwerami serwera proxy ruchu wychodzącego. Przewodnik jest przeznaczony dla klientów z sieci środowiskach z istniejących serwerów proxy.

Rozpoczniemy analizując te scenariusze wdrażania głównego:
* Skonfiguruj konektory w celu obejścia proxy ruchu wychodzącego sieci lokalnej.
* Skonfiguruj konektory w celu użycia serwera proxy ruchu wychodzącego do serwera Proxy aplikacji usługi Azure AD.

Aby uzyskać więcej informacji na temat działania łączników, zobacz [łączniki serwera Proxy aplikacji usługi AD zrozumieć Azure](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Obejście proxy ruchu wychodzącego

Łączniki mają podstawowych składników systemu operacyjnego, które żądania wychodzącego. Te składniki automatycznie podejmować próby zlokalizowania serwera proxy w sieci przy użyciu autowykrywania serwera Proxy sieci Web (WPAD).

Składniki systemu operacyjnego podejmować próby zlokalizowania serwera proxy przeprowadzając wyszukiwania DNS dla wpad.domainsuffix. Jeśli wyszukiwanie jest rozpoznawany jako w systemie DNS, do adresu IP dla wpad.dat następnie jest przeprowadzane żądania HTTP. To żądanie staje się skrypt konfiguracji serwera proxy w danym środowisku. Łącznik korzysta ten skrypt, aby wybrać serwer proxy ruchu wychodzącego. Jednak ruch łącznika może nadal nie powiodło się, ze względu na dodatkowe ustawienia konfiguracji potrzebne na serwerze proxy.

Można skonfigurować łącznik, aby pominąć lokalnego serwera proxy do zapewnienia korzysta z bezpośredniego łączności usług Azure. Takie podejście, firma Microsoft zaleca tak długo, jak zasady sieci umożliwia on, ponieważ oznacza, że masz mniej jedną konfigurację do obsługi.

Aby wyłączyć użycia serwera proxy ruchu wychodzącego dla łącznika, przeprowadź edycję pliku C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy Connector\ApplicationProxyConnectorService.exe.config i Dodaj *system.net* sekcji pokazano w tym przykładowym kodzie:

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
Aby upewnić się, że aktualizator łącznika usługi również pomija serwera proxy, należy wprowadzić zmianę podobny do pliku ApplicationProxyConnectorUpdaterService.exe.config. Ten plik znajduje się w aktualizator łącznika serwera Proxy aplikacji C:\Program Files\Microsoft usługi AAD.

Należy pamiętać o wykonaniu kopii oryginalnych plików, w razie potrzeby można przywrócić w plikach .config domyślne.

## <a name="use-the-outbound-proxy-server"></a>Użyj serwera proxy ruchu wychodzącego

Niektóre środowiska wymagają cały ruch wychodzący do go za pośrednictwem serwera proxy ruchu wychodzącego, bez wyjątku. W związku z tym pomijanie serwera proxy nie jest opcją.

Możesz skonfigurować ruchu łącznika do go za pośrednictwem serwera proxy ruchu wychodzącego, jak pokazano na poniższym diagramie:

 ![Konfigurowanie łącznika ruchu można przejść za pośrednictwem serwera proxy ruchu wychodzącego do serwera Proxy aplikacji usługi Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

W wyniku o tylko ruchu wychodzącego, nie istnieje potrzeba aby skonfigurować dostęp przychodzących na zaporach.

>[!NOTE]
>Serwer Proxy aplikacji nie obsługuje uwierzytelniania na inne serwery proxy. Konta usług sieciowych łącznika/updater powinno być możliwe do nawiązania połączenia z serwerem proxy bez trwa wezwał do uwierzytelniania.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1: Konfigurowanie łącznika i powiązane usługi, aby przejść za pośrednictwem serwera proxy ruchu wychodzącego

Jeśli WPAD jest włączona w środowisku i prawidłowo skonfigurowany, łącznik automatycznie odnajduje serwera proxy ruchu wychodzącego, a następnie spróbuj go użyć. Można jednak jawnie skonfigurować łącznik, aby go za pośrednictwem serwera proxy ruchu wychodzącego.

Aby to zrobić, Edytuj plik C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy Connector\ApplicationProxyConnectorService.exe.config i Dodaj *system.net* sekcji pokazano w tym przykładowym kodzie. Zmień *proxyserver:8080* w celu uwzględnienia nazwę lokalnego serwera proxy serwera lub adres IP i portu nasłuchiwania na.

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

Skonfiguruj usługę aktualizator łącznika do wprowadzania podobne zmiany w pliku C:\Program Files\Microsoft usługi AAD aplikacji serwera Proxy łącznika Updater\ApplicationProxyConnectorUpdaterService.exe.config za pomocą serwera proxy.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2: Konfigurowanie serwera proxy zezwalająca na ruch z łącznika i powiązane usługi przepływają przez

Istnieją cztery aspektów, które należy wziąć pod uwagę w przypadku serwera proxy ruchu wychodzącego:
* Reguły ruchu wychodzącego serwera proxy
* Uwierzytelnianie serwera proxy
* Porty serwera proxy
* Inspekcja protokołu SSL

#### <a name="proxy-outbound-rules"></a>Reguły ruchu wychodzącego serwera proxy
Zezwalaj na dostęp do następujących punktów końcowych do łącznika usługi:

* *.msappproxy.net
* *.servicebus.windows.net

Początkowe rejestracyjny można zezwolić na dostęp do następujących punktów końcowych:

* login.windows.net
* login.microsoftonline.com

Jeśli nie umożliwiają nawiązywanie połączeń przez nazwę FQDN, a zamiast tego określ zakresy adresów IP, korzystać z tych opcji:

* Zezwalaj na dostęp ruchu wychodzącego łącznika do wszystkich miejsc docelowych.
* Zezwalaj na dostęp do ruchu wychodzącego łącznika do wszystkich [zakresy IP centrum danych Azure](https://www.microsoft.com/en-gb/download/details.aspx?id=41653). Wyzwanie z listy zakresów IP centrum danych Azure jest, że jest aktualizowana co tydzień. Należy umieścić procesu w celu zapewnienia odpowiednio aktualizowany reguł dostępu. Tylko przy użyciu podzbiór adresów IP może spowodować konfiguracji, aby przerwać.

#### <a name="proxy-authentication"></a>Uwierzytelnianie serwera proxy

Uwierzytelnianie serwera proxy nie jest obecnie obsługiwane. Nasze zalecenie, bieżący jest zezwala na dostęp anonimowy łącznika do Internetu miejsc docelowych.

#### <a name="proxy-ports"></a>Porty serwera proxy

Łącznik sprawia, że oparte na protokole SSL połączeń wychodzących przy użyciu metody CONNECT. Ta metoda powoduje ustawienie zasadniczo tunelu za pośrednictwem serwera proxy ruchu wychodzącego. Konfiguracja serwera proxy, aby umożliwić tunelowania na porty 443 i 80.

>[!NOTE]
>Po uruchomieniu usługi Service Bus przy użyciu protokołu HTTPS używa portu 443. Domyślnie usługi Service Bus podejmuje próbę bezpośredniego połączenia TCP i powraca do HTTPS tylko wtedy, gdy bezpośrednie połączenie między zakończy się niepowodzeniem.

#### <a name="ssl-inspection"></a>Inspekcja protokołu SSL
Nie należy używać protokołu SSL inspekcji dla ruchu łącznika, ponieważ powoduje problemy w przypadku ruchu łącznika. Łącznik korzysta z certyfikatu do uwierzytelnienia serwera Proxy aplikacji usługi i certyfikat mogą zostać utracone podczas kontroli protokołu SSL. 

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Rozwiązywanie problemów z łącznika serwera proxy problemów i problemy z połączeniem usługi
Teraz powinien być widoczny cały ruch przepływających przez serwer proxy. Jeśli masz problemy powinny pomóc następujące informacje dotyczące rozwiązywania problemów.

Najlepszym sposobem na identyfikowanie i rozwiązywanie problemów z łącznością łącznika jest przechwytywanie sieci podczas uruchamiania usługi łącznika. Poniżej przedstawiono zwięzłe porady dotyczące przechwytywania i filtrowania śladów sieciowych.

Można użyć narzędzia monitorowania wybranych przez użytkownika. Do celów tego artykułu użyliśmy programu Microsoft Message Analyzer. Możesz [Pobierz go z Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Poniższe przykłady dotyczą Message Analyzer, ale zasady mogą być stosowane do dowolnego narzędzia do analizy.

### <a name="take-a-capture-of-connector-traffic"></a>Przechwytywanie ruchu łącznika

Do rozwiązywania problemów początkowej, wykonaj następujące czynności:

1. Z services.msc należy zatrzymać usługę łącznika serwera Proxy aplikacji w usłudze Azure AD.

   ![Usługa Azure łącznika serwera Proxy aplikacji usługi AD w celu aplikację services.msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

2. Uruchom analizator komunikatów jako administrator.
3. Wybierz **Rozpocznij śledzenie lokalnego**.

   ![Uruchom przechwytywania ruchu sieciowego](./media/application-proxy-configure-connectors-with-proxy-servers/start-local-trace.png)

3. Uruchom usługę łącznika serwera Proxy aplikacji w usłudze Azure AD.
4. Zatrzymaj przechwytywania ruchu sieciowego.

   ![Zatrzymaj przechwytywania ruchu sieciowego](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Sprawdź, czy ruch sieciowy łącznika omija proxy ruchu wychodzącego

Jeśli skonfigurowano łącznik z serwera Proxy aplikacji w taki sposób, aby obejść serwerów proxy i połącz się bezpośrednio z usługą serwera Proxy aplikacji chcesz Szukaj w przechwytywania ruchu sieciowego dla nieudanych prób połączenia TCP. 

Użyj filtru Message Analyzer, aby zidentyfikować te próby. Wprowadź `property.TCPSynRetransmit` w polu filtru i wybierz **Zastosuj**. 

Pakiet SYN jest pierwszy pakiet wysyłany do nawiązywania połączeń TCP. Jeśli ten pakiet nie zwraca odpowiedź, SYN jest podjęta ponownie. Można użyć poprzedniego filtru, aby wyświetlić wszystkie syn ponownie przesłane. Następnie można sprawdzić, czy te syn odpowiadają żadnych ruch związany z usługą łącznika.

Jeśli łącznik, aby nawiązywać połączenia bezpośrednio z usług Azure, SynRetransmit odpowiedzi na porcie 443 są wskazanie, że masz problem sieci i zapory.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Sprawdź, czy ruch łącznik używa serwerów proxy ruchu wychodzącego

Po skonfigurowaniu łącznika serwera Proxy aplikacji ruch przechodzi przez serwery proxy ma być Szukaj w połączeniach https nie powiodło się z serwera proxy. 

Aby filtrować przechwytywania ruchu sieciowego dla tych prób połączenia, wprowadź `(https.Request or https.Response) and tcp.port==8080` w filtrze Message Analyzer, zastępując 8080 portów usługi serwera proxy. Wybierz **Zastosuj** wyników filtru. 

Poprzedni filtru zawiera tylko żądania HTTPs i odpowiedzi z port serwera proxy. Szukasz żądania połączenia, które Pokaż komunikacji z serwerem proxy. Na sukces otrzymasz odpowiedź HTTP OK (200).

Jeśli widzisz innych kodów odpowiedzi, na przykład 407 lub 502, oznacza to, że serwer proxy jest wymaganie uwierzytelniania lub nie zezwala na ruch innego powodu. W tym momencie Uwzględnij się z zespołem pomocy technicznej serwera proxy.

## <a name="next-steps"></a>Kolejne kroki

- [Zrozumienie łączniki serwera Proxy aplikacji usługi Azure AD](application-proxy-connectors.md)

- Jeśli masz problemy z problemów z łącznością łącznika, Zadaj pytanie w [forum usługi Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) lub Utwórz bilet z naszym zespołem pomocy technicznej.
