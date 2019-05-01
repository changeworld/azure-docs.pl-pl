---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
description: Dowiedz się, jak usługi Azure Key Vault chroni klucze kryptograficzne i wpisy tajne, które aplikacje i usługi w chmurze Użyj.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 43794b8ef4e0272362c7695eda75f5af36a77d1e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683461"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Aplikacje w chmurze i używają kluczy kryptograficznych i wpisów tajnych, aby zapewnić informacje, bezpieczne. Usługa Azure Key Vault chroni klucze i wpisy tajne. Korzystając z usługi Key Vault możesz szyfrować klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX i hasła przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM).

Key Vault pomaga rozwiązać następujące problemy:

- **Zarządzanie wpisami tajnymi**: Bezpieczne przechowywanie i ściśle kontrolować dostęp do tokenów, hasła, certyfikaty, klucze interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami**: Utwórz i kontrolować klucze szyfrowania, które szyfrowania danych. 
- **Zarządzanie certyfikatami**: Aprowizacji, zarządzania i wdrażania prywatnych i publicznych certyfikatów Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użytku z platformą Azure i usługi wewnętrznej połączonymi zasobami. 
- **Wpisy tajne, wspierane przez sprzętowe moduły zabezpieczeń Store**: Użyj oprogramowania lub ze standardem FIPS 140-2 Level 2 zweryfikowane sprzętowych modułów zabezpieczeń, aby lepiej chronić klucze tajne i klucze.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty. Magazyn jest logiczną grupą wpisów tajnych.

Poniżej przedstawiono inne ważne terminy:

- **Dzierżawa**: Dzierżawa to organizacja, która jest właścicielem i określone wystąpienie usług chmurowych firmy Microsoft. W większości przypadków służy do odwoływania się do zestawu usług platformy Azure i usługi Office 365 dla organizacji.

- **Vault właściciel**: Właścicielem magazynu można utworzyć magazyn kluczy i korzystaj z pełnego dostępu i kontroli nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.

- **Vault konsumenta**: Konsument magazynu mogą wykonywać akcje na zasoby w magazynie kluczy, właściciela magazynu przyznaje dostęp odbiorców. Dostępne akcje zależą od przyznanych uprawnień.

- **Zasób**: Zasób jest łatwe w zarządzaniu elementu, który jest dostępny za pośrednictwem platformy Azure. Typowe przykłady to maszyna wirtualna, konto magazynu, aplikacji sieci web, bazy danych i sieci wirtualnej. Istnieje wiele innych.

- **Grupa zasobów**: Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.

- **Nazwa główna usługi**: Jednostka usługi platformy Azure to tożsamość zabezpieczeń, która umożliwia dostęp do określonych zasobów platformy Azure utworzone przez użytkownika aplikacji, usług i narzędzi automatyzacji. Ją traktować jako "tożsamość użytkownika" (nazwa użytkownika i hasło lub certyfikat) z określoną rolą i ściśle kontrolowanymi uprawnieniami. W odróżnieniu od ogólnej tożsamości użytkownika, jednostka usługi powinna wykonywać tylko określone czynności. Zwiększa bezpieczeństwo, gdy przyznasz jej tylko poziom minimalne uprawnienia wymagane do wykonywania zadań zarządzania.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Usługa Azure AD jest usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę.

- **Identyfikator dzierżawy usługi Azure**: Identyfikator dzierżawy jest nietypowy sposób do identyfikowania wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.

- **Zarządzane tożsamości**: Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Za pomocą tożsamości zarządzanej sprawia, że rozwiązywania tego problemu, prostsze, zapewniając tożsamości automatycznie zarządzanych usług platformy Azure w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższy obraz i [Przegląd zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram przedstawiający sposób zarządzanych tożsamości do pracy z zasobami platformy Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Aby wykonać dowolne operacje w usłudze Key Vault, należy najpierw uwierzytelnić się do niego. Istnieją trzy sposoby uwierzytelniania w usłudze Key Vault:

- [Zarządzane tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview): Podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure, można przypisać tożsamość się z maszyną wirtualną, która ma dostęp do usługi Key Vault. Można także przypisać tożsamości [innych zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Zaletą tego podejścia jest to, że aplikacja lub usługa nie jest zarządzanie obrót pierwszy wpis tajny. Azure automatycznie przełącza tożsamości. Najlepszym rozwiązaniem jest firma Microsoft zaleca tego podejścia. 
- **Podmiot zabezpieczeń i certyfikat usługi**: Można użyć nazwy głównej usługi i jest skojarzony certyfikat, który ma dostęp do usługi Key Vault. Nie zalecamy tego podejścia, ponieważ właściciel aplikacji lub deweloper musi Obróć certyfikatu
- **Nazwa główna usługi i klucza tajnego**: Chociaż można używać nazwy głównej usługi i klucza tajnego do uwierzytelniania w usłudze Key Vault, nie zalecamy tego. Trudno jest automatyczna rotacja ładowania początkowego klucza tajnego, który jest używany do uwierzytelniania w usłudze Key Vault.


## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |"Chcę napisać aplikację dla platformy Azure, która używa kluczy do podpisywania i szyfrowania. Ale chcę, aby te klucze znajdowały się poza moją aplikacją tak, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Powinny też być łatwe w użyciu w moich aplikacjach i mieć optymalną wydajność”. |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |„Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i kluczy tajnych dzierżawy moich klientów. <br/><br/>Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami. Dzięki temu będę w stanie się skupić na tym, co robię najlepiej – na tworzeniu podstawowych funkcji oprogramowania”. |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonać operacje kryptograficzne przy użyciu kluczy klientów, usługa Key Vault wykonuje te operacje w imieniu aplikacji. Aplikacja nie ma wglądu w klucze klientów. |
| Chief Security Officer (CSO) |„Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM w trybie FIPS 140-2 poziom 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę mieć możliwość zarządzania kluczami z jednej lokalizacji na platformie Azure”. |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Mimo że usługa Key Vault przynosi korzyści deweloperom i administratorom zabezpieczeń, można zaimplementować i zarządzane przez administratora organizacji, który zarządza innymi usługami platformy Azure. Na przykład administrator może zalogować się przy użyciu subskrypcji platformy Azure, utworzyć magazyn dla organizacji, w której chcesz przechowywać klucze, a następnie odpowiedzialne za zadania operacyjne, takie jak te:

- Tworzenie lub importowanie klucza lub klucza tajnego
- Odwoływanie lub usuwanie klucza lub klucza tajnego
- Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
- Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
- Monitorowanie użycia klucza

Administrator daje deweloperom identyfikatory URI w celu wywoływania z aplikacji. Administrator ten zawiera też informacje rejestrowania użycia klucza do administratora zabezpieczeń. 

![Omówienie sposobu działania usługi Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zabezpieczanie własnego magazynu](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
