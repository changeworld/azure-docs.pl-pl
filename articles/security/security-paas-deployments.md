---
title: Zabezpieczanie wdrożenia PaaS | Dokumentacja firmy Microsoft
description: " Zrozumieć zalety zabezpieczeń PaaS i inne modele usług w chmurze i Dowiedz się, najważniejsze wskazówki dotyczące zabezpieczania wdrożenia Azure PaaS. "
services: security
documentationcenter: na
author: techlake
manager: MBaldwin
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: f19c52629a997687692eef9bce2e13b2b7894052
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
ms.locfileid: "31794739"
---
# <a name="securing-paas-deployments"></a>Zabezpieczanie wdrożeń PaaS

Ten artykuł zawiera informacje ułatwiające:

- Zrozumienie zabezpieczeń zalety obsługi aplikacji w chmurze
- Ocena korzyści zabezpieczeń platformy jako usługa (PaaS) w porównaniu z innymi modelami usługi chmury
- Zmień fokus zabezpieczeń z sieci skoncentrowane na podejście obwodowej skoncentrowane na tożsamość zabezpieczeń
- Implementowanie ogólne PaaS zabezpieczeń poniżej rekomendowane najlepsze rozwiązania

## <a name="cloud-security-advantages"></a>Zalety zabezpieczeń w chmurze
Jest większe bezpieczeństwo w chmurze. W środowisku lokalnym, organizacje mogą stosować unmet obowiązki i ograniczone zasoby dostępne inwestycji w zabezpieczeń, który tworzy środowisko, w którym osoby atakujące mogą się wykorzystać luki w wszystkie warstwy.

![Zalety zabezpieczeń ery chmury][1]

Organizacje mogą zwiększyć ich wykrywanie zagrożeń i czasy odpowiedzi przy użyciu funkcji zabezpieczeń opartych na chmurze dostawcy i analizy chmury.  Ustalając obowiązki do dostawcy chmury, organizacje mogą korzystać więcej pokrycia zabezpieczeń, co umożliwia ich ponowne przydzielenie zasobów zabezpieczeń i budżetu priorytety innych firm.

## <a name="division-of-responsibility"></a>Podział odpowiedzialności
Należy zrozumieć podział obowiązków między Tobą a firmą Microsoft. W infrastrukturze lokalnej, właścicielem cały stos, ale przy przenoszeniu do chmury pewne obowiązki transferu do firmy Microsoft. Następujące macierzy odpowiedzialność przedstawiono obszary stosu we wdrożeniu SaaS, PaaS i IaaS, który jest odpowiedzialny za i firmy Microsoft jest odpowiedzialny za.

![Odpowiedzialność stref][2]

Dla wszystkich typów wdrożeń chmury posiadanych danych i tożsamości. Ponosisz odpowiedzialność za ochronę bezpieczeństwa danych i tożsamości, zasobów lokalnych i chmurze składniki można sterować (która zależy od typu usługi).

Obowiązki, które są zawsze zachowywane przez administratora, niezależnie od tego typu wdrożenia, są następujące:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Zalety zabezpieczeń PaaS modelu usługi w chmurze
Za pomocą tej samej macierzy odpowiedzialność, Przyjrzyjmy się zalet zabezpieczeń wdrożenie Azure PaaS lub lokalnie.

![Zalety zabezpieczeń PaaS][3]

Zaczynając od dołu stosu infrastruktury fizycznej Microsoft zmniejsza typowe zagrożenia i jego obowiązki. Ponieważ w chmurze firmy Microsoft jest stale monitorowane przez firmę Microsoft, jest trudna na ataki. Go nie ma sensu atakujący może wykonywać firmy Microsoft w chmurze jako miejsce docelowe. Jeśli nie ma dużą oszczędność pieniędzy i zasobów, osoba atakująca prawdopodobnie przenieść na inny element docelowy.  

W trakcie stosu należy nie ma żadnej różnicy między wdrożenie PaaS i lokalnych. Na warstwie aplikacji i warstwa zarządzania konta i dostępu masz podobne czynników ryzyka. W następnej sekcji kroki opisane w tym artykule firma Microsoft przeprowadzi Cię do najlepszych rozwiązań dotyczących wyeliminowanie lub zmniejszenia ryzyka.

W górnej części stosu, zarządzanie danymi i usługi rights management możesz wykonać na jednym ryzyko, że można zminimalizować przez zarządzanie kluczami. (Zarządzanie kluczami jest objęte najlepszych rozwiązań.) Podczas zarządzania kluczami jest dodatkowe odpowiedzialność, masz obszarów, wdrożenie PaaS, który nie jest już konieczne zarządzanie, mogą przeprowadzić migrację zasobów do zarządzania kluczami.

Platformy Azure również umożliwia silną ochronę przed atakami DDoS za pomocą różnych technologii sieciowych. Wszystkie typy opartych na sieci metody ochrony przed atakami DDoS jednak limity ich na podstawie na jedno łącze i na centrum danych. Aby uniknąć wpływu ataków DDoS, można wykorzystać Azure core chmury możliwości włączania można szybko i automatycznie skalować w poziomie, aby chronić przed atakami DDoS. Znajdują się bardziej szczegółowo na jak to zrobić w artykułach zalecane praktyki.

## <a name="modernizing-the-defenders-mindset"></a>Modernizacji mindset defender
Z PaaS wdrożenia są shift w Twojej ogólnego podejścia do zabezpieczeń. Następuje przejście od konieczności kontrolowania wszystko samodzielnie udostępniania odpowiedzialności z firmą Microsoft.

Inną istotną różnicą między PaaS i wdrożeń tradycyjnych, lokalnie jest nowy widok co definiuje obwodowej głównej zabezpieczeń. W przeszłości granicznej zabezpieczeń lokalnych podstawowy został sieci i większości projektów zabezpieczeń lokalnych używają sieci jako jego pivot głównej zabezpieczeń. W przypadku wdrożeń typu PaaS są lepiej obsłużone przez uwzględnieniu tożsamości jako obwodowej głównej zabezpieczeń.

## <a name="identity-as-the-primary-security-perimeter"></a>Tożsamość jako obwodowej głównej zabezpieczeń
Jeden z pięć istotnych cech chmury obliczeniowej jest szerokiego dostępu do sieci, co sprawia, że skoncentrowane sieci myśląc mniej istotne. Celem znacznie chmury obliczeniowej jest umożliwienie użytkownikom dostęp do zasobów, niezależnie od lokalizacji. Dla większości użytkowników ich lokalizacji ma być gdzieś w Internecie.

Na poniższej ilustracji przedstawiono, jak granicznej zabezpieczeń powstał z sieci obwodowej do obwodu tożsamości. Zabezpieczenia staje się mniej o jednocześnie obrona dostępu do sieci i więcej informacji o jednocześnie obrona dostępu do danych, a także zarządzanie zabezpieczeniami aplikacji i użytkowników. Najważniejsza różnica jest chcesz wypychana bliżej zabezpieczeń na najważniejszym w firmie.

![Tożsamość jako nowe granicznej zabezpieczeń][4]

Początkowo usług Azure PaaS (na przykład ról sieć web i Azure SQL) podać niewielkiego lub żadnego z tradycyjną siecią obwodową zabezpieczenia. Został rozumie, że celem elementu było łączyć się z Internetem (role sieci web) i uwierzytelniania zapewnia nowy obwodowej (na przykład obiekt BLOB lub Azure SQL).

Nowoczesne rozwiązania przyjęto założenie, atakujący dokonuje naruszony sieci obwodowej. W związku z tym obrony nowoczesnych rozwiązań zostały przeniesione do tożsamości. Organizacje należy ustanowić granicznej zabezpieczeń opartych na tożsamości z silnego uwierzytelniania i autoryzacji higieny (najlepsze rozwiązania).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Zalecenia dotyczące zarządzania obwodowej tożsamości

Zasady i wzorce dla sieci obwodowej były dostępne dla dekad. Natomiast w branży ma stosunkowo mniej środowisko z przy użyciu tożsamości jako obwodowej głównej zabezpieczeń. Z tym powiedział możemy współdzielenia za mało doświadczenie w celu zapewnienia ogólne zalecenia zostały sprawdzone w polu, które mają zastosowanie do niemal wszystkich usług PaaS.

Poniżej przedstawiono podsumowanie ogólnych najlepszych rozwiązań stosowanych do zarządzania w sieci obwodowej tożsamości.

- **Nie utrać Twoje klucze lub poświadczenia** zabezpieczania kluczy i poświadczeń jest niezbędne do zabezpieczania wdrożeń typu PaaS. Utraty kluczy i poświadczeń jest to powszechny problem. Jeden dobrym rozwiązaniem jest użycie scentralizowanego rozwiązania, w których kluczy i kluczy tajnych są przechowywane w sprzętowych modułach zabezpieczeń (HSM). Platforma Azure udostępnia w chmurze za pomocą modułu HSM [usługi Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Nie umieszczaj poświadczeń i innych informacji poufnych do kodu źródłowego lub GitHub** jedynym elementem gorsze od utraty Twoje klucze i poświadczeń o nieautoryzowana osoba uzyskać do nich dostęp. Osoby atakujące mogą wykorzystać bot technologie, aby znaleźć kluczy i kluczy tajnych przechowywane w repozytoriach kodów, takich jak usługi GitHub. Nie należy umieszczać kluczy i kluczy tajnych w tych publicznych repozytoriach kodów źródłowych.
- **Ochrona interfejsów zarządzania maszyny Wirtualnej na hybrydowego usługi PaaS i IaaS** IaaS i PaaS usługi są uruchomione na maszynach wirtualnych (VM). W zależności od typu usługi, są dostępne kilka interfejsów zarządzania tym Włącz te maszyny wirtualne można zdalnie zarządzać bezpośrednio. Zdalne zarządzanie protokołów, takich jak [protokołu Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [protokołu RDP (Remote Desktop)](https://support.microsoft.com/kb/186607), i [zdalnego programu PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) mogą być używane. Ogólnie rzecz biorąc zaleca się, że nie zostanie włączona bezpośredni dostęp zdalny do maszyn wirtualnych z Internetu. Jeśli to możliwe, należy użyć alternatywnych metod takich jak przy użyciu wirtualnej sieci prywatnej w sieci wirtualnej platformy Azure. Jeśli alternatywnych metod nie są dostępne, a następnie upewnij się, że używasz złożone hasła, a jeśli jest dostępna, uwierzytelnianie dwuskładnikowe (takich jak [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Użyj silnego uwierzytelniania i autoryzacji platformy**

  - Użyj tożsamości federacyjnych w usłudze Azure AD zamiast magazynów użytkownika niestandardowego. Korzystając z tożsamości federacyjnej, możesz korzystać z platformy opartego i przekazać Zarządzanie tożsamościami autoryzowanych partnerów. Podejście tożsamości federacyjnych jest szczególnie ważne w scenariuszach, gdy pracownicy są zakończone i informacje muszą być uwzględniane w wielu systemów tożsamości oraz autoryzacji.
  - Użyj platformy dostarczona mechanizmy uwierzytelniania i autoryzacji, zamiast kodu niestandardowego. Przyczyną jest to, że opracowywanie uwierzytelniania niestandardowego kodu może być błąd podatnych na błędy. Większość deweloperów nie są ekspertów zabezpieczeń i prawdopodobnie nie należy pamiętać o precyzyjnie oraz najnowsze osiągnięcia w uwierzytelniania i autoryzacji. Komercyjnych kodu (na przykład od firmy Microsoft) jest często często przeglądu zabezpieczeń.
  - Uwierzytelnianie wieloskładnikowe. Uwierzytelnianie wieloskładnikowe jest obecnie standardowym do uwierzytelniania i autoryzacji, ponieważ pozwala ona na uniknięcie słabych zabezpieczeń związane z nazwy użytkownika i hasła typy uwierzytelniania. Dostęp do interfejsów zarządzania platformy Azure (portal/zdalne programu PowerShell) oraz klientów usługi powinny być zaprojektowane i skonfigurowane do używania [Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Użyj standardowych protokołów uwierzytelniania, takich jak OAuth2 i protokołu Kerberos. Te protokoły zostały często dokładnie przeglądane i prawdopodobnie są zaimplementowane jako część bibliotek platformy do uwierzytelniania i autoryzacji.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule firma Microsoft skupia się na zalet zabezpieczeń wdrożenie Azure PaaS. Następnie Dowiedz się, najważniejsze wskazówki dotyczące zabezpieczania PaaS w sieci web i rozwiązań mobilnych. Zaczniemy usłudze Azure App Service, baza danych SQL Azure i usługi Azure SQL Data Warehouse. Jako artykułów na zaleceń dla innych usług platformy Azure są dostępne, linki będzie świadczona na poniższej liście:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Baza danych Azure SQL i magazyn danych Azure SQL](security-paas-applications-using-sql.md)
- Azure Storage
- Pamięć podręczna Azure REDIS
- Azure Service Bus
- Zapory aplikacji sieci Web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
