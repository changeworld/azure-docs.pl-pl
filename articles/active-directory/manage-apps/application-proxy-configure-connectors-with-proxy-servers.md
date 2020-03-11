---
title: Współpraca z istniejącymi lokalnymi serwerami proxy i usługą Azure AD | Microsoft Docs
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
ms.openlocfilehash: 5d7c7d9f6d59ffd57ddb14f7c060d0a3f6f2a6eb
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967751"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Współdziałanie z istniejącymi lokalnymi serwerami proxy

W tym artykule opisano sposób konfigurowania łączników serwera proxy aplikacji Azure Active Directory (Azure AD) do pracy z wychodzącymi serwerami proxy. Jest ona przeznaczona dla klientów ze środowiskami sieciowymi, które mają istniejące serwery proxy.

Zacznijmy od przejrzenia następujących głównych scenariuszy wdrażania:

* Skonfiguruj łączniki, aby pominąć lokalne wychodzące serwery proxy.
* Skonfiguruj łączniki do korzystania z wychodzącego serwera proxy w celu uzyskania dostępu do usługi Azure serwer proxy aplikacji usługi Azure AD.

Aby uzyskać więcej informacji o działaniu łączników, zobacz [Omówienie łączników usługi Azure serwer proxy aplikacji usługi Azure AD](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Pomijaj wychodzące serwery proxy

Łączniki mają podstawowe składniki systemu operacyjnego, które tworzą żądania wychodzące. Te składniki automatycznie próbują zlokalizować serwer proxy w sieci przy użyciu funkcji autowykrywania serwera proxy sieci Web (WPAD).

Składniki systemu operacyjnego próbują zlokalizować serwer proxy, wykonując wyszukiwanie DNS dla WPAD. domainsuffix. Jeśli wyszukiwanie jest rozpoznawane w systemie DNS, żądanie HTTP zostaje następnie wprowadzone do adresu IP dla usługi WPAD. dat. To żądanie jest skryptem konfiguracji serwera proxy w Twoim środowisku. Łącznik używa tego skryptu do wybrania wychodzącego serwera proxy. Jednak ruch łącznika może nadal się nie przechodzić z powodu dodatkowych ustawień konfiguracji wymaganych na serwerze proxy.

Łącznik można skonfigurować tak, aby pomijał lokalny serwer proxy, aby upewnić się, że korzysta on z bezpośredniej łączności z usługami platformy Azure. Zalecamy takie podejście, o ile jest to dozwolone przez zasady sieci, ponieważ oznacza to, że istnieje mniej konfiguracji do obsługi.

Aby wyłączyć użycie wychodzącego serwera proxy dla łącznika, edytuj plik Connector\ApplicationProxyConnectorService.exe.config proxy aplikacji usługi AAD i Dodaj sekcję *System.NET* pokazaną w tym przykładzie kodu:

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

Aby upewnić się, że usługa Aktualizator łączników również pomija serwer proxy, należy wprowadzić podobną zmianę do pliku ApplicationProxyConnectorUpdaterService. exe. config. Ten plik znajduje się w folderze C:\Program Files\Microsoft AAD App proxy Aktualizator łączników.

Pamiętaj, aby utworzyć kopie oryginalnych plików, na wypadek konieczności przywrócenia plików default. config.

## <a name="use-the-outbound-proxy-server"></a>Korzystanie z serwera proxy wychodzącego

Niektóre środowiska wymagają, aby cały ruch wychodzący przechodził przez wychodzący serwer proxy, bez wyjątku. W związku z tym pomijanie serwera proxy nie jest opcją.

Ruch łącznika można skonfigurować tak, aby przechodził przez wychodzący serwer proxy, jak pokazano na poniższym diagramie:

 ![Konfigurowanie ruchu łącznika w celu przechodzenia przez wychodzący serwer proxy do usługi Azure serwer proxy aplikacji usługi Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

W wyniku posiadania tylko ruchu wychodzącego nie ma potrzeby konfigurowania dostępu przychodzącego za pomocą zapór.

> [!NOTE]
> Serwer proxy aplikacji nie obsługuje uwierzytelniania do innych serwerów proxy. Konta usług sieciowych łączników/Aktualizator powinny mieć możliwość łączenia się z serwerem proxy bez konieczności uwierzytelniania.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1. Konfigurowanie łącznika i powiązanych usług w celu przechodzenia przez wychodzący serwer proxy

Jeśli funkcja WPAD jest włączona w środowisku i odpowiednio skonfigurowana, łącznik automatycznie odnajdzie wychodzący serwer proxy i spróbuje go użyć. Można jednak jawnie skonfigurować łącznik, aby przechodzić przez wychodzący serwer proxy.

W tym celu należy edytować plik C:\Program Files\Microsoft AAD proxy aplikacji Connector\ApplicationProxyConnectorService.exe.config i dodać sekcję *System.NET* pokazaną w tym przykładzie kodu. Zmień *ProxyServer: 8080* , aby odzwierciedlała nazwę lokalnego serwera proxy lub adres IP oraz port, na którym nasłuchuje. Wartość musi mieć prefiks http://, nawet jeśli jest używany adres IP.

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

Następnie skonfiguruj usługę Aktualizator łączników do korzystania z serwera proxy, wprowadzając podobną zmianę do pliku Updater\ApplicationProxyConnectorUpdaterService.exe.config łącznika serwera proxy aplikacji usługi AAD.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2. Konfigurowanie serwera proxy w celu zezwalania na przepływ ruchu z łącznika i powiązanych usług

Istnieje cztery aspekty, które należy wziąć pod uwagę na wychodzącym serwerze proxy:

* Reguły ruchu wychodzącego serwera proxy
* Uwierzytelnianie serwera proxy
* Porty serwera proxy
* Inspekcja SSL

#### <a name="proxy-outbound-rules"></a>Reguły ruchu wychodzącego serwera proxy

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Zastosowanie |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Łącznik używa tych adresów URL do weryfikowania certyfikatów |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>*. microsoftonline.com<br>* . microsoftonline-p.com<br>*. msauth.net<br>* . msauthimages.NET<br>*. msecnd.net<br>* . msftauth.NET<br>*. msftauthimages.net<br>* . PhoneFactor.NET<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Łącznik używa tych adresów URL podczas procesu rejestracji. |

Jeśli zapora lub serwer proxy umożliwia skonfigurowanie list dozwolonych DNS, można zezwolić na połączenia z \*. msappproxy.net i \*. servicebus.windows.net. W przeciwnym razie musisz zezwolić na dostęp do [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com/download/details.aspx?id=41653). Zakresy adresów IP są aktualizowane co tydzień.

Jeśli nie możesz zezwolić na połączenie przy użyciu nazwy FQDN i należy określić zakresy adresów IP, użyj następujących opcji:

* Zezwalaj na dostęp wychodzący łącznika do wszystkich miejsc docelowych.
* Zezwalaj na dostęp wychodzący łącznika do wszystkich [zakresów adresów IP centrum danych platformy Azure](https://www.microsoft.com//download/details.aspx?id=41653). Wezwanie do korzystania z listy zakresów adresów IP centrum danych platformy Azure jest aktualizowane co tydzień. Należy umieścić proces, aby upewnić się, że reguły dostępu są odpowiednio zaktualizowane. Tylko podzbiór adresów IP może spowodować przerwanie konfiguracji.

#### <a name="proxy-authentication"></a>Uwierzytelnianie serwera proxy

Uwierzytelnianie serwera proxy nie jest obecnie obsługiwane. Nasze bieżące zalecenia polegają na umożliwieniu anonimowego dostępu do Internetu do miejsc docelowych.

#### <a name="proxy-ports"></a>Porty serwera proxy

Łącznik tworzy wychodzące połączenia oparte na protokole SSL przy użyciu metody CONNECT. Ta metoda zasadniczo konfiguruje tunel za pomocą wychodzącego serwera proxy. Skonfiguruj serwer proxy w taki sposób, aby zezwalał na tunelowanie do portów 443 i 80.

> [!NOTE]
> Gdy Service Bus jest uruchamiany za pośrednictwem protokołu HTTPS, korzysta z portu 443. Jednak domyślnie Service Bus próbuje kierować połączenia protokołu TCP i wraca do protokołu HTTPS tylko w przypadku niepowodzenia łączności bezpośredniej.

#### <a name="ssl-inspection"></a>Inspekcja SSL

Nie należy używać inspekcji protokołu SSL dla ruchu łącznika, ponieważ powoduje to problemy związane z ruchem łącznika. Łącznik używa certyfikatu do uwierzytelniania w usłudze serwera proxy aplikacji i ten certyfikat może zostać utracony podczas inspekcji protokołu SSL.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Rozwiązywanie problemów z serwerem proxy łącznika i problemów z łącznością usług

Teraz powinien zostać wyświetlony cały ruch przepływający przez serwer proxy. Jeśli masz problemy, następujące informacje dotyczące rozwiązywania problemów powinny pomóc.

Najlepszym sposobem identyfikowania i rozwiązywania problemów z łącznością łącznika jest przechwycenie sieci podczas uruchamiania usługi łącznika. Poniżej znajdują się krótkie porady dotyczące przechwytywania i filtrowania śladów sieci.

Możesz użyć wybranego narzędzia do monitorowania. Na potrzeby tego artykułu korzystamy z programu Microsoft Message Analyzer. Możesz [pobrać go z firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=44226).

Poniższe przykłady są specyficzne dla analizatora komunikatów, ale zasady można zastosować do dowolnego narzędzia do analizy.

### <a name="take-a-capture-of-connector-traffic"></a>Przechwyć ruch łącznika

W celu wstępnego rozwiązywania problemów wykonaj następujące czynności:

1. Z poziomu Services. msc Zatrzymaj usługę Azure serwer proxy aplikacji usługi Azure AD Connector.

   ![Usługa łącznika usługi Azure serwer proxy aplikacji usługi Azure AD w usługach Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Uruchom analizatora komunikatów jako administrator.
1. Wybierz pozycję **Rozpocznij śledzenie lokalne**.
1. Uruchom usługę łącznika usługi Azure serwer proxy aplikacji usługi Azure AD.
1. Zatrzymaj przechwytywanie sieci.

   ![Zrzut ekranu przedstawia przycisk Zatrzymaj przechwytywanie sieci](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Sprawdź, czy ruch łącznika pomija wychodzące serwery proxy

W przypadku skonfigurowania łącznika serwera proxy aplikacji w celu obejścia serwerów proxy i nawiązania bezpośredniego połączenia z usługą serwera proxy aplikacji należy wyszukać funkcję przechwytywania sieci pod kątem nieudanych prób nawiązania połączenia TCP.

Użyj filtru analizatora komunikatów, aby zidentyfikować te próby. Wprowadź `property.TCPSynRetransmit` w polu Filtr i wybierz pozycję **Zastosuj**.

Pakiet SYN to pierwszy pakiet wysyłany w celu nawiązania połączenia TCP. Jeśli ten pakiet nie zwraca odpowiedzi, zostanie podjęta ponowna próba wykonania operacji SYN. Możesz użyć poprzedniego filtru, aby zobaczyć wszystkie reprzesłane SYNs. Następnie można sprawdzić, czy te SYNs odpowiadają jakimkolwiek ruchowi związanym z łącznikiem.

Jeśli oczekujesz, że łącznik ma kierować bezpośrednie połączenia z usługami platformy Azure, odpowiedzi SynRetransmit na porcie 443 są oznaką problemu z siecią lub zaporą.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Sprawdź, czy ruch łącznika używa wychodzących serwerów proxy

Jeśli skonfigurowano ruch łącznika serwera proxy aplikacji w celu przechodzenia przez serwery proxy, należy szukać niezakończonych połączeń HTTPS z serwerem proxy.

Aby odfiltrować przechwytywanie sieci dla tych prób połączenia, wprowadź `(https.Request or https.Response) and tcp.port==8080` w filtrze analizatora komunikatów, zastępując 8080 z portem usługi serwera proxy. Wybierz pozycję **Zastosuj** , aby wyświetlić wyniki filtrowania.

Poprzedni filtr przedstawia tylko żądania HTTPs i odpowiedzi do/z portu serwera proxy. Szukasz żądań połączeń, które pokazują komunikację z serwerem proxy. Po pomyślnym otrzymaniu odpowiedzi HTTP OK (200).

Jeśli zobaczysz inne kody odpowiedzi, na przykład 407 lub 502, oznacza to, że serwer proxy wymaga uwierzytelniania lub nie zezwala na ruch z innego powodu. W tym momencie należy skontaktować się z zespołem pomocy technicznej serwera proxy.

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
* Jeśli masz problemy z łącznością łącznika, zadawaj pytanie na [forum Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=WindowsAzureAD&forum=WindowsAzureAD) lub Utwórz bilet z zespołem pomocy technicznej.
