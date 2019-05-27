---
title: Konfiguracja — często zadawane pytania — usługa Azure App Service | Dokumentacja firmy Microsoft
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące konfiguracji i zarządzania problemy dla funkcji Web Apps w usłudze Azure App Service.
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
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 88051c45f21bdf11807ffcc63d8248cba81ae70b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "66137061"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfiguracja i zarządzanie nim często zadawane pytania dotyczące aplikacji sieci Web na platformie Azure

W tym artykule znajdują się odpowiedzi na często zadawane pytania (FAQ) dotyczących konfiguracji i zarządzania dla [funkcji Web Apps w usłudze Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Czy istnieją ograniczenia, o których warto wiedzieć, jeśli chcę przenieść zasoby usługi App Service?

Jeśli zamierzasz przenieść zasoby usługi App Service do nowej grupy zasobów lub subskrypcji, istnieją pewne ograniczenia, które należy zwrócić uwagę. Aby uzyskać więcej informacji, zobacz [ograniczenia usługi App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Jak używać niestandardowej nazwy domeny dla mojej aplikacji sieci web?

Odpowiedzi na często zadawane pytania dotyczące korzystania z niestandardowej nazwy domeny z Twoją aplikacją internetową platformy Azure, zobacz nasze 7 minutowym filmie [Dodawanie niestandardowej nazwy domeny](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). Film wideo zawiera wskazówki dotyczące sposobu dodawania niestandardowej nazwy domeny. Opisano używanie własnego adresu URL zamiast *. adresu URL azurewebsites.net z aplikacją sieci web usługi App Service. Możesz również zobaczyć szczegółowy przewodnik dotyczący [sposób mapowania niestandardowej nazwy domeny](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Jak kupić domenę niestandardową na aplikację sieci web?

Aby dowiedzieć się, jak kupić i konfigurowanie domeny niestandardowej aplikacji sieci web usługi App Service, zobacz [kupowanie i konfigurowanie niestandardowej nazwy domeny w usłudze App Service](manage-custom-dns-buy-domain.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Jak przekazać i skonfigurować istniejącego certyfikatu SSL dla aplikacji sieci web?

Aby dowiedzieć się, jak przekazywać i skonfigurować istniejącego niestandardowego certyfikatu SSL, zobacz [powiązania istniejącego niestandardowego certyfikatu SSL do aplikacji sieci web platformy Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Jak zakupić i skonfigurować nowy certyfikat SSL na platformie Azure dla aplikacji sieci web?

Aby dowiedzieć się, jak zakupić i skonfigurować certyfikat SSL dla aplikacji sieci web usługi App Service, zobacz [Dodawanie certyfikatu SSL do aplikacji usługi app Service](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Jak przenieść zasoby usługi Application Insights?

Obecnie usługa Azure Application Insights nie obsługuje operacji przenoszenia. Jeśli oryginalna grupa zasobów zawiera zasób usługi Application Insights, nie można przenieść tego zasobu. Jeśli zasób usługi Application Insights jest uwzględniony, podczas próby Przenieś aplikację usługi App Service, całą przenieść operacja kończy się niepowodzeniem. Jednak usługa Application Insights i plan usługi App Service nie muszą być w tej samej grupie zasobów co aplikacja dla aplikacji do poprawnego działania.

Aby uzyskać więcej informacji, zobacz [ograniczenia usługi App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Gdzie można znaleźć listę kontrolną wskazówki i Dowiedz się więcej na temat zasobów operacje są przenoszone?

[Ograniczenia usługi App Service](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) dowiesz się, jak przenieść zasoby do nowej subskrypcji lub do nowej grupy zasobów w tej samej subskrypcji. Możesz uzyskać informacje na temat listy kontrolnej przenoszenia zasobów, Dowiedz się, usług, które obsługują operacji przenoszenia i Dowiedz się więcej o ograniczeniach usługi App Service i innych zagadnień.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Jak ustawić strefa czasowa serwera dla mojej aplikacji sieci web?

Aby ustawić strefa czasowa serwera dla aplikacji sieci web:

1. W witrynie Azure portal w ramach subskrypcji usługi App Service, przejdź do **ustawienia aplikacji** menu.
2. W obszarze **ustawienia aplikacji**, Dodaj następujące ustawienie:
    * Klucz = WEBSITE_TIME_ZONE
    * Wartość = *ma strefę czasową*
3. Wybierz pozycję **Zapisz**.

Zobacz **strefa czasowa** kolumny w [domyślne stref czasowych](https://docs.microsoft.com/windows-hardware/manufacture/desktop/default-time-zones) artykuł, aby akceptowane wartości.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Dlaczego moja ciągłych zadań Webjob czasami awarii?

Domyślnie aplikacje sieci web są usuwane, jeśli są one bezczynne na pewien okres czasu. Dzięki temu system zaoszczędzenia zasobów. W planach Basic i Standard, możesz włączyć **Always On** ustawienie na utrzymanie aplikacji sieci web załadowana przez cały czas. Jeśli aplikacja sieci web działa ciągłych zadań Webjob, należy włączyć **Always On**, lub zadania Webjob może nie działać niezawodnie. Aby uzyskać więcej informacji, zobacz [tworzenie stale działające zadanie WebJob](webjobs-create.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Jak uzyskać adres IP ruchu wychodzącego dla mojej aplikacji sieci web?

Aby wyświetlić listę wychodzące adresy IP dla aplikacji sieci web:

1. W witrynie Azure portal w bloku aplikacji sieci web, przejdź do **właściwości** menu.
2. Wyszukaj **adresy ip ruchu wychodzącego**.

Zostanie wyświetlona lista wychodzące adresy IP.

Aby dowiedzieć się, jak uzyskać adres IP ruchu wychodzącego, jeśli witryny sieci Web znajduje się w środowisku usługi App Service, zobacz [adresy sieciowe dotyczące połączeń wychodzących](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Jak uzyskać zastrzeżony lub dedykowany dla ruchu przychodzącego adresu IP dla mojej aplikacji sieci web?

Aby skonfigurować dedykowany lub zastrzeżony adres IP dla wywołań przychodzących skierowanych do witryny sieci Web aplikacji platformy Azure, zainstaluj i skonfiguruj certyfikat SSL opartego na protokole IP.

Należy pamiętać, że aby użyć dedykowanego lub zastrzeżony adres IP dla wywołań przychodzących, plan usługi App Service musi być w planie usług w warstwie podstawowa lub wyższej.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Czy można wyeksportować certyfikatu usługi App Service na potrzeby poza systemem Azure, takich jak witryny sieci Web znajdujących się gdzie indziej? 

Certyfikaty usługi App Service są traktowane jako zasoby platformy Azure. Nie są przeznaczone do użycia poza usługami platformy Azure. Nie można wyeksportować je do użycia poza systemem Azure. Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące certyfikatów usługi App Service i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Czy można wyeksportować certyfikatu usługi App Service za pomocą innych usług Azure cloud services?

Portal zapewnia pierwszorzędne środowisko do wdrażania certyfikatu usługi App Service za pomocą usługi Azure Key Vault do aplikacji usługi App Service. Jednak Otrzymaliśmy żądań od klientów, aby użyć tych certyfikatów poza platformą App Service, na przykład usługa Azure Virtual Machines. Aby dowiedzieć się, jak utworzyć lokalną kopię PFX certyfikatu usługi App Service, aby można było używać certyfikatu z innymi zasobami platformy Azure, zobacz [utworzyć lokalną kopię PFX certyfikatu usługi App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Aby uzyskać więcej informacji, zobacz [często zadawane pytania dotyczące certyfikatów usługi App Service i domen niestandardowych](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Dlaczego widzisz komunikat "Częściowe Powodzenie", gdy próbuję do tworzenia kopii zapasowej aplikacji sieci web?

Częstą przyczyną niepowodzenia wykonywania kopii zapasowej jest, że niektóre pliki są używane przez aplikację. Pliki, które są używane są zablokowane podczas wykonywania kopii zapasowej. Zapobiega tworzona kopia zapasowa tych plików i może prowadzić do stanu "Częściowe Powodzenie". Użytkownik może potencjalnie temu zapobiec, wykluczanie plików z procesu tworzenia kopii zapasowej. Można wybrać do utworzenia kopii zapasowej tylko potrzebne elementy. Aby uzyskać więcej informacji, zobacz [kopii zapasowej tylko ważne elementy witryny przy użyciu usługi Azure web apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Jak usunąć nagłówek z odpowiedzi HTTP?

Aby usunąć nagłówki odpowiedzi HTTP, zaktualizuj plik web.config witryny. Aby uzyskać więcej informacji, zobacz [usuwanie nagłówków standardowy serwer usługi Azure websites](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Usługa App Service jest zgodne ze standardami PCI Standard 3.0 i 3.1?

Obecnie funkcji Web Apps w usłudze Azure App Service jest zgodne z wersją PCI Data Security Standard (DSS) 3.0 poziomu 1. PCI DSS 3.1 wersji znajduje się na nasz plan. Planowanie jest już w toku dla jak przyjęcia najnowszego standardu będzie kontynuowana.

PCI DSS wersja 3.1 certyfikacji wymaga wyłączenie zabezpieczeń TLS (Transport Layer) 1.0. Obecnie wyłączenie protokołu TLS 1.0 nie jest opcją w przypadku większości planów usługi App Service. Jednak jeśli używasz środowiska usługi App Service lub chcesz migrować obciążenia do środowiska App Service Environment, możesz uzyskać większą kontrolę nad środowiskiem organizacji. Obejmuje to wyłączenie protokołu TLS 1.0, kontaktując się z pomocą techniczną platformy Azure. W najbliższej przyszłości planujemy udostępnić te ustawienia dla użytkowników.

Aby uzyskać więcej informacji, zobacz [zgodności aplikacji sieci web Microsoft Azure App Service przy użyciu PCI Standard 3.0 i 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Jak używać miejsc przejściowych środowiska i wdrażania?

W planach Standard i Premium usługi App Service podczas wdrażania aplikacji sieci web usługi App Service można wdrożyć na gniazdo wdrażane pojedynczo, zamiast do miejsca produkcji domyślne. Miejsca wdrożenia to aplikacji internetowych na żywo, które mają własne nazwy hosta. Elementy zawartości i konfiguracji aplikacji sieci Web można wymieniać między 2 miejscami wdrożenia, w tym także miejscem produkcyjnym.

Aby uzyskać więcej informacji na temat używania miejsc wdrożenia, zobacz [skonfigurować środowisko przejściowe w usłudze App Service](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Jak uzyskać dostęp i przejrzyj dzienniki WebJob?

Aby przejrzeć dzienniki WebJob:

1. Zaloguj się do Twojej [Kudu witryny sieci Web](https://*yourwebsitename*.scm.azurewebsites.net).
2. Wybierz zadania WebJob.
3. Wybierz **Przełącz dane wyjściowe** przycisku.
4. Aby pobrać plik wyjściowy, zaznacz **Pobierz** łącza.
5. Dla poszczególnych przebiegów wybierz **poszczególne wywołania**.
6. Wybierz **Przełącz dane wyjściowe** przycisku.
7. Wybierz link pobierania.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Próbuję z programem SQL Server za pomocą połączeń hybrydowych. Dlaczego wyświetlony komunikat "System.OverflowException: Operacja arytmetyczna spowodowało przepełnienie"?

Korzystanie z połączeń hybrydowych dostępu do serwera SQL aktualizacji programu Microsoft .NET 10 maja 2016 r. mogą spowodować połączenia nie powiedzie się. Może zostać wyświetlony ten komunikat:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Rozwiązanie

Wyjątek zostało spowodowane przez problem z Menedżera połączeń hybrydowych, ponieważ został rozwiązany. Pamiętaj, aby [aktualizowanie Menedżera połączeń hybrydowych](https://go.microsoft.com/fwlink/?LinkID=841308) Aby rozwiązać ten problem.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Jak dodać lub edytować reguły ponownego pisania adresu URL?

Aby dodać lub edytować reguły ponownego pisania adresu URL:

1. Skonfiguruj Menedżera usług Internet Information Services (IIS), aby połączyć je z usługą aplikacji sieci web usługi App Service. Aby dowiedzieć się, jak połączyć Menedżera usług IIS w usłudze App Service, zobacz [zdalne administrowanie witryn sieci Web za pomocą Menedżera usług IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. W Menedżerze usług IIS, Dodaj lub Edytuj regułę ponownego zapisywania adresu URL. Aby dowiedzieć się, jak dodawanie lub edytowanie reguły ponownego zapisywania adresu URL, zobacz [Utwórz reguły ponownego zapisywania adresu URL moduł ponowne zapisywanie adresów](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Jak kontrolować ruch przychodzący do usługi App Service?

Na poziomie witryny masz dwie opcje do kontrolowania ruchu przychodzącego w usłudze App Service:

* Włącz funkcję ograniczenia dynamicznego adresu IP. Aby dowiedzieć się, jak włączyć funkcję ograniczenia dynamicznego adresu IP, zobacz [ograniczenia adresów IP i domen dla usługi Azure websites](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Włącz funkcję zabezpieczeń modułu. Aby dowiedzieć się, jak włączyć funkcję zabezpieczeń modułu, zobacz [zapory aplikacji sieci web zapory ModSecurity w usłudze Azure websites](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Jeśli używasz środowiska App Service Environment, możesz użyć [Zapora Barracuda](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Jak zablokować portów w aplikacji sieci web usługi App Service?

W środowisku udostępnionym dzierżawy usługi App Service nie jest możliwe zablokowanie określonych portów ze względu na charakter infrastruktury. Również porty TCP 4016 4018 i 4020 może być otwarty dla zdalnego debugowania programu Visual Studio.

W środowisku App Service masz pełną kontrolę nad ruchem przychodzącym i wychodzącym. Sieciowe grupy zabezpieczeń można użyć do ograniczenia lub zablokować określone porty. Aby uzyskać więcej informacji na temat środowiska usługi App Service, zobacz [wprowadzenie do środowiska App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Jak przechwycić ślad F12?

Masz dwie opcje do przechwytywania śladu F12:

* Ślad F12 HTTP
* Dane wyjściowe konsoli F12

### <a name="f12-http-trace"></a>Ślad F12 HTTP

1. W programie Internet Explorer przejdź do witryny sieci Web. Należy się zalogować przed wykonaniem dalszych kroków. W przeciwnym razie śledzenia F12 przechwytuje poufnych danych logowania.
2. Naciśnij klawisz F12.
3. Upewnij się, że **sieci** karta jest zaznaczone, a następnie wybrać kolor zielony **Odtwórz** przycisku.
4. Wykonaj kroki odtworzenia problemu.
5. Wybierz kolor czerwony **zatrzymać** przycisku.
6. Wybierz **Zapisz** przycisku (ikona dysku), a następnie zapisz plik HAR (w programie Internet Explorer i Microsoft Edge) *lub* kliknij prawym przyciskiem myszy plik HAR, a następnie wybierz **Zapisz jako plik HAR z zawartością**(w przeglądarce Chrome).

### <a name="f12-console-output"></a>Dane wyjściowe konsoli F12

1. Wybierz **konsoli** kartę.
2. Dla każdej karty zawiera elementy, większa niż zero, wybierz kartę (**błąd**, **ostrzeżenie**, lub **informacji**). Jeśli karta nie jest zaznaczone, ikona kartę jest czarnego lub szarego, gdy przeniesiesz kursor poza.
3. Kliknij prawym przyciskiem myszy w obszarze wiadomości w okienku, a następnie wybierz **Kopiuj wszystko**.
4. Wklej skopiowany tekst w pliku, a następnie zapisz plik.

Aby wyświetlić plik HAR, można użyć [podglądu HAR](https://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Dlaczego otrzymuję błąd podczas próby połączenia usługi App Service aplikacja sieci web do sieci wirtualnej, która jest połączona z usługą ExpressRoute?

Jeśli spróbujesz połączyć aplikację internetową platformy Azure do sieci wirtualnej, który jest połączony z usługi Azure ExpressRoute, go nie powiedzie się. Zostanie wyświetlony następujący komunikat: "Brama nie jest brama sieci VPN."

Obecnie nie ma połączenia sieci VPN punkt lokacja do sieci wirtualnej, która jest połączona z usługą ExpressRoute. Punkt lokacja sieci VPN, a usługi ExpressRoute nie mogą współistnieć dla tej samej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [usługi ExpressRoute i sieci VPN typu lokacja lokacja połączenia limity i ograniczenia](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Jak połączyć aplikację sieci web usługi App Service do sieci wirtualnej, która ma statyczny routingu bramy (oparte na zasadach)?

Łączenie aplikacji sieci web usługi App Service do sieci wirtualnej, która ma statyczny routingu bramy (oparte na zasadach) nie jest obecnie obsługiwane. Docelowa sieć wirtualna już istnieje, musi on mieć point-to-site VPN włączone za pomocą bramy o dynamicznym routingu, przed podłączeniem do aplikacji. Jeśli brama jest ustawiony na routing statyczny, nie można włączyć sieć VPN punkt lokacja. 

Aby uzyskać więcej informacji, zobacz [zintegrować aplikację z siecią wirtualną platformy Azure](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>W mojej środowiska App Service Environment, dlaczego można utworzyć tylko jeden plan usługi App Service, nawet jeśli mam dwa procesy robocze, które są dostępne?

Aby zapewnić odporność na uszkodzenia, App Service Environment wymaga, że każdej puli procesów roboczych wymaga co najmniej jeden zasób obliczeniowy. Zasób obliczeniowy nie można przypisać obciążenia.

Aby uzyskać więcej informacji, zobacz [jak utworzyć środowisko App Service Environment](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Dlaczego są wyświetlane przekroczeń limitu czasu podczas próby utworzenia środowiska usługi App Service?

Czasami tworzenie środowiska App Service Environment nie powiedzie się. W takim przypadku zostanie wyświetlony następujący błąd w dziennikach aktywności:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Aby rozwiązać ten problem, upewnij się, że żaden z następujących warunków nie jest spełniony:
* Podsieć jest za mały.
* Podsieć nie jest pusty.
* Usługa ExpressRoute zapobiega wymagania łączności sieci środowiska App Service Environment.
* Zły sieciowej grupy zabezpieczeń uniemożliwia wymagania łączności sieci środowiska App Service Environment.
* Wymuszanie tunelowania jest włączone.

Aby uzyskać więcej informacji, zobacz [częste problemy podczas wdrażania (tworzenia) nowego środowiska usługi aplikacji Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Dlaczego nie można usunąć planu usługi App Service?

Nie można usunąć planu usługi App Service, jeśli jakiekolwiek aplikacje usługi App Service są skojarzone z planem usługi App Service. Zanim usuniesz plan usługi App Service, należy usunąć wszystkie skojarzone aplikacje usługi App Service z planu usługi App Service.

## <a name="how-do-i-schedule-a-webjob"></a>Jak zaplanować zadanie WebJob?

Zaplanowane zadania WebJob można utworzyć za pomocą wyrażeń Cron:

1. Należy utworzyć plik settings.job.
2. W tym pliku JSON zawierają właściwości harmonogramu przy użyciu wyrażenia Cron: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Aby uzyskać więcej informacji na temat zaplanowane zadania Webjob, zobacz [tworzenie zaplanowanego zadania WebJob przy użyciu wyrażenia Cron](webjobs-create.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Jak wykonać testy penetracyjne dla mojej aplikacji usługi App Service?

Aby wykonać testy penetracyjne, [przesłać żądanie](https://portal.msrc.microsoft.com/en-us/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Jak skonfigurować niestandardową nazwę domeny dla aplikacji sieci web usługi App Service, która korzysta z usługi Traffic Manager?

Aby dowiedzieć się, jak używać niestandardowej nazwy domeny za pomocą aplikacji usługi App Service, która używa usługi Azure Traffic Manager do równoważenia obciążenia, zobacz [Konfigurowanie niestandardowej nazwy domeny dla aplikacji sieci web platformy Azure za pomocą usługi Traffic Manager](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Certyfikatu usługi App Service ma flagę oszustwa. Jak rozwiązać ten problem?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Podczas weryfikacji domeny zakup certyfikatu usługi App Service może zostać wyświetlony następujący komunikat:

"Twój certyfikat oznaczono jako ewentualnym oszustwem. Żądanie jest obecnie w trakcie przeglądu. Jeśli certyfikat nie zostanie można używać w ciągu 24 godzin, skontaktuj się z pomocą techniczną platformy Azure."

Jak wskazuje komunikat, ten proces sprawdzania poprawności oszustwa, może potrwać do 24 godzin. W tym czasie nadal będzie zostanie wyświetlony komunikat.

Jeśli certyfikat usługi App Service w dalszym ciągu wyświetlać ten komunikat po 24 godzinach, uruchom następujący skrypt programu PowerShell. Kontakty skryptu [dostawcę certyfikatów](https://www.godaddy.com/) bezpośrednio, aby rozwiązać ten problem.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Jak uwierzytelnianie i autoryzacja działają w usłudze App Service?

Szczegółowa dokumentacja uwierzytelniania i autoryzacji w usłudze App Service Zobacz dokumenty dotyczące różnych identyfikacji dostawcy logowania:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Konto Microsoft](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Jak przekierować domyślnie *. azurewebsites.net domeny do domeny niestandardowej na aplikację internetową platformy Azure?

Podczas tworzenia nowej witryny sieci Web za pomocą aplikacji sieci Web na platformie Azure, domyślny *sitename*. domenie azurewebsites.net jest przypisany do lokacji. Jeśli dodać niestandardową nazwą hosta do swojej witryny, a nie chcesz, aby użytkownicy mogli korzystać z domyślnego *. domenie azurewebsites.net, można przekierować domyślnego adresu URL. Aby dowiedzieć się przekierować cały ruch z domeny domyślnej witryny sieci Web do domeny niestandardowej, zobacz [przekierować domyślnej domeny do domeny niestandardowej w usłudze Azure web apps](https://zainrizvi.io/blog/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Jak ustalić, która wersja programu .NET jest zainstalowana wersja w usłudze App Service?

To najszybszy sposób, aby znaleźć wersję programu Microsoft .NET, która jest zainstalowana w usłudze App Service przy użyciu konsoli Kudu. Można uzyskać dostęp do konsoli Kudu, z poziomu portalu lub przy użyciu adresu URL aplikacji usługi App Service. Aby uzyskać szczegółowe instrukcje, zobacz [ustalanie zainstalowanej wersji platformy .NET w usłudze App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Dlaczego funkcja automatycznego skalowania nie działa zgodnie z oczekiwaniami?

Jeśli skalowania automatycznego platformy Azure nie przeskalować w pionie lub skalowanie wystąpienia aplikacji sieci web, zgodnie z oczekiwaniami, możliwe, że uruchomione w scenariuszu, w którym celowo Wybierzmy skali, aby uniknąć wejścia w nieskończoną pętlę ze względu na "niestabilny." Zwykle dzieje się tak, gdy nie ma odpowiedni poziom między wartościami progowymi skalowalnego w poziomie i skalowania na zewnątrz. Aby dowiedzieć się, jak można uniknąć "niestabilny" i informacji o najlepszych rozwiązaniach dotyczących innych skalowania, zobacz [najlepszych praktykach dotyczących skalowania](../azure-monitor/platform/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Dlaczego funkcja automatycznego skalowania czasami skalowany tylko częściowo?

Funkcja automatycznego skalowania jest wyzwalany, gdy metryki przekroczą granice wstępnie skonfigurowane. Czasami można zauważyć, że pojemność jest tylko częściowo wypełnione w porównaniu do oczekiwanej. Taka sytuacja może wystąpić, gdy liczba wystąpień, które mają nie są dostępne. W tym scenariuszu automatyczne skalowanie częściowo wypełnia się przy użyciu dostępnych liczbę wystąpień. Automatyczne skalowanie następnie uruchamia logiki ponowne równoważenie uzyskanie większej pojemności. Przydziela pozostałe wystąpienia. Należy pamiętać, że może to potrwać kilka minut.

Jeśli po kilku minutach nie widzisz oczekiwana liczba wystąpień, może to być powodu częściowe uzupełnienia wystarczająco dużo, aby wyświetlić metryki w granicach. Lub automatycznego skalowania może mieć przeskalowano w dół, ponieważ osiągnęła ona dolna granica metryki.

Jeśli żaden z tych warunków, a problem nie zniknie, Prześlij żądanie pomocy technicznej.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Jak włączyć kompresję HTTP dla Moja zawartość

Aby włączyć kompresję obu typów zawartości statycznej i dynamicznej, Dodaj następujący kod do pliku web.config na poziomie aplikacji:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Można również określić określone statycznych i dynamicznych typów MIME, które chcesz kompresować. Aby uzyskać więcej informacji, zobacz nasze odpowiedzi na pytanie na forum [httpCompression ustawień prostą witrynę sieci Web platformy Azure](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Jak migrować ze środowiska lokalnego do usługi App Service?

Aby migrować witryn z serwerów sieci web Windows i Linux w usłudze App Service, można użyć Asystent migracji usługi aplikacji Azure. Narzędzie migracji tworzy aplikacje sieci web i baz danych na platformie Azure, zgodnie z potrzebami, a następnie publikuje zawartość. Aby uzyskać więcej informacji, zobacz [Asystent migracji usługi aplikacji Azure](https://www.migratetoazure.net/).
