---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: c44b39effdc6d8fcdc144915ec7b51489e3798cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66145854"
---
Należy chronić maszyny wirtualnej (VM) dla aplikacji, które zostanie uruchomione. Zabezpieczanie maszyn wirtualnych może zawierać jeden lub więcej usług platformy Azure, jak i funkcji, które obejmują bezpieczny dostęp do maszyn wirtualnych i bezpieczne przechowywanie danych. Ten artykuł zawiera informacje, która pozwala zapewnić bezpieczeństwo swoich maszyn wirtualnych i aplikacji.

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Nowoczesne zagrożeniach dla środowisk chmury jest dynamiczny, zwiększyć wykorzystanie, aby zachować skuteczną ochronę, aby zaspokoić potrzeby dotyczące zgodności i wymagań dotyczących zabezpieczeń. [Microsoft Antimalware dla platformy Azure](../articles/security/azure-security-antimalware.md) funkcja Bezpłatna ochrona w czasie rzeczywistym, który ułatwia identyfikowanie i usuwanie wirusów, programów szpiegujących oraz innego złośliwego oprogramowania. Alerty można skonfigurować tak, aby otrzymywać powiadomienia, gdy znany prób złośliwego lub niechcianego oprogramowania do zainstalowania lub uruchomienia na maszynie Wirtualnej.

## <a name="azure-security-center"></a>Azure Security Center

[Usługa Azure Security Center](../articles/security-center/security-center-intro.md) pomaga zapobiec, wykrywania i reagowania na zagrożenia na maszynach wirtualnych. Usługa Security Center zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

Dostęp just in time Security Center mogą być stosowane dla wdrożenia maszyny Wirtualnej do blokowania ruchu przychodzącego do maszyn wirtualnych platformy Azure, zmniejszenia narażenia na ataki przy zapewnianiu łatwego dostępu, aby nawiązać połączenie z maszynami wirtualnymi w razie. Gdy just in time jest włączone i użytkownik poprosi o dostęp do maszyny Wirtualnej, Centrum zabezpieczeń sprawdza uprawnienia, jakie użytkownik ma dla maszyny Wirtualnej. Jeśli mają odpowiednie uprawnienia, żądanie zostanie zatwierdzone, a usługa Security Center automatycznie skonfiguruje sieciowe grupy zabezpieczeń (NSG), aby zezwolić na ruch przychodzący do portów wybranych przez ograniczony czas. Po upływie czasu, usługa Security Center przywraca sieciowych grup zabezpieczeń do poprzedniego stanu. 

## <a name="encryption"></a>Szyfrowanie

Do rozszerzonego [Windows VM](../articles/virtual-machines/windows/encrypt-disks.md) i [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/encrypt-disks.md) zabezpieczeń i zgodności, dyski wirtualne na platformie Azure mogą być szyfrowane. Dyski wirtualne na maszynach wirtualnych Windows są szyfrowane za pomocą funkcji BitLocker. Dyski wirtualne na maszynach wirtualnych z systemem Linux są szyfrowane za pomocą dm-crypt. 

Nie ma opłat do szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usłudze Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane zgodnych ze standardami FIPS 140-2 poziom 2 standardy. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania dyski wirtualne dołączone do maszyny Wirtualnej. Zachowanie kontroli nad te klucze kryptograficzne i przeprowadzać ich inspekcje ich użycie. Jednostki usługi Azure Active Directory zapewnia mechanizm bezpiecznego wydawania te klucze szyfrowania, jak maszyny wirtualne są włączone, włączać i wyłączać.

## <a name="key-vault-and-ssh-keys"></a>Magazyn kluczy i kluczy SSH

Modelowane jako zasoby oraz podał wpisów tajnych i certyfikatów [usługi Key Vault](../articles/key-vault/key-vault-whatis.md). Azure PowerShell umożliwia tworzenie magazynów kluczy na potrzeby [maszyn wirtualnych Windows](../articles/virtual-machines/windows/key-vault-setup.md) i interfejsu wiersza polecenia platformy Azure dla [maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/key-vault-setup.md). Można również utworzyć klucze szyfrowania.

Zasady dostępu magazynu kluczy przyznają oddzielnie uprawnienia do kluczy, wpisów tajnych i certyfikatów. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy [zasad dostępu magazynu kluczy](../articles/key-vault/key-vault-secure-your-key-vault.md) nie obsługuje uprawnień na poziomie obiektu.

Podczas łączenia z maszyn wirtualnych, należy używać kryptografii klucza publicznego do Podaj Bezpieczniejszym sposobem Zaloguj się do nich. Ten proces polega na wymianę kluczy publicznych i prywatnych przy użyciu polecenia protokołu secure shell (SSH) do uwierzytelniania użytkownika, a nie nazwę użytkownika i hasło. Hasła są narażone na atak metodą siłową ataków, zwłaszcza na maszyny wirtualne połączone z Internetem, takich jak serwery sieci web. Przy użyciu protokołu secure shell (SSH) pary kluczy, można utworzyć [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) używającą kluczy SSH do uwierzytelniania, eliminując potrzebę hasła do logowania. Można również użyć kluczy SSH, nawiązać połączenie z [Windows VM](../articles/virtual-machines/linux/ssh-from-windows.md) do maszyny Wirtualnej z systemem Linux.

## <a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Typowym wyzwaniem podczas kompilowania aplikacji w chmurze jest sposób zarządzania poświadczeniami w kodzie w przypadku uwierzytelniania przy użyciu usług w chmurze. Zabezpieczanie poświadczeń to ważne zadanie. W idealnej sytuacji poświadczenia nie są nigdy wyświetlane na stacjach roboczych deweloperów ani zaewidencjonowane do kontroli źródła. Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń, wpisów tajnych i innych kluczy, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. 

Funkcja tożsamości zarządzanych dla platformy Azure w usłudze Azure Active Directory (Azure AD) rozwiązuje ten problem. Funkcja ta udostępnia usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Za pomocą tej tożsamości można uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym w usłudze Key Vault, bez konieczności umieszczania poświadczeń w kodzie.  Swój kod, który jest uruchomiony na maszynie Wirtualnej mogą żądać tokenu z dwa punkty końcowe, które są dostępne tylko z poziomu maszyny Wirtualnej. Aby uzyskać szczegółowe informacje o tej usłudze, przejrzyj [zarządzanych tożsamości dla zasobów platformy Azure](../articles/active-directory/managed-identities-azure-resources/overview.md) strona przeglądu.   

## <a name="policies"></a>Zasady

[Zasady usługi Azure](../articles/azure-policy/azure-policy-introduction.md) może służyć do definiowania żądane zachowanie dla Twojej organizacji [maszyn wirtualnych Windows](../articles/virtual-machines/windows/policy.md) i [maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/policy.md). Za pomocą zasad, organizacja może wymusić różnych konwencji i reguł w całym przedsiębiorstwie. Wymuszanie żądane zachowanie może pomóc w zmniejszeniu ryzyka, przyczyniając się do sukcesu organizacji.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Za pomocą [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md), można segregować obowiązki w ramach zespołu i udzielać uprawnień dostępu użytkownikom na maszynie Wirtualnej, których potrzebują do wykonywania swoich zadań. Zamiast wszystkich nieograniczone uprawnienia na maszynie Wirtualnej, można zezwolić tylko określone akcje. Możesz skonfigurować kontrolę dostępu dla maszyny Wirtualnej w [witryny Azure portal](../articles/role-based-access-control/role-assignments-portal.md)przy użyciu [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/role), lub[programu Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Kolejne kroki
- Przewodnik po krokach monitorowanie zabezpieczeń maszyny wirtualnej przy użyciu usługi Azure Security Center dla [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) lub [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).