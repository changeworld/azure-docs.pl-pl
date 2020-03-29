---
title: 'Usługi Azure Databricks: Typowe pytania i pomoc'
description: Uzyskaj odpowiedzi na często zadawane pytania i informacje dotyczące rozwiązywania problemów z usługą Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671575"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Często zadawane pytania dotyczące usługi Azure Databricks

W tym artykule wymieniono najważniejsze pytania, które mogą być związane z usługą Azure Databricks. Zawiera również listę niektórych typowych problemów, które mogą mieć podczas korzystania z Databricks. Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Czy mogę używać usługi Azure Key Vault do przechowywania kluczy/wpisów tajnych, które mają być używane w usłudze Azure Databricks?
Tak. Usługi Azure Key Vault można używać do przechowywania kluczy/wpisów tajnych do użycia z usługą Azure Databricks. Aby uzyskać więcej informacji, zobacz [Zakresy wspierane przez usługę Azure Key Vault](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Czy mogę korzystać z sieci wirtualnych platformy Azure z databricks?
Tak. Można użyć sieci wirtualnej platformy Azure (VNET) z usługi Azure Databricks. Aby uzyskać więcej informacji, zobacz [Wdrażanie usługi Azure Databricks w sieci wirtualnej platformy Azure.](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Jak uzyskać dostęp do usługi Azure Data Lake Storage z notesu? 

Wykonaj następujące kroki:
1. W usłudze Azure Active Directory (Azure AD) aprowizuj jednostkę usługi i zarejestruj jej klucz.
1. Przypisz niezbędne uprawnienia do jednostki usługi w magazynie usługi Usługi Lake.
1. Aby uzyskać dostęp do pliku w magazynie usługi Usługi Lake, użyj poświadczeń głównej usługi w notesie.

Aby uzyskać więcej informacji, zobacz [Korzystanie z usługi Azure Data Lake Storage z usługą Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Rozwiązywanie typowych problemów

Oto kilka problemów, które mogą wystąpić z Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: Ta subskrypcja nie jest zarejestrowana do używania obszaru nazw "Microsoft.Databricks"

#### <a name="error-message"></a>Komunikat o błędzie

"Ta subskrypcja nie jest zarejestrowana do korzystania z obszaru nazw 'Microsoft.Databricks'. Zobacz, https://aka.ms/rps-not-found jak zarejestrować subskrypcje. (Kod: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Wybierz **pozycję Subskrypcje**, używasz subskrypcji, a następnie **dostawców zasobów**. 
1. Na liście dostawców zasobów w obszarze **Microsoft.Databricks**wybierz pozycję **Zarejestruj**. Aby zarejestrować dostawcę zasobów, musisz mieć rolę współautora lub właściciela w subskrypcji.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Twoje konto {email} nie ma roli właściciela ani współautora w zasobie obszaru roboczego Databricks w witrynie Azure portal

#### <a name="error-message"></a>Komunikat o błędzie

"Twoje konto {email} nie ma roli właściciela ani współautora w zasobie obszaru roboczego Databricks w witrynie Azure portal. Ten błąd może również wystąpić, jeśli jesteś użytkownikiem-gościem w dzierżawie. Poproś administratora o udzielenie ci dostępu lub dodanie Cię jako użytkownika bezpośrednio w obszarze roboczym Databricks." 

#### <a name="solution"></a>Rozwiązanie

Oto kilka rozwiązań tego problemu:

* Aby zainicjować dzierżawy, należy zalogować się jako zwykły użytkownik dzierżawy, a nie jako użytkownik-gość. Musisz również mieć rolę współautora w zasobie obszaru roboczego Databricks. Dostęp użytkownika można udzielić na karcie **Kontrola dostępu (IAM)** w obszarze roboczym Databricks w witrynie Azure portal.

* Ten błąd może również wystąpić, jeśli nazwa domeny e-mail jest przypisana do wielu katalogów w usłudze Azure AD. Aby obejść ten problem, należy utworzyć nowego użytkownika w katalogu, który zawiera subskrypcję z obszaru roboczego Databricks.

    a. W witrynie Azure portal przejdź do usługi Azure AD. Wybierz **pozycję Użytkownicy i grupy** > **Dodaj użytkownika**.

    b. Dodaj użytkownika za `@<tenant_name>.onmicrosoft.com` pomocą wiadomości `@<your_domain>` e-mail zamiast wiadomości e-mail. Tę opcję można znaleźć w **domenach niestandardowych**w obszarze Azure AD w witrynie Azure portal.
    
    d. Przyznaj temu nowemu użytkownikowi rolę **współautora** w zasobie obszaru roboczego Databricks.
    
    d. Zaloguj się do witryny Azure portal z nowym użytkownikiem i znajdź obszar roboczy Databricks.
    
    e. Uruchom obszar roboczy Databricks jako ten użytkownik.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Twoje konto {e-mail} nie zostało zarejestrowane w Databricks 

#### <a name="solution"></a>Rozwiązanie

Jeśli obszar roboczy nie został utworzony i zostanie dodany jako użytkownik, skontaktuj się z osobą, która utworzyła obszar roboczy. Niech ta osoba dodasz Cię za pomocą konsoli administracyjnej usługi Azure Databricks. Aby uzyskać instrukcje, zobacz [Dodawanie użytkowników i zarządzanie nimi](/azure/databricks/administration-guide/users-groups/users). Jeśli utworzono obszar roboczy i nadal pojawia się ten błąd, spróbuj ponownie wybrać opcję **Inicjuj obszar roboczy** z witryny Azure portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: Błąd uruchamiania dostawcy chmury podczas konfigurowania klastra (PublicIPCountLimitReached)

#### <a name="error-message"></a>Komunikat o błędzie

"Błąd uruchamiania dostawcy chmury: podczas konfigurowania klastra wystąpił błąd dostawcy chmury. Aby uzyskać więcej informacji, zobacz Przewodnik Databricks. Kod błędu platformy Azure: PublicIPCountLimitReached. Komunikat o błędzie platformy Azure: nie można utworzyć więcej niż 10 publicznych adresów IP dla tej subskrypcji w tym regionie."

#### <a name="background"></a>Tło

Klastry Databricks używają jednego publicznego adresu IP na węzeł (w tym węzła sterownika). Subskrypcje platformy Azure mają [limity publicznego adresu IP](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) dla regionu. W związku z tym tworzenie klastra i skalowanie w górę operacji może zakończyć się niepowodzeniem, jeśli spowoduje to, że liczba publicznych adresów IP przydzielonych do tej subskrypcji w tym regionie przekroczy limit. Ten limit obejmuje również publiczne adresy IP przydzielone do użycia innych niż Databricks, takie jak niestandardowe maszyny wirtualne zdefiniowane przez użytkownika.

Ogólnie rzecz biorąc klastry zużywają publiczne adresy IP tylko wtedy, gdy są aktywne. Jednak `PublicIPCountLimitReached` błędy mogą nadal występować przez krótki okres czasu, nawet po zakończeniu innych klastrów. Dzieje się tak, ponieważ Databricks tymczasowo buforuje zasoby platformy Azure po zakończeniu klastra. Buforowanie zasobów jest zgodne z projektem, ponieważ znacznie zmniejsza opóźnienie uruchamiania klastra i skalowania automatycznego w wielu typowych scenariuszach.

#### <a name="solution"></a>Rozwiązanie

Jeśli subskrypcja osiągnęła już limit publicznego adresu IP dla danego regionu, należy wykonać jedną lub drugą z następujących czynności.

- Tworzenie nowych klastrów w innym obszarze roboczym Databricks. Inny obszar roboczy musi znajdować się w regionie, w którym nie osiągnięto limitu publicznego adresu IP subskrypcji.
- [Poproś o zwiększenie limitu publicznego adresu IP](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Wybierz **przydział** jako **typ problemu,** a **sieć: ARM** jako typ **przydziału**. W **szczegółów**, zażądać zwiększenia przydziału publicznego adresu IP. Na przykład jeśli limit wynosi obecnie 60 i chcesz utworzyć klaster 100 węzłów, zażądaj zwiększenia limitu do 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: Drugi typ niepowodzenia uruchamiania dostawcy chmury podczas konfigurowania klastra (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Komunikat o błędzie

"Błąd uruchamiania dostawcy chmury: podczas konfigurowania klastra wystąpił błąd dostawcy chmury. Aby uzyskać więcej informacji, zobacz Przewodnik Databricks.
Kod błędu platformy Azure: Komunikat o błędzie MissingSubscriptionRegistration Azure: Subskrypcja nie jest zarejestrowana w celu używania obszaru nazw "Microsoft.Compute". Zobacz, https://aka.ms/rps-not-found jak zarejestrować subskrypcje."

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
1. Wybierz **pozycję Subskrypcje**, używasz subskrypcji, a następnie **dostawców zasobów**. 
1. Na liście dostawców zasobów w obszarze **Microsoft.Compute**wybierz pozycję **Zarejestruj**. Aby zarejestrować dostawcę zasobów, musisz mieć rolę współautora lub właściciela w subskrypcji.

Aby uzyskać bardziej szczegółowe instrukcje, zobacz [Dostawcy zasobów i typy](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Usługa Azure Databricks potrzebuje uprawnień dostępu do zasobów w organizacji, które może udzielić tylko administrator.

#### <a name="background"></a>Tło

Usługa Azure Databricks jest zintegrowana z usługą Azure Active Directory. Można ustawić uprawnienia w ramach usługi Azure Databricks (na przykład w notesach lub klastrach), określając użytkowników z usługi Azure AD. Aby usługa Azure Databricks mogła wyświetlić listę nazw użytkowników z usługi Azure AD, wymaga uprawnienia do odczytu tych informacji i zgody, które mają być udzielone. Jeśli zgoda nie jest jeszcze dostępna, zostanie wyświetlony błąd.

#### <a name="solution"></a>Rozwiązanie

Zaloguj się jako administrator globalny w witrynie Azure portal. W przypadku usługi Azure Active Directory przejdź do karty **Ustawienia użytkownika** i upewnij się, że **użytkownicy mogą wyrazić zgodę na dostęp do danych firmy w ich imieniu** na **Tak**.

## <a name="next-steps"></a>Następne kroki

- [Szybki start: wprowadzenie do usługi Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co to jest Azure Databricks?](what-is-azure-databricks.md)
