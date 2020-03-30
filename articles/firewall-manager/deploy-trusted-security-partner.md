---
title: Wdrażanie zaufanego partnera zabezpieczeń usługi Azure Firewall Manager
description: Dowiedz się, jak wdrożyć zaufane zabezpieczenia usługi Azure Firewall Manager za pomocą witryny Azure portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73931304"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>Wdrażanie zaufanego partnera w zakresie zabezpieczeń (wersja zapoznawcza)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

*Zaufani partnerzy zabezpieczeń* w usłudze Azure Firewall Manager umożliwiają korzystanie ze znanych, najlepiej znanych w swojej branży ofert zabezpieczeń jako usługi (SECaaS) innych firm w celu ochrony dostępu do Internetu dla użytkowników.

Aby dowiedzieć się więcej o obsługiwanych scenariuszach i wskazówkach dotyczących najlepszych praktyk, zobacz [Co to są zaufani partnerzy zabezpieczeń (wersja zapoznawcza)?](trusted-security-partners.md).

Obsługiwanymi partnerami zabezpieczeń są **ZScaler** i **iboss** dla tej wersji zapoznawczej. Obsługiwane regiony to WestCentralUS, NorthCentralUS, WestUS, WestUS2 i EastUS.

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> Usługa Azure Firewall Manager Preview musi `Register-AzProviderFeature` być jawnie włączona za pomocą polecenia programu PowerShell.

W wierszu polecenia programu PowerShell uruchom następujące polecenia:

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
Rejestracja funkcji trwa do 30 minut. Uruchom następujące polecenie, aby sprawdzić swój status rejestracji:

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>Wdrażanie zewnętrznego dostawcy zabezpieczeń w nowym centrum

1. Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.
2. W **polu Szukaj**wpisz **Menedżera zapory** i wybierz go w obszarze **Usługi**.
3. Przejdź do **pozycji Wprowadzenie**. Wybierz **pozycję Utwórz zabezpieczoną koncentrator wirtualny**. 
4. Wprowadź subskrypcję i grupę zasobów, wybierz obsługiwany region i dodaj informacje o koncentratorze i wirtualnej sieci WAN. 
5. **Wdrażanie bramy sieci VPN** jest domyślnie włączone. Brama sieci VPN jest wymagana do wdrożenia zaufanego partnera zabezpieczeń w centrum. 
6. Wybierz **dalej: Zapora platformy Azure**
   > [!NOTE]
   > Zaufani partnerzy zabezpieczeń łączą się z Twoim centrum za pomocą tuneli bramy SIECI VPN. Jeśli usuniesz bramę VPN, połączenia z Zaufanymi partnerami bezpieczeństwa zostaną utracone.
7. Jeśli chcesz wdrożyć Zaporę platformy Azure, aby filtrować ruch prywatny wraz z dostawcą usług innych firm w celu filtrowania ruchu internetowego, wybierz zasadę dla Zapory platformy Azure. Zobacz [obsługiwane scenariusze](trusted-security-partners.md#key-scenarios).
8. Jeśli chcesz wdrożyć tylko zewnętrznego dostawcę zabezpieczeń w centrum, wybierz **pozycję Zapora platformy Azure: Włączono/Wyłączone,** aby ustawić ją na **Wyłączona**. 
9. **Wybierz dalej: Zaufani partnerzy w dziedzinie bezpieczeństwa**.
10. Wybierz **pozycję Zaufany partner zabezpieczeń,** aby ustawić go na **Włączone**. Wybierz partnera. 
11. Wybierz **pozycję Dalej**. 
12. Przejrzyj zawartość, a następnie wybierz pozycję **Utwórz**.

Wdrożenie bramy sieci VPN może potrwać dłużej niż 30 minut.

Aby sprawdzić, czy centrum zostało utworzone, przejdź do usługi Azure Firewall Manager->zabezpieczonych koncentratorów. Wybierz stronę Przegląd >koncentratora, aby wyświetlić nazwę partnera i stan **oczekiwania na połączenie zabezpieczeń**.

Po utworzeniu koncentratora i skonfigurowaniu partnera zabezpieczeń kontynuuj łączenie dostawcy zabezpieczeń z koncentratorem.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>Wdrażanie zewnętrznego dostawcy zabezpieczeń w istniejącym centrum

Można również wybrać istniejący koncentrator w wirtualnej sieci WAN i przekonwertować go na *zabezpieczony koncentrator wirtualny*.

1. W **obszarze Wprowadzenie**wybierz pozycję **Konwertuj istniejące koncentratory**.
2. Wybierz subskrypcję i istniejące centrum. Wykonaj pozostałe kroki, aby wdrożyć dostawcę innej firmy w nowym centrum.

Należy pamiętać, że brama sieci VPN musi zostać wdrożona w celu konwersji istniejącego koncentratora na zabezpieczony koncentrator z dostawcami zewnętrznymi.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>Konfigurowanie zewnętrznych dostawców zabezpieczeń do łączenia się z zabezpieczonym koncentratorem

Aby skonfigurować tunele do bramy sieci VPN centrum wirtualnego, dostawcy zewnętrzni potrzebują praw dostępu do centrum. Aby to zrobić, skojarz jednostkę usługi z subskrypcją lub grupą zasobów i przyznaj prawa dostępu. Następnie należy podać te poświadczenia do innej firmy przy użyciu ich portalu.

1. Utwórz jednostkę usługi Azure Active Directory (AD): możesz pominąć adres URL przekierowania. 

   [Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. Dodaj prawa dostępu i zakres dla jednostki usługi.
   [Jak: Użyj portalu, aby utworzyć aplikację i jednostkę usługi Azure AD, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > Można ograniczyć dostęp tylko do grupy zasobów dla kontroli bardziej szczegółowe.
3. Postępuj zgodnie z [instrukcjami dotyczącymi integracji wirtualnej sieci WAN platformy Microsoft Azure](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) w celu:

   - Zaloguj się do portalu partnera i dodaj poświadczenia, aby dać zaufanemu partnerowi dostęp do zabezpieczonego centrum.
   - Zsynchronizuj koncentratory wirtualne w portalu partnera i skonfiguruj tunel do koncentratora wirtualnego. Można to zrobić po sprawdzeniu poprawności poświadczeń uwierzytelniania usługi Azure AD.
   
4. Możesz sprawdzić stan tworzenia tunelu w portalu azure virtual w sieci WAN na platformie Azure. Po wyświetleniu tuneli **połączonych** na platformie Azure i w portalu partnerskim przejdź do kolejnych kroków, aby skonfigurować trasy, aby wybrać gałęzie i sieci wirtualne, które powinny wysyłać ruch internetowy do partnera.

## <a name="configure-route-settings"></a>Konfigurowanie ustawień trasy

1. Przejdź do usługi Azure Firewall Manager -> zabezpieczonych koncentratorów. 
2. Wybierz koncentrator. Stan Centrum powinien teraz wyświetlać **aprowizacji** zamiast **połączenia zabezpieczeń oczekujące**.

   Upewnij się, że dostawca innej firmy może połączyć się z koncentratorem. Tunele na bramie sieci VPN powinny być w stanie **Połączony.** Ten stan jest bardziej odzwierciedlające kondycji połączenia między koncentratora i partnera innej firmy, w porównaniu do poprzedniego stanu.
3. Wybierz koncentrator i przejdź do **pozycji Ustawienia trasy**.

   Po wdrożeniu dostawcy zewnętrznego do koncentratora centrum konwertuje centrum na *zabezpieczone centrum wirtualne.* Gwarantuje to, że dostawca zewnętrzny reklamuje trasę 0.0.0.0/0 (domyślnie) do koncentratora. Jednak połączenia sieci wirtualnej i lokacje połączone z koncentratorem nie otrzymują tej trasy, chyba że zdecydujesz się na to, które połączenia powinny uzyskać tę trasę domyślną.
4. W obszarze **Ruch internetowy**wybierz pozycję **Sieć wirtualna do Internetu** lub **Sieć-Internet** lub oba, aby trasy były skonfigurowane za pośrednictwem strony trzeciej.

   Wskazuje tylko, który typ ruchu powinien być kierowany do koncentratora, ale nie ma to jeszcze wpływu na trasy w sieciach wirtualnych lub oddziałach. Trasy te nie są propagowane do wszystkich sieci wirtualnych/oddziałów dołączonych do koncentratora domyślnie.
5. Należy wybrać **bezpieczne połączenia** i wybrać połączenia, na których należy ustawić te trasy. Wskazuje, które sieci wirtualne/gałęzie mogą rozpocząć wysyłanie ruchu internetowego do dostawcy zewnętrznego.
6. W **obszarze Ustawienia trasy**wybierz pozycję Bezpieczne **połączenia** w obszarze Ruch internetowy, a następnie wybierz sieć wirtualną lub gałęzie *(lokacje* w wirtualnej sieci WAN), które mają być zabezpieczone. Wybierz **pozycję Bezpieczny ruch internetowy**.
   ![Bezpieczny ruch internetowy](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. Przejdź z powrotem do strony koncentratorów. Status **zaufanego partnera zabezpieczeń** centrum powinien być teraz **zabezpieczony.**

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>Ruch internetowy oddziału lub sieci wirtualnej za pośrednictwem usługi innej firmy

Następnie można sprawdzić, czy maszyny wirtualne sieci wirtualnej lub lokacji oddziału mogą uzyskać dostęp do Internetu i sprawdzić, czy ruch jest przepływa do usługi innej firmy.

Po zakończeniu kroków ustawiania trasy maszyny wirtualne sieci wirtualnej oraz lokacje w oddziale są wysyłane 0/0 do trasy usługi innej firmy. Nie można RDP lub SSH do tych maszyn wirtualnych. Aby się zalogować, można wdrożyć usługę [Bastion platformy Azure](../bastion/bastion-overview.md) w sieci wirtualnej w trybie równorzędnym.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Zabezpiecz sieć w chmurze za pomocą usługi Azure Firewall Manager Preview przy użyciu portalu Azure](secure-cloud-network.md)




