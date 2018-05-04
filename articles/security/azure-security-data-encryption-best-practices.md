---
title: Najważniejsze wskazówki dotyczące bezpieczeństwa danych i szyfrowania | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące bezpieczeństwa danych i szyfrowania za pomocą wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbalwin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/26/2018
ms.author: barclayn
ms.openlocfilehash: 574ca8a68bf6e532331a4b6f1106e472c8ab0449
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Dobre praktyki dotyczące zabezpieczeń danych platformy Azure i szyfrowania

Jeden z kluczy do ochrony danych w chmurze jest księgowanie możliwe stany, w których może wystąpić danych oraz kontrolki są dostępne dla tego stanu. Danych Azure na potrzeby szyfrowania najlepsze praktyki dotyczące zabezpieczeń i zalecenia będą wokół stanów następujące dane:

* W pozostałych: W tym wszystkie informacje, które typy, statycznie występujących na nośnik fizyczny, kontenerów i obiektów magazynu można go magnetyczne lub optyczne dysku.
* Podczas przesyłania: Podczas transferu danych między składnikami, lokalizacji lub programy, takie jak przez sieć, przez usługi service bus (z lokalnymi do chmury i odwrotnie, łącznie z połączeń hybrydowych, takie jak ExpressRoute) lub w trakcie operacji wejścia/wyjścia on jest traktować jako w ruchu.

W tym artykule omówiono kolekcji danych Azure zabezpieczeń i szyfrowania najlepszych rozwiązań. Następujące najlepsze rozwiązania są uzyskiwane z wiemy z doświadczenia z zabezpieczeniami danych Azure i szyfrowania i doświadczenia klientów, takie jak samodzielnie.

Dla każdego ze względów wyjaśniamy:

* Co to jest najlepszym rozwiązaniem
* Dlaczego chcesz włączyć tej najlepsze praktyki
* Jeśli nie zostanie włączone najlepszym rozwiązaniem, co może być skutkiem
* Możliwe alternatywy najlepsze praktyki
* Jak można znaleźć umożliwiające najlepsze praktyki

W tym artykule Azure bezpieczeństwo danych i najlepsze rozwiązania w zakresie szyfrowania jest na podstawie opinii konsensu i funkcji platformy Azure i zestawy funkcji istniejących w chwili ten artykuł dotyczy. Opinie i technologie ulegną zmianom, a ten artykuł będzie aktualizowany na bieżąco w celu odzwierciedlenia tych zmian.

Danych Azure szyfrowania najlepsze praktyki dotyczące zabezpieczeń i omówione w tym artykule obejmują:

* Wymusić uwierzytelnianie wieloskładnikowe
* Kontrola dostępu (RBAC) oparta na rolach użycia
* Szyfrowanie maszyn wirtualnych platformy Azure
* Używanie modeli zabezpieczeń sprzętu
* Zarządzanie za pomocą bezpiecznego stacji roboczych
* Włącz szyfrowanie danych SQL
* Ochrona danych podczas przesyłania
* Wymuszanie szyfrowania danych na poziomie plików

## <a name="enforce-multi-factor-authentication"></a>Wymusić uwierzytelnianie wieloskładnikowe

Pierwszym krokiem podczas dostępu do danych i sterowania w programie Microsoft Azure jest do uwierzytelnienia użytkownika. [Usługa Azure Multi-Factor Authentication (MFA)](../active-directory/authentication/multi-factor-authentication.md) jest metodą weryfikacji tożsamości użytkownika przy użyciu innej metody niż tylko nazwę użytkownika i hasło. Tego uwierzytelniania — metoda ułatwia zabezpieczenie dostępu do danych i aplikacji, spełniając zapotrzebowanie na prosty proces logowania.

Przez włączenie usługi Azure MFA dla użytkowników, dodajesz drugą warstwę zabezpieczeń do logowania użytkowników i transakcji. W takim przypadku transakcji może uzyskiwać dostęp do dokumentu znajdującego się na serwerze plików lub w trybie Online programu SharePoint. Usługa Azure MFA ułatwia również IT, aby zmniejszyć prawdopodobieństwo, że przejęciem poświadczeń dostępu do danych organizacji.

Na przykład: wymusić uwierzytelnianie wieloskładnikowe Azure dla użytkowników i skonfigurować go do użycia jako weryfikacji, połączenia telefonicznego lub wiadomości tekstowej w przypadku złamania zabezpieczeń poświadczeń użytkownika, atakujący będzie mógł dostęp do dowolnych zasobów, ponieważ on nie ma dostępu do telefonu użytkownika. Organizacje, które nie dodawaj tego dodatkową warstwę ochrony tożsamości są bardziej narażony na atak kradzieży poświadczeń, co może prowadzić do naruszenia danych.

Jeden alternatywą dla organizacji, które mają być zachowane uwierzytelniania sterowania lokalnej jest użycie [serwera usługi Azure Multi-Factor Authentication](../active-directory/authentication/howto-mfaserver-deploy.md), nazywany również lokalne usługi MFA. Za pomocą tej metody nadal będzie mógł wymusić uwierzytelnianie wieloskładnikowe, przy zachowaniu MFA serwera lokalnego.

Aby uzyskać więcej informacji dotyczących usługi Azure MFA, przeczytaj artykuł [wprowadzenie do korzystania z usługi Azure Multi-Factor Authentication w chmurze](../active-directory/authentication/howto-mfa-getstarted.md).

## <a name="use-role-based-access-control-rbac"></a>Kontrola dostępu (RBAC) oparta na rolach użycia

Ograniczanie dostępu na podstawie [, trzeba znać](https://en.wikipedia.org/wiki/Need_to_know) i [najniższych uprawnień](https://en.wikipedia.org/wiki/Principle_of_least_privilege) zasad zabezpieczeń. Jest to konieczne w przypadku organizacji, które mają być wymuszać zasady zabezpieczeń dla dostępu do danych. Azure opartej na rolach kontroli dostępu (RBAC) może służyć do przypisywania uprawnień do użytkowników, grup i aplikacji w określonego zakresu. Zakres przypisania roli może być pojedynczego zasobu, grupy zasobów lub subskrypcji.

Można wykorzystać [wbudowane role RBAC](../role-based-access-control/built-in-roles.md) na platformie Azure, aby przypisać uprawnienia do użytkowników. Należy rozważyć użycie *współautora konta magazynu* dla operatorów chmury, które muszą zarządzać kontami magazynu i *klasycznego współautora konta magazynu* roli do zarządzania klasycznych kont magazynu. Operatorzy chmury, które musi zarządzać maszynami wirtualnymi i konto magazynu, należy rozważyć dodanie ich do *Współautor·maszyny·wirtualnej* roli.

Organizacje, które nie wymusić kontrolę dostępu danych dzięki wykorzystaniu możliwości, takie jak RBAC może nadanie więcej uprawnień niż jest to niezbędne dla użytkowników. Może to prowadzić do naruszenia danych dzięki użyciu niektórzy użytkownicy mieli dostęp do danych, które nie powinny mieć w pierwszej kolejności.

Użytkownik może dowiedzieć się więcej o Azure RBAC przeczytaj artykuł [kontroli dostępu](../role-based-access-control/role-assignments-portal.md).

## <a name="encrypt-azure-virtual-machines"></a>Szyfrowanie maszyn wirtualnych platformy Azure

W przypadku wielu organizacji [szyfrowanie danych magazynowanych](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) jest obowiązkowy krok na drodze suwerenności dane, zgodności i ochrony prywatności danych. Szyfrowanie dysków Azure umożliwia administratorom IT szyfrowanie dysków systemu Windows i Linux IaaS maszyn wirtualnych (VM). Szyfrowanie dysków Azure korzysta z branży funkcje BitLocker standardowych systemu Windows i DM-Crypt systemu Linux w celu zapewnienia szyfrowania woluminów systemu operacyjnego i dysków z danymi.

Można korzystać z szyfrowania dysków Azure, aby chronić i ochrony danych w celu spełnienia zabezpieczeń organizacji i wymagań dotyczących zgodności. Organizacje powinny również należy wziąć pod uwagę przy użyciu szyfrowania w celu zmniejszenia ryzyka danych związanych z nieautoryzowanego dostępu. Zalecane jest również, szyfrowanie dysków przed zapisywania poufnych danych.

Upewnij się, że szyfrowania woluminów danych maszyny Wirtualnej i wolumin rozruchowy, aby chronić dane przechowywane na koncie magazynu Azure. Ochrona kluczy szyfrowania i klucze tajne dzięki wykorzystaniu [usługi Azure Key Vault](../key-vault/key-vault-whatis.md).

Dla lokalnych serwerów systemu Windows należy wziąć pod uwagę następujące szyfrowania najlepsze rozwiązania:

* Użyj [funkcji BitLocker](https://technet.microsoft.com/library/dn306081.aspx) do szyfrowania danych
* Przechowuje informacje dotyczące odzyskiwania w usługach AD DS.
* Jeśli ma żadnych problemu, że klucze funkcji BitLocker zostały naruszone, zaleca się albo sformatować dysk, aby usunąć wszystkie wystąpienia metadanych funkcji BitLocker z dysku lub odszyfrować, a następnie ponownie szyfrować cały dysk.

Organizacje, które nie wymusić szyfrowanie danych więcej mogą być narażone na problemy z integralnością danych, takich jak użytkownicy złośliwego lub nieautoryzowane kradzieży danych i naruszenia zabezpieczeń kont uzyskujących nieautoryzowany dostęp do danych w formacie zwykłego. Oprócz tych zagrożeń firm, które muszą być zgodne z przepisów branżowych dowieść większego i ich za pomocą formantów poprawnych zabezpieczeń w celu zwiększenia bezpieczeństwa danych.

Użytkownik może dowiedzieć się więcej o szyfrowania dysków Azure od przeczytania artykułu [Azure dysku szyfrowanie dla systemu Windows i maszyn wirtualnych systemu Linux IaaS](azure-security-disk-encryption.md).

## <a name="use-hardware-security-modules"></a>Użyj sprzętowych modułów zabezpieczeń
Rozwiązania szyfrowania zgodne ze standardami używać kluczy tajnych do szyfrowania danych. W związku z tym jest krytyczny, że te klucze są bezpiecznie przechowywane. Zarządzanie kluczami staje się integralną częścią ochrony danych, ponieważ będzie można użyć do przechowywania kluczy tajnych, które są używane do szyfrowania danych.

Używa szyfrowania dysków Azure [usługi Azure Key Vault](https://azure.microsoft.com/services/key-vault/) ułatwiają kontrolowanie i zarządzanie nimi klucze szyfrowania dysku i kluczy tajnych w ramach subskrypcji magazynu kluczy, podczas gdy zapewnienie, że wszystkie dane na dyskach maszyny wirtualnej są szyfrowane, gdy w magazynie Azure. Inspekcja kluczy i użycia zasad należy używać usługi Azure Key Vault.

Istnieje wiele ryzyka związanego z używaniem związanych z nie posiadają odpowiednie środki zabezpieczające w celu ochrony kluczy tajnych, które były używane do szyfrowania danych. Jeśli atakujący ma dostęp do kluczy tajnych, będzie mógł odszyfrować dane i mogących mieć dostęp do poufnych informacji.

Użytkownik może dowiedzieć się więcej o ogólne zalecenia dotyczące zarządzania certyfikatami w usłudze Azure od przeczytania artykułu [zarządzania certyfikatami w usłudze Azure: porady](https://blogs.msdn.microsoft.com/azuresecurity/2015/07/13/certificate-management-in-azure-dos-and-donts/).

Aby uzyskać więcej informacji na temat usługi Azure Key Vault, przeczytaj [wprowadzenie do usługi Azure Key Vault](../key-vault/key-vault-get-started.md).

## <a name="manage-with-secure-workstations"></a>Zarządzanie za pomocą bezpiecznego stacji roboczych
Ponieważ większość ataków skierować użytkownika końcowego, staje się punkt końcowy, jednego z punktów głównej ataku. Jeśli osoba atakująca obniża punktu końcowego, on korzystać z poświadczeń użytkownika w celu uzyskania dostępu do danych organizacji. Większość ataków na punkt końcowy będą mogli korzystać z faktu, że użytkownicy końcowi są administratorami w swoich lokalnych stacji roboczych.

Aby zmniejszyć to ryzyko, należy za pomocą bezpiecznego zarządzania stacji roboczej. Firma Microsoft zaleca użycie [uprzywilejowanego dostępu do stacji roboczych (ŁAPY)](https://technet.microsoft.com/library/mt634654.aspx) Aby zmniejszyć obszar ataków w stacji roboczych. Te stacje robocze bezpiecznego zarządzania mogą pomóc ograniczyć niektóre z nich ataków zapewnienia Twoje dane są bezpieczniejsze. Upewnij się, że umożliwia ŁAPY ograniczenia funkcjonalności i zablokować stację roboczą. Jest to ważny krok, aby zapewnić zapewnienia wysokiego poziomu zabezpieczeń dla kont poufnych, zadania i ochrony danych.

Brak programu endpoint protection może zagrożenie danych, upewnij się, że wymuszać zasady zabezpieczeń dla wszystkich urządzeń, które są używane do pracy z danymi, bez względu na lokalizację danych (chmurze lub lokalnie).

Dowiedz się więcej o uprawnieniach dostępu stacji roboczej od przeczytania artykułu [zabezpieczanie uprzywilejowanego dostępu](https://technet.microsoft.com/library/mt631194.aspx).

## <a name="enable-sql-data-encryption"></a>Włącz szyfrowanie danych SQL
[Azure SQL Database przezroczystego szyfrowania danych](https://msdn.microsoft.com/library/dn948096.aspx) (funkcji TDE) chroni przed zagrożeniem złośliwych działań, wykonując w czasie rzeczywistym szyfrowanie i odszyfrowywanie bazy danych, skojarzonych kopii zapasowych, i pliki dziennika transakcji w stanie spoczynku bez konieczności wprowadzania zmian w aplikacji.  Funkcji TDE szyfruje magazyn całej bazy danych przy użyciu klucza symetrycznego o nazwie klucza szyfrowania bazy danych.

Nawet wtedy, gdy cały magazyn jest zaszyfrowany, bardzo ważne jest także szyfrowanie Twojej bazy danych. Jest to implementacja obrony w głębokość podejście do ochrony danych. Jeśli używasz [bazy danych SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx) i chcesz chronić poufne dane, takie jak karty kredytowej lub numerów ubezpieczenia społecznego, można zaszyfrować baz danych z trybem FIPS 140-2 szyfrowania AES zweryfikowanych 256-bitową, który spełnia wymagania wiele standardy branżowe (np. HIPAA, PCI).

Ważne jest, aby zrozumieć, że pliki związane z [rozszerzenia puli bufora](https://msdn.microsoft.com/library/dn133176.aspx) (BPE) nie są szyfrowane, gdy baza danych jest szyfrowana przy użyciu funkcji TDE. Należy użyć narzędzia szyfrowania na poziomie systemu plików, takich jak BitLocker lub [systemu szyfrowania plików](https://technet.microsoft.com/library/cc700811.aspx) (EFS) dla BPE powiązane pliki.

Od autoryzowanym użytkownikiem, takie jak administrator zabezpieczeń lub administrator bazy danych ma dostęp do danych, nawet jeśli bazy danych jest szyfrowany przy funkcji TDE, należy również postępować zgodnie z poniższymi zaleceniami:

* Uwierzytelnianie na poziomie bazy danych programu SQL
* Uwierzytelniania systemu Azure AD przy użyciu role RBAC
* Użytkownicy i aplikacje powinny używać osobnych kont do uwierzytelniania. W ten sposób można ograniczyć uprawnień użytkowników i aplikacje i zmniejszyć ryzyko złośliwych działań
* Implementowanie zabezpieczeń na poziomie bazy danych przy użyciu ról stałej bazy danych (takich jak db_datareader lub db_datawriter), lub można utworzyć niestandardowe role w aplikacji można przyznać uprawnienia jawne do zaznaczonych obiektów bazy danych

Organizacje, które nie używają szyfrowania na poziomie bazy danych może być bardziej narażony na ataki, które mogą negatywnie wpłynąć na danych znajdujących się w bazach danych SQL.

Użytkownik może dowiedzieć się więcej o funkcji SQL TDE szyfrowania przeczytaj artykuł [przezroczystego szyfrowania danych z bazy danych SQL Azure](https://msdn.microsoft.com/library/0bf7e8ff-1416-4923-9c4c-49341e208c62.aspx).

## <a name="protect-data-in-transit"></a>Ochrona danych podczas przesyłania

Ochrona danych podczas przesyłania powinien być integralną część strategii ochrony danych. Ponieważ dane będą przenoszone i z powrotem w wielu lokalizacjach, ogólne zalecenie jest zawsze używają protokołów SSL/TLS do wymiany danych w różnych lokalizacjach. W niektórych sytuacjach można odizolować kanału całej komunikacji między lokalnymi i w chmurze infrastruktury przy użyciu wirtualnej sieci prywatnej (VPN).

Przenoszenie między lokalną infrastrukturą i Azure danych należy rozważyć odpowiednie zabezpieczenia, takie jak HTTPS lub sieci VPN.

Dla organizacji, które trzeba bezpieczny dostęp z wielu stacji roboczych lokalnego do platformy Azure, użyj [Azure VPN lokacja lokacja](../vpn-gateway/vpn-gateway-site-to-site-create.md).

Dla organizacji, które trzeba bezpieczny dostęp z jednej stacji roboczej znajdujących się lokalnie do platformy Azure, użyj [punkt-lokacja sieci VPN](../vpn-gateway/vpn-gateway-point-to-site-create.md).

Większych zestawów danych można przenieść za pośrednictwem dedykowanej o dużej szybkości łącza sieci WAN takich jak [ExpressRoute](https://azure.microsoft.com/services/expressroute/). Jeśli chcesz używać usługi ExpressRoute, można także szyfrowanie danych na poziomie aplikacji przy użyciu [SSL/TLS](https://support.microsoft.com/kb/257591) lub innymi protokołami zapewnia dodatkową ochronę.

Jeśli użytkownik korzysta z usługi Azure Storage za pośrednictwem portalu Azure, wszystkich transakcji jest realizowana za pośrednictwem protokołu HTTPS. [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) over HTTPS można również wchodzić w interakcje z [usługi Azure Storage](https://azure.microsoft.com/services/storage/) i [bazy danych SQL Azure](https://azure.microsoft.com/services/sql-database/).

Organizacje, które się nie powieść, aby chronić przesyłane dane są bardziej narażony na [ataków man-in--middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwaniu](https://technet.microsoft.com/library/gg195641.aspx) i przejęcie kontroli sesji. Tego rodzaju ataki może być pierwszym krokiem w uzyskiwaniu dostępu do poufnych danych.

Możesz można dowiedzieć się więcej o sieci VPN platformy Azure opcja przeczytaj artykuł [planowania i projektowania dla bramy sieci VPN](../vpn-gateway/vpn-gateway-plan-design.md).

## <a name="enforce-file-level-data-encryption"></a>Wymuszanie szyfrowania danych na poziomie plików

Kolejną warstwę ochrony, która może zwiększyć poziom zabezpieczeń dla danych jest szyfrowanie pliku, bez względu na lokalizację pliku.

[Usługa Azure RMS](https://technet.microsoft.com/library/jj585026.aspx) używa zasad szyfrowania, tożsamości i autoryzacji, aby ułatwić zabezpieczanie plików i wiadomości e-mail. Usługa Azure RMS działa na wielu urządzeniach — telefonach, tablety i komputery, aby chronić w obrębie organizacji i poza organizacją. Ta funkcja jest możliwa, ponieważ usługa Azure RMS dodaje poziom ochrony, która jest powiązana z danymi nawet wtedy, gdy opuszczą teren organizacji.

Jeśli używasz usługi Azure RMS do ochrony plików za pomocą branżowego standardu kryptografii pełnej obsługi [FIPS 140-2](http://csrc.nist.gov/groups/STM/cmvp/standards.html). Podczas ochrony danych należy korzystać z usługi Azure RMS, masz gwarantują, że ta ochrona pozostanie z plikiem, nawet jeśli zostanie skopiowany do magazynu, które nie są pod kontrolą IT, takich jak Usługa magazynu w chmurze. Taki sam występuje w przypadku plików udostępnianych za pośrednictwem poczty e-mail, plik jest chroniony jako załącznik do wiadomości e-mail z instrukcjami sposobu otwierania chronionego załącznika.

Podczas planowania wdrożenia usługi Azure RMS firma Microsoft zaleca następujące czynności:

* Zainstaluj [aplikacji RMS sharing](https://technet.microsoft.com/library/dn339006.aspx). Ta aplikacja jest zintegrowany z pakietu Office aplikacji przez zainstalowanie pakietu Office dodatek tak, aby użytkownikom ochronę plików bezpośrednio.
* Skonfiguruj aplikacje i usługi obsługują usługę Azure RMS
* Utwórz [szablonów niestandardowych](https://technet.microsoft.com/library/dn642472.aspx) która odzwierciedla wymagania firmy. Na przykład: szablon do górnej poufne dane, które powinny być stosowane w wszystkich ściśle tajne związane z wiadomości e-mail.

Organizacje, które są słabe na [klasyfikacji danych](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrona plików może być bardziej narażony na wycieku danych. Bez ochrony prawidłowego pliku organizacji nie będzie można uzyskiwać cenne informacje biznesowe, monitorować nadużycia i uniemożliwić nieautoryzowanym dostępem do plików.

Dodatkowe informacje na temat usługi Azure RMS od przeczytania artykułu [wprowadzenie do korzystania z usługi Azure Rights Management](https://technet.microsoft.com/library/jj585016.aspx).
