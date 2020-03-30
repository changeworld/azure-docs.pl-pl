---
title: Lista kontrolna zabezpieczeń operacyjnych platformy Azure| Dokumenty firmy Microsoft
description: Ten artykuł zawiera zestaw listy kontrolnej dla zabezpieczeń operacyjnych platformy Azure.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: tomsh
ms.openlocfilehash: fb940857a0f88590cb9bbbf56b9e6a791299309f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75980961"
---
# <a name="azure-operational-security-checklist"></a>Lista kontrolna zabezpieczeń operacyjnych platformy Azure
Wdrażanie aplikacji na platformie Azure jest szybkie, łatwe i ekonomiczne. Przed wdrożeniem aplikacji w chmurze w produkcji przydatne do listy kontrolnej, aby pomóc w ocenie aplikacji na liście podstawowych i zalecanych akcji zabezpieczeń operacyjnych, które należy wziąć pod uwagę.

## <a name="introduction"></a>Wprowadzenie

Platforma Azure udostępnia pakiet usług infrastruktury, których można użyć do wdrażania aplikacji. Usługa Azure Operational Security odnosi się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony ich danych, aplikacji i innych zasobów na platformie Microsoft Azure.

-   Aby uzyskać maksymalne korzyści z platformy w chmurze, zaleca się korzystanie z usług platformy Azure i postępuj zgodnie z listą kontrolną.
-   Organizacje, które inwestują czas i zasoby oceniające gotowość operacyjną swoich aplikacji przed uruchomieniem, mają znacznie wyższy wskaźnik zadowolenia niż te, które tego nie robią. Podczas wykonywania tej pracy listy kontrolne mogą być nieocenionym mechanizmem zapewniającym, że aplikacje są oceniane konsekwentnie i całościowo.
-   Poziom oceny operacyjnej różni się w zależności od poziomu dojrzałości w chmurze w organizacji oraz fazy rozwoju aplikacji, potrzeb w zakresie dostępności i wymagań dotyczących wrażliwości na dane.

## <a name="checklist"></a>Lista kontrolna

Ta lista kontrolna ma na celu pomóc przedsiębiorstwom przemyśleć różne zagadnienia dotyczące zabezpieczeń operacyjnych podczas wdrażania zaawansowanych aplikacji dla przedsiębiorstw na platformie Azure. Może również służyć do tworzenia bezpiecznej migracji do chmury i strategii działania dla twojej organizacji.

|Lista kontrolna kategoria| Opis|
| ------------ | -------- |
| [<br>Role zabezpieczeń & kontroli dostępu](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Użyj [kontroli dostępu opartej na rolach (RBAC),](../../role-based-access-control/role-assignments-portal.md) aby zapewnić specyficzne dla użytkownika, które służy do przypisywania uprawnień użytkownikom, grupom i aplikacjom w określonym zakresie.</li></ul> |
| [<br>Gromadzenie danych & magazynowanie](../../storage/blobs/security-recommendations.md)|<ul><li>Użyj zabezpieczeń płaszczyzny zarządzania, aby zabezpieczyć swoje konto magazynu przy użyciu [kontroli dostępu opartej na rolach (RBAC).](../../role-based-access-control/role-assignments-portal.md)</li><li>Bezpieczeństwo płaszczyzny danych do zabezpieczania dostępu do danych przy użyciu [sygnatur dostępu współdzielonego (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md) i zasad dostępu przechowywanego.</li><li>Użyj szyfrowania na poziomie transportu — przy użyciu protokołu HTTPS i szyfrowania używanego przez [protokół SMB (protokoły bloków komunikatów serwera) 3.0](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) dla [udziałów plików platformy Azure](../../storage/files/storage-dotnet-how-to-use-files.md).</li><li>[Szyfrowanie po stronie klienta](../../storage/common/storage-client-side-encryption.md) służy do zabezpieczania danych wysyłanych do kont magazynu, gdy wymagana jest wyłączna kontrola kluczy szyfrowania. </li><li>Użyj [szyfrowania usługi magazynu (SSE),](../../storage/common/storage-service-encryption.md) aby automatycznie szyfrować dane w usłudze Azure Storage, a [szyfrowanie dysków platformy Azure](../azure-security-disk-encryption-overview.md) do szyfrowania plików dysków maszyn wirtualnych dla dysków systemu operacyjnego i dysków z danymi.</li><li>Usługa Azure [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics) służy do monitorowania typu autoryzacji; podobnie jak w przypadku magazynu obiektów Blob, można sprawdzić, czy użytkownicy używali sygnatury dostępu współdzielonego lub kluczy konta magazynu.</li><li>Udostępnianie [zasobów między źródłami (CORS) umożliwia](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) dostęp do zasobów magazynu z różnych domen.</li></ul> |
|[<br>Zasady bezpieczeństwa & zalecenia](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Użyj [usługi Azure Security Center](../../security-center/security-center-install-endpoint-protection.md) do wdrażania rozwiązań końcowych.</li><li>Dodaj [zaporę aplikacji sieci web (WAF)](../../application-gateway/waf-overview.md) do bezpiecznych aplikacji sieci web.</li><li>   Użyj [zapory](../../sentinel/connect-data-sources.md) od partnera firmy Microsoft, aby zwiększyć zabezpieczenia. </li><li>Zastosuj szczegóły kontaktu zabezpieczeń dla subskrypcji platformy Azure; w ten sposób [Centrum Microsoft Security Response Center (MSRC)](https://technet.microsoft.com/security/dn528958.aspx) skontaktuje się z Tobą, jeśli wykryje, że dane klienta zostały uzyskiczone przez osobę niezgodną z prawem lub nieautoryzowaną.</li></ul> |
| [<br>Zarządzanie dostępem & tożsamości](identity-management-best-practices.md)|<ul><li>[Synchronizuj katalog lokalny z katalogiem w chmurze przy użyciu usługi Azure AD.](../../active-directory/hybrid/whatis-hybrid-identity.md)</li><li>Użyj [logowania jednokrotnego,](https://azure.microsoft.com/resources/videos/overview-of-single-sign-on/) aby umożliwić użytkownikom dostęp do aplikacji SaaS na podstawie ich konta organizacji w usłudze Azure AD.</li><li>Raport [Aktywności rejestracji resetowania hasła](../../active-directory/active-directory-passwords-reporting.md) służy do monitorowania użytkowników, którzy się rejestrują.</li><li>Włącz [uwierzytelnianie wieloskładnikowe (MFA)](../../active-directory/authentication/multi-factor-authentication.md) dla użytkowników.</li><li>Deweloperzy do korzystania z funkcji bezpiecznej tożsamości dla aplikacji, takich jak [Microsoft Security Development Lifecycle (SDL)](https://www.microsoft.com/download/details.aspx?id=12379).</li><li>Aktywnie monitoruj pod kątem podejrzanych działań przy użyciu raportów anomalii usługi Azure AD Premium i [funkcji ochrony tożsamości usługi Azure AD.](../../active-directory/identity-protection/overview.md)</li></ul> |
|[<br>Bieżące monitorowanie zabezpieczeń](../../security-center/security-center-planning-and-operations-guide.md)|<ul><li>Użyj [dzienników usługi Azure Monitor](../../log-analytics/log-analytics-queries.md) rozwiązania do oceny złośliwego oprogramowania, aby raportować stan ochrony przed złośliwym oprogramowaniem w infrastrukturze.</li><li>Użyj [update assessment,](../../automation/automation-update-management.md) aby określić ogólną ekspozycję na potencjalne problemy z zabezpieczeniami oraz czy i jak krytyczne są te aktualizacje dla twojego środowiska.</li><li>[Tożsamość i dostęp](../../security-center/security-center-monitoring.md) zapewniają przegląd użytkowników </li><ul><li>stan tożsamości użytkownika,</li><li>liczba nieudanych prób zalogowania się,</li><li> konto użytkownika, które były używane podczas tych prób, konta, które zostały zablokowane</li> <li>konta ze zmienionym lub zresetowaniem hasła </li><li>Aktualnie liczba kont, które są zalogowane.</li></ul></ul> |
| [<br>Funkcje wykrywania usługi Azure Security Center](../../security-center/security-center-alerts-overview.md#detect-threats)|<ul><li>Użyj [funkcji wykrywania,](../../security-center/security-center-alerts-overview.md#detect-threats)aby zidentyfikować aktywne zagrożenia kierowane na zasoby platformy Microsoft Azure.</li><li>Użyj [zintegrowanej analizy zagrożeń,](https://blogs.msdn.microsoft.com/azuresecurity/2016/12/19/get-threat-intelligence-reports-with-azure-security-center/) która wyszukuje znanych złych aktorów, wykorzystując globalną analizę zagrożeń z produktów i usług firmy [Microsoft, Jednostkę Microsoft Digital Crimes Unit (DCU),](https://www.microsoft.com/trustcenter/security/cybercrime) [Microsoft Security Response Center (MSRC)](response-center.md)i zewnętrzne kanały informacyjne.</li><li>Użyj [analizy behawioralnej,](https://blogs.technet.microsoft.com/enterprisemobility/2016/06/30/ata-behavior-analysis-monitoring/) która stosuje znane wzorce, aby odkryć złośliwe zachowanie. </li><li>Użyj [wykrywania anomalii,](https://msdn.microsoft.com/library/azure/dn913096.aspx) które używa profilowania statystycznego do tworzenia historycznej linii bazowej.</li></ul> |
| [<br>Operacje deweloperskie (DevOps)](https://docs.microsoft.com/azure/architecture/checklist/dev-ops)|<ul><li>[Infrastruktura jako kod (IaC)](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) jest praktyką, która umożliwia automatyzację i walidację tworzenia i niszczenia sieci i maszyn wirtualnych, aby pomóc w dostarczaniu bezpiecznych, stabilnych platform hostingowych aplikacji.</li><li>[Ciągła integracja i wdrażanie](/visualstudio/containers/overview#continuous-delivery-and-continuous-integration-cicd) napędzają trwające scalanie i testowanie kodu, co prowadzi do wczesnego znajdowania wad. </li><li>[Zarządzanie wersjami](https://msdn.microsoft.com/library/vs/alm/release/overview) Zarządzaj zautomatyzowanymi wdrożeniami na każdym etapie potoku.</li><li>[Monitorowanie wydajności aplikacji](https://azure.microsoft.com/documentation/articles/app-insights-start-monitoring-app-health-usage/) uruchomionych aplikacji, w tym środowisk produkcyjnych dla kondycji aplikacji i użycia klientów, pomaga organizacjom tworzyć hipotezę i szybko sprawdzać lub obalać strategie.</li><li>Korzystając z [testów obciążenia & automatycznej skali,](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing) możemy znaleźć problemy z wydajnością w naszej aplikacji, aby poprawić jakość wdrażania i upewnić się, że nasza aplikacja jest zawsze dostępna lub dostępna w celu zaspokojenia potrzeb biznesowych.</li></ul> |


## <a name="conclusion"></a>Podsumowanie
Wiele organizacji pomyślnie wdrożyło i obsługiwało swoje aplikacje w chmurze na platformie Azure. Listy kontrolne pod warunkiem, podkreślają kilka list kontrolnych, które są niezbędne i pomagają zwiększyć prawdopodobieństwo udanych wdrożeń i operacji bez frustracji. Zdecydowanie zalecamy te kwestie operacyjne i strategiczne dotyczące istniejących i nowych wdrożeń aplikacji na platformie Azure.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zabezpieczeniach, zobacz następujące artykuły:

- [Projektowanie i bezpieczeństwo operacyjne](https://www.microsoft.com/trustcenter/security/designopsecurity).
- [Planowanie i operacje usługi Azure Security Center](../../security-center/security-center-planning-and-operations-guide.md).
