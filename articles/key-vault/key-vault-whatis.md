---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
description: Dowiedz się, w jaki sposób Azure Key Vault zabezpiecza klucze kryptograficzne i wpisy tajne używane przez aplikacje i usługi w chmurze.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: mbaldwin
ms.openlocfilehash: cf8101923f898c4ce190f3eaf2f3a9b2430cc723
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70885132"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Aplikacje i usługi w chmurze używają kluczy kryptograficznych i wpisów tajnych w celu zapewnienia bezpieczeństwa informacji. Azure Key Vault zabezpiecza te klucze i wpisy tajne. Korzystając z Key Vault, można szyfrować klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX i hasła przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń).

Key Vault pomaga rozwiązywać następujące problemy:

- **Zarządzanie kluczami tajnymi**: Bezpiecznie Przechowuj i ściśle Kontroluj dostęp do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami**: Twórz i kontroluj klucze szyfrowania, które szyfrują dane. 
- **Zarządzanie certyfikatami**: Udostępniaj i wdrażaj publiczne i prywatne certyfikaty SSL/Transport Layer Security (SSL/TLS), które mają być używane z platformą Azure i wewnętrznymi zasobami połączonymi, oraz zarządzaj nimi. 
- **Przechowuj wpisy tajne w usłudze sprzętowych modułów zabezpieczeń**: Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 oprogramowania lub FIPS 140-2, aby pomóc chronić klucze tajne i klucze.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty. Magazyn jest logiczną grupą kluczy tajnych.

Oto inne ważne terminy:

- **Dzierżawa**: Dzierżawca to organizacja, która jest właścicielem określonego wystąpienia usług w chmurze firmy Microsoft i zarządza nim. Jest to najczęściej używane do odwoływania się do zestawu usług platformy Azure i pakietu Office 365 dla organizacji.

- **Właściciel magazynu**: Właściciel magazynu może utworzyć magazyn kluczy i uzyskać pełny dostęp i kontrolę nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.

- **Odbiorca magazynu**: Odbiorca magazynu może wykonywać akcje na zasobach w magazynie kluczy, gdy właściciel magazynu przyznaje dostęp konsumenta. Dostępne akcje zależą od przyznanych uprawnień.

- **Zasób**: Zasób jest elementem możliwym do zarządzania, który jest dostępny za pomocą platformy Azure. Typowe przykłady to maszyna wirtualna, konto magazynu, aplikacja sieci Web, baza danych i Sieć wirtualna. Istnieje wiele innych.

- **Grupa zasobów**: Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

- Nazwa **główna usługi**: Jednostka usługi platformy Azure to tożsamość zabezpieczeń, którą aplikacje, usługi i narzędzia automatyzacji zostały utworzone przez użytkownika w celu uzyskania dostępu do określonych zasobów platformy Azure. Należy je traktować jako "tożsamość użytkownika" (nazwę użytkownika i hasło lub certyfikat) z określoną rolą i ściśle kontrolowanymi uprawnieniami. W odróżnieniu od ogólnej tożsamości użytkownika, jednostka usługi powinna wykonywać tylko określone czynności. Zwiększa zabezpieczenia, jeśli przyznasz mu tylko minimalny poziom uprawnień potrzebny do wykonywania zadań zarządzania.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Usługa Azure AD jest usługą Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę.

- **Identyfikator dzierżawy platformy Azure**: Identyfikator dzierżawy jest unikatowym sposobem identyfikowania wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.

- **Zarządzane tożsamości**: Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Korzystanie z tożsamości zarządzanej ułatwia rozwiązanie tego problemu, oferując usługi platformy Azure, które automatycznie zarządza tożsamością w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższy obraz oraz [Omówienie zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram przedstawiający sposób działania zarządzanych tożsamości dla zasobów platformy Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Aby wykonać wszelkie operacje z Key Vault, musisz najpierw przeprowadzić do nich uwierzytelnienie. Istnieją trzy sposoby uwierzytelniania do Key Vault:

- [Zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md): Podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure można przypisać tożsamość do maszyny wirtualnej, która ma dostęp do Key Vault. Możesz również przypisywać tożsamości do [innych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). Zaletą tego podejścia jest to, że aplikacja lub usługa nie zarządza rotacją pierwszego klucza tajnego. Platforma Azure automatycznie obraca tożsamość. Zalecamy to podejście jako najlepsze rozwiązanie. 
- Nazwa **główna usługi i certyfikat**: Możesz użyć nazwy głównej usługi i skojarzonego certyfikatu, który ma dostęp do Key Vault. Nie zalecamy tego podejścia, ponieważ właściciel aplikacji lub Deweloper musi obrócić certyfikat.
- Nazwa **główna usługi i klucz tajny**: Mimo że można użyć jednostki usługi i wpisu tajnego do uwierzytelniania w celu Key Vault, nie jest to zalecane. Trudno jest automatycznie obrócić wpis tajny Bootstrap używany do uwierzytelniania w Key Vault.


## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Role | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |"Chcę napisać aplikację dla platformy Azure, która używa kluczy do podpisywania i szyfrowania. Ale chcę, aby te klucze były zewnętrzne od mojej aplikacji, aby rozwiązanie było odpowiednie dla aplikacji, która jest geograficznie dystrybuowana. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Powinny też być łatwe w użyciu w moich aplikacjach i mieć optymalną wydajność”. |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |„Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i kluczy tajnych dzierżawy moich klientów. <br/><br/>Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami. Dzięki temu będę w stanie się skupić na tym, co robię najlepiej – na tworzeniu podstawowych funkcji oprogramowania”. |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonywać operacje kryptograficzne przy użyciu kluczy klientów, Key Vault wykonuje te operacje w imieniu aplikacji. Aplikacja nie ma wglądu w klucze klientów. |
| Chief Security Officer (CSO) |„Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM w trybie FIPS 140-2 poziom 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę mieć możliwość zarządzania kluczami z jednej lokalizacji na platformie Azure”. |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Chociaż Key Vault korzyści dla deweloperów i administratorów zabezpieczeń, można je wdrożyć i zarządzać przez administratora organizacji, który zarządza innymi usługami platformy Azure. Na przykład ten administrator może zalogować się przy użyciu subskrypcji platformy Azure, utworzyć magazyn dla organizacji, w której będą przechowywane klucze, a następnie być odpowiedzialny za zadania operacyjne podobne do tych:

- Tworzenie lub importowanie klucza lub klucza tajnego
- Odwoływanie lub usuwanie klucza lub klucza tajnego
- Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
- Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
- Monitorowanie użycia klucza

Umożliwia to administratorom identyfikatory URI do wywołania z aplikacji. Ten administrator zapewnia również informacje o rejestrowaniu użycia klucza dla administratora zabezpieczeń. 

![Przegląd sposobu działania Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [zabezpieczyć swój magazyn](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
