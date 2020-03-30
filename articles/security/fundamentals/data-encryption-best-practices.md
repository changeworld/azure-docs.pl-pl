---
title: Najważniejsze wskazówki dotyczące bezpieczeństwa i szyfrowania danych — Microsoft Azure
description: Ten artykuł zawiera zestaw najlepszych rozwiązań dotyczących zabezpieczeń i szyfrowania danych przy użyciu wbudowanych funkcji platformy Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: TomSh
ms.assetid: 17ba67ad-e5cd-4a8f-b435-5218df753ca4
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/09/2020
ms.author: terrylan
ms.openlocfilehash: c5bf62f434b2095f7200b5562c38c252a0195c5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243499"
---
# <a name="azure-data-security-and-encryption-best-practices"></a>Najlepsze rozwiązania z zakresu zabezpieczeń i szyfrowania danych platformy Azure
W tym artykule opisano najważniejsze wskazówki dotyczące zabezpieczeń i szyfrowania danych.

Najlepsze rozwiązania są oparte na konsensusie opinii i działają z bieżącymi możliwościami platformy Azure i zestawami funkcji. Opinie i technologie zmieniają się w czasie, a ten artykuł jest regularnie aktualizowany w celu odzwierciedlenia tych zmian.

## <a name="protect-data"></a>Ochrona danych
Aby pomóc w ochronie danych w chmurze, należy uwzględnić możliwe stany, w których dane mogą wystąpić i jakie formanty są dostępne dla tego stanu. Najważniejsze wskazówki dotyczące zabezpieczeń i szyfrowania danych platformy Azure odnoszą się do następujących stanów danych:

- W spoczynku: Obejmuje to wszystkie obiekty magazynu informacji, kontenery i typy, które istnieją statycznie na nośniku fizycznym, czy magnetyczne lub optyczne.
- Podczas przesyłania: gdy dane są przesyłane między składnikami, lokalizacjami lub programami, są przesyłane. Przykładami są transfery za pośrednictwem sieci, przez magistralę usług (z lokalnego do chmury i odwrotnie, w tym połączenia hybrydowe, takie jak ExpressRoute) lub podczas procesu wprowadzania/przesyłania danych.

## <a name="choose-a-key-management-solution"></a>Wybierz kluczowe rozwiązanie do zarządzania

Ochrona kluczy jest niezbędna do ochrony danych w chmurze.

Usługa [Azure Key Vault](/azure/key-vault/key-vault-overview) ułatwia ochronę kluczy kryptograficznych i wpisów tajnych używanych przez aplikacje i usługi w chmurze. Usługa Key Vault usprawnia proces zarządzania kluczami i pozwala zachować kontrolę nad kluczami, które mają dostęp do danych i szyfrują je. Deweloperzy mogą w klika minut utworzyć klucze do programowania i testowania, a następnie przeprowadzić ich migrację do kluczy produkcyjnych. W razie potrzeby administratorzy zabezpieczeń mogą przydzielić (i cofnąć) uprawnienia do używania kluczy.

Za pomocą usługi Key Vault można utworzyć wiele bezpiecznych kontenerów, nazywanych magazynami. Te magazyny są wspierane przez moduły HSM. Magazyny zmniejszają prawdopodobieństwo przypadkowej utraty danych zabezpieczeń, stanowiąc centrum przechowywania wpisów tajnych aplikacji. Magazyny kluczy umożliwiają także kontrolowanie i rejestrowanie dostępu do wszelkich elementów, które są w nich przechowywane. Usługa Azure Key Vault może obsługiwać żądania i odnawianie certyfikatów TLS (Transport Layer Security). Zapewnia funkcje niezawodnego rozwiązania do zarządzania cyklem życia certyfikatów.

Usługa Azure Key Vault została zaprojektowana do obsługi kluczy aplikacji i wpisów tajnych. Usługa Key Vault nie jest przeznaczona do przechowywania haseł użytkowników.

Poniżej przedstawiono najlepsze rozwiązania dotyczące zabezpieczeń dotyczące korzystania z usługi Key Vault.

**Najlepsze rozwiązanie**: Udziel dostępu użytkownikom, grupom i aplikacjom w określonym zakresie.   
**Szczegóły:** Użyj wstępnie zdefiniowanych ról RBAC. Na przykład, aby udzielić użytkownikowi dostępu do zarządzania magazynami kluczy, należy przypisać wstępnie zdefiniowaną rolę [Key Vault Contributor](/azure/role-based-access-control/built-in-roles) do tego użytkownika w określonym zakresie. Zakres w tym przypadku będzie subskrypcja, grupa zasobów lub tylko określonego magazynu kluczy. Jeśli wstępnie zdefiniowane role nie odpowiadają Twoim potrzebom, można [zdefiniować własne role.](/azure/role-based-access-control/custom-roles)

**Najlepsze rozwiązanie**: Kontroluj, do czego użytkownicy mają dostęp.   
**Szczegóły:** Dostęp do magazynu kluczy jest kontrolowany za pomocą dwóch oddzielnych interfejsów: płaszczyzny zarządzania i płaszczyzny danych. Mechanizmy kontroli dostępu do płaszczyzny zarządzania i płaszczyzny danych działają niezależnie.

Użyj kontroli dostępu na podstawie ról, aby kontrolować, do czego użytkownicy mają dostęp. Na przykład jeśli chcesz udzielić aplikacji dostępu do używania kluczy w magazynie kluczy, wystarczy udzielić uprawnień dostępu do płaszczyzny danych przy użyciu zasad dostępu magazynu kluczy i nie jest potrzebny dostęp do płaszczyzny zarządzania dla tej aplikacji. Z kolei aby umożliwić użytkownikowi odczyt właściwości i tagów magazynu, ale bez dostępu do kluczy, wpisów tajnych i certyfikatów, możesz udzielić temu użytkownikowi uprawnienia do odczytu przy użyciu kontroli dostępu opartej na rolach, dzięki czemu nie będzie potrzebny dostęp do płaszczyzny danych.

**Najlepsze rozwiązanie:** Przechowywanie certyfikatów w magazynie kluczy. Certyfikaty mają wysoką wartość. W niewłaściwych rękach bezpieczeństwo aplikacji lub bezpieczeństwo danych mogą zostać naruszone.   
**Szczegóły:** Usługa Azure Resource Manager może bezpiecznie wdrażać certyfikaty przechowywane w usłudze Azure Key Vault na maszynach wirtualnych platformy Azure po wdrożeniu maszyn wirtualnych. Ustawiając odpowiednie zasady dostępu dla magazynu kluczy, możesz również kontrolować, kto uzyskuje dostęp do certyfikatu. Kolejną zaletą jest zarządzanie wszystkimi certyfikatami w jednym miejscu za pomocą usługi Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Wdrażanie certyfikatów na maszynach wirtualnych z usługi Key Vault zarządzanej](https://blogs.technet.microsoft.com/kv/2016/09/14/updated-deploy-certificates-to-vms-from-customer-managed-key-vault/) przez klienta.

**Najlepsze rozwiązanie:** Upewnij się, że można odzyskać usunięcie magazynów kluczy lub obiektów magazynu kluczy.   
**Szczegóły:** Usunięcie magazynów kluczy lub obiektów magazynu kluczy może być przypadkowe lub złośliwe. Włącz usuwanie nietrwałe i funkcje ochrony przed ochrony przed przeczyszczaniem w usłudze Key Vault, szczególnie w przypadku kluczy używanych do szyfrowania danych w spoczynku. Usunięcie tych kluczy jest równoznaczne z utratą danych, więc w razie potrzeby możesz odzyskać usunięte magazyny i obiekty magazynu. Regularnie ćwicz operacje odzyskiwania magazynu kluczy.

> [!NOTE]
> Jeśli użytkownik ma uprawnienia współautora (w ramach kontroli dostępu na podstawie ról) do płaszczyzny zarządzania magazynu kluczy, może udzielić sobie dostępu do płaszczyzny danych przez ustawienie zasad dostępu magazynu kluczy. Zaleca się ścisłe kontrolowanie, kto ma dostęp współautora do magazynów kluczy, aby upewnić się, że tylko autoryzowane osoby mogą uzyskiwać dostęp do magazynów kluczy, kluczy, wpisów tajnych i certyfikatów oraz zarządzać nimi.
>
>

## <a name="manage-with-secure-workstations"></a>Zarządzanie za pomocą bezpiecznych stacji roboczych

> [!NOTE]
> Administrator lub właściciel subskrypcji powinien używać stacji roboczej bezpiecznego dostępu lub stacji roboczej z dostępem uprzywilejowanym.
>
>

Ponieważ zdecydowana większość ataków jest kierowana na użytkownika końcowego, punkt końcowy staje się jednym z podstawowych punktów ataku. Osoba atakująca, która naruszy pozycję punktu końcowego, może użyć poświadczeń użytkownika w celu uzyskania dostępu do danych organizacji. Większość ataków punktu końcowego korzysta z faktu, że użytkownicy są administratorami w swoich lokalnych stacjach roboczych.

**Najlepsze rozwiązanie:** Użyj bezpiecznej stacji roboczej do zarządzania, aby chronić poufne konta, zadania i dane.   
**Szczegóły:** Użyj [uprzywilejowanej stacji roboczej dostępu,](https://technet.microsoft.com/library/mt634654.aspx) aby zmniejszyć powierzchnię ataku na stacjach roboczych. Te bezpieczne stacje robocze zarządzania mogą pomóc w ograniczeniu niektórych z tych ataków i upewnić się, że dane są bezpieczniejsze.

**Najlepsze rozwiązanie:** Zapewnienie ochrony punktów końcowych.   
**Szczegóły:** Wymuszaj zasady zabezpieczeń na wszystkich urządzeniach, które są używane do korzystania z danych, niezależnie od lokalizacji danych (chmury lub lokalnie).

## <a name="protect-data-at-rest"></a>Ochrona danych magazynowanych

[Szyfrowanie danych w spoczynku](https://cloudblogs.microsoft.com/microsoftsecure/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) jest obowiązkowym krokiem w kierunku prywatności, zgodności i suwerenności danych.

**Najlepsze rozwiązanie:** Zastosuj szyfrowanie dysku, aby chronić dane.   
**Szczegóły:** Użyj [szyfrowania dysków platformy Azure](/azure/security/azure-security-disk-encryption-overview). Umożliwia administratorom IT szyfrowanie dysków maszyn wirtualnych Windows i Linux IaaS. Szyfrowanie dysków łączy w sobie standardową w branży funkcję Windows BitLocker i funkcję Linux dm-crypt, aby zapewnić szyfrowanie woluminów dla systemu operacyjnego i dysków z danymi.

Usługa Azure Storage i usługa Azure SQL Database domyślnie szyfrują dane w stanie spoczynku, a wiele usług oferuje szyfrowanie jako opcja. Za pomocą usługi Azure Key Vault można zachować kontrolę nad kluczami, za pomocą których jest uzyskiwany dostęp do danych i następuje ich szyfrowanie. Aby [dowiedzieć się więcej, zobacz obsługa modeli szyfrowania dostawców zasobów platformy Azure.](encryption-atrest.md#azure-resource-providers-encryption-model-support)

**Najważniejsze wskazówki:** użyj szyfrowania, aby ograniczyć ryzyko związane z nieautoryzowanym dostępem do danych.   
**Szczegóły:** Zaszyfruj dyski przed zapisaniem poufnych danych.

Organizacje, które nie wymuszają szyfrowania danych, są bardziej narażone na problemy z poufnością danych. Na przykład nieautoryzowani lub fałszywi użytkownicy mogą wykraść dane na zainfekowanych kontach lub uzyskać nieautoryzowany dostęp do danych zakodowanych w formacie Clear Format. Firmy muszą również udowodnić, że są staranne i stosują odpowiednie środki kontroli bezpieczeństwa w celu zwiększenia bezpieczeństwa danych w celu zapewnienia zgodności z przepisami branżowymi.

## <a name="protect-data-in-transit"></a>Ochrona danych podczas przesyłania

Ochrona danych podczas ich przesyłania powinna być istotną częścią strategii ochrony danych. Ponieważ dane są przenoszone pomiędzy wieloma lokalizacjami, zazwyczaj zalecamy, aby do wymiany danych między różnymi lokalizacjami zawsze używać protokołów SSL/TLS. W niektórych sytuacjach może być konieczne odizolowanie całego kanału komunikacyjnego łączącego infrastruktury lokalne i chmury przy użyciu sieci VPN.

W przypadku danych przenoszonych między infrastrukturą lokalną i platformą Azure należy wziąć pod uwagę odpowiednie zabezpieczenia, takie jak protokół HTTPS lub sieć VPN. Podczas wysyłania zaszyfrowanego ruchu między siecią wirtualną platformy Azure a lokalizacją lokalną za pośrednictwem publicznego Internetu należy użyć [usługi Azure VPN Gateway](../../vpn-gateway/index.yml).

Poniżej przedstawiono najlepsze rozwiązania dotyczące korzystania z bramy sieci VPN platformy Azure, protokołu SSL/TLS i HTTPS.

**Najlepsze rozwiązanie:** Bezpieczny dostęp z wielu stacji roboczych znajdujących się lokalnie do sieci wirtualnej platformy Azure.   
**Szczegóły:** Użyj [sieci VPN typu lokacja lokacja](/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal).

**Najlepsze rozwiązanie:** Bezpieczny dostęp z pojedynczej stacji roboczej znajdującej się lokalnie do sieci wirtualnej platformy Azure.   
**Szczegóły:** Użyj [sieci VPN typu punkt-lokacja](/azure/vpn-gateway/vpn-gateway-point-to-site-create).

**Najlepsze rozwiązanie:** Przenoszenie większych zestawów danych za na dedykowanym szybkim łączem WAN.   
**Szczegóły:** Użyj [usługi ExpressRoute](/azure/expressroute/expressroute-introduction). Jeśli wybierzesz korzystanie z usługi ExpressRoute, możesz także szyfrować dane na poziomie aplikacji przy użyciu protokołu SSL/TLS lub innych protokołów w celu zapewnienia dodatkowej ochrony.

**Najlepsze rozwiązanie:** Interakcja z usługą Azure Storage za pośrednictwem witryny Azure portal.   
**Szczegóły:** Wszystkie transakcje odbywają się za pośrednictwem protokołu HTTPS. Do interakcji z [usługą](https://msdn.microsoft.com/library/azure/dd179355.aspx) Azure Storage [.](https://azure.microsoft.com/services/storage/)

Organizacje, które nie chronią danych podczas przesyłania, są bardziej podatne na [ataki typu man-in-the-middle,](https://technet.microsoft.com/library/gg195821.aspx) [podsłuchiwanie](https://technet.microsoft.com/library/gg195641.aspx)i porwanie sesji. Takie ataki mogą być pierwszym krokiem do uzyskania dostępu do poufnych danych.

## <a name="secure-email-documents-and-sensitive-data"></a>Bezpieczna poczta e-mail, dokumenty i poufne dane

Chcesz kontrolować i zabezpieczać wiadomości e-mail, dokumenty i poufne dane udostępniane poza firmą. [Azure Information Protection](/azure/information-protection/) to rozwiązanie chmurowe ułatwiające klasyfikowanie, etykietowanie i ochronę dokumentów oraz wiadomości e-mail w firmie. Może to być wykonywane automatycznie przez administratorów, którzy definiują reguły i warunki, ręcznie przez użytkowników lub kombinacji, w której użytkownicy otrzymują rekomendacje.

Klasyfikacja jest identyfikowalna przez cały czas, niezależnie od tego, gdzie dane są przechowywane lub komu są udostępniane. Etykiety zawierają oznaczenia wizualne, takie jak nagłówek, stopka lub znak wodny. Metadane dodawane do plików i nagłówków wiadomości e-mail mają postać zwykłego tekstu. Jasny tekst zapewnia, że inne usługi, takie jak rozwiązania zapobiegające utracie danych, mogą identyfikować klasyfikację i podejmować odpowiednie działania.

Technologia ochrony używa usługi Azure Rights Management (Azure RMS). Technologia ta jest zintegrowana z innymi usługami i aplikacjami chmurowymi firmy Microsoft, takimi jak Office 365 czy Azure Active Directory. Ta technologia ochrony używa zasad szyfrowania, tożsamości i autoryzacji. Ochrona stosowana za pośrednictwem usługi Azure RMS pozostaje z dokumentami i wiadomościami e-mail, niezależnie od lokalizacji — w organizacji lub poza nią, sieciami, serwerami plików i aplikacjami.

To rozwiązanie do ochrony informacji zapewnia kontrolę nad danymi, nawet jeśli są one udostępniane innym osobom. Usługi Azure RMS można również używać z własnymi aplikacjami biznesowymi i rozwiązaniami ochrony informacji od dostawców oprogramowania, niezależnie od tego, czy te aplikacje i rozwiązania są lokalne, czy w chmurze.

Zalecamy wykonanie następujących czynności:

- [Wdrażanie usługi Azure Information Protection](/azure/information-protection/deployment-roadmap) dla swojej organizacji.
- Zastosuj etykiety, które odzwierciedlają twoje wymagania biznesowe. Na przykład: Zastosuj etykietę o nazwie "wysoce poufne" do wszystkich dokumentów i wiadomości e-mail, które zawierają ściśle tajne dane, aby sklasyfikować i chronić te dane. Następnie tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do tych danych z wszelkimi ograniczeniami określonymi przez użytkownika.
- Skonfiguruj [rejestrowanie użycia dla usługi Azure RMS,](/azure/information-protection/log-analyze-usage) aby monitorować sposób, w jaki twoja organizacja korzysta z usługi ochrony.

Organizacje, które są słabe w [klasyfikacji danych](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) i ochrony plików mogą być bardziej podatne na wyciek danych lub niewłaściwe wykorzystanie danych. Dzięki odpowiedniej ochronie plików możesz analizować przepływy danych, aby uzyskać wgląd w swoją firmę, wykrywać ryzykowne zachowania i podejmować działania naprawcze, śledzić dostęp do dokumentów i tak dalej.

## <a name="next-steps"></a>Następne kroki

Zobacz [najlepsze rozwiązania i wzorce zabezpieczeń platformy Azure,](best-practices-and-patterns.md) aby uzyskać więcej najlepszych rozwiązań w zakresie zabezpieczeń, których należy używać podczas projektowania, wdrażania i zarządzania rozwiązaniami w chmurze przy użyciu platformy Azure.

Dostępne są następujące zasoby, aby zapewnić bardziej ogólne informacje na temat zabezpieczeń platformy Azure i powiązanych usług firmy Microsoft:
* [Blog zespołu zabezpieczeń platformy Azure](https://blogs.msdn.microsoft.com/azuresecurity/) — aby uzyskać aktualne informacje na temat najnowszych informacji w usłudze Azure Security
* [Microsoft Security Response Center](https://technet.microsoft.com/library/dn440717.aspx) — gdzie można zgłaszać luki w zabezpieczeniach firmy Microsoft, w tym problemy z platformą Azure, lubsecure@microsoft.com
