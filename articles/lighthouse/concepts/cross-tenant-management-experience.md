---
title: Zarządzanie międzydzierżawowa oferujące morskiej Azure
description: Zarządzanie zasobami platformy Azure delegowanego umożliwia to środowisko do zarządzania w wielu dzierżawach.
author: JnHs
ms.service: lighthouse
ms.author: jenhayes
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 15454d4b3f0abad6166c4b163df6c8652669d649
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809904"
---
# <a name="cross-tenant-management-experiences"></a>Zarządzanie dzierżawą dla wielu środowisk

W tym artykule opisano scenariusze, które, jako dostawca usług za pomocą [Azure delegowane zarządzanie zasobami](../concepts/azure-delegated-resource-management.md) do zarządzania zasobami platformy Azure dla wielu klientów z w ramach dzierżawy w [witryny Azure portal ](https://portal.azure.com).

> [!NOTE]
> Zarządzanie zasobami platformy Azure delegowanego można również w przedsiębiorstwie, który ma wiele dzierżaw własne w celu ułatwienia administracji w wielu dzierżawach.

## <a name="understanding-customer-tenants"></a>Informacje o dzierżawach klienta

Dzierżawy usługi Azure Active Directory (Azure AD) to reprezentacja organizacji. Jest to dedykowane wystąpienie usługi Azure AD organizacja otrzymuje podczas tworzenia relacji z firmą Microsoft przez zarejestrowanie się do platformy Azure, Microsoft 365 lub innych usług. Każda dzierżawa usługi Azure AD jest odrębna i oddzielona od innych dzierżaw usługi Azure AD, a ma swój własny identyfikator dzierżawy (GUID). Aby uzyskać więcej informacji, zobacz [co to jest Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis)

Zazwyczaj aby można było zarządzać zasobami platformy Azure dla klientów, dostawców usług musi zalogować się do witryny Azure portal przy użyciu konta skojarzone z dzierżawcą tego klienta, wymagających administratorem w dzierżawie klienta, aby utworzyć i zarządzać kontami użytkowników w przypadku dostawcy usług.

Za pomocą funkcji zarządzania usługi Azure resource delegowanego procesu dołączania określa użytkowników w dzierżawie dostawcy usług, którzy będą mogli uzyskać dostęp i zarządzanie nimi, subskrypcji, grupy zasobów i zasobów w dzierżawie klienta. Tacy użytkownicy mogą następnie zaloguj się do witryny Azure portal przy użyciu własnych poświadczeń. W witrynie Azure portal mogą zarządzać zasobami należącymi do wszystkich klientów, do których mają dostęp. Można to zrobić, odwiedzając [moich klientów](../how-to/view-manage-customers.md) strony w witrynie Azure portal lub, pracując bezpośrednio w kontekście tego klienta subskrypcji, w witrynie Azure portal lub za pośrednictwem interfejsów API.

Zarządzanie zasobami platformy Azure delegowanego umożliwia większą elastyczność i zarządzanie zasobami dla wielu klientów bez konieczności logowania się na różnych kontach w różnych dzierżawach. Na przykład dostawcy usług mogą mieć trzech klientów przy użyciu różne obowiązki i poziomy dostępu, jak pokazano poniżej:

![Trzy dzierżaw klientów przedstawiający obowiązki dostawcy usług](../media/azure-delegated-resource-management-customer-tenants.jpg)

Za pomocą usługi Azure resource delegowanego zarządzania autoryzowani użytkownicy zalogować się do dzierżawy dostawcy usług dostępu do tych zasobów, jak pokazano poniżej:

![Zasoby dla klientów zarządzanych przy użyciu jednej usługi dostawcy dzierżawy](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="supported-services-and-scenarios"></a>Obsługiwane usługi i scenariusze

Obecnie możliwości zarządzania międzydzierżawowa obsługuje następujące scenariusze z zasobami delegowanego klienta:

[Usługa Azure Automation](https://docs.microsoft.com/azure/automation/):

- Użyj konta usługi automation, aby dostępu oraz pracy z zasobami klienta delegowanego

[Azure Backup](https://docs.microsoft.com/azure/backup/):

- Tworzenie kopii zapasowej i przywracanie danych klientów w dzierżawach klienta

[Usługa Azure Kubernetes Service (AKS)](https://docs.microsoft.com//azure/aks/):

- Zarządzanie środowiskami usługi hostowanej platformy Kubernetes i wdrażać konteneryzowane aplikacje i zarządzać w ramach dzierżawy klienta

[Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/):

- Wyświetl alerty dla delegowanego subskrypcje w witrynie Azure portal lub programowo przy użyciu wywołań interfejsu API REST, możliwość wyświetlania alertów dla wszystkich subskrypcji
- Wyświetl szczegóły dziennika aktywności dla delegowanego subskrypcji
- Analiza dzienników: Wykonywanie zapytań dotyczących danych w obszarach roboczych klienta zdalnego, w wielu dzierżaw

[Usługa Azure Policy](https://docs.microsoft.com/azure/governance/policy/):

- Migawki zgodności Pokaż szczegóły dla przypisanych zasad w ramach subskrypcji delegowanego
- Tworzenie i edytowanie definicji zasad w ramach subskrypcji delegowanego
- Przypisywanie definicji zasad zdefiniowanych przez klienta w ramach subskrypcji delegowanego
- Klienci widzą zasad utworzonych przez dostawcę usług wraz z jakiekolwiek zasady, które zostały one utworzone samodzielnie
- Koryguje deployIfNotExists przypisania w ramach dzierżawy klienta, jeśli dla klienta została skonfigurowana tożsamość zarządzaną i *roleDefinitionIds* dla tego przypisania zasad

[Wykres zasobów platformy Azure](https://docs.microsoft.com/azure/governance/resource-graph/):

- W wynikach kwerendy zwrócone, dzięki czemu możesz ustalić, czy subskrypcja należy do dzierżawy klienta lub dzierżawa dostawcy usługi obejmuje teraz identyfikator dzierżawy

[Usługa Azure Security Center](https://docs.microsoft.com/azure/security-center/):

- Widoczność międzydzierżawowe
  - Monitorowanie zgodności z zasadami zabezpieczeń i upewnij się zakresu zabezpieczeń, w zasobach wszystkich dzierżaw
  - Zgodność z przepisami ciągłe monitorowanie z przekraczaniem wielu klientów w ramach jednego widoku
  - Monitorowanie, klasyfikacja i priorytety informacje z możliwością działania zabezpieczeń zalecenia za pomocą secure obliczania wyniku
- Zarządzanie ogólnego poziomu zabezpieczeń międzydzierżawowe
  - Zarządzanie zasadami zabezpieczeń
  - Podejmowanie akcji na zasoby, które są niezgodne z zaleceniami dotyczącymi zabezpieczeń informacje z możliwością działania
  - Zbieranie i przechowywanie danych związanych z zabezpieczeniami
- Wykrywanie zagrożeń międzydzierżawowy i ochrony
  - Wykrywanie zagrożeń w zasobach dzierżawców
  - Stosowanie kontrolek ochrony zaawansowanych zagrożeń, takich jak just-in-time (JIT) w maszynie Wirtualnej dostępu
  - Utrwalanie konfiguracji grupy zabezpieczeń sieci z adaptacyjne wzmacniania ochrony sieci
  - Upewnij się, że serwery działają tylko aplikacji i procesów, które należy je funkcje adaptacyjnego sterowania aplikacjami
  - Monitor zmienia się na ważnych plików i wpisy rejestru za pomocą pliku Integrity Monitoring (FIM)

[Azure Service Health](https://docs.microsoft.com/azure/service-health/):

- Monitorowanie kondycji zasobów klienta za pomocą usługi Azure Resource Health
- Śledź kondycję usług platformy Azure używane przez klientów

[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/):

- Zarządzanie opcje odzyskiwania po awarii dla maszyn wirtualnych platformy Azure w dzierżawach klienta (należy zauważyć, że nie można użyć konta Uruchom jako można skopiować rozszerzenia maszyn wirtualnych)

[Usługa Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/):

- Użyj rozszerzenia maszyny wirtualnej, aby podać po wdrożeniu konfiguracji oraz zadania automatyzacji na maszynach wirtualnych platformy Azure w dzierżawach klienta
- Rozwiązywanie problemów z maszynami wirtualnymi platformy Azure w dzierżawach klientów przy użyciu diagnostyki rozruchu
- Dostęp do maszyn wirtualnych, za pomocą konsoli szeregowej w dzierżawach klienta
- Pamiętaj, że usługi Azure Active Directory nie można używać do logowania zdalnego z maszyną wirtualną maszyny Wirtualnej nie można zintegrować z usługą Key Vault hasła, wpisy tajne i klucze szyfrowania do szyfrowania dysku

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/):

- Wdrażanie i zarządzanie sieciami wirtualnymi i wirtualne karty sieciowe (vNICs) w ramach dzierżawy klienta

Żądania pomocy technicznej:

- Otwierać żądania pomocy technicznej dla delegowanego zasobów z **Pomoc i obsługa techniczna** bloku w witrynie Azure portal (Wybieranie planu pomocy technicznej dostępne dla delegowanego zakresu)

Za pomocą wszystkich scenariuszy należy pamiętać o następujących ograniczeniach bieżący:

- Żądań obsługiwanych przez usługę Azure Resource Manager mogą być wykonywane przy użyciu usługi Azure resource delegowanego management. Operacja identyfikatorów URI dla tych żądań, rozpoczynać `https://management.azure.com`. Jednakże żądań, które są obsługiwane przez wystąpienie typu zasobu (np. dostęp do wpisów tajnych usługi KeyVault lub dostępu do magazynu danych) nie są obsługiwane za pomocą funkcji zarządzania usługi Azure resource delegowanego. Operacja identyfikatorów URI te żądania zaczynają się zwykle adres który jest unikatowy dla wystąpienia, takie jak `https://myaccount.blob.core.windows.net` lub `https://mykeyvault.vault.azure.net/`. One również są zwykle operacje na danych, a nie operacji zarządzania. 
- Przypisania ról, należy użyć kontroli dostępu opartej na rolach (RBAC) [wbudowane role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). Wszystkie wbudowane role są obecnie obsługiwane za pomocą funkcji zarządzania usługi Azure resource delegowanego z wyjątkiem właściciela, Administrator dostępu użytkowników lub wszystkie wbudowane role z [elementy DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) uprawnień. Role niestandardowe i [ról administratora klasyczni](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators) również nie są obsługiwane.
- Obecnie nie można dołączyć subskrypcji (lub grupy zasobów w ramach subskrypcji) na platformie Azure delegować zarządzanie zasobami, jeśli subskrypcja używa usługi Azure Databricks. Podobnie jeśli subskrypcja została zarejestrowana dla dołączania przy użyciu **Microsoft.ManagedServices** dostawcy zasobów nie można utworzyć obszar roboczy usługi Databricks dla tej subskrypcji w tej chwili.

## <a name="using-apis-and-management-tools-with-cross-tenant-management"></a>Za pomocą narzędzia do zarządzania i interfejsów API za pomocą funkcji zarządzania wielu dzierżawach

Obsługiwane usługi i wymienione powyżej można wykonywać zadania zarządzania, bezpośrednio w portalu lub przy użyciu narzędzia interfejsów API i zarządzania (na przykład wiersza polecenia platformy Azure i programu Azure PowerShell). Wszystkie istniejące interfejsy API mogą służyć podczas pracy z zasobami delegowanego (dla usługi, które są obsługiwane).

Istnieją również interfejsy API specyficzne dla wykonywanie zadań zarządzania platformy Azure zasobu delegowanego. Aby uzyskać więcej informacji, zobacz **odwołania** sekcji.


## <a name="next-steps"></a>Następne kroki

- Dołączanie klientów na platformie Azure delegowane zarządzanie zasobami, albo przez [przy użyciu szablonów usługi Azure Resource Manager](../how-to/onboard-customer.md) lub [publikowanie oferty usług zarządzanych prywatną lub publiczną w portalu Azure Marketplace](../how-to/publish-managed-services-offers.md).
- [Wyświetlanie i zarządzanie nimi klienci](../how-to/view-manage-customers.md) , przechodząc do **moich klientów** w witrynie Azure portal.