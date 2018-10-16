---
title: Co to jest usługa Azure Key Vault? | Microsoft Docs
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
ms.devlang: na
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: barclayn
ms.openlocfilehash: 7e98853b5b2ccc779dca970337fc44217977c8c9
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2018
ms.locfileid: "49342570"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:
- **Zarządzanie wpisami tajnymi** — usługi Azure Key Vault pozwala bezpiecznie przechowywać i ściśle kontrolować dostęp do tokenów, hasła, certyfikaty, klucze interfejsu API i innych wpisów tajnych
- **Zarządzanie kluczami** — usługi Azure Key Vault umożliwia także jako rozwiązania do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- **Zarządzanie certyfikatami** — usługi Azure Key Vault jest również usługa, która pozwala na łatwe aprowizowanie, zarządzanie i wdrażanie prywatnych i publicznych certyfikatów Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użytku z platformą Azure i wewnętrznych połączone zasoby. 
- **Store wpisów tajnych, wspierane przez sprzętowe moduły zabezpieczeń** — klucze tajne i klucze mogą być chronione przez oprogramowanie lub ze standardem FIPS 140-2 Level 2 weryfikuje sprzętowych modułów zabezpieczeń

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty. A **magazynu** jest logiczną grupą wpisów tajnych. Teraz można wykonywać wszystkie operacje za pomocą usługi Key Vault należy najpierw uwierzytelnić się do niego. 

Zasadniczo są 3 sposoby uwierzytelniania w usłudze Key Vault

1. **Za pomocą [zarządzanych tożsamości dla zasobów platformy Azure](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview)**  (**zalecane i najlepszych praktyk**): podczas wdrażania aplikacji na maszynie wirtualnej na platformie Azure, można przypisać tożsamość maszyny wirtualnej który ma dostęp do usługi Key Vault. Można także przypisać tożsamości do innych zasobów platformy azure, które są wymienione [tutaj](https://docs.microsoft.com/en-us/azure/active-directory/managed-identities-azure-resources/overview). Korzyści w przypadku tej metody jest aplikacja / Usługa nie zarządza obrót pierwszy klucz tajny. Azure automatycznie przełącza tożsamości. 
2. **Za pomocą jednostki usługi i certyfikatu:** 2nd opcją jest użycie nazwy głównej usługi i jest skojarzony certyfikat, który ma dostęp do usługi Key Vault. Ciężar rotacji certyfikatu znajduje się w właściciela aplikacji lub dla deweloperów i dlatego nie jest to zalecane
3. **Za pomocą nazwy głównej usługi i klucza tajnego:** opcja 3 (nie preferowaną opcję) jest użycie jednostki usługi oraz klucza tajnego do uwierzytelniania w usłudze Key Vault

Poniżej przedstawiono niektóre kluczowe terminy:
- **Dzierżawa** — dzierżawa to organizacja, która jest właścicielem konkretnego wystąpienia usług firmy Microsoft w chmurze i zarządza nim. Tym terminem najczęściej określa się zestaw usług platformy Azure i Office 365 dla organizacji.
- **Właściciel magazynu** — właściciel magazynu może utworzyć magazyn kluczy, ma do niego pełny dostęp i kontrolę nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.
- **Użytkownik magazynu** — użytkownik magazynu może wykonywać akcje na zasobach wewnątrz magazynu kluczy, jeśli właściciel magazynu udzieli mu dostępu. Dostępne akcje zależą od przyznanych uprawnień.
- **Zasób** — zasób to dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Niektóre typowe zasoby to: maszyna wirtualna, konto magazynu, aplikacja internetowa czy sieć wirtualna. Istnieje ich jednak wiele więcej.
- **Grupa zasobów** — grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.
- **Nazwa główna usługi** — w celu uzyskania dostępu do zasobów, które są zabezpieczone przez dzierżawę usługi Azure AD, jednostka, która wymaga dostępu musi być reprezentowana przez podmiot zabezpieczeń. Ta zasada obowiązuje dla użytkowników (nazwy głównej użytkownika) i aplikacji (nazwy głównej usługi). Podmiot zabezpieczeń definiuje zasady dostępu i uprawnień dla aplikacji/użytkownika w tej dzierżawie. Dzięki temu podstawowe funkcje, takie jak uwierzytelnianie aplikacji/użytkownika podczas logowania i autoryzacji podczas uzyskiwania dostępu do zasobów.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Azure AD to usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę. 
- **Identyfikator dzierżawy Azure** — identyfikator dzierżawy to unikatowy sposób identyfikacji wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.
- **Zarządzane tożsamości dla zasobów platformy Azure**: usługi Azure Key Vault umożliwia bezpieczne przechowywanie poświadczeń i innych kluczy i wpisów tajnych, ale Twój kod będzie wymagała uwierzytelnienia do usługi Key Vault, aby je pobrać. Za pomocą tożsamości zarządzanej sprawia, że rozwiązywania tego problemu, prostsze, zapewniając tożsamości automatycznie zarządzanych usług platformy Azure w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz poniższy obraz i [zarządzanych tożsamości dla zasobów platformy Azure — omówienie](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagram przedstawiający sposób zarządzać tożsamościami dla zasobów platformy Azure działa](./media/key-vault-whatis/msi.png)

## <a name="key-vault-roles"></a>Role usługi Key Vault

Użyj poniższej tabeli, aby lepiej zrozumieć, w jaki sposób usługa Key Vault może pomóc deweloperom i administratorom zabezpieczeń w spełnianiu ich potrzeb.

| Rola | Opis problemu | Rozwiązanie usługi Azure Key Vault |
| --- | --- | --- |
| Deweloper aplikacji platformy Azure |„Chcę napisać aplikację dla platformy Azure, która używa kluczy do logowania i szyfrowania, ale chcę, żeby te klucze znajdowały się poza moją aplikacją tak, aby rozwiązanie było odpowiednie dla aplikacji rozproszonej geograficznie. <br/><br/>Chcę także, aby klucze i wpisy tajne były chronione, ale bez konieczności samodzielnego pisania kodu. Powinny też być łatwe w użyciu w moich aplikacjach i mieć optymalną wydajność”. |√ Klucze są przechowywane w magazynie i w razie potrzeby wywoływane przez identyfikator URI.<br/><br/> √ Klucze są chronione przez platformę Azure przy użyciu branżowych standardów dotyczących algorytmów, długości klucza i sprzętowych modułów zabezpieczeń (HSM, hardware security module).<br/><br/> √ Klucze są przetwarzane w modułach HSM, które znajdują się w tych samych centrach danych platformy Azure co aplikacje. Ta metoda zapewnia większą niezawodność i mniejsze opóźnienia niż klucze przechowywane w osobnej lokalizacji, na przykład lokalnie. |
| Deweloper oprogramowania jako usługi (SaaS) |„Nie chcę ponosić odpowiedzialności ani mieć potencjalnych problemów względem kluczy i kluczy tajnych dzierżawy moich klientów. <br/><br/>Chcę, aby klienci mogli niezależnie zarządzać swoimi kluczami. Dzięki temu będę w stanie się skupić na tym, co robię najlepiej – na tworzeniu podstawowych funkcji oprogramowania”. |√ Klienci mogą importować własne klucze do platformy Azure i zarządzać nimi. Gdy aplikacja SaaS musi wykonać operacje kryptograficzne przy użyciu kluczy swoich klientów, usługa Key Vault wykonuje te operacje w jej imieniu. Aplikacja nie ma wglądu w klucze klientów. |
| Chief Security Officer (CSO) |„Chcę wiedzieć, że nasze aplikacje są zgodne z modułami HSM w trybie FIPS 140-2 poziom 2 w celu bezpiecznego zarządzania kluczami. <br/><br/>Chcę się upewnić, że moja organizacja kontroluje cykl życia klucza i monitoruje jego użycie. <br/><br/>I chociaż korzystamy z wielu usług i zasobów platformy Azure, chcę mieć możliwość zarządzania kluczami z jednej lokalizacji na platformie Azure”. |√ Moduły HSM są zweryfikowane w trybie FIPS 140-2 poziom 2.<br/><br/>√ Usługa Key Vault jest zaprojektowana w taki sposób, aby firma Microsoft nie miała wglądu w Twoje klucze ani nie mogła ich wyodrębnić.<br/><br/>√ Użycie klucza jest rejestrowane w czasie niemal rzeczywistym.<br/><br/>√ Magazyn zapewnia jeden interfejs, niezależnie od tego, jak wiele magazynów masz na platformie Azure, które regiony są przez nie obsługiwane oraz które aplikacje ich używają. |

Każdy posiadacz subskrypcji Azure może tworzyć magazyny kluczy i z nich korzystać. Mimo że usługa Key Vault przynosi korzyści głównie deweloperom i administratorom zabezpieczeń, może być wdrażana i zarządzana przez administratora organizacji, który zarządza innymi usługami platformy Azure dla organizacji. Administrator może na przykład zalogować się przy użyciu subskrypcji platformy Azure, utworzyć dla organizacji magazyn, w którym będą przechowywane klucze, a następnie odpowiadać za takie zadania operacyjne jak:

* Tworzenie lub importowanie klucza lub klucza tajnego
* Odwoływanie lub usuwanie klucza lub klucza tajnego
* Zezwalanie użytkownikom lub aplikacjom na dostęp do magazynu kluczy, aby mogli zarządzać kluczami i kluczami tajnymi lub używać ich
* Konfigurowanie użycia klucza (na przykład rejestrowanie lub szyfrowanie)
* Monitorowanie użycia klucza

Administrator może następnie dostarczyć deweloperom identyfikatory URI w celu wywoływania z aplikacji oraz dostarczyć administratorom zabezpieczeń informacje o rejestrowaniu użycia klucza. 

![Omówienie usługi Azure Key Vault][1]

Deweloperzy mogą również zarządzać kluczami bezpośrednio za pomocą interfejsów API. Aby uzyskać więcej informacji, zobacz artykuł [Przewodnik dewelopera usługi Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Kolejne kroki

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
