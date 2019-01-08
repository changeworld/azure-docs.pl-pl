---
title: Co to jest usługa Azure Key Vault? — Usługi azure Key Vault | Dokumentacja firmy Microsoft
description: Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą usługi Azure Key Vault klient może szyfrować klucze i klucze tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych, pliki PFX oraz hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM, hardware security module).
services: key-vault
documentationcenter: ''
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: e759df6f-0638-43b1-98ed-30b3913f9b82
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: barclayn
ms.openlocfilehash: f3c198ab8a17df019f1735a9b62e27f1051f64c5
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2019
ms.locfileid: "54076335"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:

- **Zarządzanie wpisami tajnymi** — usługi Azure Key Vault pozwala bezpiecznie przechowywać i ściśle kontrolować dostęp do tokenów, hasła, certyfikaty, klucze interfejsu API i innych wpisów tajnych.
- **Zarządzanie kluczami** — usługa Azure Key Vault może być także używana jako rozwiązanie do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- **Zarządzanie certyfikatami** — usługa Azure Key Vault pozwala również łatwo aprowizować i wdrażać prywatne i publiczne certyfikaty Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użycia z platformą Azure i połączonymi zasobami wewnętrznymi oraz zarządzać tymi certyfikatami. 
- **Store wpisów tajnych, wspierane przez sprzętowe moduły zabezpieczeń** — klucze tajne i klucze mogą być chronione przez oprogramowanie lub ze standardem FIPS 140-2 Level 2 weryfikuje sprzętowych modułów zabezpieczeń.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty. A **magazynu** jest logiczną grupą wpisów tajnych. Teraz można wykonywać wszystkie operacje za pomocą usługi Key Vault należy najpierw uwierzytelnić się do niego. 

Zasadniczo istnieją trzy sposoby uwierzytelniania w usłudze Key Vault:

1. **Za pomocą [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)**  (**zalecane i najlepszych praktyk**): Podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure, można przypisać tożsamość się z maszyną wirtualną, która ma dostęp do usługi Key Vault. Można także przypisać tożsamości do innych zasobów platformy azure, które są wymienione [tutaj](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Korzyści w przypadku tej metody jest aplikacja / Usługa nie zarządza obrót pierwszy klucz tajny. Azure automatycznie przełącza tożsamości. 
2. **Za pomocą jednostki usługi i certyfikatu:** Drugą opcją jest używać nazwy głównej usługi i jest skojarzony certyfikat, który ma dostęp do usługi Key Vault. Ciężar rotacji certyfikatu znajduje się w właściciela aplikacji lub dla deweloperów i dlatego nie jest to zalecane.
3. **Przy użyciu nazwy głównej usługi i klucza tajnego:** Trzecia opcja (nie preferowaną opcję) jest używać nazwy głównej usługi i wpisu tajnego do uwierzytelniania w usłudze Key Vault.

> [!NOTE]
> Opcja 3 powyżej nie należy używać, ponieważ trudno automatyczne obracanie bootstrap hasło używane do uwierzytelniania w usłudze Key Vault.

Poniżej przedstawiono niektóre kluczowe terminy:

- **Dzierżawa**: Dzierżawa to organizacja, która jest właścicielem i określone wystąpienie usług chmurowych firmy Microsoft. Tym terminem najczęściej określa się zestaw usług platformy Azure i Office 365 dla organizacji.
- **Vault właściciel**: Właścicielem magazynu można utworzyć magazyn kluczy i korzystaj z pełnego dostępu i kontroli nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.
- **Vault konsumenta**: Konsument magazynu mogą wykonywać akcje na zasoby w magazynie kluczy, właściciela magazynu przyznaje dostęp odbiorców. Dostępne akcje zależą od przyznanych uprawnień.
- **Zasób**: Zasób jest łatwe w zarządzaniu elementu, który jest dostępny za pośrednictwem platformy Azure. Niektóre typowe zasoby to: maszyna wirtualna, konto magazynu, aplikacja internetowa czy sieć wirtualna. Istnieje ich jednak wiele więcej.
- **Grupa zasobów**: Grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.
- **Nazwa główna usługi** -jednostki usługi platformy Azure to tożsamość zabezpieczeń używana przez aplikacje utworzone przez użytkownika, usługi i narzędzia automatyzacji, dostęp do określonych zasobów platformy Azure. Można ją traktować jako „tożsamość użytkownika” (nazwa logowania i hasło lub certyfikat) z określoną rolą i ściśle kontrolowanymi uprawnieniami. W odróżnieniu od ogólnej tożsamości użytkownika, jednostka usługi powinna wykonywać tylko określone czynności. Nazwa główna usługi pozwala poprawić bezpieczeństwo, jeśli przyznasz jej tylko minimalny poziom uprawnień potrzebny do wykonywania zadań zarządzania.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Usługa Azure AD jest usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę. 
- **Identyfikator dzierżawy usługi Azure**: Identyfikator dzierżawy jest nietypowy sposób do identyfikowania wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.
- **Zarządzane tożsamości dla zasobów platformy Azure**: Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Za pomocą tożsamości zarządzanej sprawia, że rozwiązywania tego problemu, prostsze, zapewniając tożsamości automatycznie zarządzanych usług platformy Azure w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższy obraz i [zarządzanych tożsamości dla zasobów platformy Azure — omówienie](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram przedstawiający sposób zarządzać tożsamościami do pracy z zasobami platformy Azure](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |„Chcę napisać aplikację dla platformy Azure, która używa kluczy do logowania i szyfrowania, ale chcę, żeby te klucze znajdowały się poza moją aplikacją tak, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Powinny też być łatwe w użyciu w moich aplikacjach i mieć optymalną wydajność”. |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |„Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i kluczy tajnych dzierżawy moich klientów. <br/><br/>Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami. Dzięki temu będę w stanie się skupić na tym, co robię najlepiej – na tworzeniu podstawowych funkcji oprogramowania”. |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonać operacje kryptograficzne przy użyciu kluczy swoich klientów, usługa Key Vault wykonuje te operacje w jej imieniu. Aplikacja nie ma wglądu w klucze klientów. |
| Chief Security Officer (CSO) |„Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM w trybie FIPS 140-2 poziom 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę mieć możliwość zarządzania kluczami z jednej lokalizacji na platformie Azure”. |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Mimo że usługa Key Vault przynosi korzyści głównie deweloperom i administratorom zabezpieczeń, może być wdrażana i zarządzana przez administratora organizacji, który zarządza innymi usługami platformy Azure dla organizacji. Administrator może na przykład zalogować się przy użyciu subskrypcji platformy Azure, utworzyć dla organizacji magazyn, w którym będą przechowywane klucze, a następnie odpowiadać za takie zadania operacyjne jak:

- Tworzenie lub importowanie klucza lub klucza tajnego
- Odwoływanie lub usuwanie klucza lub klucza tajnego
- Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
- Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
- Monitorowanie użycia klucza

Administrator może następnie dostarczyć deweloperom identyfikatory URI w celu wywoływania z aplikacji oraz dostarczyć administratorom zabezpieczeń informacje o rejestrowaniu użycia klucza. 

! [Omówienie usługi Azure Key Vault] [1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak [zabezpieczanie własnego magazynu](key-vault-secure-your-key-vault.md) 
 <!--Image references--> [1]:./media/key-vault-whatis/AzureKeyVault_overview.png usługi Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
