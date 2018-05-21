---
title: Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure | Dokumentacja firmy Microsoft
description: Ten dokument przeprowadzi Cię przez jaki sposób zalecenia w Centrum zabezpieczeń Azure ułatwiają ochronę zasobów platformy Azure i są aktualizowane zgodnie z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/04/2018
ms.author: terrylan
ms.openlocfilehash: 72070f46309adb526901192752fe421a3846398b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Zarządzanie zaleceniami dotyczącymi zabezpieczeń w Centrum zabezpieczeń Azure
Ten dokument przedstawia sposób użycia zalecenia w Centrum zabezpieczeń Azure ułatwiają ochronę zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="what-are-security-recommendations"></a>Jakie są zalecenia dotyczące zabezpieczeń?
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

## <a name="implementing-security-recommendations"></a>Wdrażanie zaleceń dotyczących zabezpieczeń
### <a name="set-recommendations"></a>Zestaw zaleceń
W [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md), Dowiedz się, jak:

* Konfigurowanie zasad zabezpieczeń.
* Włącz zbieranie danych.
* Wybierz, które zalecenia, aby zobaczyć jako część zasad zabezpieczeń.

Bieżący Centrum zalecenia dotyczące zasad wokół aktualizacji systemu, reguły linii bazowej, programów chroniących przed złośliwym [sieciowej grupy zabezpieczeń](../virtual-network/security-overview.md) na podsieci i interfejsów sieciowych, inspekcja bazy danych SQL, SQL bazy danych przezroczystego szyfrowania danych, i zapory aplikacji sieci web.  [Ustawianie zasad zabezpieczeń](security-center-policies.md) zawiera opis poszczególnych opcji zalecenia.

### <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania
Po skonfigurowaniu zasad zabezpieczeń usługa Security Center analizuje stan zabezpieczeń zasobów w celu identyfikowania potencjalnych luk w zabezpieczeniach. **Zalecenia** kafelka w obszarze **omówienie** informuje Cię o całkowitą liczbę zalecenia określone przez Centrum zabezpieczeń.

![Zalecenia dotyczące kafelka][1]

Aby wyświetlić szczegóły każde zalecenie, wybierz **kafelka zalecenia** w obszarze **omówienie**. **Zalecenia dotyczące** otwiera.

![Zalecenia dotyczące filtru][2]

Zalecenia są wyświetlane w postaci tabeli, gdzie każdy wiersz reprezentuje jedno zalecenie. Kolumny tabeli są:

* **Opis elementu**: przedstawiono zalecenia i co trzeba zrobić, aby go rozwiązać.
* **ZASÓB**: zawiera listę zasobów, do których ma zastosowanie tego zalecenia.
* **Stan**: Opisuje bieżący stan zalecenia:
  * **Otwórz**: zalecenie nie zostały jeszcze zarejestrowane.
  * **Trwa**: zalecenie jest aktualnie stosowane do zasobów i jest wymagana żadna akcja.
  * **Rozwiązane**: zalecenie zostało już zakończone (w tym przypadku wiersz jest wyszarzona).
* **WAŻNOŚĆ**: opisuje ważność określonego zalecenia:
  * **Wysoka**: Luka w zabezpieczeniach istnieje istotnego zasobu (np. aplikacji, maszyny Wirtualnej lub grupy zabezpieczeń sieci) i wymaga uwagi.
  * **Średnia liczba godzin**: istnieje luka w zabezpieczeniach i niekrytyczne lub dodatkowe kroki są wymagane, aby wyeliminować go lub do ukończenia procesu.
  * **Niski**: istnieje luka w zabezpieczeniach, które powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale można filtrować według zalecenia o niskiej ważności, jeśli chcesz zobaczyć je).

Użyj poniższej tabeli jako odwołanie, aby lepiej zrozumieć dostępne zalecenia i jak każdą z nich działa w przypadku zastosowania.

> [!NOTE]
> Warto poznać [klasycznego i modeli wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>
>

| Zalecenie | Opis |
| --- | --- |
| [Włącz zbieranie danych dla subskrypcji](security-center-enable-data-collection.md) |Zaleca się włączenie funkcji zbierania danych w zasadach zabezpieczeń dla każdej subskrypcji i wszystkich maszyn wirtualnych platformy Azure (maszyny wirtualne) oraz komputerów z systemem innym niż Azure. |
| [Korygowanie konfiguracji zabezpieczeń](security-center-remediate-os-vulnerabilities.md) |Zaleca się, że Dopasuj konfiguracje systemu operacyjnego z zasadami konfiguracji zabezpieczeń, na przykład, nie zezwalaj na zapisywanie haseł. |
| [Zastosuj aktualizacje systemu](security-center-apply-system-updates.md) |Zaleca się wdrożyć Brak zabezpieczenia systemu i krytycznych aktualizacji do systemu Windows i maszyn wirtualnych systemu Linux i komputerów. |
| [Zastosuj Just In Time kontroli dostępu do sieci](security-center-just-in-time.md) | Zaleca się zastosowanie tylko w dostęp maszyny Wirtualnej. Tylko w czasie jest funkcja w wersji zapoznawczej i dostępne w warstwie standardowa Centrum zabezpieczeń. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. |
| [Uruchom ponownie po zaktualizowaniu systemu](security-center-apply-system-updates.md#reboot-after-system-updates) |Zaleca ponowne uruchomienie maszyny wirtualnej w celu ukończenia procesu stosowania aktualizacji systemu. |
| [Dodawanie zapory aplikacji sieci Web](security-center-add-web-application-firewall.md) |Zaleca się, że wdrażania zapory aplikacji sieci web (WAF) dla punktów końcowych sieci web. Wszelkie publicznego połączonej adresu IP (IP poziomie wystąpienia lub IP równoważenia obciążenia) zawierający sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzący sieci web (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web. </br>Centrum zabezpieczeń zaleca się zainicjowanie obsługi zapory aplikacji sieci Web pomagających chronić przed atakami przeznaczonych dla aplikacji sieci web na maszynach wirtualnych i środowiska usługi aplikacji. Środowisko aplikacji (ASE) jest [Premium](https://azure.microsoft.com/pricing/details/app-service/) opcji plan usługi aplikacji Azure, która udostępnia środowisko pełni izolowanym środowisku, aby bezpiecznie pracować z aplikacjami usługi App service. Aby dowiedzieć się więcej na temat ASE, zobacz [dokumentację środowiska usługi aplikacji](../app-service/environment/intro.md).</br>Wiele aplikacji sieci web w Centrum zabezpieczeń można chronić, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację zapory aplikacji sieci Web, ruch musi zostać przekierowany do urządzenia zapory aplikacji sieci Web. Zmiany konfiguracji niezbędne kończy się po tego zalecenia. |
| [Dodawanie zapory nowej generacji](security-center-add-next-generation-firewall.md) |Zaleca się dodanie dalej zapory generacji (NGFW) z partnerem firmy Microsoft, aby zwiększyć z ochrony zabezpieczeń. |
| [Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Zaleca się, aby skonfigurować reguły grupy (NSG) zabezpieczeń sieciowych, który wymusza ruch przychodzący do maszyny Wirtualnej za pośrednictwem sieci NGFW. |
| [Zainstaluj punkt końcowy](security-center-install-endpoint-protection.md) |Zaleca aprowizację programów chroniących przed złośliwym oprogramowaniem na maszynach wirtualnych (dotyczy tylko maszyn wirtualnych z systemem Windows). |
| [Włączenie grup zabezpieczeń sieci dla podsieci lub maszyny wirtualne](security-center-enable-network-security-groups.md) |Zaleca się włączenie grup NSG dla podsieci lub maszyn wirtualnych. |
| [Ogranicz dostęp za pośrednictwem internetowy punkt końcowy](security-center-restrict-access-through-internet-facing-endpoints.md) |Zaleca się, aby skonfigurować reguły ruchu przychodzącego dla grup NSG. |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla serwerów SQL platformy Azure. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych Azure SQL. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych). |
| [Funkcja przezroczystego szyfrowania danych, Włącz bazy danych SQL](security-center-enable-transparent-data-encryption.md) |Zaleca się, aby włączyć szyfrowanie dla bazy danych SQL. (Tylko usługa azure SQL). |
| [Włącz agenta maszyny wirtualnej](security-center-enable-vm-agent.md) |Umożliwia zobaczenie, które maszyny wirtualne wymagają agenta maszyny wirtualnej. Agent maszyny Wirtualnej musi być zainstalowany na maszynach wirtualnych do udostępniania poprawki skanowanie skanowanie linii bazowej i programy chroniące przed złośliwym kodem. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/). |
| [Zastosuj szyfrowanie dysków](security-center-apply-disk-encryption.md) |Zaleca szyfrowanie dysków maszyny wirtualnej przy użyciu usługi Azure Disk Encryption (maszyny wirtualne z systemami Windows i Linux). Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej. |
| [Podawanie szczegółów dotyczących kontaktu ds. zabezpieczeń](security-center-provide-security-contact-details.md) |Zaleca, aby zapewnić ochronę informacji kontaktowych dla każdej subskrypcji. Informacje kontaktowe są wiadomości e-mail adres i numer telefonu. Informacje są używane się z Tobą, jeśli nasz zespół zabezpieczeń stwierdzi, że zasoby są uszkodzone. |
| [Aktualizacja wersji systemu operacyjnego](security-center-update-os-version.md) |Zaleca się, zaktualizuj wersję systemu operacyjnego (OS) dla usługi w chmurze do najnowszej dostępnej wersji do Twojej rodziny systemów operacyjnych.  Aby dowiedzieć się więcej na temat usługi w chmurze, zobacz [Omówienie usługi w chmurze](../cloud-services/cloud-services-choose-me.md). |
| [Funkcja oceny luk w zabezpieczeniach nie jest zainstalowana](security-center-vulnerability-assessment-recommendations.md) |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| [Korygowanie luk w zabezpieczeniach](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umożliwia wyświetlenie luk w zabezpieczeniach systemu i aplikacji wykrytych przez rozwiązanie do oceny luk w zabezpieczeniach zainstalowane na maszynie wirtualnej. |
| [Włącz szyfrowanie dla konta magazynu Azure](security-center-enable-encryption-for-storage-account.md) | Zaleca się, Włącz szyfrowanie usługi Magazyn Azure dla przechowywanych danych. Szyfrowanie usługi Magazyn (SSE) polega na szyfrowaniu danych podczas ich zapisywania do magazynu Azure i odszyfrowuje przed pobierania. SSE jest obecnie dostępny tylko dla usługi obiektów Blob platformy Azure i może służyć do blokowe i stronicowe obiekty BLOB i uzupełnialnych obiektów blob. Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Magazyn danych magazynowanych](../storage/common/storage-service-encryption.md).</br>SSE jest obsługiwana tylko na kontach magazynu Menedżera zasobów. |

Można filtrować i odrzucić zalecenia.

1. Wybierz **filtru** na **zalecenia** bloku. **Filtru** zostanie otwarty blok i wybierz ważność i stan wartości, które chcesz wyświetlić.

2. Jeśli okaże się, że zalecenia nie ma zastosowania, możesz zignorować zalecenie i odfiltrować z widoku. Istnieją dwa sposoby odrzucania zalecenia. Jednym ze sposobów jest element kliknij prawym przyciskiem myszy, a następnie wybierz **odrzucenia**. Druga to wskaźnik myszy nad elementem, kliknij przycisk z wielokropkiem po prawej stronie, a następnie wybierz **odrzucenia**. Odrzucony zalecenia można wyświetlić, klikając **filtru**, a następnie wybierając **odrzucone**.

    ![Odrzuć zalecenia][3]

### <a name="apply-recommendations"></a>Stosować zalecenia
Po przejrzeniu wszystkie zalecenia decyzji, które co należy najpierw zastosować. Firma Microsoft zaleca użycie ocenę ważności jako główny parametr do oceny zaleceń, które powinny być stosowane najpierw.

W tabeli powyższe zalecenia wybierz zalecenia i przeprowadzenie go jako przykład sposobu stosowania zaleceń.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie Przedstawiliśmy zalecenia dotyczące zabezpieczeń w Centrum zabezpieczeń. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w Centrum zabezpieczeń Azure](security-center-policies.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak reagowania na alerty zabezpieczeń i zarządzania nimi.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](http://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
[3]: ./media/security-center-recommendations/dismiss-recommendations.png
