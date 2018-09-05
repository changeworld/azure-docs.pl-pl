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
ms.topic: get-started-article
ms.date: 08/02/2018
ms.author: barclayn
ms.openlocfilehash: 26828efedac9953ce1c7375fc62269e93019ce50
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43094874"
---
# <a name="what-is-azure-key-vault"></a>Co to jest usługa Azure Key Vault?

Usługa Azure Key Vault pomaga rozwiązać następujące problemy:
- Usługa Azure Key Vault może służyć do bezpiecznego przechowywania i ścisłego kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych.
- Usługa Azure Key Vault może być także używana jako rozwiązanie do zarządzania kluczami. Usługa Azure Key Vault ułatwia tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 
- Usługa Azure Key Vault pozwala również łatwo aprowizować i wdrażać prywatne i publiczne certyfikaty Secure Sockets Layer/Transport Layer Security (SSL/TLS) do użycia z platformą Azure i połączonymi zasobami wewnętrznymi oraz zarządzać tymi certyfikatami. 
- Wpisy tajne i klucze mogą być chronione przez oprogramowanie lub moduły HSM zweryfikowane w trybie FIPS 140-2 poziom 2.

## <a name="basic-concepts"></a>Podstawowe pojęcia

Usługa Azure Key Vault jest narzędziem do bezpiecznego przechowywania wpisów tajnych i uzyskiwania do nich dostępu. Wpis tajny to dowolny zasób, do którego dostęp powinien być ściśle kontrolowany. Przykładowe wpisy tajne to klucze interfejsu API, hasła i certyfikaty.
Poniżej przedstawiono niektóre kluczowe terminy:
- **Dzierżawa** — dzierżawa to organizacja, która jest właścicielem konkretnego wystąpienia usług firmy Microsoft w chmurze i zarządza nim. Tym terminem najczęściej określa się zestaw usług platformy Azure i Office 365 dla organizacji.
- **Właściciel magazynu** — właściciel magazynu może utworzyć magazyn kluczy, ma do niego pełny dostęp i kontrolę nad nim. Właściciel magazynu może też skonfigurować inspekcję, aby rejestrować, kto uzyskuje dostęp do wpisów tajnych i kluczy. Administratorzy mogą kontrolować cykl życia klucza. Mogą oni wdrażać nowe wersje klucza, tworzyć jego kopie zapasowe i wykonywać powiązane zadania.
- **Zasób** — zasób to dostępny za pośrednictwem platformy Azure element, którym można zarządzać. Niektóre typowe zasoby to: maszyna wirtualna, konto magazynu, aplikacja internetowa czy sieć wirtualna. Istnieje ich jednak wiele więcej.
- **Grupa zasobów** — grupa zasobów to kontener, który zawiera powiązane zasoby dla rozwiązania platformy Azure. Grupa zasobów może zawierać wszystkie zasoby dla rozwiązania lub tylko te zasoby, które mają być zarządzane jako grupa. Użytkownik decyduje o sposobie przydziału zasobów do grup zasobów pod kątem tego, co jest najbardziej odpowiednie dla danej organizacji.
- **Użytkownik magazynu** — użytkownik magazynu może wykonywać akcje na zasobach wewnątrz magazynu kluczy, jeśli właściciel magazynu udzieli mu dostępu. Dostępne akcje zależą od przyznanych uprawnień.
- **[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md)**: Azure AD to usługa Active Directory dla dzierżawy. Każdy katalog ma co najmniej jedną domenę. Katalog może mieć wiele skojarzonych subskrypcji, ale tylko jedną dzierżawę. 
- **Identyfikator dzierżawy Azure** — identyfikator dzierżawy to unikatowy sposób identyfikacji wystąpienia usługi Azure AD w ramach subskrypcji platformy Azure.
- **Tożsamość usługi zarządzanej** — usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych kluczy i wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. Tożsamość usługi zarządzanej ułatwia rozwiązywanie tego problemu, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w usłudze Key Vault lub dowolnej innej usłudze obsługującej uwierzytelnianie usługi Azure AD bez konieczności przechowywania poświadczeń w kodzie. Aby uzyskać więcej informacji, zobacz [Managed Service Identity (MSI) for Azure resources (Tożsamość usługi zarządzanej (MSI) dla zasobów platformy Azure)](../active-directory/managed-service-identity/overview.md).

    ![Diagram przedstawiający sposób działania tożsamości usługi zarządzanej](./media/key-vault-whatis/msi.png)

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

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z samouczkiem wprowadzającym dla administratora, zobacz [Rozpoczynanie pracy z usługą Azure Key Vault](key-vault-get-started.md).

Aby uzyskać więcej informacji na temat rejestrowania użycia usługi Key Vault, zobacz [Funkcja rejestrowania usługi Azure Key Vault](key-vault-logging.md).

Aby uzyskać więcej informacji na temat używania kluczy i wpisów tajnych w usłudze Azure Key Vault, zobacz [Informacje o kluczach, wpisach tajnych i certyfikatach](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Usługa Azure Key Vault jest dostępna w większości regionów. Aby uzyskać więcej informacji, zobacz stronę [Cennik usługi Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
