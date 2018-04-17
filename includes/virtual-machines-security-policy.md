Należy chronić maszyny wirtualnej (VM) dla aplikacji, które zostanie uruchomione. Zabezpieczanie maszyn wirtualnych może zawierać jeden lub więcej usług platformy Azure i funkcje, które obejmują bezpieczny dostęp do maszyn wirtualnych i bezpiecznego magazynu danych. Ten artykuł zawiera informacje, które umożliwia zabezpieczenie sieci maszyny Wirtualnej i aplikacji.

## <a name="antimalware"></a>Oprogramowanie chroniące przed złośliwym kodem

Nowoczesne zagrożeń dla środowisk chmury jest dynamiczny, zwiększenie nacisku Obsługa skutecznej ochrony, aby spełnić wymagania dotyczące zabezpieczeń i zgodności. [Antimalware Microsoft Azure](../articles/security/azure-security-antimalware.md) to możliwość bezpłatnej ochrony w czasie rzeczywistym, ułatwiający identyfikację oraz usunięcie wirusów, programów szpiegujących i innego złośliwego oprogramowania. Alerty można skonfigurować do powiadomienie, gdy wiadomo, że złośliwego lub niechcianego oprogramowania próbuje się zainstalować lub uruchomić na maszynie Wirtualnej.

## <a name="azure-security-center"></a>Azure Security Center

[Centrum zabezpieczeń Azure](../articles/security-center/security-center-intro.md) pomaga zapobiec, wykrywania i reagowania na zagrożenia dla maszyn wirtualnych. Centrum zabezpieczeń zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokim ekosystemem rozwiązań zabezpieczających.

## <a name="encryption"></a>Szyfrowanie

Dla rozszerzonego [maszyny Wirtualnej systemu Windows](../articles/virtual-machines/windows/encrypt-disks.md) i [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/encrypt-disks.md) zabezpieczeń i zgodności, dyski wirtualne na platformie Azure mogą być szyfrowane. Dyski wirtualne na maszynach wirtualnych systemu Windows są szyfrowane, gdy za pomocą funkcji Bitlocker. Dyski wirtualne na maszynach wirtualnych systemu Linux są szyfrowane, gdy przy użyciu dm-crypt. 

Jest bezpłatna dla szyfrowania dysków wirtualnych na platformie Azure. Klucze szyfrowania są przechowywane w usługi Azure Key Vault przy użyciu ochrony oprogramowania, lub możesz zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowane do FIPS 140-2 poziom 2 standardów. Te klucze szyfrowania są używane do szyfrowania i odszyfrowywania wirtualnych dysków dołączonych do maszyny Wirtualnej. Zachowanie kontroli nad tych kluczy kryptograficznych i przeprowadzić inspekcję ich używania. Nazwy głównej usługi Azure Active Directory zapewnia mechanizm bezpiecznego do wystawiania tych kluczy kryptograficznych, ponieważ maszyny wirtualne są zasilane i wyłączanie.

## <a name="key-vault-and-ssh-keys"></a>Magazyn kluczy i kluczy SSH

Formę zasobów i udostępnionych przez kluczy tajnych i certyfikatów [Key Vault](../articles/key-vault/key-vault-whatis.md). Można użyć programu Azure PowerShell do tworzenia magazynów kluczy dla [maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/key-vault-setup.md) i wiersza polecenia platformy Azure dla [maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/key-vault-setup.md). Można również utworzyć klucze szyfrowania.

Zasady dostępu do magazynu kluczy przyznać uprawnień dostępu do kluczy, kluczy tajnych i certyfikaty oddzielnie. Na przykład można udzielić użytkownikowi dostępu tylko do kluczy, ale żadnych uprawnień do wpisów tajnych. Uprawnienia dostępu do kluczy, wpisów tajnych lub certyfikatów są jednak przyznawane na poziomie magazynu. Innymi słowy [klucza magazynu zasad dostępu](../articles/key-vault/key-vault-secure-your-key-vault.md) nie obsługuje uprawnień na poziomie obiektu.

Po podłączeniu do maszyn wirtualnych, należy używać kryptografii klucza publicznego do zapewnienia bardziej bezpieczny sposób na logowanie się do nich. Ten proces obejmuje wymianę kluczy publicznych i prywatnych za pomocą polecenia bezpiecznej powłoki (SSH) do uwierzytelniania użytkownika, a nie nazwę użytkownika i hasło. Hasła są narażone na ataki, szczególnie w przypadku maszyn wirtualnych połączonych z Internetem, takich jak serwery sieci web siłowych. Pary kluczy bezpiecznej powłoki (SSH), umożliwia tworzenie [maszyny Wirtualnej systemu Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) używającą kluczy SSH do uwierzytelnienia, eliminując konieczność hasła do logowania. Umożliwia także kluczy SSH do łączenia z [maszyny Wirtualnej systemu Windows](../articles/virtual-machines/linux/ssh-from-windows.md) do maszyny Wirtualnej systemu Linux.

## <a name="policies"></a>Zasady

[Zasady usługi Azure](../articles/azure-policy/azure-policy-introduction.md) może służyć do definiowania zachowania odpowiednie dla Twojej organizacji [maszyn wirtualnych systemu Windows](../articles/virtual-machines/windows/policy.md) i [maszyn wirtualnych systemu Linux](../articles/virtual-machines/linux/policy.md). Korzystając z zasad, organizacja może wymusić różnych konwencje i zasady w całym przedsiębiorstwie. Wymuszanie zachowanie można zmniejszenia ryzyka podczas pracy nad dla sukcesu organizacji.

## <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Przy użyciu [kontroli dostępu opartej na rolach (RBAC)](../articles/role-based-access-control/overview.md), możesz rozdzielenie obowiązków w obrębie organizacji i udzielać użytkownikom tylko takiego dostępu na maszynie Wirtualnej, które są niezbędne do wykonywania swoich zadań. Zamiast nadanie każdy nieograniczonych uprawnień na Maszynie wirtualnej, można zezwolić tylko pewne akcje. Kontrola dostępu można skonfigurować dla maszyny Wirtualnej w [portalu Azure](../articles/role-based-access-control/role-assignments-portal.md)za pomocą [interfejsu wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/role), lub[programu Azure PowerShell](../articles/role-based-access-control/role-assignments-powershell.md).


## <a name="next-steps"></a>Kolejne kroki
- Zapoznaj się z artykułem kroki procedury monitorowania zabezpieczeń maszyny wirtualnej przy użyciu Centrum zabezpieczeń Azure dla [Linux](../articles/virtual-machines/linux/tutorial-azure-security.md) lub [Windows](../articles/virtual-machines/windows/tutorial-azure-security.md).