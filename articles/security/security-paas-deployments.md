---
title: Zabezpieczanie wdrożeń typu PaaS | Dokumentacja firmy Microsoft
description: " Omówienie zabezpieczeń zalety rozwiązania PaaS i inne modele usług w chmurze i Dowiedz się, zalecane rozwiązania dotyczące zabezpieczania wdrożenia modelu PaaS platformy Azure. "
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
ms.openlocfilehash: da5d59aaaea8e6186609eb5f3419fba5e67d4279
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "42055181"
---
# <a name="securing-paas-deployments"></a>Zabezpieczanie wdrożeń PaaS

Ten artykuł zawiera informacje ułatwiające:

- Poznaj zalety zabezpieczeń, obsługi aplikacji w chmurze
- Ocena korzyści związane z bezpieczeństwem, platforma jako usługa (PaaS) w porównaniu z innymi modelami usługi chmury
- Zmienić nastawienie zabezpieczeń sieci skoncentrowane na podejście obwodowej skoncentrowane na tożsamość zabezpieczeń
- Ogólne PaaS najlepszych praktyk zalecenia dotyczące implementowania zabezpieczeń

## <a name="cloud-security-advantages"></a>Korzyści związane z bezpieczeństwem chmury
Istnieją korzyści związane z bezpieczeństwem do bycia w chmurze. W środowisku lokalnym, prawdopodobnie organizacjach niewypełnienia obowiązków i ograniczone zasoby dostępne dla wartości inwestycji w zabezpieczeń, która tworzy środowisko, gdzie osoby atakujące mogą wykorzystać luki w zabezpieczeniach we wszystkich warstwach.

![Korzyści związane z bezpieczeństwem ery chmury][1]

Organizacje mogą zwiększyć ich wykrywanie zagrożeń i najlepsze czasy odpowiedzi przy użyciu funkcji zabezpieczeń opartych na chmurze dostawcy i rozwiązań inteligentnych w chmurze.  Przesuwając obowiązki do dostawcy chmury, organizacje mogą uzyskać więcej zakresu zabezpieczeń, co umożliwia ich ponowne przydzielenie zasoby dotyczące zabezpieczeń i budżetu do innych priorytetów biznesowych.

## <a name="division-of-responsibility"></a>Podział odpowiedzialności
Należy zrozumieć podział odpowiedzialności pomiędzy użytkownikiem a firmą Microsoft. W środowisku lokalnym, jesteś właścicielem całego stosu, ale po przeniesieniu do chmury pewne obowiązki przesyłanie do firmy Microsoft. Następujące macierzy odpowiedzialność przedstawiono obszary stosu w danym wdrożeniu rozwiązania SaaS, PaaS i IaaS, które ponosisz odpowiedzialność za i firmy Microsoft jest odpowiedzialna.

![Odpowiedzialność za stref][2]

Dla wszystkich typów wdrożeń chmury jesteś właścicielem swoich danych i tożsamości. Odpowiedzialność za ochronę bezpieczeństwa danych i tożsamości, zasoby lokalne i chmurowe składniki Ty określasz, (która zależy od typu usługi).

Obowiązki, które są zawsze zachowywane przez użytkownika, niezależnie od tego typu wdrożenia, są następujące:

- Dane
- Punkty końcowe
- Konto
- Zarządzanie dostępem

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>Korzyści związane z bezpieczeństwem rozwiązania paas, model usług w chmurze
Korzystając z tej samej macierzy odpowiedzialność, Przyjrzyjmy się korzyści związane z bezpieczeństwem, wdrożenia usługi PaaS platformy Azure, czy w środowisku lokalnym.

![Korzyści związane z bezpieczeństwem rozwiązania paas][3]

Uruchamianie w dolnej części stosu infrastruktury fizycznej, Microsoft zmniejsza typowych zagrożeń i obowiązków. Ponieważ firmy Microsoft w chmurze jest stale monitorowane przez firmę Microsoft, jest trudny do ataków. Nie ma sensu osobie atakującej do prowadzenia firmy Microsoft w chmurze jako element docelowy. Chyba, że osoba atakująca ma dużo pieniędzy i zasobów, osoba atakująca jest prawdopodobne przejść do innego obiektu docelowego.  

W środku stosu nie ma żadnej różnicy między to wdrożenie PaaS i lokalne. Warstwa aplikacji oraz warstwa zarządzania kontem i dostępu będziesz mieć podobne ryzyka. W następnej sekcji kroki opisane w tym artykule firma Microsoft przeprowadzi Cię do najlepsze rozwiązania dotyczące eliminując lub zmniejszenia ryzyka.

W górnej części stosu, nadzór nad danymi i rights management, należy wykonać na jednym ryzyko, że może być ograniczona przez zarządzanie kluczami. (Zarządzanie kluczami omówiono najlepsze rozwiązania.) Podczas zarządzania kluczami odpowiada dodatkowych, masz obszarów to wdrożenie PaaS, która nie jest już konieczne zarządzanie nimi, dzięki czemu mogą przeprowadzić migrację zasobów do zarządzania kluczami.

Platforma Azure również zapewnia silną ochronę przed atakami DDoS przy użyciu różnych technologiach opartych na sieci. Wszystkie typy sieciowymi programami wykorzystującymi metody ochrony przed atakami DDoS jednak ich ograniczeń na zasadach na jedno łącze i na centrum danych. Aby uniknąć wpływu ataków DDoS, możesz korzystać z zalet platformy Azure core chmury możliwości dzięki któremu można szybko i automatyczne skalowanie w poziomie obrony przed atakami DDoS. Szczegółowo omówimy bardziej szczegółowo w jaki sposób można to zrobić w artykułach zalecane praktyki.

## <a name="modernizing-the-defenders-mindset"></a>Modernizowanie sposób myślenia programu defender
Dzięki temu wdrożenia Przyjdź na zmianę w danej metody ogólnej zabezpieczeń. Następuje przejście od konieczności kontrolowania wszystko samodzielnie na potrzeby współużytkowania odpowiedzialność firmy Microsoft.

Inny istotną różnicą między PaaS i tradycyjnych lokalnych wdrożeń jest nowy widok definiuje głównych zabezpieczeń obwodowych. W przeszłości zabezpieczeń obwodowych podstawowego środowiska lokalnego zostało sieci i większości projektów zabezpieczeń lokalnych sieć jest używana jako jego pivot głównych zabezpieczeń. Dla wdrożeń typu PaaS są lepiej byłoby, biorąc pod uwagę tożsamości jako głównych zabezpieczeń obwodowych.

## <a name="identity-as-the-primary-security-perimeter"></a>Tożsamość jako głównych zabezpieczeń obwodowych
Jeden z pięć istotnych cech chmury obliczeniowej jest szeroki dostęp do sieci, co sprawia, że na interfejsach sieciowych myśl mniej istotne. Celem większości chmury obliczeniowej jest umożliwienie użytkownikom dostęp do zasobów, niezależnie od lokalizacji. Dla większości użytkowników ich lokalizacji będzie zawarty w Internecie.

Na poniższej ilustracji przedstawiono, jak zabezpieczeń obwodowych powstał z obwód sieci obwodowej tożsamości. Zabezpieczenia staje się mniej o obronie sieci i więcej o obronie dane, a także zarządzanie zabezpieczeniami aplikacji i użytkowników. Główną różnicą jest chcesz wypychana bliżej zabezpieczeń, co ważne jest, aby Twoja firma.

![Tożsamość jako nową strefą zabezpieczeń][4]

Początkowo usług PaaS platformy Azure (na przykład ról sieć web i Azure SQL) podany niewielkiego lub żadnego tradycyjnej sieci obwodowej zabezpieczenia. Został rozumie, że celem elementu było łączyć się z Internetem (rola sieci web) i uwierzytelniania oferuje nową strefą (na przykład obiekt BLOB lub Azure SQL).

Rozwiązania w zakresie zabezpieczeń nowoczesnych przyjęto założenie, że osoba planująca atak złamała obwód sieci. W związku z tym defense nowoczesnych rozwiązań zostało przeniesione do tożsamości. Organizacje należy ustanowić oparta na tożsamości zabezpieczeń obwodowych przy użyciu silnego uwierzytelniania i autoryzacji higieny (najlepsze rozwiązania).

## <a name="recommendations-for-managing-the-identity-perimeter"></a>Zalecenia dotyczące zarządzania obwodowej tożsamości

Zasady i wzorców dla sieci obwodowej były dostępne od dziesięcioleci. Natomiast w branży zawiera stosunkowo mniej środowisko przy użyciu tożsamości jako głównych zabezpieczeń obwodowych. Dzięki temu powiedział zakumulowaliśmy wystarczająco dużo doświadczenie w celu zapewnienia ogólne zalecenia są sprawdzone w polu, które dotyczą praktycznie wszystkie usługi PaaS.

Poniżej przedstawiono podsumowanie ogólne najlepszym rozwiązaniem rozwiązania do zarządzania w sieci obwodowej tożsamości.

- **Nie strać swoje klucze lub poświadczenia** zabezpieczania kluczy i poświadczeń są one niezbędne przy zabezpieczanie wdrożeń typu PaaS. Utraty kluczy i poświadczeń jest to powszechny problem. Jeden dobrym rozwiązaniem jest użycie scentralizowanego rozwiązania, w których klucze i wpisy tajne są przechowywane w sprzętowych modułach zabezpieczeń (HSM). Platforma Azure udostępnia w chmurze za pomocą modułu HSM [usługi Azure Key Vault](../key-vault/key-vault-whatis.md).
- **Nie umieszczaj poświadczeń i innych wpisów tajnych do kodu źródłowego lub GitHub** jedyną czynnością co gorsza, nie tracąc kluczy i poświadczeń jest nieautoryzowana osoba uzyskać do nich dostęp. Osoby atakujące mogą korzystać z zalet bot technologie, aby znaleźć klucze i wpisy tajne przechowywane w repozytoriach kodu, takich jak GitHub. Nie umieszczaj klucza i wpisów tajnych w tych publicznych repozytoriach kodów źródłowych.
- **Ochrona interfejsów zarządzania sieci maszyny Wirtualnej na usługach PaaS i IaaS hybrydowego** usługi IaaS i PaaS są uruchomione na maszynach wirtualnych (VM). W zależności od używanego typu usługi są dostępne kilka interfejsów zarządzania tym Włącz umożliwia zdalne zarządzanie te maszyny wirtualne bezpośrednio. Zdalne zarządzanie protokoły, takie jak [protokołu Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell), [protokołu RDP (Remote Desktop)](https://support.microsoft.com/kb/186607), i [zdalnego programu PowerShell](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) mogą być używane. Ogólnie rzecz biorąc zaleca się, że nie zostanie włączone bezpośredniego dostępu zdalnego do maszyn wirtualnych z Internetu. Jeśli to możliwe, należy użyć metody alternatywne, na przykład przy użyciu wirtualnej sieci prywatnej w sieci wirtualnej platformy Azure. Jeśli alternatywnych metod nie są dostępne, a następnie upewnij się, że używasz złożone hasło, a jeśli są dostępne, uwierzytelniania dwuskładnikowego (takie jak [usługi Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)).
- **Korzystać z silnego uwierzytelniania i autoryzacji platform**

  - W usłudze Azure AD zamiast niestandardowych magazynów użytkownika, należy używać tożsamości federacyjnej. Korzystając z tożsamości federacyjnej, możesz korzystać z podejścia opartego na platformie i delegować Zarządzanie tożsamościami autoryzowanych do partnerów. Podejście tożsamości federacyjnej jest szczególnie ważne w sytuacjach, gdy pracownicy są kończone, a informacje muszą być uwzględniane w wielu systemów tożsamości i autoryzacji.
  - Użyj platformę mechanizmy uwierzytelniania i autoryzacji, zamiast kodu niestandardowego. Przyczyną jest to, że opracowanie kodu, uwierzytelnianie niestandardowe może prowadzić do występowania błędów. Większość deweloperów nie są ekspertami w dziedzinie zabezpieczeń i prawdopodobnie nie trzeba pamiętać precyzyjnie i Poznaj najnowsze osiągnięcia w sekcji uwierzytelnianie i autoryzację. Komercyjne kodu (na przykład z firmy Microsoft) jest często często przeglądowi zabezpieczeń.
  - Używanie uwierzytelniania wieloskładnikowego. Uwierzytelnianie wieloskładnikowe jest bieżące normy, do uwierzytelniania i autoryzacji, ponieważ eliminuje luki bezpieczeństwa związane z typów uwierzytelniania nazwy użytkownika i hasła. Dostęp do interfejsów zarządzania platformy Azure (portal/zdalnego programu PowerShell) i usług przeznaczonych dla klientów powinny być zaprojektowane i skonfigurowany do używania [usługi Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md).
  - Użyj standardowych protokołów uwierzytelniania, takich jak OAuth2 i protokołu Kerberos. Protokoły te zostały często elementu równorzędnego przeglądu i prawdopodobnie są implementowane jako część platformy bibliotek uwierzytelniania i autoryzacji.

## <a name="next-steps"></a>Kolejne kroki
W tym artykule skupiliśmy się na korzyści związane z bezpieczeństwem wdrożenia modelu PaaS platformy Azure. Dowiedz się, zalecane praktyki zabezpieczanie aplikacji PaaS w sieci web i rozwiązań mobilnych. Rozpoczniemy od usługi Azure App Service, Azure SQL Database i Azure SQL Data Warehouse. W miarę udostępniania artykuły dotyczące zalecanych rozwiązań dotyczących innych usług platformy Azure będzie świadczona łącza na poniższej liście:

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database i Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Usługi Azure REDIS Cache
- Azure Service Bus
- Zapory aplikacji sieci Web

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
