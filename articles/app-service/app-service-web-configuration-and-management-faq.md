---
title: Często zadawane pytania dotyczące konfiguracji dla aplikacji sieci web platformy Azure | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania dotyczące problemów dotyczących konfiguracji i zarządzania dla funkcji aplikacji sieci Web w usłudze Azure App Service.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 6aa0c08fad98eeff7c937f817ca0a8fa20238c4a
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurowanie i zarządzanie często zadawane pytania dotyczące aplikacji sieci Web na platformie Azure

Ten artykuł zawiera odpowiedzi na często zadawane pytania (FAQ) dotyczących konfiguracji i zarządzania dla [funkcja Web Apps usługi Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Czy istnieją ograniczenia I należy pamiętać o przenoszenie zasobów usługi aplikacji?

Jeśli planujesz przeniesienie zasobów z usługi aplikacji do nowej grupy zasobów lub subskrypcji, istnieje kilka ograniczeń pod uwagę. Aby uzyskać więcej informacji, zobacz [ograniczenia usługi aplikacji](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak używać niestandardowej nazwy domeny dla mojej aplikacji sieci web?

Odpowiedzi na często zadawane pytania dotyczące korzystania z niestandardowej nazwy domeny z aplikacji sieci web platformy Azure, można znaleźć w naszych 7 minutowy film wideo [Dodawanie niestandardowej nazwy domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Klip wideo zawiera wskazówki dotyczące sposobu dodawania niestandardowej nazwy domeny. Opisuje sposób używania własnego adresu URL zamiast *. azurewebsites.net adresu URL aplikacji sieci web usługi aplikacji. Również widoczne szczegółowe wskazówki dotyczące [jak zamapować niestandardową nazwę domeny](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak kupić nowej domeny niestandardowej dla mojej aplikacji sieci web?

Aby dowiedzieć się, jak kupić i skonfiguruj niestandardową domenę na potrzeby aplikacji sieci web usługi aplikacji, zobacz [kupowanie i konfigurowanie niestandardowej nazwy domeny w usłudze App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Jak przekazać i skonfigurować certyfikat SSL dla mojej aplikacji sieci web?

Aby dowiedzieć się przekazać i skonfigurować niestandardowe istniejącego certyfikatu SSL, zobacz [Powiąż istniejący certyfikat SSL niestandardowe dla aplikacji sieci web platformy Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Jak kupić i skonfigurować nowy certyfikat SSL dla mojej aplikacji sieci web na platformie Azure?

Aby dowiedzieć się, jak kupić i skonfigurować certyfikat SSL dla aplikacji sieci web usługi aplikacji, zobacz [Dodawanie certyfikatu SSL do aplikacji usługi App Service](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak przenieść zasobów usługi Application Insights?

Obecnie usługa Azure Application Insights nie obsługuje operacji przenoszenia. Jeśli oryginalną grupę zasobów zawiera zasób usługi Application Insights, nie można przenieść tego zasobu. Jeśli dołączysz zasobu usługi Application Insights przy próbie przeniesienia aplikację usługi aplikacji, całego Przenieś operacja kończy się niepowodzeniem. Jednak usługa Application Insights i plan usługi aplikacji nie są w tej samej grupie zasobów co aplikacja dla aplikacji do poprawnego działania.

Aby uzyskać więcej informacji, zobacz [ograniczenia usługi aplikacji](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Gdzie można znaleźć listę kontrolną wskazówki i Dowiedz się więcej na temat zasobów operacje są przenoszone?

[Ograniczenia usługi aplikacji](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) przedstawiono sposób przenoszenia zasobów do nowej subskrypcji lub do nowej grupy zasobów w tej samej subskrypcji. Możesz uzyskać informacje na temat listy kontrolnej przenoszenia zasobów, Dowiedz się więcej usług, które obsługują operacji przenoszenia i Dowiedz się więcej na temat ograniczeń usługi aplikacji i innych tematach.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak ustawić strefy czasowej serwera dla mojej aplikacji sieci web?

Aby ustawić strefę czasową serwera dla aplikacji sieci web:

1. W portalu Azure w ramach subskrypcji usługi aplikacji, przejdź do **ustawienia aplikacji** menu.
2. W obszarze **ustawień aplikacji**, Dodaj następujące ustawienie:
    * Klucz = WEBSITE_TIME_ZONE
    * Wartość = *strefa czasowa ma*
3. Wybierz pozycję **Zapisz**.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Dlaczego moje ciągłe zadania Webjob czasami nie powiodło się?

Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynności na wybrany okres czasu. Pozwala to zaoszczędzić zasoby systemu. W planie Basic i Standard, możesz włączyć **zawsze na** ustawienie do zachowania aplikacji sieci web załadowana przez cały czas. Jeśli aplikacja sieci web jest uruchomiony ciągłe zadania Webjob, należy włączyć **zawsze na**, lub zadania Webjob może nie działać prawidłowo. Aby uzyskać więcej informacji, zobacz [tworzenie stale uruchomione zadania WebJob](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak uzyskać wychodzący adres IP dla mojej aplikacji sieci web?

Aby wyświetlić listę adresów IP ruchu wychodzącego dla aplikacji sieci web:

1. W portalu Azure, w bloku aplikacja sieci web, przejdź do **właściwości** menu.
2. Wyszukaj **adresy ip wychodzących**.

Zostanie wyświetlona lista wychodzących adresów IP.

Aby dowiedzieć się, jak uzyskać wychodzący adres IP, jeśli witryny sieci Web znajduje się w środowisku usługi aplikacji, zobacz [adresów sieciowych wychodzących](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak uzyskać zarezerwowany lub jest dedykowany dla ruchu przychodzącego adresu IP dla mojej aplikacji sieci web?

Aby skonfigurować dedykowanej lub zastrzeżony adres IP dla wywołań przychodzących do witryny sieci Web aplikacji Azure, należy zainstalować i skonfigurować certyfikat SSL opartego na protokole IP.

Należy zauważyć, że aby użyć dedykowanego lub zastrzeżony adres IP dla połączeń przychodzących, planu usługi aplikacji musi być w planie usługi podstawowe lub nowszej.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Można wyeksportować certyfikatu usługi aplikacji można używać poza Azure, takich jak witryny sieci Web hostowana w innej lokalizacji? 

Certyfikaty usługi aplikacji są traktowane jako zasobów platformy Azure. Nie są one przeznaczone do użycia poza usługami Azure. Nie można wyeksportować je, aby używać poza Azure. Aby uzyskać więcej informacji, zobacz [— często zadawane pytania dotyczące certyfikaty usługi aplikacji i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Można wyeksportować certyfikatu do użycia z innych usług w chmurze Azure App Service?

Portal zawiera najwyższej jakości środowisko wdrażania certyfikatu usług aplikacji za pomocą usługi Azure Key Vault w aplikacji usługi App Service. Jednak firma Microsoft ma zostały odbierania żądań od klientów, aby korzystały z tych certyfikatów poza platformą usługi aplikacji, na przykład z maszyn wirtualnych platformy Azure. Aby dowiedzieć się, jak utworzyć PFX lokalną kopię certyfikatu z usługi aplikacji, dzięki czemu można użyć certyfikatu z innych zasobów platformy Azure, zobacz [utworzyć kopię lokalną PFX certyfikatu usługi aplikacji](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Aby uzyskać więcej informacji, zobacz [— często zadawane pytania dotyczące certyfikaty usługi aplikacji i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Dlaczego widzę komunikat "Częściowo powiodła się" podczas próby wykonaj kopię zapasową mojej aplikacji sieci web?

Częstą przyczyną niepowodzenia wykonywania kopii zapasowej jest, że niektóre pliki są używane przez aplikację. Pliki, które są używane są blokowane podczas wykonywania kopii zapasowej. To zapobiega tworzona kopia zapasowa tych plików i może spowodować stanem "Częściowo powiodła się". Użytkownik może potencjalnie temu zapobiec przez wykluczanie plików z procesu tworzenia kopii zapasowej. Można utworzyć kopię zapasową tylko co jest potrzebne. Aby uzyskać więcej informacji, zobacz [kopii zapasowej tylko ważne części swoją witrynę przy użyciu aplikacji sieci web platformy Azure](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak usunąć nagłówek z odpowiedzi HTTP?

Aby usunąć nagłówki odpowiedzi HTTP, należy zaktualizować pliku web.config witryny. Aby uzyskać więcej informacji, zobacz [usunąć nagłówki standard server na witryny sieci Web Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Usługi aplikacji jest zgodne z PCI standardowe 3.0 i 3.1?

Funkcja Web Apps w usłudze Azure App Service jest obecnie zgodnie z PCI danych zabezpieczeń Standard (DSS) w wersji 3.0 poziomu 1. PCI DSS w wersji 3.1 znajduje się w naszej plan. Planowanie trwa już jak zastosowanie najnowszego standardu będzie kontynuowana.

PCI DSS wersji 3.1 certyfikacji wymaga wyłączenie zabezpieczeń TLS (Transport Layer) 1.0. Obecnie wyłączenie protokołu TLS 1.0 nie jest opcją dla większości planów usługi aplikacji. Jednak jeśli Użyj środowiska usługi aplikacji lub chcesz przeprowadzić migrację obciążenia do środowiska usługi aplikacji, możesz uzyskać większą kontrolę nad środowiska. Obejmuje to wyłączenie protokołu TLS 1.0, kontaktując się z pomocą techniczną platformy Azure. W niedalekiej przyszłości planujemy udostępnić te ustawienia dla użytkowników.

Aby uzyskać więcej informacji, zobacz [zgodności aplikacji sieci web Microsoft Azure App Service z PCI standardowe 3.0 i 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak używać przemieszczania miejsc środowiska i wdrażania?

W planie Standard i Premium usługi aplikacji podczas wdrażania aplikacji sieci web do usługi App Service można wdrożyć do miejsca wdrożenia oddzielne zamiast do miejsca produkcji domyślne. Miejsca wdrożenia są aplikacje sieci web, które mają własne nazwy hosta. Elementy zawartości i konfiguracji aplikacji sieci Web może być zamieniona między dwóch miejsc wdrożenia, w tym miejsca produkcji.

Aby uzyskać więcej informacji na temat używania miejsc wdrożenia, zobacz [Konfigurowanie środowiska przemieszczania w usłudze App Service](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak uzyskać dostęp i przejrzyj dzienniki zadania WebJob?

Aby przejrzeć dzienniki zadań WebJob:

1. Zaloguj się do Twojego [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz zadania WebJob.
3. Wybierz **dane wyjściowe Przełącz** przycisku.
4. Aby pobrać plik wyjściowy, wybierz **Pobierz** łącza.
5. Dla poszczególnych przebiegów wybierz **wywołania poszczególnych**.
6. Wybierz **dane wyjściowe Przełącz** przycisku.
7. Wybierz łącze pobierania.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Próbuję się z programem SQL Server za pomocą połączeń hybrydowych. Dlaczego wyświetlony komunikat "System.OverflowException: operacji arytmetycznej nastąpiło przepełnienie w czasie"?

Aktualizacja programu Microsoft .NET, 10 maja 2016 r połączenia hybrydowe umożliwiają dostęp do programu SQL Server, może spowodować połączeń zakończyć się niepowodzeniem. Ten komunikat może zostać wyświetlony:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Rozwiązanie

Wyjątek spowodowane przez problem przy użyciu hybrydowego połączenia Menedżera który odwałania został rozwiązany. Upewnij się, [aktualizacji Menedżera połączeń hybrydowych](https://go.microsoft.com/fwlink/?LinkID=841308) Aby rozwiązać ten problem.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Jak dodać lub edytować reguły ponownego zapisywania adresów URL?

Aby dodać lub edytować reguły ponownego zapisywania adresów URL:

1. Skonfiguruj Menedżera usług Internet Information Services (IIS), aby nawiąże połączenie z aplikacji sieci web usługi aplikacji. Aby dowiedzieć się, jak połączyć Menedżera usług IIS do usługi App Service, zobacz [zdalne administrowanie Azure witryn sieci Web za pomocą Menedżera usług IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. W Menedżerze usług IIS, dodawanie lub edytowanie reguły ponownego zapisywania adresów URL. Aby dowiedzieć się, jak dodawanie lub edytowanie reguły ponownego zapisywania adresów URL, zobacz [Utwórz reguły ponownego zapisywania dla adresu URL przepisywania moduł](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak kontrolować ruch przychodzący do usługi App Service?

Na poziomie witryny masz dwie opcje do kontroli ruchu przychodzącego do usługi App Service:

* Włącz ograniczenia dynamicznego adresu IP. Aby dowiedzieć się, jak włączyć funkcję ograniczenia dynamicznego adresu IP, zobacz [ograniczenia adresów IP i domen dla witryn sieci Web Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Włącz moduł zabezpieczeń. Aby dowiedzieć się, jak włączyć modułu zabezpieczeń, zobacz [ModSecurity zapory aplikacji sieci web w witrynach sieci Web Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Jeśli używasz środowiska usługi aplikacji, możesz użyć [zapory Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak zablokować portów w aplikacji sieci web usługi aplikacji?

W środowisku usługi aplikacji udostępnionych dzierżawy nie jest możliwość zablokowania określonych portów z powodu charakter infrastruktury. Również porty TCP 4016, 4018 i 4020 może być otwarty dla zdalnego debugowania programu Visual Studio.

W środowisku usługi aplikacji należy mieć pełną kontrolę nad ruchu przychodzącego i wychodzącego. Grup zabezpieczeń sieci umożliwia ograniczenie lub blokowanie określonych portów. Aby uzyskać więcej informacji dotyczących środowiska usługi aplikacji, zobacz [wprowadzenie do środowiska usługi aplikacji](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak przechwycić śledzenia F12?

Masz dwie opcje do przechwytywania śledzenia F12:

* F12 HTTP śledzenia
* Dane wyjściowe konsoli F12

### <a name="f12-http-trace"></a>F12 HTTP śledzenia

1. W programie Internet Explorer przejdź do witryny sieci Web. Należy się zalogować przed wykonaniem dalszych kroków. W przeciwnym razie śledzenia F12 przechwytuje poufne dane logowania.
2. Naciśnij klawisz F12.
3. Sprawdź, czy **sieci** karta jest zaznaczone, a następnie wybierz zielonego **odtwarzanie** przycisku.
4. Wykonaj kroki, które odtworzyć problem.
5. Wybierz kolor czerwony **zatrzymać** przycisku.
6. Wybierz **zapisać** przycisk (ikona dysku) i Zapisz plik HAR (w programie Internet Explorer i krawędzi) *lub* kliknij prawym przyciskiem myszy plik HAR, a następnie wybierz **Zapisz jako HAR z zawartością** (w Chrome).

### <a name="f12-console-output"></a>Dane wyjściowe konsoli F12

1. Wybierz **konsoli** kartę.
2. Dla każdej karty, która zawiera kilka elementów, wybierz kartę (**błąd**, **ostrzeżenie**, lub **informacji**). Jeśli karta nie została wybrana, ikona kartę jest czarnego lub szarego po przeniesieniu kursora poza.
3. Kliknij prawym przyciskiem myszy w obszarze wiadomości w okienku, a następnie wybierz **skopiuj wszystkie**.
4. Wklej skopiowany tekst w pliku, a następnie zapisz plik.

Aby wyświetlić plik HAR, można użyć [HAR podglądu](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Dlaczego jest zgłaszany błąd podczas próby połączenia z usługi aplikacji — aplikacji sieci web do sieci wirtualnej, która jest połączona z usługi ExpressRoute?

Jeśli spróbujesz Połącz aplikację sieci web platformy Azure do sieci wirtualnej, która jest połączona z Azure ExpressRoute go nie powiedzie się. Zostanie wyświetlony następujący komunikat: "Brama nie jest brama sieci VPN."

Obecnie nie ma lokacji punktu połączenia sieci VPN do sieci wirtualnej, która jest połączona z usługi ExpressRoute. Punkt do lokacji sieci VPN i ExpressRoute nie mogą współistnieć w tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [ExpressRoute i sieci VPN typu lokacja lokacja połączeń limity i ograniczenia](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak łączyć aplikację usługi aplikacji sieci web z siecią wirtualną mającą statycznego routingu bramy (oparte na zasadach)?

Aplikację sieci web usługi aplikacji nawiązywania połączenia z siecią wirtualną, która ma statyczny routingu bramy (oparte na zasadach) nie jest obecnie obsługiwane. Sieci wirtualnej docelowy już istnieje, musi on mieć włączone z bramą routingu dynamicznego przed podłączeniem do aplikacji VPN punkt lokacja. Jeśli brama ustawiono routingu statycznego, nie można włączyć sieć VPN punkt lokacja. 

Aby uzyskać więcej informacji, zobacz [integracji aplikacji z sieci wirtualnej platformy Azure](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>W moich środowiska usługi aplikacji, dlaczego można utworzyć tylko jeden plan usługi aplikacji, nawet jeśli użytkownik ma dwa procesy robocze, które są dostępne?

Aby zapewnić odporność na uszkodzenia, środowiska usługi aplikacji wymaga, że każdego procesu roboczego puli wymaga co najmniej jeden zasób dodatkowe zasoby obliczeniowe. Dodatkowe zasoby obliczeniowe zasobu nie można przypisać obciążenia.

Aby uzyskać więcej informacji, zobacz [tworzenie środowiska usługi aplikacji](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Dlaczego widzę przekroczeń limitu czasu podczas tworzenia środowiska usługi aplikacji?

Czasami tworzenie środowiska usługi aplikacji nie powiedzie się. W takim przypadku zostanie wyświetlony następujący błąd w dziennikach działania:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Aby rozwiązać ten problem, upewnij się, że żaden z następujących warunków nie jest spełniony:
* Podsieć jest za mały.
* Podsieć nie jest pusty.
* ExpressRoute zapobiega wymagania dotyczące łączności sieciowej środowiska usługi aplikacji.
* Nieprawidłowa grupa zabezpieczeń sieci uniemożliwia wymagania dotyczące łączności sieciowej środowiska usługi aplikacji.
* Wymuszanie tunelowania jest włączona.

Aby uzyskać więcej informacji, zobacz [częste problemy podczas wdrażania (Tworzenie) nowego środowiska usługi aplikacji Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Dlaczego nie można usunąć planu usługi aplikacji?

Nie można usunąć planu usługi App Service, jeśli wszystkie aplikacje usługi aplikacji są skojarzone z planu usługi aplikacji. Przed usunięciem plan usługi aplikacji, Usuń wszystkie skojarzone aplikacje usługi aplikacji z plan usługi aplikacji.

## <a name="how-do-i-schedule-a-webjob"></a>Jak zaplanować zadanie WebJob?

Zaplanowane zadania WebJob można utworzyć za pomocą usługi Cron wyrażeń:

1. Utwórz plik settings.job.
2. W tym pliku JSON obejmują właściwości harmonogramu przy użyciu wyrażenia Cron: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Aby uzyskać więcej informacji na temat zaplanowane zadania Webjob, zobacz [utworzyć zaplanowane zadanie WebJob przy użyciu wyrażenia Cron](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak wykonać penetracji testowania dla mojej aplikacji usługi app Service?

Aby przeprowadzić testy penetracji, [Prześlij żądanie](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak skonfigurować niestandardową nazwę domeny dla aplikacji sieci web usługi aplikacji, który używa Menedżera ruchu?

Aby dowiedzieć się, jak używać niestandardowej nazwy domeny w aplikacji usługi App Service, która używa usługi Azure Traffic Manager w programie Równoważenie obciążenia, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web platformy Azure z usługą Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Certyfikatu usługi aplikacji jest oznaczona flagą oszustw. Jak rozwiązać ten problem?

Podczas weryfikacji domeny zakupu certyfikatów usługi aplikacji może być wyświetlony następujący komunikat:

"Certyfikat ma została oznaczona flagą możliwym oszustwie. Żądanie jest obecnie w ramach przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

Jak wskazuje komunikat, ten proces sprawdzania poprawności oszustwa może potrwać do 24 godzin. W tym czasie będzie nadal wyświetlony komunikat.

Jeśli certyfikat usługi aplikacji nadal pokazuj tego komunikatu po 24 godzinach, uruchom następujący skrypt programu PowerShell. Kontakty skryptu [dostawcę certyfikatów](https://www.godaddy.com/) bezpośrednio, aby rozwiązać ten problem.

```
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak uwierzytelnianie i autoryzacja działają w usłudze App Service?

Szczegółowa dokumentacja uwierzytelniania i autoryzacji w usłudze App Service Zobacz dokumenty dla różnych zidentyfikować dostawcy logowania:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Konto Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak przekierować domyślne *. azurewebsites.net domeny na domenę niestandardową Moja aplikacja sieci web platformy Azure?

Podczas tworzenia nowej witryny sieci Web za pomocą aplikacji sieci Web na platformie Azure, domyślny *sitename*. azurewebsites.net domeny jest przypisany do lokacji. Możesz dodać nazwę hosta niestandardowego do lokacji i nie mają użytkownicy mogli uzyskać dostępu do domyślnego *. azurewebsites.net domeny, można przekierowywać domyślny adres URL. Aby dowiedzieć się przekierować cały ruch z domeny domyślnej witryny sieci Web do domeny niestandardowej, zobacz [przekierować domyślną domenę do domeny niestandardowej w aplikacjach sieci web platformy Azure](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak ustalić, która wersja programu .NET jest zainstalowana wersja w usłudze App Service?

Jest najszybszym sposobem, aby znaleźć wersję platformy Microsoft .NET, zainstalowanym w usłudze App Service przy użyciu konsoli Kudu. Z portalu lub przy użyciu adresu URL aplikacji usługi aplikacji można uzyskać dostępu do konsoli Kudu. Aby uzyskać szczegółowe instrukcje, zobacz [ustalić zainstalowanych wersji platformy .NET w usłudze App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Dlaczego skalowania automatycznego nie działa zgodnie z oczekiwaniami?

Jeśli Azure skalowania automatycznego nie skalować w lub skalowanie wystąpienie aplikacji sieci web, zgodnie z oczekiwaniami, może być uruchomiona w scenariuszu, w którym firma Microsoft celowo wybierz nie skalować, aby uniknąć nieskończoną pętlę z powodu "niestabilny." Zazwyczaj dzieje się tak, gdy nie ma odpowiedni poziom między wartościami progowymi skalowalnego w poziomie i w skali. Aby uzyskać informacje, aby uniknąć "niestabilny" oraz na temat skalowania automatycznego najlepsze rozwiązania, zobacz [najlepsze rozwiązania w zakresie skalowania automatycznego](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Dlaczego skalowania automatycznego czasami skalowany tylko częściowo?

Skalowania automatycznego jest wyzwalane, gdy metryki przekroczą wstępnie skonfigurowanych granicach. Czasami można zauważyć, że pojemność jest tylko częściowo wypełnione w porównaniu do oczekiwanej. Taka sytuacja może wystąpić, gdy liczba wystąpień, które mają nie są dostępne. W tym scenariuszu skalowania automatycznego częściowo wypełnia się przy użyciu dostępnej liczby wystąpień. Funkcja automatycznego skalowania następnie uruchamia logiki Zrównoważ uzyskanie większej pojemności. Pozostałe wystąpienia go przydziela. Należy pamiętać, że może to potrwać kilka minut.

Jeśli nie widzisz oczekiwanej liczby wystąpień po upływie kilku minut, może być powodu częściowe uzupełnienia do dostosowania metryki w granicach. Lub automatycznego skalowania może mieć skalowany w dół, ponieważ osiągnął dolna granica metryki.

Jeśli żaden z tych warunków, a problem będzie się powtarzał, należy przesłać żądanie pomocy technicznej.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak włączyć kompresję HTTP zawartości Moje?

Aby włączyć kompresję zarówno dla statycznych i dynamicznych typów zawartości, Dodaj następujący kod do pliku web.config na poziomie aplikacji:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

Można również określić określonych dynamiczną i statyczną MIME typy, które chcesz kompresować. Aby uzyskać więcej informacji, zobacz nasze odpowiedzi na pytania forum w [httpCompression ustawień na prosty witryny sieci Web Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak migracji ze środowiska lokalnego do usługi App Service?

Aby dokonać migracji lokacji z serwerów sieci web systemu Windows i Linux do usługi App Service, można użyć Asystenta migracji usługi aplikacji Azure. Narzędzie do migracji tworzy aplikacje sieci web i baz danych na platformie Azure, zgodnie z potrzebami, a następnie publikuje zawartości. Aby uzyskać więcej informacji, zobacz [Asystenta migracji usługi aplikacji Azure](https://www.migratetoazure.net/).
