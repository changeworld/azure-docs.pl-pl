---
title: Bezpieczeństwo i szyfrowanie danych najlepsze praktyki — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepsze rozwiązania dotyczące bezpieczeństwa danych i szyfrowanie za pomocą wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/06/2019
ms.author: terrylan
ms.openlocfilehash: 9955450b468ef38ba456d7ee73d9681de677494d
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65190704"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Bezpieczeństwo i szyfrowanie danych platformy Azure, najlepsze rozwiązania
W tym artykule opisano najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania danych.

Najlepsze rozwiązania są oparte na konsensus opinii i pracować z aktualnymi możliwościami platformy Azure, a zestawy funkcji. Opinie i technologii zmieniają się wraz z upływem czasu, a w tym artykule jest aktualizowana w regularnych odstępach czasu, aby odzwierciedlać wprowadzone zmiany.

## <a name="protect-data"></a>Ochrona danych
Aby ułatwić ochronę danych w chmurze, musisz uwzględnić możliwe stany, w którym dane mogą wystąpić i kontrolki są dostępne dla tego stanu. Najlepsze rozwiązania dotyczące zabezpieczeń i szyfrowania danych platformy Azure odnoszą się do następujących stanów danych:

- Magazynowane: Obejmuje to wszystkie informacje obiektów magazynu, kontenerów i typy, statycznie występujących na nośniku fizycznym, czy magnetyczne lub dysków optycznych.
- Podczas przesyłania: Dane są przesyłane między składnikami, lokalizacji lub programów, jest w drodze. Przykłady to transfer za pośrednictwem sieci, między usługi Service bus (ze środowiska lokalnego do chmury i na odwrót, w tym połączenia hybrydowe, takie jak usługi ExpressRoute), lub w trakcie procesu wejścia/wyjścia.

## <a name="choose-a-key-management-solution"></a>Wybieranie rozwiązania do zarządzania kluczami

Ochrona kluczy jest podstawą ochrony danych w chmurze.

[Usługa Azure Key Vault](../key-vault/key-vault-overview.md) pomaga chronić klucze kryptograficzne i używanie wpisów tajnych, które aplikacje i usługi w chmurze. Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą tworzyć klucze na potrzeby tworzenia i testowania w ciągu kilku minut i przeprowadzić ich migrację do kluczy produkcyjnych. W razie potrzeby administratorzy zabezpieczeń mogą przydzielić (i cofnąć) uprawnienia do używania kluczy.

Usługa Key Vault umożliwia utworzenie wielu zabezpieczonych kontenerów nazywanych magazynami. Te magazyny są wspierane przez sprzętowe moduły zabezpieczeń. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny kluczy również kontrolować i rejestrowanie dostępu do żadnego elementu są w nich przechowywane. Usługa Azure Key Vault obsługuje żądania i odnawianie certyfikatów zabezpieczeń TLS (Transport Layer). Oferuje ona funkcje niezawodne rozwiązanie do zarządzania cyklem życia certyfikatu.

Usługa Azure Key Vault jest przeznaczony do obsługi aplikacji kluczy i wpisów tajnych. Key Vault nie jest przeznaczony do magazynu haseł użytkowników.

Poniżej przedstawiono najlepsze rozwiązania w zakresie zabezpieczeń za pomocą usługi Key Vault.

**Najlepsze rozwiązanie**: Udzielanie dostępu do użytkowników, grup i aplikacji w konkretnym zakresie.   
**Szczegóły**: Użyj wstępnie zdefiniowanych ról RBAC firmy. Na przykład, aby udzielić dostępu użytkownika do zarządzania magazynami kluczy, możesz przypisać wstępnie zdefiniowaną rolę [Współautor magazynu klucz](../role-based-access-control/built-in-roles.md) do tego użytkownika w określonym zakresie. Zakres w tym przypadku będzie subskrypcją, grupą zasobów lub po prostu określonego magazynu kluczy. Jeśli wstępnie zdefiniowane role nie odpowiadają potrzebom, możesz to zrobić [zdefiniować własne role](../role-based-access-control/custom-roles.md).

**Najlepsze rozwiązanie**: Co użytkownicy mają dostęp do formantu.   
**Szczegóły**: Dostęp do magazynu kluczy jest kontrolowany za pośrednictwem dwóch oddzielnych interfejsów: płaszczyzny zarządzania i płaszczyzny danych. Mechanizmy kontroli dostępu do płaszczyzny zarządzania i płaszczyzny danych działają niezależnie.

Użycie funkcji RBAC w celu kontrolowania tego, jakie użytkownicy mają dostęp do. Na przykład jeśli użytkownik chce udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, należy udzielić uprawnień dostępu do płaszczyzny danych za pomocą zasad dostępu magazynu kluczy i nie dostępu do płaszczyzny zarządzania jest wymagany dla tej aplikacji. Z drugiej strony Jeśli użytkownik powinien mieć możliwość odczytu właściwości magazynu i tagi, ale nie mają dostępu do kluczy, wpisów tajnych lub certyfikatów, to dostęp do odczytu użytkowników można przyznać za pośrednictwem RBAC i Brak dostępu do płaszczyzny danych jest wymagana.

**Najlepsze rozwiązanie**: Store certyfikaty w magazynie kluczy. Certyfikaty są o wysokiej wartości. W niepowołane ręce mogą zostać złamane zabezpieczenia swojej aplikacji lub bezpieczeństwo powierzonych jej danych.   
**Szczegóły**: Usługa Azure Resource Manager można bezpiecznie wdrożyć certyfikatów przechowywanych w usłudze Azure Key Vault do maszyn wirtualnych platformy Azure, gdy maszyny wirtualne są wdrażane. Ustawiając odpowiednie zasady dostępu dla usługi key vault, możesz również decydować, kto otrzymuje dostęp do swojego certyfikatu. Kolejną korzyścią jest to, które są zarządzane wszystkie certyfikaty w jednym miejscu w usłudze Azure Key Vault. Zobacz [wdrażanie certyfikatów na maszynach wirtualnych z zarządzanego przez klienta usługi Key Vault](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) Aby uzyskać więcej informacji.

**Najlepsze rozwiązanie**: Upewnij się, można odzyskać usuwanie magazynów kluczy lub obiektów magazynu kluczy.   
**Szczegóły**: Usuwanie klucza magazyny lub obiektów magazynu kluczy może być przypadkowego lub celowego. Włącz usuwanie nietrwałe i czyszczenie funkcje ochrony usługi Key Vault, szczególnie w przypadku kluczy, które są używane do szyfrowania danych magazynowanych. Usunięcie tych kluczy jest równoznaczne z utratą danych, aby można było odzyskać usuniętych magazynów i magazynu obiektów, jeśli to konieczne. Ćwicz operacje odzyskiwania usługi Key Vault w regularnych odstępach czasu.

> [!NOTE]
> Jeśli użytkownik ma uprawnienia współautora (RBAC) do płaszczyzny zarządzania magazynu kluczy, ich może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Zaleca się, że należy ściśle kontrolować kto ma dostęp współautora do magazynów kluczy, aby zapewnić tylko upoważnione osoby mogą uzyskać dostęp i Zarządzanie magazynami kluczy, kluczy, wpisów tajnych i certyfikatów.
>
>

## <a name="manage-with-secure-workstations"></a>Zarządzanie przy użyciu bezpiecznego stacji roboczych

> [!NOTE]
> Administrator subskrypcji lub właściciela, należy używać bezpiecznego dostępu do stacji roboczej lub na stacji roboczej z dostępem uprzywilejowanym.
>
>

Ponieważ większość ataków skierować użytkownika końcowego, staje się punkt końcowy, jednego z punktów głównej ataku. Osoba atakująca, która obniża punkt końcowy, można użyć poświadczeń użytkownika do uzyskania dostępu do danych w organizacji. Większość ataków na punkt końcowy zalet fakt, że użytkownicy są administratorami w swoich lokalnych stacji roboczych.

**Najlepsze rozwiązanie**: Użyj bezpiecznego zarządzania stacji roboczej, aby chronić wrażliwych kont, zadań i danych.   
**Szczegóły**: Użyj [stacji roboczej z dostępem uprzywilejowanym](https://technet.microsoft.com/library/mt634654.aspx) Aby zmniejszyć obszar narażony na ataki w stacji roboczych. Te stacje robocze bezpiecznego zarządzania może pomóc Ci rozwiązać niektóre z tych ataków i upewnij się, że Twoje dane są bezpieczniejsze.

**Najlepsze rozwiązanie**: Upewnij się, program endpoint protection.   
**Szczegóły**: Wymuszanie zasad zabezpieczeń na wszystkich urządzeniach, które są używane do pracy z danymi niezależnie od lokalizacji danych (w chmurze lub lokalnie).

## <a name="protect-data-at-rest"></a>Ochrona magazynowanych danych

[Szyfrowanie danych magazynowanych](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) stanowi krok obowiązkowy kierunku niezależność danych ochrony prywatności, zgodności i danych.

**Najlepsze rozwiązanie**: Zastosuj szyfrowanie dysków, które pomagają w ochronie danych.   
**Szczegóły**: Użyj [usługa Azure Disk Encryption](azure-security-disk-encryption.md). Umożliwia administratorom IT szyfrowanie dysków Windows i maszyn wirtualnych IaaS z systemem Linux. Szyfrowanie dysków łączy standardowych funkcji Windows BitLocker oraz funkcji dm-crypt systemu Linux, aby zapewnić szyfrowanie woluminów systemu operacyjnego i dysków z danymi.

Usługi Azure Storage i Azure SQL Database jako opcja szyfrowania przechowywanych danych domyślnie i wiele szyfrowania oferty usługi. Aby zachować kontrolę nad kluczami, które dostępu i szyfrowanie danych, można użyć usługi Azure Key Vault. Zobacz [obsługi modelu szyfrowania dostawcy zasobów platformy Azure, aby dowiedzieć się więcej](azure-security-encryption-atrest.md#azure-resource-providers-encryption-model-support).

**Najlepsze praktyki**: Aby zmniejszyć ryzyko związane z nieautoryzowany dostęp do danych, należy używać szyfrowania.   
**Szczegóły**: Szyfrowanie dysków, przed przystąpieniem do napisania danych poufnych do nich.

Organizacje, które nie wymuszają szyfrowanie danych są bardziej widoczne kwestii poufności danych. Na przykład nieautoryzowani lub nieautoryzowane elementy użytkownicy mogą kradzieży danych na kontach których bezpieczeństwo zostało naruszone lub uzyskania nieautoryzowanego dostępu do danych zakodowane w formacie zwykłego. Firm również Musisz udowodnić, że są one skrupulatne poprawianie listy i korzystać z funkcji poprawne zabezpieczenia, aby zwiększyć ich bezpieczeństwo danych w celu zachowania zgodności z przepisami w branży.

## <a name="protect-data-in-transit"></a>Ochrona przenoszonych danych

Ochrona przenoszonych danych powinien być integralną część strategii ochrony danych. Ponieważ dane są przenoszone i z powrotem w wielu lokalizacjach, ogólnie zalecamy zawsze używają protokołów SSL/TLS do wymiany danych w różnych lokalizacjach. W niektórych przypadkach możesz chcieć izolowania kanał całej komunikacji między lokalnych i w chmurze infrastruktury przy użyciu sieci VPN.

W przypadku danych przenoszenia między sieci na infrastrukturę lokalną i platformą Azure należy wziąć pod uwagę odpowiednie zabezpieczenia, takie jak HTTPS lub sieci VPN. Wysyłając zaszyfrowany ruch sieciowy między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznej sieci internet, użyj [bramy Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/).

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z bramy Azure VPN Gateway, SSL/TLS i protokół HTTPS.

**Najlepsze rozwiązanie**: Bezpieczny dostęp z wielu stacjach roboczych znajdujących się na lokalnych z siecią wirtualną platformy Azure.   
**Szczegóły**: Użyj [sieci VPN typu lokacja lokacja](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

**Najlepsze rozwiązanie**: Bezpieczny dostęp z poszczególnych stacji roboczych znajdujących się na lokalnych z siecią wirtualną platformy Azure.   
**Szczegóły**: Użyj [sieci VPN typu punkt lokacja](../vpn-gateway/vpn-gateway-point-to-site-create.md).

**Najlepsze rozwiązanie**: Przenieś większych zestawów danych za pośrednictwem dedykowanej o dużej szybkości łącza sieci WAN.   
**Szczegóły**: Użyj [ExpressRoute](../expressroute/expressroute-introduction.md). Jeśli zdecydujesz się używać usługi ExpressRoute, można również zaszyfrować dane na poziomie aplikacji, za pomocą [SSL/TLS](https://support.microsoft.com/kb/257591) lub innych protokołów w celu zapewnienia dodatkowej ochrony.

**Najlepsze rozwiązanie**: Korzystaj z usługi Azure Storage w witrynie Azure portal.   
**Szczegóły**: Wszystkie wykonywane transakcje za pośrednictwem protokołu HTTPS. Można również użyć [interfejsu API REST magazynu](https://msdn.microsoft.com/library/azure/dd179355.aspx) za pośrednictwem protokołu HTTPS do interakcji z [usługi Azure Storage](https://azure.microsoft.com/services/storage/).

Organizacje, które się nie powieść, aby chronić przesyłane dane są bardziej podatne na [ataków typu man-in--middle](https://technet.microsoft.com/library/gg195821.aspx), [podsłuchiwaniu](https://technet.microsoft.com/library/gg195641.aspx)i przejęcie kontroli sesji. Te ataki może być pierwszym działaniem wykonywanym w uzyskiwaniu dostępu do poufnych danych.

## <a name="secure-email-documents-and-sensitive-data"></a>Zabezpieczanie poczty e-mail, dokumenty i poufne dane

Chcesz kontrolować i zabezpieczanie poczty e-mail, dokumenty i dane poufne, które są udostępniane poza firmę. [Usługa Azure Information Protection](https://docs.microsoft.com/azure/information-protection/) to rozwiązanie oparte na chmurze ułatwia klasyfikowanie, etykietowanie i ochronę dokumentów oraz wiadomości e-mail. Można to zrobić automatycznie przez administratorów definiujących reguły i warunki, ręcznie przez użytkowników lub kombinacji, w której użytkownicy uzyskują zalecenia.

Klasyfikacja jest zawsze rozpoznać cały czas, niezależnie od tego, gdzie dane są przechowywane lub komu zostały udostępnione. Etykiety zawierają oznaczenia wizualne, takie jak nagłówek, stopka lub znak wodny. Metadane dodawane do plików i nagłówków wiadomości e-mail mają postać zwykłego tekstu. Zwykłego tekstu zapewnia, że inne usługi, takie jak rozwiązania zapobiegania utracie danych, mogą rozpoznać klasyfikację i podjąć odpowiednie działania.

Technologia ochrony korzysta z usługi Azure Rights Management (Azure RMS). Technologia ta jest zintegrowana z innymi usługami chmurowymi firmy Microsoft i aplikacji, takich jak usługi Office 365 i Azure Active Directory. Ta technologia ochrony używa zasad szyfrowania, tożsamości i autoryzacji. Ochrona stosowana przy użyciu usługi Azure RMS pozostaje z dokumentami i wiadomościami e-mail niezależnie od lokalizacji — wewnątrz lub na zewnątrz organizacji, sieci, serwerów plików i aplikacji.

To rozwiązanie ochrony informacji zapewnia kontrolę nad danymi nawet wtedy, gdy są one udostępniane innym osobom. Umożliwia także usługę Azure RMS za pomocą własnych line-of-business, aplikacji i rozwiązań do ochrony informacji od dostawców oprogramowania, czy tych aplikacji i rozwiązań hostowanych lokalnie lub w chmurze.

Zaleca się, możesz:

- [Wdrażanie usługi Azure Information Protection](https://docs.microsoft.com/azure/information-protection/deployment-roadmap) dla Twojej organizacji.
- Zastosowanie etykiet, które odzwierciedlają Twoje wymagania biznesowe. Na przykład: Zastosuj etykietę o nazwie "wysoce poufne" dla wszystkich dokumentów i wiadomości e-mail, które zawierają dane top-secret, klasyfikować i chronić te dane. Następnie tylko autoryzowani użytkownicy mogą uzyskać dostęp do tych danych przy zachowaniu dowolnych ograniczeń, które określisz.
- Konfigurowanie [rejestrowania użycia usługi Azure RMS](https://docs.microsoft.com/azure/information-protection/log-analyze-usage) tak, aby monitorować, jak Twoja organizacja używa usługi ochrony.

Organizacje, które będą słabe na [klasyfikacji danych](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrona plików może być bardziej podatne na danych wyciekom lub nieprawidłowemu użyciu danych. Za pomocą odpowiedniego pliku ochrony można analizować przepływów danych, aby uzyskać wgląd w firmie, wykrywać niebezpieczne zachowania i podejmować działania naprawcze, śledzić dostęp do dokumentów i tak dalej.

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zabezpieczeń platformy Azure najlepsze rozwiązania i wzorce](security-best-practices-and-patterns.md) dla więcej najważniejsze wskazówki dotyczące zabezpieczeń do użycia podczas one projektowanie, wdrażanie i zarządzanie rozwiązań w chmurze, korzystając z platformy Azure.

Do dyspozycji więcej ogólnych informacji na temat zabezpieczeń platformy Azure i powiązane usługi firmy Microsoft są następujące zasoby:
* [Blog zespołu usługi Azure Security](https://blogs.msdn.microsoft.com/azuresecurity/) — aktualne instrukcje dotyczące najnowszych zabezpieczeń platformy Azure
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — w przypadku, gdy luk w zabezpieczeniach firmy Microsoft, w tym problemów z platformą Azure, mogą zostać zgłoszone lub za pośrednictwem poczty e-mail do secure@microsoft.com
